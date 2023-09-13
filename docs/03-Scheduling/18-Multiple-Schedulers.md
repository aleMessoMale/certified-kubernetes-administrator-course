# Multiple Schedulers 
  - Take me to [Video Tutorial](https://kodekloud.com/topic/multiple-schedulers/)

In this section, we will take a look at multiple schedulers

## Custom Schedulers
- Your kubernetes cluster can schedule multiple schedulers at the same time.
- Puoi creare uno scheduler e renderlo disponibile come quello di default o uno addizionale

  ![ms](../../images/ms.PNG)
  
## Deploy additional scheduler

Il modo più semplice è scaricare il binario dello scheduler, ed impostarlo con dei valori di configurazione
come p.es. il valore di config del servizio. Ogni scheduler avrà il suo file yaml

**Questo non è il modo in cui solitamente si fa, ma si fa con kubeadm**

- Download the binary
  ```
  $ wget https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/linux/amd64/kube-scheduler
  ```
  ![das](../../images/das.PNG)
  
## Deploy additional scheduler - kubeadm

  In questo modo, siamo in grado di deployare lo scheduler come un Pod e possiamo passargli anche qua la config option
per puntare al file yaml, come si vede qua. 
- come vedi il command punta al binario nell'immagine, a cui passiamo determinate configurazioni
  - possiamo puntare al file yaml, come avviene qua, oppure possiamo montare una configmap come volume e puntare
  al file montato creato dalla configmap
- Nel caso in cui ci siano diverse copie dello stesso scheduler, girando
su diversi master, solo uno può esser attivo alla volta, quello attivo è ottenuto tramite appunto il true sul leaderElect.
Questo solo nel caso di custom scheduler. Se abbiamo una replica, non è necessario impostarla.
   
  ![das](../../images/scheduler_pod.PNG)
  
  - To create a scheduler pod
    ```
    $ kubectl create -f my-custom-scheduler.yaml
    ```
  
## View Schedulers
- To list the scheduler pods
  ```
  $ kubectl get pods -n kube-system
  ```

## Use the Custom Scheduler
- Create a pod definition file and add new section called **`schedulerName`** and specify the name of the new scheduler
- Se il Pod rimane in `Pending` state, significa che il nuovo scheduler non sta funzionando
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
  spec:
    containers:
    - image: nginx
      name: nginx
    schedulerName: my-custom-scheduler
  ```
  ![cs](../../images/cs.png)
  
- To create a pod definition
  ```
  $ kubectl create -f pod-definition.yaml
  ```
- To list pods
  ```
  $ kubectl get pods
  ```

## View Events
- Per listare gli eventi puoi usare il comando sotto.
- Se vedi eventi di tipo `Scheduled`, son quelli relativi p.es. al fatto che il Pod x è stato
gestito tramite lo scheduler y.

  ```
  $ kubectl get events
  ```
  ![cs1](../../images/cs1.PNG)
  
## View Scheduler Logs
- To view scheduler logs
  ```
  $ kubectl logs my-custom-scheduler -n kube-system
  ```
  ![cs2](../../images/cs2.PNG)
  
#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/extend-kubernetes/configure-multiple-schedulers/
  
