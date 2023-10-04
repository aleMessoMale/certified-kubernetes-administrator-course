# Certificate API
  - Take me to [Video Tutorial](https://kodekloud.com/topic/certificates-api/)
  
In this section, we will take a look at how to manage certificates and certificate API's in kubernetes

In pratica generi una CSR e da la il relativo descrittore. In questo modo puoi utilizzare la certificates API di kubernetes 
per farti firmare la CSR dalla CA di kubernetes

## CA (Certificate Authority)
- The CA is really just the pair of key and certificate files that we have generated, 
whoever gains access to these pair of files can sign any certificate for the kubernetes environment, può creare 
gli utenti che vuole, con i permessi che vuole

- Essendo chiave e certificato, due cose molto rischiose a livello di sicurezza, deve esser storato in un server
solitamente chiamato CA Server. O ne hai uno ad hoc, oppure il CA Server è solitamente nei nodi master, ossia nei 
nodi master è memorizzata chiave e certificato, questo è esposto poi tramite una cosa chiamata Certificates API


#### Kubernetes has a built-in certificates API that can do this for you. 
- With the certificate API, we now send a certificate signing request (CSR) directly to kubernetes through an API call.
   
  ![csr](../../images/csr.PNG)
   
#### This certificate can then be extracted and shared with the user.
- A user first creates a key
  ```
  $ openssl genrsa -out jane.key 2048
  ```
- Generates a CSR
  ```
  $ openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr 
  ```
- Sends the request to the administrator and the adminsitrator takes the key and creates a CSR object, 
with kind as "CertificateSigningRequest" and a encoded "jane.csr". In pratica questo è il descrittore
per sottoporre una CSR al Certificate API

Nella parte spec, come si vede, c'è la parte groups, per indicare i privilegi dell'utente e nella parte request
c'è il CSR codificato in base64. Questo è per un utente che può loggarsi, se aggiungessimo `system:masters`, sarebbe
praticamente admin. Il nome utente sarà jane, in questo caso. Il certificato va senza \n characters.

  ```
  apiVersion: certificates.k8s.io/v1beta1
  kind: CertificateSigningRequest
  metadata:
    name: jane
  spec:
    signerName: kubernetes.io/kube-apiserver-client
    expirationSeconds: 86400  # one day
    groups:
    - system:authenticated
    usages:
    - digital signature
    - key encipherment
    - server auth
    request:
      <certificate-goes-here>
  ```
  
Generazione della parte che va nella request e successiva creazione del descrittore per sottoporre una CSR alla 
CA di kubernetes. Elimina i caratteri "\n" con il `tr -d `command, questo perché abbiamo bisogno che il certificato
sia una singola riga

   ```
  $ cat jane.csr | base64 | tr -d "\n"
  $ kubectl create -f jane.yaml
  ```
 ![csr1](../../images/csr1.PNG)
  
- To list the csr's
  ```
  $ kubectl get csr
  ```
- Approve the request
  ```
  $ kubectl certificate approve jane
  ```
- To view the certificate
  ```
  $ kubectl get csr jane -o yaml
  ```
- To decode it
  ```
  $ echo "<certificate>" | base64 --decode
  ```
  
  ![csr2](../../images/csr2.PNG)
  
#### All the certificate releated operations are carried out by the controller manager. 
- If anyone has to sign the certificates they need the CA Servers, root certificate and private key. 
The controller manager configuration has two options where you can specify this.
- Il controller manager è il component della control plane che si occupa di approvare e firmare le CSR e infatti
fra i parametri di avvio ha la chiave e il certificato della CA

  ![csr3](../../images/csr3.PNG)
  
  ![csr4](../../images/csr4.PNG)
  
  
#### K8s Reference Docs
- https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/
- https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/
 
  


