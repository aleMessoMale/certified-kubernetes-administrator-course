# Network Policies
  - Take me to [Video Tutorials](https://kodekloud.com/topic/network-policies-3/)
  
#### Trafic flowing through a webserver serving frontend to users an app server serving backend API and a database server

  ![traffic](../../images/traffic.PNG)
  
- There are two types of traffic
  - Ingress -> traffico in ingresso nel sistema
  - Egress -> traffico in uscita dal sistema

Ingress ed Egress son sempre relativi ad un sistema (p.es. un DB, un microservizio, ecc ecc)
  
   ![ing1](../../images/ing1.PNG)
  
   ![ing2](../../images/ing2.PNG)
  
## Network Security

Un prerequisito di k8s è che, indipendentemente dalla tipologia di implementazione scelta, senza dover configurare
alcuna configurazione aggiuntiva, i Pod devono esser in grado di comunicare fra loro.

Possiamo implementare una network policy per attivare il traffico nel DB Pod, solo dall'API Pod (ingress), su una
determinata porta. Network Policy è ovviamente un altro descrittore k8s.

  ![nsec](../../images/nsec.PNG)
  
## Network Policy

Ipotizziamo che vogliamo che il Front End Pod non sia in grado di comunicare con il DB Pod, implementiamo delle policy
per farlo, visto che di base, ogni Pod è in grado di comunicare con ogni altro Pod.

  ![npol](../../images/npol.PNG)
  
  ![npol1](../../images/npol1.PNG)

Al fine di linkare la network policy con il relativo Pod, quello che facciamo è utilizzare labels and selectors, così
come avviene per linkare ReplicaSet con Pod.

Quindi le `labels` del Pod, son selezionate dal campo `podSelector.matchLabels` della network policy.

  
  ![npolsec](../../images/npolsec.PNG)
  
Questo è il modo in cui creiamo le policy:

  ![npol2](../../images/npol2.PNG)

Considera che:
- se noi abbiamo una regola di apertura dalla porta 3306, la risposta a quella richiesta è aperta di default, ossia
è stateful come il security group e non come le regole nacl.
- naturalmente, ingress o egress, dipendono dalla prospettiva rispetto al Pod a cui la regola è associata, ossia 
sul quale abbiamo effettuato la selezione sulle labels, ossia `spec.podSelector`
- una volta che associamo una network policy ad un Pod, di base non può avere traffico ingress o egress, a meno
di quello specificato nella network policy, quindi se specifichiamo solo una ingress rule, non può effettuare 
connessioni verso l'esterno
- E' possibile effettuare `matchLabels`, sulle labels in generale presenti sui vari Pod, ma anche su namespace
che hanno una determinata label, attraverso il campo `namespaceSelector`. In quel caso selezioniamo ogni Pod che
genera traffico da quel namespace. **Possiamo utilizzare un tipo di selettore o l'altro o entrambi**
- ulteriori filtering sono possibili con la clausola `ipBlock.cidr`, ipBlock è una lista, logicamente puoi
permettere il traffico da diversi CIDR
  - puoi avere quindi una lista di regole podSelector o namespaceSelector e una lista di regole ipBlock per ogni 
  elemento della lista from, che rappresenta la singola regola di ingress (p.es.)
  
Una regola fatta così: 
  ```
  - from:
    - podSelector:
        matchLabels:
          role: api-pod
      namespaceSelector:
        matchLabels: 
          name: prod
  ```

significa che le due selezioni (pod e namespace) sono in AND. Nel secondo caso, invece, sono in OR, ossia
il traffico è consentito se una delle 2 regole è consentita

  ```
  - from:
    - podSelector:
        matchLabels:
          role: api-pod
    - namespaceSelector:
        matchLabels: 
          name: prod
  ```

Idem se avessimo l'ipBlock, sulla singola regola, sarebbe in OR.

## Create network policy
 
- To create a network policy
  ```
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
   name: db-policy
  spec:
    podSelector:
      matchLabels:
        role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            role: api-pod
        namespaceSelector:
          matchLabels: 
            name: prod
      - ipBlock:
          cidr: 192.168.5.10/32
      ports:
      - protocol: TCP
        port: 3306
    egress:
    - to:
      - ipBlock:
          cidr: 192.168.5.11/32
      ports:
      - protocol: TCP
        port: 5502
  ```
  
  ```
  $ kubectl create -f policy-definition.yaml
  ```
  
 ![npol3](../../images/npol3.PNG)
 
 ![npol4](../../images/npol4.PNG)
  
## Note
 
 ![note1](../../images/note1.PNG)
 
#### Additional lecture on [Developing Networking Policies](https://kodekloud.com/topic/developing-network-policies/)

#### K8s Reference Docs
- https://kubernetes.io/docs/concepts/services-networking/network-policies/
- https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/
 
  
  
  
  
