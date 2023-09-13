# Resource Limits
  - Take me to [Video Tutorials](https://kodekloud.com/topic/resource-limits/)
  
In this section we will take a look at Resource Limits

#### Let us take a look at 3 node kubernetes cluster.
- Each node has a set of CPU, Memory and Disk resources available.
- If there is no sufficient resources available on any of the nodes, kubernetes holds the scheduling the pod. 
You will see the pod in pending state. If you look at the events, you will see the reason as insufficient CPU.

- tendenzialmente, ogni container potrà definire richieste, che determinano se lo scheduler riesce a piazzare il Pod
in qualche nodo e limit, che è la massima quantità di risorse (cpu e memoria) che il Pod può utilizzare


- requests e limits son impostate a livello di Pod (solitamente nel descrittore di Deployment)
- **Kubernetes doesn’t provide default resource limits out-of-the-box** 
- se specifichi i limits e non le requests, le request avranno lo stesso valore del limits
- si possono definire dei valori per requests e non per limit, in quel caso, il Pod è in grado di esser schedulato
e non è limitato nell'esecuzione dalla quantità di risorse che utilizza
- si possono specificare dei valori di default per namespace dei limit, tramite i **LimitRange**
  - alcuni cluster k8s li hanno già definiti
- si possono definire dei valori di default per le requests e i limit, tramite i **ResourceQuota**
  - It can limit the quantity of objects that can be created in a namespace by type, as well as the total amount of 
  compute resources that may be consumed by resources in that namespace (cpu, memory e hugepages)
  - **vedi in Core-Concept, Namespaces and ResourceQuota**
  
    ![rl](../../images/rl.PNG)
  
## Resource Requirements
- ~~By default, K8s assume that a pod or container within a pod requires **`0.5`** CPU and **`256Mi`** of memory. 
This is known as the **`Resource Request` for a container**.~~ -> PER ME SBAGLIATO
  - TBV: Da quel che capisco dalla doc, questo valore di default, che non compare da nessuna parte tranne qua, è 
  quello che si dovrebbe trovare nel cluster quando lo installi, come default del ResourceQuota

- Di default, come scritto sotto, i limit sono al doppio:  **`1`** CPU e **`512Mi`** di memoria

Le richieste di risorse (limit and request per cpu e memoria), avvengono per container, ma ha senso 
ragionare per le risorse necessarie per il singolo Pod, somma dei vari containers.
  
  ![rr](../../images/rr.PNG)
  
- If your application within the pod requires more than the default resources, you need to set them in the pod definition file.

  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: simple-webapp-color
    labels:
      name: simple-webapp-color
  spec:
   containers:
   - name: simple-webapp-color
     image: simple-webapp-color
     ports:
      - containerPort:  8080
     resources:
       requests:
        memory: "1Gi"
        cpu: "1"
  ```
  ![rr-pod](../../images/rr-pod.PNG) 
   
## Resources - Limits
- ~~By default, k8s sets resource limits to 1 CPU and 512Mi of memory~~ -> PER ME SBAGLIATO
  - TBV: quello che capisco è che di default i cluster hanno dei valori impostati di LimitRange (il default non
  dovrebbe esistere se non c'è un LimitRange impostato)
  
  ![rsl](../../images/rsl.PNG)
  
- You can set the resource limits in the pod definition file.
  
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: simple-webapp-color
    labels:
      name: simple-webapp-color
  spec:
   containers:
   - name: simple-webapp-color
     image: simple-webapp-color
     ports:
      - containerPort:  8080
     resources:
       requests:
        memory: "1Gi"
        cpu: "1"
       limits:
         memory: "2Gi"
         cpu: "2"
  ```
  ![rsl1](../../images/rsl1.PNG)
  
#### Note: Remember Requests and Limits for resources are set per container in the pod.
  
## Exceed Limits
- what happens when a pod tries to exceed resources beyond its limits?

   ![el](../../images/el.PNG)

**If a container exceeds its memory request** and the node that it runs on becomes short of memory overall, **it is likely 
that the Pod the container belongs to will be evicted.**
 - se un Pod richiede risorse, ne viene killato un altro per liberare risorse, perché una volta assegnata la memoria
non si può condividere

A container might or might not be allowed to exceed its CPU limit for extended periods of time. However, **container 
runtimes don't terminate Pods or containers for excessive CPU usage**.

Tendenzialmente il problema di terminazione del Pod, è evidente dai log e dal describe dal Pod, come evento.
   
## Esempio di LimitRange

Definiti a livello di namespace

  ```
  apiVersion: v1
  kind: LimitRange
  metadata:
    name: cpu-resource-constraint
  spec:
    limits:
    - default: # this section defines default limits
        cpu: 500m
      defaultRequest: # this section defines default requests
        cpu: 500m
        memory: 1Gi
      max: # max and min define the limit range
        cpu: "1"
      min:
        cpu: 100m
      type: Container
  ```

#### K8s Reference Docs:
- https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
  
  
