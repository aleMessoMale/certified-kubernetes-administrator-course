# OS Upgrades
  - Take me to [Video Tutorial](https://kodekloud.com/topic/os-upgrades/)
  
In this section, we will take a look at OS upgrades.

#### If the node was down for more than 5 minutes, then the pods are terminated from that node
in questo caso, sono ricreati in un altro nodo, se ci sono risorse a disposizione e se il Pod è parte
di un ReplicaSet, ReplicationController, Job, DaemonSet, StatefulSet (quindi p.es. un Deployment), 
in caso non faccia parte di un ReplicaSet, il Pod è perso.

Il tempo che intercorre fra quando un nodo non è più disponibile e quando k8s lo distrugge, è noto come 
pod-eviction-timeout ed è impostato dal controller-manager con un valore di default di 5 minuti

  ![os](../../images/os.PNG)
  
- You can purposefully **`drain`** the node of all the workloads so that the workloads are moved to other nodes
  ```
  $ kubectl drain node-1 [--force] [--ignore-daemonsets]
  ```
- The node is also cordoned or marked as unschedulable.
- Considera inoltre, l'opzione per ignorare i deamon-set (`--ignore-daemonsets`) e l'opzione per forzare 
il drain del nodo, avviene con eviction dei vari Pod e spostamento su altri nodi, se parte di:
 - un ReplicaSet
 - un ReplicationController
 - un Job
 - un DaemonSet
 - uno StatefulSet
Se al contrario, sono Pod singoli, è necessaria anche l'opzione `--force`, perché con l'eviction sarebbero persi

- When the node is back online after a maintenance, it is still unschedulable. You then need to uncordon it.
  ```
  $ kubectl uncordon node-1
  ```
A questo punto, nel nodo son nuovamente schedulabili Pod, naturalmente, i Pod son solo schedulabili quando son creati  

- There is also another command called cordon. Cordon simply marks a node unschedulable. 
Unlike drain it does not terminate or move the pods on an existing node.

  ![drain](../../images/drain.PNG)
  
  
#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
