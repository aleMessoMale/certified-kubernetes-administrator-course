# Taints and Tolerations
  - Take me to [Video Tutorial](https://kodekloud.com/topic/taints-and-tolerations-2/)
  
In this section, we will take a look at taints and tolerations.
- Pod to node relationship and how you can restrict what pods are placed on what nodes.

#### Taints and Tolerations are used to set restrictions on what pods can be scheduled on a node. 
- Taint e Tolerations, son pensati per limitare l'esecuzione dei Pod su certi nodi, non per piazzare i Pod su
determinati nodi. Un Pod con una determinata tolerations ad un nodo, potrebbe benissimo finire su un altro se
scarico
- Only pods which are tolerant to the particular taint on a node will get scheduled on that node.
- Di default, i Pod non son tolleranti ad alcun taint, ma se gli aggiungiamo la tolerations, possiamo
schedulare quel Pod in quel particolare nodo


  ![tandt](../../images/tandt.PNG)
  
## Taints
- Use **`kubectl taint nodes`** command to taint a node.

  Syntax
  ```
  $ kubectl taint nodes <node-name> key=value:taint-effect
  ```
 
  Example
  ```
  $ kubectl taint nodes node1 app=blue:NoSchedule
  ```
  
- The taint effect defines what would happen to the pods **if they do not tolerate the taint**.
- There are 3 taint effects
  - **`NoSchedule`**
  - **`PreferNoSchedule`**
  - **`NoExecute`**

- Se aggiungiamo il taint ad un determinato nodo, con effetto NoExecute, quel che accade è che sui Pod
presenti su quel nodo che non hanno l'adeguata tolerations, viene effettuata l'eviction, ossia sono killati
e schedulati altrove. Su NoSchedule o PreferNoSchedule, chiaramente non avviene un'eviction del Pod.

- Per rimuovere un taint da un nodo:
  ```
  $ k taint nodes controlplane node-role.kubernetes.io/control-plane:NoSchedule-
  ```
  
  ![tn](../../images/tn.PNG)
  
## Tolerations
   - Tolerations are added to pods by adding a **`tolerations`** section in pod definition.
   - Equal non è il solo valore di operator, esiste anche Exists, nel qual caso, il Value non va definito
   - Nota che tolerations è una lista, quindi ne puoi avere più di 1

     ```
     apiVersion: v1
     kind: Pod
     metadata:
      name: myapp-pod
     spec:
      containers:
      - name: nginx-container
        image: nginx
      tolerations:
      - key: "app"
        operator: "Equal"
        value: "blue"
        effect: "NoSchedule"
     ```
    
  ![tp](../../images/tp.PNG)
    

#### Taints and Tolerations do not tell the pod to go to a particular node. Instead, they tell the node to only accept pods with certain tolerations.

- Per vedere i Taint in un determinato nodo, si può eseguire il seguente comando:

  ```
  $ kubectl describe node kubemaster |grep Taint
  ```
  
kubemaster rappresenta il nodo master.
 
 ![tntm](../../images/tntm.PNG)


     
#### K8s Reference Docs
- https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/

