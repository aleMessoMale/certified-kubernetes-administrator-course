# Backup and Restore Methods
  - Take me to [Video Tutorial](https://kodekloud.com/topic/backup-and-restore-methods/)
  
In this section, we will take a look at backup and restore methods

## Backup Candidates
 
 ![bc](../../images/bc.PNG)

- Resource Configurations - magari salvate su repo e applicate con apply -f
- ETCD cluster
- Persistent Volume
 
## Resource Configuration
- Imperative way
  
  ![rci](../../images/rci.PNG)

- Declarative Way (Preferred approach)
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: myapp-pod
    labels:
      app: myapp
      type: front-end
  spec:
    containers:
    - name: nginx-container
      image: nginx
  ```
 ![rcd](../../images/rcd.PNG)
 
- A good practice is to store resource configurations on source code repositories like github.

  ![rcd1](../../images/rcd1.PNG)

## Backup - Resource Configs

  - Questo è l'approccio consigliato:

  ```
  $ kubectl get all --all-namespaces -o yaml > all-deploy-services.yaml (only for few resource groups)
  ```

- There are many other resource groups that must be considered. There are tools like **`ARK`** or now called **`Velero`** by Heptio that can do this for you.

  ![brc](../../images/brc.PNG)
  
## Backup - ETCD
- So, instead of backing up resources as before, you may choose to backup the ETCD cluster itself. 

Quindi p.es. possiamo salvare direttamente la folder data-dir dell'ETCD Server
  
  ![be](../../images/be.PNG)
  
- **You can take a snapshot of the etcd database by using** **`etcdctl`** utility snapshot save command.
  ```
  $ ETCDCTL_API=3 etcdctl snapshot save snapshot.db
  ```
  ```
  $  ETCDCTL_API=3 etcdctl snapshot status snapshot.db
  ```
  ![be1](../../images/be1.PNG)
  
## Restore - ETCD
- To restore etcd from the backup at later in time. First stop kube-apiserver service
  ```
  $ service kube-apiserver stop
  ```
- Run the `etcdctl snapshot restore` command, facendolo puntare al db creato dal comando di snapshot save e 
restorando il file di backup su una directory specifica (--data-dir)
  -  ``` ETCDCTL_API=3 etcdctl snapshot restore snapshot.db --data-dir /var/lìb/etcd-from-backup  ```
- Update the etcd service configuration, facendo puntare la data-dir ai dati restorati dal file .db
- Reload system configs
  ```
  $ systemctl daemon-reload
  ```
- Restart etcd
  ```
  $ service etcd restart
  ```
  
  ![er](../../images/er.PNG)
  
- Start the kube-apiserver
  ```
  $ service kube-apiserver start
  ```
#### With most etcdctl commands specify the cert,key,cacert and endpoint for authentication (per il restore non serve)

**Ricordati che il backup di ETCD va fatto dal controlplane, dove è in esecuzione.
Può esser in esecuzione come POD o come servizio** Questo perché si riferisce a nomi di file
presenti nel nodo, che non son presenti altrimenti.

ETCD server è un server distribuito, presente in ogni nodo master

Se installato come servizio esterno: 

Vedere come è installato il componente dell’API Server (come Pod o come servizio) e  ricavare quindi il parametro:

`--etcd-servers=https://192.22.202.22:2379`

Ricordati che i dati son salvati su `--data-dir`, **parametro di come parte il componente ETCD**


Magari meglio fare l'export all'inizio della versione: 

```
export ETCDCTL_API=3
```
Questo è il command di un etctctl eseguito come Pod, vedi i comandi da passare alla CLI etcdctl
```
Command:
      etcd
      # questo è il --endpoints
      --advertise-client-urls=https://192.13.90.3:2379
      # server certificate, --cert
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.13.90.3:2380
      --initial-cluster=controlplane=https://192.13.90.3:2380
      # chiave del server, --key
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.13.90.3:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.13.90.3:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      # questa sarebbe la ca, --cacert
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

```
$ ETCDCTL_API=3 etcdctl \
  snapshot save /tmp/snapshot.db \
  --endpoints=https://[127.0.0.1]:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/etcd-server.crt \
  --key=/etc/kubernetes/pki/etcd/etcd-server.key
```

Per il restore, basta solamente il file .db e la folder (che non deve esistere), dove restorare i dati

Modificare poi la configurazione dell'etcd server, anche installabile\installato come Pod, per far puntare
il parametro `--data` ai dati restorati

  ![erest](../../images/erest.PNG)
  
#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/


 
