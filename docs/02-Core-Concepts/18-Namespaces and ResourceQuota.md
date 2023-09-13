# Namespaces
  - Take me to [Video Tutorial](https://kodekloud.com/topic/namespaces/)
  
In this section, we will take a look at **`Namespaces`**

So far in this course we have created **`Objects`** such as **`PODs`**, **`Deployments`** and **`Services`** in our 
cluster. Whatever we have been doing we have been doing in a **`NAMESPACE`**.
This namespace is the **`default`** namespace in kubernetes. 
It is automatically created when kubernetes is setup initially.

K8s crea una serie di servizi e Pod per i suoi scopi (networking, dns, ecc ecc) e li crea in un namespace
a parte chiamato kube-system.
Tutto ciò che è accessibile da tutti gli utenti sta in un namespace chiamato kube-public.

Le risorse fra loro si risolvono, nello stesso namespace, con il nome della risorsa stessa.
In altri namespace, hai bisogno di utilizzare anche il nome del namespace. 

p.es. nel caso del service:
- db-service 
- db-service.dev.svc.cluster.local

- cluster.local è il default domain name del k8s cluster
- svc è il sottodominio per i service
- dev è il namespace
- db-service è il nome del servizio stesso

  ![ns](../../images/ns.PNG)
 
- You can create your own namespaces as well.

  ![ns3](../../images/ns3.PNG)
  
- To list the pods in default namespace
  ```
  $ kubectl get pods
  ```
- To list the pods in another namespace. Use **`kubectl get pods`** command along with the **`--namespace`** flag or argument.
  ```
  $ kubectl get pods --namespace=kube-system
  ```
  ![ns8](../../images/ns8.PNG)
  
- Here we have a pod definition file, when we create a pod with pod-definition file, the pod is created in the default namespace.

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
  ```
  $ kubectl create -f pod-definition.yaml
  ```
- To create the pod with the pod-definition file in another namespace, use the **`--namespace`** option.
  ```
  $ kubectl create -f pod-definition.yaml --namespace=dev
  ```
  ![ns9](../../images/ns9.PNG)

- If you want to make sure that this pod gets you created in the **`dev`** env all the time, even if you don't specify in the command line, you can move the **`--namespace`** definition into the pod-definition file.
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev
  labels:
     app: myapp
     type: front-end
spec:
  containers:
  - name: nginx-container
    image: nginx
 ```
  
  ![ns10](../../images/ns10.PNG)
  
- To create a new namespace, create a namespace definition as shown below and then run **`kubectl create`**
```
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

  ```
  $ kubectl create -f namespace-dev.yaml
  ```
  Another way to create a namespace
  ```
  $ kubectl create namespace dev
  ```
  ![ns11](../../images/ns11.PNG)
  
- By default, we will be in a **`default`** namespace. To switch to a particular namespace permenently run the below command.
  ```
  $ kubectl config set-context $(kubectl config current-context) --namespace=dev
  ```
- To view pods in all namespaces
  ```
  $ kubectl get pods --all-namespaces
  ```
  ![ns12](../../images/ns12.PNG)
  
- **To limit resources in a namespace**, create a resource quota. 
To create one start with **`ResourceQuota`** definition file.

Questo limita sia il numero di oggetti creati per namespace per tipologia, sia la request e limit dei container creati.


```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```
  ```
  $ kubectl create -f compute-quota.yaml
  ```
  ![ns13](../../images/ns13.PNG)

- Pods in the cluster have one of the three priority classes, "low", "medium", "high".
- E' possibile creare un oggetto quota per ogni priority class

```
apiVersion: v1
kind: List
items:
- apiVersion: v1
  kind: ResourceQuota
  metadata:
    name: pods-high
  spec:
    hard:
      cpu: "1000"
      memory: 200Gi
      pods: "10"
    scopeSelector:
      matchExpressions:
      - operator : In
        scopeName: PriorityClass
        values: ["high"]
  - apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: pods-medium
    spec:
      hard:
        cpu: "10"
        memory: 20Gi
        pods: "10"
      scopeSelector:
        matchExpressions:
        - operator : In
          scopeName: PriorityClass
          values: ["medium"]
  - apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: pods-low
    spec:
      hard:
        cpu: "5"
        memory: 10Gi
        pods: "10"
      scopeSelector:
        matchExpressions:
        - operator : In
          scopeName: PriorityClass
          values: ["low"]
```

  
K8s Reference Docs:
- https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces/
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/
- https://kubernetes.io/docs/tasks/access-application-cluster/list-all-running-container-images/
  
  
