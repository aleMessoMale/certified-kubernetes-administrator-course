# Pre-requisite DNS

  - Take me to [Lecture](https://kodekloud.com/topic/prerequsite-dns/)

In this section, we will take a look at **DNS in the Linux**

Differenza fra file `hosts` e `resolv.conf`

`/etc/resolv.conf`: Lists nameservers that are used by your host for DNS resolution. 
If you are using DHCP, this file is automatically populated with DNS record issued by DHCP server.
`/etc/hosts/`: It is just a static lookup method for resolution. Il DNS non è coinvolto, è una risoluzione statica, anzi
se tramite il comando `hostname`, il nome macchina destinazione fosse differente, per il chiamante, sarebbe trasparente.
`/etc/nsswitch.conf`: It defined order of resolution. Who should it consult first for resolution, 
a DNS or a host file? For example, if the file has following configuration hosts: `files dns` then /etc/hosts file will 
be checked first for resolution, if domain is still un-resolvable, DNS will then be consulted.

Questo è il modo in cui la macchina risolve i nomi, che sia per un comando di `ping`, `ssh`, `curl` ecc ecc

## Name Resolution 

- With help of the `ping` command. Checking the reachability of the IP Addr on the Network.

```
$ ping 172.17.0.64
PING 172.17.0.64 (172.17.0.64) 56(84) bytes of data.
64 bytes from 172.17.0.64: icmp_seq=1 ttl=64 time=0.384 ms
64 bytes from 172.17.0.64: icmp_seq=2 ttl=64 time=0.415 ms

```
- Checking with their hostname

```
$ ping web
ping: unknown host web

```
- Adding entry in the `/etc/hosts` file to resolve by their hostname.

```
$ cat >> /etc/hosts
172.17.0.64  web


# Ctrl + c to exit
```
- It will look into the `/etc/hosts` file.

```
$ ping web
PING web (172.17.0.64) 56(84) bytes of data.
64 bytes from web (172.17.0.64): icmp_seq=1 ttl=64 time=0.491 ms
64 bytes from web (172.17.0.64): icmp_seq=2 ttl=64 time=0.636 ms

$ ssh web

$ curl http://web
```

## DNS

Fondamentalmente si è partiti tramite file hosts, ma con il passare del tempo e la crescita dei sistemi, 
ci si è resi conto che fosse troppo complesso, per cui si è passati  a spostare le entry sempre più lunghe
nei file hosts, nei DNS Servers.

- **Every host has a DNS resolution configuration file at `/etc/resolv.conf`**.

```
$ cat /etc/resolv.conf
nameserver 127.0.0.53
nameserver 8.8.8.8
options edns0
```

In questo caso, il default DNS è `127.0.0.53`, quindi durante la risoluzione, una volta controllato 
quanto presente sul file hosts, si passa a chiedere al DNS il nome dell'host (se il file nsswitch.conf non è 
stato modificato)

- To change the order of dns resolution, we need to do changes into the `/etc/nsswitch.conf` file.

```
$ cat /etc/nsswitch.conf

hosts:          files dns
networks:       files

```

- If it fails in some conditions.

```
$ ping wwww.github.com
ping: www.github.com: Temporary failure in name resolution

```

- Adding well known public nameserver in the `/etc/resolv.conf` file.

```
$ cat /etc/resolv.conf
nameserver   127.0.0.53
nameserver   8.8.8.8
options edns0
``` 
```
$ ping www.github.com
PING github.com (140.82.121.3) 56(84) bytes of data.
64 bytes from 140.82.121.3 (140.82.121.3): icmp_seq=1 ttl=57 time=7.07 ms
64 bytes from 140.82.121.3 (140.82.121.3): icmp_seq=2 ttl=57 time=5.42 ms

```

## Domain Names

Logicamente i DNS sono gerarchici, e risolvono i domini
- top level domain (.com, .edu, ecc ecc)
- domain name (google)
- subdomain (maps, mail, maps, ecc ecc)

![img.png](../../images/dns-resolution.png)

Come vedi, c'è prima il DNS dell'organizzazione, che non conoscendo il nome, lo spara su internet al root DNS, che legge `.com `
e lo gira al top-level-domain .com che leggendo il dominio `google`, lo gira al Google DNS che leggendo `apps` restituisce
l'indirizzo corretto

![net-8](../../images/net8.PNG)




## Search Domain

Idem è all'interno dell'organizzazione, così come fosse il Google DNS.

Specificando nel resolv.conf `search mycompany.com`, **gli stiamo dicendo che, se non specificato il dominio, 
il dominio di default è questo**, così possiamo risolvere web.mycompany.com, semplicemente tramite web.

![img.png](../../images/search-domain.png)

## Record Types

![net-10](../../images/net10.PNG)

## Networking Tools

- Useful networking tools to test dns name resolution.

#### nslookup 

Query un hostname tramite la risoluzione DNS presente sulla macchina (ignora `/etc/hosts` file)

```
$ nslookup www.google.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   www.google.com
Address: 172.217.18.4
Name:   www.google.com
```

#### dig

Sempre risoluzione DNS, fornisce più informazioni, più vicino a come viene memorizzata la info nel server DNS.

```
$ dig www.google.com

; <<>> DiG 9.11.3-1 ...
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8738
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;www.google.com.                        IN      A

;; ANSWER SECTION:
www.google.com.         63      IN      A       216.58.206.4

;; Query time: 6 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
```
