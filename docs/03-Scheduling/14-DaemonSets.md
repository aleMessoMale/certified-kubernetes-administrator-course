# DaemonSets
  - Take me to [Video Tutorial](https://kodekloud.com/topic/daemonsets/)

In this section, we will take a look at DaemonSets.

I Daemonset son dei descrittori, simili come struttura del descrittore, ai ReplicaSet, 
che permettono di avere un Pod disponibile in ogni Nodo, con una ed una sola istanza. 
**Quel che cambia è fondamentalmente il kind del descrittore.**

Gli use case solitamente sono: 
- monitoring solutions
- log solutions
- kube-proxy
- networking solutions e agent in generale

Per sapere quali sono i desired e current number of nodes scheduled possiamo effettuare il describe del daemonset

Per crearlo non c'è il comando diretto di create, possiamo partire da un Deployment, ma forse è meglio partire da 
quanto presente in documentazione k8s.


#### DaemonSets are like replicasets, as it helps in to deploy multiple instances of pod. But it runs one copy of your pod on each node in your cluster.
  
  ![ds](../../images/ds.PNG)
  
## DaemonSets - UseCases

  ![ds-uc](../../images/ds-uc.PNG)
  
  ![ds-uc-kp](../../images/ds-uc-kp.PNG)
  
  ![ds-ucn](../../images/ds-ucn.PNG)
  
## DaemonSets - Definition
- Creating a DaemonSet is similar to the ReplicaSet creation process.
- For DaemonSets, we start with apiVersion, kind as **`DaemonSets`** instead of **`ReplicaSet`**, metadata and spec. 
  ```
  apiVersion: apps/v1
  kind: Replicaset
  metadata:
    name: monitoring-daemon
    labels:
      app: nginx
  spec:
    selector:
      matchLabels:
        app: monitoring-agent
    template:
      metadata:
       labels:
         app: monitoring-agent
      spec:
        containers:
        - name: monitoring-agent
          image: monitoring-agent
  ```
  
  ```
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    name: monitoring-daemon
    labels:
      app: nginx
  spec:
    selector:
      matchLabels:
        app: monitoring-agent
    template:
      metadata:
       labels:
         app: monitoring-agent
      spec:
        containers:
        - name: monitoring-agent
          image: monitoring-agent
  ```
  ![dsd](../../images/dsd.PNG)
  
- To create a daemonset from a definition file
  ```
  $ kubectl create -f daemon-set-definition.yaml
  ```

## View DaemonSets
- To list daemonsets
  ```
  $ kubectl get daemonsets
  ```
- For more details of the daemonsets
  ```
  $ kubectl describe daemonsets monitoring-daemon
  ```
  ![ds1](../../images/ds1.PNG)
  
## How DaemonSets Works

  ![ds2](../../images/ds2.PNG)

#### K8s Reference Docs
- https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/#writing-a-daemonset-spec
