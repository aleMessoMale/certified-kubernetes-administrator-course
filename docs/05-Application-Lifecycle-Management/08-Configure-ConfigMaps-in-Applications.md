# Configure ConfigMaps in Applications
  - Take me to [Video Tutorial](https://kodekloud.com/topic/configure-configmaps-in-applications/)
  
In this section, we will take a look at configuring configmaps in applications

## ConfigMaps
- There are 2 phases involved in configuring ConfigMaps. 
  - First, create the configMaps
  - Second, Inject then into the pod.
- There are 2 ways of creating a configmap.
  - The Imperative way: 
    - from-literal - passando i valori a raso (=key=value)
    - from-file - passando un file di properties

    ```
    $ kubectl create configmap app-config --from-literal=APP_COLOR=blue --from-literal=APP_MODE=prod
    $ kubectl create configmap app-config --from-file=app_config.properties (Another way)
    ```
    ![cmi](../../images/cmi.PNG)
    
  - The Declarative way - **nota che non c'è spec, ma data**: 
    
    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
     name: app-config
    data:
     APP_COLOR: blue
     APP_MODE: prod
    ```
    ```
    Create a config map definition file and run the 'kubectl create` command to deploy it.
    $ kubectl create -f config-map.yaml
    ```
    ![cmd1](../../images/cmd1.PNG)
    
 ## View ConfigMaps
 - To view configMaps
   ```
   $ kubectl get configmaps (or)
   $ kubectl get cm
   ```
 - To describe configmaps
   ```
   $ kubectl describe configmaps
   ```
   
   ![cmv](../../images/cmv.PNG)
   
 ## ConfigMap in Pods
 - Inject configmap in pod

Ci basta aggiungere una sezione, nel container, con i valori `envFrom` e `configMapRef`, a cui segue poi ancora
il `name`, per indicare il nome della configmap
 - `envFrom` è una **lista**, quindi possiamo passare tutte le configmap che vogliamo, ognuna con il suo `configMapRef`

   ```
   apiVersion: v1
   kind: Pod
   metadata:
     name: simple-webapp-color
   spec:
    containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
      - containerPort: 8080
      envFrom:
      - configMapRef:
          name: app-config
   ```
   ```
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
   data:
     APP_COLOR: blue
     APP_MODE: prod
   ```
   ```
   $ kubectl create -f pod-definition.yaml
   ```
  
   ![cmp](../../images/cmp.PNG)
   
 #### There are other ways to inject configuration variables into pod   
 - You can inject it as a **`Single Environment Variable`** 

    ```
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the value
              key: special.how
    ```
 - 
 - You can inject it as a file in a **`Volume`**

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: dapi-test-pod
    spec:
      containers:
        - name: test-container
          image: registry.k8s.io/busybox
          command: [ "/bin/sh", "-c", "ls /etc/config/" ]
          volumeMounts:
          - name: config-volume
            mountPath: /etc/config
      volumes:
        - name: config-volume
          configMap:
            # Provide the name of the ConfigMap containing the files you want
            # to add to the container
            name: special-config
      restartPolicy: Never
     ```
   
   ![cmp1](../../images/cmp1.PNG)
   
 #### K8s Reference Docs
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#define-container-environment-variables-using-configmap-data
 - https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#create-configmaps-from-files
