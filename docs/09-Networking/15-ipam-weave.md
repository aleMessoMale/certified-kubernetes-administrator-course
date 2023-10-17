# IPAM weave

  - Take me to [Lecture](https://kodekloud.com/topic/ipam-weave/)

In questa lezione spiega: 
- chi assegna i cidr delle subnet bridge al nodo
- chi assegna l'indirizzo IP al Pod
- chi garantisce che il Pod abbia un IP univoco nel cluster

CNI logicamente definisce le responsabilità e dice che **è responsabilità del plugin fornire un indirizzo IP univico
al Pod**.

In generale poi saranno i vari plugins a implementare, garantendo il funzionamento, p.es. weave crea un'unica grande
rete tra i vari nodi, configurabile quando si installa il weave plugin nel cluster.


- IP Address Management in the Kubernetes Cluster

![net-3](../../images/net3.PNG)


- How weaveworks Manages IP addresses in the Kubernetes Cluster 

![net-4](../../images/net4.PNG)


## References Docs

- https://www.weave.works/docs/net/latest/kubernetes/kube-addon/
- https://kubernetes.io/docs/concepts/cluster-administration/networking/ 