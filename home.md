# Wifiphisher

## Introduzione

Wifiphisher è un potente strumento per condurre attacchi di phishing su reti Wi-Fi. Sfruttando ingegneria sociale e tecniche di spoofing, induce gli utenti a fornire credenziali o a compiere azioni che compromettono la loro sicurezza.

## Funzionamento di Wifiphisher

Il funzionamento di Wifiphisher si basa su tre fasi principali:

1. **Disconnessione del target**: Wifiphisher inizia inviando pacchetti di deautenticazione al client target e all'AP legittimo. Questi pacchetti, causano la disconnessione forzata del client dalla rete originale.
Per poter effettuare questa operazione è necessario che la scheda di rete dell'attacante supporti sia la monitor mode, per poter scansionare le reti wireless nelle vicinanze, sia la packet injection, per poter inviare i pacchetti di deautenticazione.
2. **Creazione di un AP malevolo**: Dopo aver disconnesso il client, Wifiphisher crea un access point (AP) malevolo con lo stesso SSID dell'AP legittimo. Il client, non riuscendo a connettersi alla propria rete a causa dei pacchetti di deautenticazione, si collega inconsapevolmente all'AP malevolo.
3. **Phishing tramite pagina web**: Una volta connesso all'AP malevolo, il client viene reindirizzato a una pagina web di phishing che può assumere diverse forme a seconda dell'attacco specifico. Queste pagine sono progettate per sembrare legittime e convincere l'utente a fornire informazioni sensibili.

![Vignetta che rappresenta il funzionamento dell'attacco](images/scheme.jpeg)
_Figura 1: Vignetta che rappresenta il funzionamento dell'attacco_


## Tipologie di attacco

Il tool include diverse tecniche di attacco:

1. **Firmware Update Page**: Questo attacco presenta all'utente una pagina che simula un aggiornamento del firmware del router. L'utente viene invitato a inserire la password del Wi-Fi per procedere con l'aggiornamento.
2. **OAuth Login Page**: Questa tecnica reindirizza l'utente a una pagina di login che simula un servizio OAuth (ad esempio, Google, Facebook) per ottenere le credenziali di accesso.
3. **Browser Plugin Update**: In questo caso, l'utente vede una pagina che gli comunica la necessità di aggiornare un plugin del browser. L'utente viene quindi indotto a scaricare e installare un malware.
4. **Network Manager Connect**: Questo attacco presenta all'utente una pagina che simula l'interfaccia di connessione del sistema operativo, richiedendo nuovamente la password del Wi-Fi per connettersi alla rete.

## Tecnica Firmware Update Page

La tecnica sfrutta la fiducia che gli utenti ripongono nelle notifiche di aggiornamento del firmware dei loro router. Al termine dell'attacco, l'utente non esperto, non si sentirà di aver subito una truffa ma, al contrario, sarà felice di aver aggiornato il firmware del suo modem.

### Fasi dell'attacco in dettaglio

1. **Selezione del target**: L'attaccante identifica un punto di accesso Wi-Fi con un elevato numero di utenti connessi, utilizzando strumenti come `airodump-ng` per monitorare le reti wireless vicine e individuare quelle con più client. In questa fase, è necessario che la scheda di rete sia configurata in modalità monitor.

2. **Disconnessione del target**: Wifiphisher invia pacchetti di deautenticazione ai client connessi alla rete target, forzandoli a disconnettersi dal punto di accesso legittimo. Questa interruzione temporanea del servizio induce gli utenti a cercare di riconnettersi. Per realizzare questa fase, la scheda di rete deve essere in grado di iniettare pacchetti.

3. **Creazione dell'AP malevolo**: Simultaneamente alla disconnessione, Wifiphisher crea un AP con lo stesso SSID della rete legittima il quale può anche utilizzare un canale differente per evitare interferenze con l'access point originale. Questa pratica è chiamata *evil twin*, ovvero gemello maligno.

4. **Connessione del client all'AP**: Quando l'utente nota che il proprio dispositivo è disconnesso, tenta di riconnettersi al Wi-Fi nelle impostazioni del dispositivo. Non trovando la rete legittima, l'utente vede solo la rete creata dall'AP maligno, poiché la rete legittima è stata nascosta poiché considerata non funzionante.

5. **Reindirizzamento alla pagina di phishing**: Una volta connessi, i client vengono automaticamente reindirizzati a una pagina web che simula l'interfaccia di aggiornamento del firmware del loro router (Figura 2). Questo è possibile grazie a un web server e alla manipolazione dei server DHCP e DNS all'interno dell'AP maligno.

![Un esempio della pagina di phishing](images/fw_upgrade.png)
_Figura 2: Un esempio della pagina di phishing_

6. **Raccolta delle credenziali**: La pagina di phishing chiede agli utenti di inserire la password del Wi-Fi per procedere con l'aggiornamento del firmware, per rendenre il tutto credibile sono aggiunti anche dei finti termini e condizioni. Essendo la pagina personalizzabile, con pochissimo sforzo è possibile creare una pagina identica a quella originale. Quando l'utente inserisce la password quest'ultima verrà trasmessa all'attaccante, nel mentre, l'utente, verrà condotto in una pagina contenente una barra di progressione che gli darà l'illusione di star effettivamente facendo l'aggiornmento software (Figura 3).

![Un esempio della pagina di phishing](images/fw_upgrade-2.png)
_Figura 3: Un esempio della barra di progressione_

7. **Ripristino situazione precedente**: Una volta ottenuta la password l'attaccante spegnerà l'AP malevolo, l'utente avrà l'illusione che l'aggiornamento software sia terminato e quindi si accingerà a riconnettersi al proprio Wi-Fi che sarà nuovamente quello legittimo perfettamente funzionante.


## Modello MITRE ATT&CK

In riferimento al modello MITRE ATT&CK, la tecnica del Firmware Update Page può essere classificata sotto diverse categorie di tattiche e tecniche.

- **Tattica: Initial Access**: L'attacco inizia con l'accesso iniziale alla rete della vittima.
  - **Tecnica: Drive-by Compromise**: L'utente è indotto a interagire con una pagina web maligna.
- **Tattica: Credential Access**: L'obiettivo principale è ottenere le credenziali dell'utente.
  - **Tecnica: Input Capture**: La pagina di phishing cattura direttamente le credenziali inserite dall'utente.

## Prevenzione

Per proteggersi contro attacchi di questo tipo è fondamentale educare gli utenti riguardo alle buone pratiche di sicurezza informatica. Gli utenti devono essere sensibilizzati sull'importanza di connettersi solo a reti Wi-Fi sicure e conosciute, evitando reti pubbliche o non protette. Inoltre, è cruciale riconoscere le caratteristiche di un tentativo di phishing, come richieste improvvise di inserimento di credenziali o messaggi di avviso insoliti. 

## Conclusioni

Wifiphisher rappresenta uno strumento potente per dimostrare le vulnerabilità delle reti Wi-Fi attraverso tecniche di ingegneria sociale. La tecnica del Firmware Update Page evidenzia come l'attaccante possa sfruttare la fiducia dell'utente in contesti apparentemente legittimi per ottenere credenziali di accesso sensibili. Comprendere il funzionamento di queste tecniche e adottare adeguate contromisure è essenziale per proteggere le reti wireless da attacchi di phishing e altre forme di compromissione. Educare gli utenti, mantenere aggiornati i dispositivi e implementare protocolli di sicurezza avanzati sono passi fondamentali per mitigare i rischi associati a questi attacchi.

## Fonti
[https://github.com/wifiphisher/wifiphisher](https://github.com/wifiphisher/wifiphisher)
