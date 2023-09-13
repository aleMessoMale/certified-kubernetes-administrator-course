# Kubernetes Services
  - Take me to [Video Tutorial](https://kodekloud.com/topic/services-3/)
  
In this section we will take a look at **`services`** in kubernetes

## Services
- Kubernetes Services enables communication between various components within and outside of the application.

Tre tipi di service:
- NodePort -> espone il servizio su tutti i nodi, su una determinata porta (<nodeIp>:<port>), rendendolo accessibile
  all'esterno del cluster
- ClusterIP -> servizio raggiungibile solo all'interno del cluster, con un virtualIP interno
- LoadBalancer (disponibile tramite cloud providers) -> crea un external load balancer che punta ai servizi ClusterIP
  o comunque ai nodi

Per funzionare utilizza le funzionalità del kube-proxy, che è un servizio che è presente nei worker, come detto e mantiene
la virtual network, per i servizi, consistente, aggiornando il virtualIP in physicalIP, modificando dinamicamente le 
iptable rule nel nodo.

Vedi kubernetes-service-updated.pdf e il solito PDF del corso k8s pagato da Vodafone.


  ![srv1](../../images/srv1.PNG)
  
#### Let's look at some other aspects of networking

## External Communication

- How do we as an **`external user`** access the **`web page`**?

  - From the node (Able to reach the application as expected)
  
    ![srv2](../../images/srv2.PNG)
    
  - From outside world (This should be our expectation, without something in the middle it will not reach the application)
  
    ![srv3](../../images/srv3.PNG)
   
    
 ## Service Types
 
 #### There are 3 types of service types in kubernetes
 
   ![srv-types](../../images/srv-types.PNG)
 
 1. NodePort
    - Where the service makes an internal port accessible on a port on the NODE.
      ```
      apiVersion: v1
      kind: Service
      metadata:
       name: myapp-service
      spec:
       types: NodePort
       ports:
       - targetPort: 80
         port: 80
         nodePort: 30008
      ```
     ![srvnp](../../images/srvnp.PNG)

Per far si che il traffico venga reindirizzato sui vari Pod che son identificati da quel servizio, come spesso accade 
come abbiamo visto nel Deployment e nel ReplicaSet, basta copiare la sezione label del Pod, sulla sezione selector del 
service e il gioco è fatto
      
      #### To connect the service to the pod
```
      apiVersion: v1
      kind: Service
      metadata:
       name: myapp-service
      spec:
       type: NodePort
       ports:
       - targetPort: 80
         port: 80
         nodePort: 30008
       selector:
         app: myapp
         type: front-end
   ```
![srvnp](../../images/srvnp1.PNG)
      
      #### To create the service
      ```
      $ kubectl create -f service-definition.yaml
      ```
      
      #### To list the services
      ```
      $ kubectl get services
      ```
      
      #### To access the application from CLI instead of web browser (nodeIP:nodePort>)
      ```
      $ curl http://192.168.1.2:30008
      ```
      
![srvnp2](../../images/srvnp2.PNG)

A service with multiple pods (in questo caso utilizza il selector per matchare i Pod con le labels) e agisce 
da load balancer fra i diversi Pod
      
![srvnp3](../../images/srvnp3.PNG)
      
When Pods are distributed across multiple nodes, sempre disponibile su <nodeIp>:<nodePort>, per ogni nodo
     
![srvnp4](../../images/srvnp4.PNG)
     
            
 1. ClusterIP
    - In this case the service creates a **`Virtual IP`** inside the cluster (che ha una virtaual network) 
    to enable communication between different services such as a set of frontend servers to a set of backend servers.
    
 1. LoadBalancer
    - Where the service provisions a **`loadbalancer`** for our application in supported cloud providers e crea un
 external load balancer fra gli indirizzi IP e nodePort dei vari nodi
    
K8s Reference Docs:
- https://kubernetes.io/docs/concepts/services-networking/service/
- https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/

