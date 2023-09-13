# ReplicaSets
  - Take me to [Video Tutorial](https://kodekloud.com/topic/replicasets/)

Il ReplicationController, sostituito poi dal ReplicaSet, serve per garantire un certo numero di repliche del Pod 
(almeno 1, o il numero replicas)

In this section, we will take a look at the below
- Replication Controller
- ReplicaSet

#### Controllers are brain behind kubernetes

## What is a Replica and Why do we need a replication controller?

  ![rc](../../images/rc.PNG)
  
  ![rc1](../../images/rc1.PNG)
  
## Difference between ReplicaSet and Replication Controller
- **`Replication Controller`** is the older technology that is being replaced by a **`ReplicaSet`**.
- **`ReplicaSet`** is the new way to setup replication.

## Creating a Replication Controller

## Replication Controller Definition File
  
   ![rc2](../../images/rc2.PNG)
  
```
    apiVersion: v1
    kind: ReplicationController
    metadata:
      name: myapp-rc
      labels:
        app: myapp
        type: front-end
    spec:
     template:
        metadata:
          name: myapp-pod
          labels:
            app: myapp
            type: front-end
        spec:
         containers:
         - name: nginx-container
           image: nginx
     replicas: 3
```

In pratica quel che accade è che prendi tutta la parte di definizione del Pod sotto spec
e lo copi sotto template. Su spec del ReplicationController, aggiungerai anche la parte del numero di repliche richieste (replicas)

  - To Create the replication controller
    ```
    $ kubectl create -f rc-definition.yaml
    ```
  - To list all the replication controllers
    ```
    $ kubectl get replicationcontroller
    ```
  - To list pods that are launch by the replication controller
    ```
    $ kubectl get pods
    ```
    ![rc3](../../images/rc3.PNG)
    
## Creating a ReplicaSet
  
## ReplicaSet Definition File

   ![rs](../../images/rs.PNG)

```
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: myapp-replicaset
      labels:
        app: myapp
        type: front-end
    spec:
     template:
        metadata:
          name: myapp-pod
          labels:
            app: myapp
            type: front-end
        spec:
         containers:
         - name: nginx-container
           image: nginx
     replicas: 3
     selector:
       matchLabels:
        type: front-end
 ```

#### ReplicaSet vs ReplicationController

- cambia l'apiVersion
- utilizza i selector, per garantire il numero di repliche, su tutti i Pod che matchano quelle labels.

Nel caso di ReplicationController, è opzionale, e quando non fornito, assume le stesse labels di quelle fornite 
nel template. Nel caso invece di ReplicaSet è obbligatorio.



#### ReplicaSet requires a selector definition when compare to Replication Controller.
   
  - To Create the replicaset
    ```
    $ kubectl create -f replicaset-definition.yaml
    ```
  - To list all the replicaset
    ```
    $ kubectl get replicaset
    ```
  - To list pods that are launch by the replicaset
    ```
    $ kubectl get pods
    ```
   
    ![rs1](../../images/rs1.PNG)
    
## Labels and Selectors

E' in grado di gestire anche Pod già creati, quindi anche non creati da lui. Puoi dirgli, voglio n repliche
di questo Pod che matcha queste label e ha questo template e li crea se non ci sono o ne garantisce 
il numero se già presenti.

Quello che possiamo fare è fondamentalmente utilizzare il matchLabels, sotto il gruppo selector, per 
matchare i Pod con quelle labels.

La parte template è necessaria, anche se i Pod esistono già, perché deve esser in grado di ricrearli se muiono.

Questo meccanismo è utilizzato in tanti altri posti in k8s.

#### What is the deal with Labels and Selectors? Why do we label pods and objects in kubernetes?

  ![labels](../../images/labels.PNG)
  
## How to scale replicaset
- There are multiple ways to scale replicaset
  - First way is to update the number of replicas in the replicaset-definition.yaml definition file. E.g replicas: 6 and then run 
 ```
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: myapp-replicaset
      labels:
        app: myapp
        type: front-end
    spec:
     template:
        metadata:
          name: myapp-pod
          labels:
            app: myapp
            type: front-end
        spec:
         containers:
         - name: nginx-container
           image: nginx
     replicas: 6
     selector:
       matchLabels:
        type: front-end
```

  ```
  $ kubectl apply -f replicaset-definition.yaml
  ```
  - Second way is to use **`kubectl scale`** command.
  ```
  $ kubectl scale --replicas=6 -f replicaset-definition.yaml
  ```

Naturalmente non modifica il file.

  - Third way is to use **`kubectl scale`** command with type and name
  ```
  $ kubectl scale --replicas=6 replicaset myapp-replicaset
  ```
  ![rs2](../../images/rs2.PNG)

#### K8s Reference Docs:
- https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/
- https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/
