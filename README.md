# spid-certificates-privatesector
Procedura per la generazione di certificati per SP privati.

Procedura testata e funzionante per la generazione dei certificati per SP privati che vogliono implementare l'autenticazione SPID per i loro servizi.

Requisiti: openssl versione 1.1.1 (testata) and xmlsectool versione 3.0.0..

Passi da effettuare:

1) scaricare in una directory i files spid_private.conf e oids.conf;

2) editare il file spid_private.conf modificandolo con i vostri dati (che dovranno essere identici a quelli riportati nel pdf da inviare ad AGID);

3) generare la chiave privata e la csr per l'invio successivo ad AGID tramite il seguente comando modificando il nome del CSR come da avviso:
   
   openssl req -config spid_private.conf -new -newkey rsa:2048 -nodes -keyout privkey.pem -out NOMEAZIENDA-PROGRESSIVOINVIO-YYYYMMDD.csr -extensions req_ext
   
4) leggere l'hash del csr da inviare ad AGID:

   openssl req -noout -modulus -in NOMEAZIENDA-PROGRESSIVOINVIO-YYYYMMDD.csr | openssl sha256

5) Compilare il modulo riportando esattamente i dati inseriti nel file spid_private.conf e l'hash sopraestratto;


Una volta ricevuto il certificato editate il metadata inserendo la chiave pubblica nella signing ed nella encryption.

Infine firmate tramite xmlsectool il metadata:

6) copiate nella cartella la chiave privata (privkey.pem) generata al punto 3, il vostro metadata (es: sp-metadata.xml) ed il certificato ricevuto ad AGID:
  
    ./xmlsectool.sh --sign --inFile sp-metadata.xml --outFile sp-metadata-signed.xml --certificate CERTIFICATORICEVUTO.crt --keyFile privkey.pem


