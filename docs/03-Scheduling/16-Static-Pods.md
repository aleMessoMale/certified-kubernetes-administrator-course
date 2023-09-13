# Static Pods 
  - Take me to [Video Tutorial](https://kodekloud.com/topic/static-pods/)
  
In this section, we will take a look at Static Pods

#### How do you provide a pod definition file to the kubelet without a kube-apiserver?
- You can configure the kubelet to read the pod definition files from a directory on the server designated to store 
information about pods.

## Configure Static Pod
- The designated directory can be any directory on the host and the location of that directory is passed in to the 
kubelet as an option while running the service. 
  - The option is named as **`--pod-manifest-path`**.
- **Questa roba funziona solo per i Pod**

- Controlla prima di tutto tra i parametri del kubelet.service, il pod-manifest-path parameter e successivamente
vai a controllare il parametro config.

Nel parametro config, un file yaml esterno, possiamo poi porre come valore staticPodPath, il valore della folder
dove creare i Pod statici.

Lui nei lab va anche a cannone su `/var/lib/kubelet/config.yaml`, ma è più pulito vedere con systemctl status kubelet 
perché il valore potrebbe cambiare
  
  ![sp](../../images/sp.PNG)
  
## Another way to configure static pod 
- Instead of specifying the option directly in the **`kubelet.service`** file, you could provide a path to another 
config file using the config option, and define the directory path as staticPodPath in the file.

  ![sp1](../../images/sp1.PNG)

## View the static pods
- To view the static pods utilizziamo Docker, perché kubectl funziona laddove funziona l'API Server, ma senza 
Api Server, dobbiamo utilizzare docker

Inoltre, se ci fosse l'API server, tramite kubectl, vedremmo i Pod statici, come ogni altro Pod,
perché il control plane conosce anche i Pod statici, visto che kubelet ne fa un mirroring, per almeno listarlo.
  - per vederlo o vederne i dettagli, ci basta fare come al solito, un kubectl get\describe po
  - **per modificarlo o cancellarlo, ci serve invece modificare il manifest file, direttamente nella pod manifest folder
dell'host**

Il nome degli static pods, termina con un suffiso che indica il nome nel quale si trovano, p.es. 
- kube-apiserver-controlplane
- etd-controlplane
- static-pod-node01

Su singolo Pod, c'è una sezione dello yaml che si chiama `ownerReferences`, dove se il `kind:Node`, indica chiaramente
uno static Pod, altrimenti p.es. avremmo trovato un ReplicaSet o cose simili, possiamo p.es. quindi utilizzare i 
vari filtri del comando kubectl per capirlo

  ```
  $ docker ps
  ```
  ![sp2](../../images/sp2.PNG)

#### The kubelet can create both kinds of pods - the static pods and the ones from the api server at the same time.

In pratica, kubelet può creare entrambi i tipi di Pod, da una parte crea i Pod statici, come visto sopra, ma è in grado
anche di creare i Pod, esponendo un'API, solitamente chiamata dall'API Server, che è il modo normale.

  ![sp3](../../images/sp3.PNG)

## Static Pods - Use Case

In pratica, si utilizza per deployare il control plane stesso, quindi si mettono le definizioni dei Pod nella Pod manifest 
folder e il kubelet si occupa di crearli e mantenerli attivi, restartarli se non funzionano e così via
 - quindi controller-manager.yaml, apiserver.yaml o etcd.yaml

  ![sp4](../../images/sp4.PNG)
  
  ![sp5](../../images/sp5.PNG)
  
## Static Pods vs DaemonSets

   ![spvsds](../../images/spvsds.PNG)
  

#### K8s Reference Docs
- https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/
