# Introduction to Docker Storage

  - Take me to [Lecture](https://kodekloud.com/topic/introduction-to-docker-storage-3/)
  
In this section, we will take a look at **Docker storage**.

- To understand storage in the container orchestration tools like **Kubernetes**, It is important to first understand how storage works with containers. Understanding how storage works with **Docker** first and getting all the basics right will later make it so much easier to understand how it works in **Kubernetes**.

- If you are new to **Docker** then you can learn some basics of docker from the course [Docker for the absolute beginner course](https://kodekloud.com/courses/docker-for-the-absolute-beginner/), that is free. 

## Docker Storage

**- There are two concepts comes into the docker, Storage drivers and Volume drivers plugins**
In pratica 
- Quando montiamo una folder esistente nell'host, utilizziamo gli storage drivers
  - AUFS, ZFS, Overlay, Overlay2 ecc ecc
- Quando invece creiamo (imperativamente o automaticamente) e montiamo un volume, sotto la folder dell'host 
`/var/lib/docker`, utilizziamo il Volume Driver, in pratica forniamo un volume al container dove vengono
memorizzati i dati, rispetto al caso dello storage, dove invece montiamo un volume esistente. Il volume, quando
utilizziamo il volume driver, è storato sotto `/var/lib/docker`
  - Local, Azure File Storage, gce-docker ecc ecc (per salvare nel cloud)

![class-1](../../images/class1.PNG)

#### We will first discuss about Storage drivers.

#### Docker Reference Docs

- https://docs.docker.com/storage/storagedriver/
- https://docs.docker.com/storage/volumes/