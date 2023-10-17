# Security Context
  - Take me to [Video Tutorial](https://kodekloud.com/topic/security-contexts-2/)
  
In this section, we will take a look at security context

## Container Security



Normalmente i container son eseguiti come utente root, puoi specificarlo nel Dockerfile o
all'atto dello starting, così come, come abbiamo visto, puoi aggiungere o rimuovere privilegi.

Anche quando eseguiti come utenti root, hanno un sottoinsieme dei privilegi dell'utente root nella macchina
fisica e questo è ottenuto tramite le capabilities Unix.

Quando esegui un container in Docker, puoi aggiungere una serie di privilegi o eliminarli (`cap-remove`)
o addirittura eseguire un container come `privileged`, ossia come root o come un utente differente
tramite la clausola `--user`

 ```
 $ docker run --user=1001 ubuntu sleep 3600
 $ docker run -cap-add MAC_ADMIN ubuntu
 ```
 
 ![csec](../../images/csec.PNG)
 
Questo può esser impostato anche in Kubernetes, sia a livello di Pod che di Container.

## Kubernetes Security
- You may choose to configure the security settings at a container level or at a pod level.
- Pod settings, si propagano anche a livello di container, se impostati a livello di container, quelli a livello
di container, sovrascrivono quelli a livello di Pod

 ![ksec](../../images/ksec.PNG)

## Security Context

**Ricordati di distruggere il Pod o il deploy, se vuoi modificare la parte di securityContext**

- To add security context on the container and a field called **`securityContext`** under the spec section.
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: web-pod
  spec:
    securityContext:
      runAsUser: 1000
    containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep", "3600"]
  ```
  ![sxc1](../../images/sxc1.PNG)
  
- To set the same context at the container level, then move the whole section under container section.
  
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: web-pod
  spec:
    containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep", "3600"]
      securityContext:
        runAsUser: 1000
  ```
  ![sxc2](../../images/sxc2.PNG)
  
- To add capabilities use the **`capabilities`** option (disponibili solo a livello di container e non a livello di Pod)
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: web-pod
  spec:
    containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep", "3600"]
      securityContext:
        runAsUser: 1000
        capabilities: 
          add: ["MAC_ADMIN"]
  ```
  ![cap](../../images/cap.PNG)
  
  
### K8s Reference Docs
- https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
