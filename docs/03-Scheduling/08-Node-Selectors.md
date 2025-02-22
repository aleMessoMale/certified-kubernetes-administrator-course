# Node Selectors
  - Take me to [Video Tutorial](https://kodekloud.com/topic/node-selectors/)

In this section, we will take a look at Node Selectors in Kubernetes

#### We add new property called Node Selector to the spec section and specify the label.
- The scheduler uses these labels to match and identify the right node to place the pods on.
- In pratica da una parte nel nodo aggiungi delle label e dall'altra nel Pod utilizzi lo spec.nodeSelector
per selezionare il nodo migliore
- E' molto semplice, per le condizioni più complesse (uno o più label nel nodo, non in un nodo con una determinata label, 
ecc ecc), utilizza NodeAffinity

  ```
  apiVersion: v1
  kind: Pod
  metadata:
   name: myapp-pod
  spec:
   containers:
   - name: data-processor
     image: data-processor
   nodeSelector:
    size: Large
  ```
![nsel](../../images/nsel.PNG)
  
- To label nodes

  Syntax
  ```
  $ kubectl label nodes <node-name> <label-key>=<label-value>
  ```
  Example
  ```
  $ kubectl label nodes node-1 size=Large
  ```
  
![ln](../../images/ln.PNG)
  
- To create a pod definition
  ```
  apiVersion: v1
  kind: Pod
  metadata:
   name: myapp-pod
  spec:
   containers:
   - name: data-processor
     image: data-processor
   nodeSelector:
    size: Large
  ```
  ```
  $ kubectl create -f pod-definition.yml
  ```
  
![nsel](../../images/nsel.PNG)
  
## Node Selector - Limitations
- We used a single label and selector to achieve our goal here. But what if our requirement is much more complex
come p.es. condizioni in OR sulle label oppure condizioni del tipo, non nei nodi con quella label, allora dobbiamo
utilizzare la NodeAffinity, in quanto il node label non è sufficiente
  
![nsl](../../images/nsl.PNG)
 
- For this we have **`Node Affinity and Anti Affinity`**
  
#### K8s Reference Docs
- https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector





