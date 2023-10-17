# Persistent Volume Claims

  - Take me to [Lecture](https://kodekloud.com/topic/persistent-volume-claims-4/)

In this section, we will take a look at **Persistent Volume Claim**

- Now we will create a Persistent Volume Claim to make the storage available to the node.
- Volumes and Persistent Volume Claim are two separate objects in the Kubernetes namespace.
- Once the Persistent Volume Claim created, Kubernetes binds the Persistent Volumes to claim based on the request and properties set on the volume.
- Ogni persistentVolumeClaim è legato ad un PersistentVolume e questo avviene durante la fase di Binding, dove
k8s prova a trovare il miglior match rispetto alla richiesta effettuata riguardo p.es.:
- Capacity 
- Access Mode
- Volume Mode
- Storage Class

Si può sempre forzare un determinato volume, al volume claim, utilizzando la solita modalità di labels and selectors
per esser sicuri che volume e volumeClaim matchino quando ci possono esser più possibili pv per un determinato pvc:

Lato pvc utilizziamo 

```
selector:
  matchLabels:
    name: my-pv
```

lato pv:

```
labels: 
  name: my-pv
```

![class-17](../../images/class17.PNG)

- If properties not matches or Persistent Volume is not available for the Persistent Volume Claim then 
it will display the pending state.

```
pvc-definition.yaml

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: myclaim
spec:
  accessModes: [ "ReadWriteOnce" ]
  resources:
   requests:
     storage: 1Gi
```

```
pv-definition.yaml

kind: PersistentVolume
apiVersion: v1
metadata:
    name: pv-vol1
spec:
    accessModes: [ "ReadWriteOnce" ]
    capacity:
     storage: 1Gi
    hostPath:
     path: /tmp/data
```

#### Create the Persistent Volume

```
$ kubectl create -f pv-definition.yaml
persistentvolume/pv-vol1 created

$ kubectl get pv
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-vol1   1Gi        RWO            Retain           Available                                   10s
```


#### Create the Persistent Volume Claim

```
$ kubectl create -f pvc-definition.yaml
persistentvolumeclaim/myclaim created

$ kubectl get pvc
NAME      STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
myclaim   Pending                                                     35s

$ kubectl get pvc
NAME      STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE
myclaim   Bound    pv-vol1   1Gi        RWO                           1min

```

#### Delete the Persistent Volume Claim

```
$ kubectl delete pvc myclaim
```

A seconda di cosa c'è scritto nella property `persistentVolumeReclaimPolicy`, il volume è cancellato ugualmente o meno.
Di **default** il valore è `Reclaim`, che fa si che il volume non è cancellato e non viene nemmeno bindato su un altro
Pod. Ma p.es. con il valore `Delete`, viene automaticamente cancellato e con `Recycle` i dati son cancellati e il
volume è reso disponibili ad altri Pod.

#### Delete the Persistent Volume

```
$ kubectl delete pv pv-vol1
```


#### Kubernetes Persistent Volume Claims Reference Docs

- https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims
- https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#persistentvolumeclaim-v1-core
- https://docs.cloud.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengcreatingpersistentvolumeclaim.htm
