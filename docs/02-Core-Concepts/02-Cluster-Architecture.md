# Cluster Architecture

  - Take me to [Video Tutorial](https://kodekloud.com/topic/cluster-architecture/)

In this section , we will take a look at the kubernetes Architecture at high level.
- 10,000 Feet Look at the Kubernetes Architecture

- kubelet è sempre in comunicazione con l'Api Server, riceve comunicazioni e invia aggiornamenti sullo stato 
del worker node su cui è presente
- kube proxy è sui vari worker e permette la comunicazione fra Pod anche su nodi differenti
- abbiamo anche una soluzione dns, solitamente coreDNS
- **l'Api server è il punto di ingresso per le comunicazioni con il cluster, tutta la controlplane passa per l'api
server e lo contatta, quindi se è giù, le cose iniziano a non funzionare**, incluso il client di kubectl che passa da la
- i vari controller, come p.es. il NodeController, il ReplicationController, si occupano di monitorare gli eventi 
nel cluster e garantire che il traffico sia solo nei nodi sani, le repliche siano il numero corretto e così via
  - questi son gestiti dal controller manager
- lo scheduler si occupa di mettere i Pod nei nodi corretti a seconda di Taints, resource e limits, occupazione, 
nodeSelector labels, nodeAffinity ecc ecc
- inoltre essendo tutto basato su container, è necessario un container engine per far funzionare il tutto

  ![Kubernetes Architecture](../../images/k8s-arch.PNG)
  
  ![Kubernetes Architecture 1](../../images/k8s-arch1.PNG)


# Architettura di Kubernetes

## Control Plane (Plancia di Controllo)
- **API Server:** È l'interfaccia principale per l'utente e gli altri componenti di Kubernetes per interagire con il sistema. 
- **Controller Manager:** Gestisce i controller che regolano lo stato desiderato del cluster. Ad esempio, il controller di Replica  
assicura che il numero desiderato di repliche sia sempre in esecuzione. Il Controller Manager comunica con l'API Server per leggere  
lo stato attuale del cluster e apportare le modifiche necessarie per far sì che lo stato corrisponda a quello desiderato.
- **Scheduler:** Assegna i pod ai nodi del cluster in base ai requisiti di risorse e alle politiche definite.

## etcd
- Un datastore distribuito che contiene la configurazione del cluster, lo stato e altre informazioni critiche.

## Worker Node (Nodo Lavoratore)
- **Kubelet:** Si occupa di garantire che i container all'interno di un pod siano in esecuzione. Comunica con l'API Server per ricevere  
comandi e rapportare lo stato del nodo.
- **Kube Proxy:** Gestisce il networking del cluster. Mantiene le regole del network e gestisce il traffico di rete ai pod permettendone  
la comunicazione anche quando su nodi differenti  
- **Container Runtime:** Il sistema che esegue i container, come Docker o containerd.

## Interazioni
- Ogni componente comunica con gli altri attraverso l'API Server, che è la principale interfaccia di comunicazione 
nel cluster Kubernetes
- Il kubelet comunica con l'API Server per ricevere comandi e rapportare lo stato del nodo.
- Il Controller Manager e il Scheduler monitorano lo stato del cluster e prendono decisioni per mantenere lo stato desiderato.
- L'etcd è un datastore centralizzato che memorizza lo stato del cluster.
- l'API Server è l'unico componente nel control plane di Kubernetes che comunica direttamente con il server etcd. Gli altri componenti  
del control plane, come il Controller Manager e il Scheduler, interagiscono con l'etcd attraverso l'API Server. Il motivo di ciò è che   
l'etcd è il datastore centrale per lo stato del cluster, e l'API Server funge da interfaccia principale per accedere e modificare tale stato.  
Quando i componenti del control plane o altri nodi del cluster devono leggere o scrivere dati nello store di etcd, lo fanno attraverso  
richieste all'API Server.

K8s Reference Docs:
- https://kubernetes.io/docs/concepts/architecture/
