# View Certificate Details
  - Take me to [Video Tutorial](https://kodekloud.com/topic/view-certificate-details/)
  
In this section, we will take a look how to view certificates in a kubernetes cluster.

## View Certs 

Se lo installi con "The Hard way", ti generi te i certificati e starti te i componenti come servizi, con kubeadm,
quello che fa è installare i componenti come Pod e generare lui i certificati

 ![hrd](../../images/hrd.PNG)

 ![hrd1](../../images/hrd1.PNG)

Possiamo partire dal `kube-apiserver.yaml` per vedere dove sono i vari certificati che utilizza e ispezionarli
 
 - To view the details of the certificate
   ```
   $ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
   ```
   
   ![hrd2](../../images/hrd2.PNG)
   
#### Follow the same procedure to identify information about of all the other certificates

   ![hrd3](../../images/hrd3.PNG)
   
## Inspect Server Logs - Hardware setup (the hard way)

Vedere i log di un servizio, interessante:

- Inspect server logs using journalctl
  ```
  $ journalctl -u etcd.service -l
  ```
  
  ![hrd4](../../images/hrd4.PNG)
  
## Inspect Server Logs - kubeadm setup

Vedere log del Pod

- View logs using kubectl
  ```
  $ kubectl logs etcd-master
  ```
  ![hrd5](../../images/hrd5.PNG)
  
- View logs using docker ps and docker logs se le cose non vanno bene e non è disponibile nemmeno kubectl

Cerchi il Pod dell'etcd, p.es., e poi prosegui leggendone i logs

  ```
  $ docker ps -a
  $ docker logs <container-id>
  ```

Oppure se non è installato docker: 

  ```
    $ crictl ps -a
    $ crictl logs container-id
  ```

  ![hrd6](../../images/hrd6.PNG)
  
#### K8s Reference Docs
- https://kubernetes.io/docs/setup/best-practices/certificates/#certificate-paths
  
  

  

   
   

