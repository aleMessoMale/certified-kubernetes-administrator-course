# TLS in Kubernetes
  - Take me to [Video Tutorial](https://kodekloud.com/topic/tls-in-kubernetes/)
  
In this section, we will take a look at TLS in kubernetes

#### The two primary requirements are to have all the various services within the cluster to use server certificates and all clients to use client certificates to verify they are who they say they are.
- Server Certificates for Servers (api-server, etcd, kubelet)
- Client Certificates for Clients: 
  - admin, kube-scheduler, kube-controller-manager, kube-proxy, tutti questi sono client che chiamano l'api-server
  - api-server comunica con l'etcd server, è l'unico che lo fa e comunica inoltre con i vari kubelet nei worker, 
per monitorare la situazione dei vari worker

- il kubelet è un server, quando esposto dai worker e chiamato dall'API Server, idem per lo scheduler

  ![tls](../../images/tls.PNG)
  
#### Let's look at the different components within the k8s cluster and identify the various servers and clients and who talks to whom.

  ![certs](../../images/certs.PNG)
  
