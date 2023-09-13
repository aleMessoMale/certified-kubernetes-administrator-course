# Kubernetes Services - ClusterIP
  - Take me to [Video Tutorial](https://kodekloud.com/topic/services-cluster-ip-2/)
  
In this section we will take a look at **`services - ClusterIP`** in kubernetes
         
## ClusterIP
- In this case the service creates a **`Virtual IP`** inside the cluster to enable communication between different 
services such as a set of frontend servers to a set of backend servers.

Questo è logicamente necessario, perché continuamente i Pod son creati e distrutti nel Cluster, ma devono sempre
esser raggiungibili tramite il Service
    
    ![srvc1](../../images/srvc1.PNG)
    
#### What is a right way to establish connectivity between these services or tiers  
- A kubernetes service can help us group the pods together and provide a single interface to access the pod in a group.

  ![srvc2](../../images/srvc2.PNG)
  
#### To create a service of type ClusterIP

Nella definizione del service, ricordati che port e targetPort, riguardano sempre la prospettiva del servizio, quindi
targetPort è la porta del Pod e port è dove il service, espone la sua porta. Per linkare il service con un gruppo di Pod, 
come sempre, utilizziamo il selector.

```
apiVersion: v1
kind: Service
metadata:
 name: back-end
spec:
 types: ClusterIP
 ports:
 - targetPort: 80
   port: 80
 selector:
   app: myapp
   type: back-end
```
```
$ kubectl create -f service-definition.yaml
```

#### To list the services
```
$ kubectl get services
```
  ![srvc3](../../images/srvc3.PNG)
   
K8s Reference Docs:
- https://kubernetes.io/docs/concepts/services-networking/service/
- https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/
