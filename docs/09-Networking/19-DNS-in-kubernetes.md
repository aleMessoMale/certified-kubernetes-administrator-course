# DNS in Kubernetes

  - Take me to [Lecture](https://kodekloud.com/topic/dns-in-kubernetes/)

In this section, we will take a look at **DNS in the Kubernetes Cluster**

In questa lezione vediamo la parte di risoluzione DNS nel cluster

k8s deploya un DNS Server di default, quando imposti un cluster.

In generale vediamo Pod DNS Record e Service DNS Record nel DNS Server installato di default nel cluster k8s.

Come sappiamo ogni Pod può raggiungere ogni altro Pod, è un requisito della soluzione di networking.

Quando creiamo un service, k8s stesso aggiunge il record A per risolvere il nome del servizio, quindi all'interno
del cluster, essendo un servizio ClusterIP, ogni Pod può raggiungerlo.

Se vogliamo raggiungerlo da un altro namespace, dobbiamo aggiungere anche il suffisso del namespace.

- service: `web-service`
- namespace: `apps`
- DNS service name: `web-service.apps`

Inoltre, all'interno del root domain `cluster.local`, abbiamo il sottodominio `svc` per i servizi e `pod` per i Pod.

quindi: 

- `<service-name>.<namespace>.svc.cluster.local` è l'FQDN del servizio

Nel caso dei Pod, i nomi non son aggiunti di default nei DNS, ma se impostati, son ottenuti sostituendo i
'.' con i '-' nell'indirizzo IP

- `10-244-2-5.<namespace>.pod.cluster.local` è l'FQDN del Pod


## Pod DNS Record


- The following DNS resolution:

```
<POD-IP-ADDRESS>.<namespace-name>.pod.cluster.local
```
> Example
```
# Pod is located in a default namespace

10-244-1-10.default.pod.cluster.local
```

```
# To create a namespace
$ kubectl create ns apps

# To create a Pod
$ kubectl run nginx --image=nginx --namespace apps

# To get the additional information of the Pod in the namespace "apps"
$ kubectl get po -n apps -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          99s   10.244.1.3   node01   <none>           <none>

# To get the dns record of the nginx Pod from the default namespace
$ kubectl run -it test --image=busybox:1.28 --rm --restart=Never -- nslookup 10-244-1-3.apps.pod.cluster.local
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      10-244-1-3.apps.pod.cluster.local
Address 1: 10.244.1.3
pod "test" deleted

# Accessing with curl command
$ kubectl run -it nginx-test --image=nginx --rm --restart=Never -- curl -Is http://10-244-1-3.apps.pod.cluster.local
HTTP/1.1 200 OK
Server: nginx/1.19.2

```

## Service DNS Record

- The following DNS resolution:

```
<service-name>.<namespace-name>.svc.cluster.local
```
> Example
```
# Service is located in a default namespace

web-service.default.svc.cluster.local
```
- Pod, Service is located in the `apps` namespace

```
# Expose the nginx Pod
$ kubectl expose pod nginx --name=nginx-service --port 80 --namespace apps
service/nginx-service exposed

# Get the nginx-service in the namespace "apps"
$ kubectl get svc -n apps
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   10.96.120.174   <none>        80/TCP    6s

# To get the dns record of the nginx-service from the default namespace
$ kubectl run -it test --image=busybox:1.28 --rm --restart=Never -- nslookup nginx-service.apps.svc.cluster.local
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-service.apps.svc.cluster.local
Address 1: 10.96.120.174 nginx-service.apps.svc.cluster.local
pod "test" deleted

# Accessing with curl command
$ kubectl run -it nginx-test --image=nginx --rm --restart=Never -- curl -Is http://nginx-service.apps.svc.cluster.local
HTTP/1.1 200 OK
Server: nginx/1.19.2

```



#### References Docs

- https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/
- https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/