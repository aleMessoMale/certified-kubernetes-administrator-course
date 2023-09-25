# TLS Basics
  - Take me to [Video Tutorial](https://kodekloud.com/topic/tls-basics/)
  
In this section, we will take a look at TLS Basics

## Certificate
- A certificate is used to guarantee trust between 2 parties during a transaction.
- Example: when a user tries to access web server, tls certificates ensure that the communication between them is encrypted.

  ![cert1](../../images/cert1.PNG)
  
  
## Symmetric Encryption

- It is a secure way of encryption, but it uses the same key to encrypt and decrypt the data and the key has to be exchanged between the sender and the receiver, there is a risk of a hacker gaining access to the key and decrypting the data.
- Per questo motivo, quel che si fa è: 
  - stabilire il canale di comunicazione criptato (utilizzando la crittografia assimetrica e costosa)
  - scambiare la chiave simmetrica
  - utilizzare il canale criptato per scambiare i dati criptati con la chiave simmetrica scambiata

    ![cert2](../../images/cert2.PNG)
  
## Asymmetric Encryption
- Instead of using single key to encrypt and decrypt data, asymmetric encryption uses a pair of keys, 
a private key and a public key.
- la private key è solo mia e privata
- la public key, che lui chiama lock (lucchetto), è pubblica e tutti possono conoscerla

**Qualcosa di criptato con la chiave pubblica, può essere decriptato solo con la chiave privata**

E' quello che facciamo, quando vogliamo p.es. accedere ad un server remoto tramite chiave, generiamo una coppia di
chiavi con il comando ssh-keygen e teniamo la chiave privata, mettendo la chiave pubblica nel server che deve farci
entrare nelle authorized keys

Tieni conto che nella criptazione assimetrica, si creano 2 coppie di chiave che sono connesse fra loro. Entrambe
possono criptare e decriptare, anche se chiaramente è necessario criptare con la pubblica e decriptare con la
privata, se facessimo il contrario, chiunque potrebbe leggere quel che inviamo.

  ![cert3](../../images/cert3.PNG)
  
  ![cert4](../../images/cert4.PNG)

Tornando a parlare del server http:

Come già detto, la strategia è creare un canale di comunicazione criptato, tramite chiave assimetrica, scambiarsi
una chiave simmetrica, per poi permettere la comunicazione dei dati criptati, tramite chiave simmetrica tra il 
chiamante ed il chiamato.

Con la chiave pubblica, possiamo solo criptare, non decriptare.

Quel che succede è che: 
- il client genera una chiave simmetrica e le cripta utilizzando la chiave pubblica del server.
- Il server utilizza la sua chiave privata per decriptare la chiave simmetrica fornita dal chiamato
- A questo punto possono iniziare a scambiarsi dati, utilizzando la chiave simmetrica per criptare e decriptare, 
mettendo di fatto su un canale di comunicazione criptato

P.es. con il comando openssl, siamo in grado di generare una coppia di chiavi private e pubbliche
  
  ![cert5](../../images/cert5.PNG)


Come possiamo verificare che stiamo parlando con il server corretto?
  
  ![cert6](../../images/cert6.PNG)
  

#### How do you look at a certificate and verify if it is legit?
- who signed and issued the certificate

E' la CA (Certificate Authority) che emette i certificati e li firma, associandoli ad una nome
- Nel campo **CN (Common Name)** c'è il nome del sito (mybank.com)
- Se l'ente o sito è "noto" con altri nomi, dovrebbero esser specificati in questo certificato, nel campo 
**Subject Alternative Name**, per matchare con quello che l'utente ha scritto nel browser

- If you generate the certificate then you will have it sign it by yourself; that is known as self-signed certificate
ed è per questo che, quando poi navighiamo con il browser, vede che è emesso e firmato da un ente che non è 
una CA e quindi è come se non fosse valido.

Di base per trustare una CA, e quindi dire che il certificato è valido, è necessario che il browser abbia la relativa 
chiave pubblica (ca.crt). Le chiavi pubbliche delle principali CA son builtin nel browser e permettono al browser di verificare
che il certificato è stato firmato realmente da quella CA, questo è il motivo per cui se creiamo un certificato
tramite una private CA (Vodafone Internal), è necessario aggiungere la chiave pubblica della CA al browser.
 
  ![cert7](../../images/cert7.PNG)
  
#### How do you generate legitimate certificate? How do you get your certificates singed by someone with authority?
- That's where **`Certificate Authority (CA)`** comes in for you. 
Some of the popular ones are Symantec, DigiCert, Comodo, GlobalSign etc.

- In pratica tu gli dici su che dominio devi generare il certificato e lui te lo emette e firma e associa a te.
- Chiaramente se arriva un hacker e richiede un certificato per lo stesso nome e dominio, la fase di validazione
della CA che porta alla firma del certificato, fallirebbe, perché quel dominio è associato a te

  ![cert8](../../images/cert8.PNG)
  
  ![cert9](../../images/cert9.PNG)
  
  ![cert10](../../images/cert10.PNG)
  
## Public Key Infrastructure
   
   ![pki](../../images/pki.PNG)

Quel che succede è:
- l'utente accede al sito e il sito gli invia il suo certificato (che contiene la sua chiave pubblica)
- lui controlla che sia un certificato valido e dopo di che utilizza la chiave pubblica per criptare la chiave simmetrica
- dopo che il server ha ricevuto il pacchetto criptato, utilizza la chiave privata per decriptare la chiave simmetrica
criptata con la chiave pubblica
- da la in avanti le comunicazioni sono criptate e decriptate utilizzando la chiave simmetrica

Se il server vuole esser sicuro dell'identità del client, effettua quella che viene chiamata mutua autenticazione,
dove in pratica anche il client ha un certificato client e il server agisce da client.


   
## Certificates naming convention

- i certificati pubblici e le chiavi pubbliche, solitamente hanno estensione `pem` o `crt`
- le chiavi private, hanno estensione solitamente `.key` o `-key.pem`

  ![cert11](../../images/cert11.PNG)
  
  

  
   

  
  
  

  
  
  
  
  
  

  
  
