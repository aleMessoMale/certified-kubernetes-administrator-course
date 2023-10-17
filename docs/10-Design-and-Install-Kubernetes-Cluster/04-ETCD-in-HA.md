# ETCD in HA

 - Take me to [Lecture](https://kodekloud.com/topic/etcd-in-ha/)


- ETCD è distribuito e si ha un processo di elezione di leader e follower
- ETCD è in HA fra diversi nodi, con ETCD, solo un nodo scrive ed è il leader, gli altri
sono i follower e laddove arrivi una richiesta di scrittura ad uno dei follower, loro si preoccupano
di girare la richiesta di scrittura al leader, che a sua volta si preoccupa di far si che loro la ricevano.
Questo per evitare che si scrivano dati differenti nelle varie copie del db.
- Il processo di scrittura è terminato, quando i vari follower confermano la ricezione della scrittura al leader, 
ossia quando i dati son replicati fra le varie istanze nel cluster
  - meglio, quando il dato è replicato nella maggioranza dei nodi nel cluster (quindi 2 su 3 va ancora bene, ossia
N/2 + 1, chiamato anche quorum)
- Essendo in HA, anche se si perde un nodo, il sistema rimane ancora in funzione

- Il processo di elezione del leader è RAFT. Una volta che è eletto un leader, se i follower non ricevono
conferma dal leader che continuerà a effettuare la leadership, iniziano un nuovo processo di scelta del leader
fra loro.

- **Meglio sempre scegliere un numero dispari di nodi**, perché in caso di perdita di un certo numero di nodi, 
per un qualsiasi motivo, il quorum si mantiene con una probabilità più alta, rispetto ad avere il numero
pari più vicino. Se ne hai 7, puoi dividerli in 3 e 4, e il gruppo con 4 continuerà a funzionare. Se son 6 
e li dividi in 2 da 3, non funzioneranno entrambi.

- Per installare etcd, abbiamo già visto: 
    - scarichi il binario
    - generi i certificati
    - imposti l'etcd.service
    - testalo con l'etcdctl utility (ricordati la variabile di ambiente ETCDCTL_API=3)