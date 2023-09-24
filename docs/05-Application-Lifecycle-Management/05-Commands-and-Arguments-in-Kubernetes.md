# Commands and Arguments in Kubernetes
  - Take me to [Video Tutorial](https://kodekloud.com/topic/commands-and-arguments-in-kubernetes-2/)

In this section, we will take a look at commands and arguments in kubernetes

- Anything that is appended to the docker run command will go into the **`args`** property of the pod 
definition file in the form of an array.
- The command field corresponds to the entrypoint instruction in the Dockerfile so to summarize there are 2 fields
that correspond to 2 instructions in the Dockerfile.

- In pratica, quello che in Docker è il comando di default, quando ci sono parametri, l'entrypoint, qua nel Pod k8s
è chiamato command e i parametri son passati come args nel descrittore k8s, in poche parole, in k8s è più pulito e 
comprensibile di Docker, dove hai il comando di default che è l'ENTRYPOINT e i parametri il CMD

- Docker: 
  - ENTRYPOINT -> comando di default
  - CMD -> parametri di default
- K8s Pod:
  - COMMAND -> comando di default
  - ARGS -> parametri di default

Metti in ogni elemento dell'array, una singola parola non divisa da spazi, p,es. `args: ["--color", "green"]`

In questo modo, sono in grado dal k8s, di sovrascrivere l'entrypoint e il CMD dell'immagine Docker

P.es. se volessi sovrascrivere l'entrypoint del dockerfile, mi basta impostare il command del k8s pod definition

command può esser anche una lista, quindi p.es.: 
```
command: 
  - "sleep"
  - "5000"
```
Descrittore intero:

```
  apiVersion: v1
  kind: Pod
  metadata:
    name: ubuntu-sleeper-pod
  spec:
   containers:
   - name: ubuntu-sleeper
     image: ubuntu-sleeper
     command: ["sleep2.0"]
     args: ["10"]
  ```
  ![args](../../images/args.PNG)
  
#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/
