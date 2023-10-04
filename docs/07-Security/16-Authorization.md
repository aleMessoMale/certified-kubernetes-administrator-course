# Authorization
  - Take me to [Video Tutorial](https://kodekloud.com/topic/authorization/)
  
In this section, we will take a look at authorization in kubernetes, what you can do when you're authenticated

## Why do you need Authorization in your cluster?
- As an admin, you can do all operations
  ```
  $ kubectl get nodes
  $ kubectl get pods
  $ kubectl delete node worker-2
  ```
  
  ![at1](../../images/at1.PNG)
  
## Authorization Mechanisms
- There are different authorization mechanisms supported by kubernetes
  - Node Authorization
  - Attribute-based Authorization (ABAC)
  - Role-Based Authorization (RBAC)
  - Webhook
  
## Node Authorization

kubelet fa parte del gruppo system:node e ha privilegi per poter scrivere anche info relativamente ai nodi

  ![node-auth](../../images/node-auth.png)
  
## ABAC - Attribute Based Access Control

In pratica crei un descrittore di `Policy` per ogni utente

  ![abac](../../images/abac.PNG)
  
## RBAC

Role based access control crea un ruolo ed associa un utente a quel ruolo. Questo è il modo più standard per fornire
l'accesso al cluster k8s

  ![rbac](../../images/rbac.PNG)

## Webhook

In questo caso, attraverso l'Open Policy Agent, siamo in grado di delegare esternamente, se un utente è in grado 
di effettuare una certa azione, ossia deleghiamo ad un'api o altro quest'azione che normalmente k8s effettua 
internamente. 

  
  ![webhook](../../images/webhook.PNG)
  
## Authorization Modes
- The mode options can be defined on the kube-apiserver, come parametro, default AlwaysAllow.
- Ne puoi specificare diversi e in quel caso la richiesta è autorizzata con una sorta di chain, nell'ordine in cui
sono specificate nel file, exit alla prima approvazione 

  ![mode](../../images/mode.PNG)

  ![mode1](../../images/mode1.PNG)
  
  
  #### K8s Reference Docs
  - https://kubernetes.io/docs/reference/access-authn-authz/authorization/
  
