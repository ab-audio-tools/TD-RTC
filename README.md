# Avviare il progetto in locale

## 1) Clona e prepara l'ambiente

Clona questo repository sulla tua macchina.

Assicurati di avere installato **Node.js LTS 18.12.1** (include **npm 8.19.2**).

## 2) Avvia il server di sviluppo

Dalla cartella del progetto, esegui:

```bash
npm start
```

Questo comando avvia l'app in **modalità sviluppo**. Apri [http://localhost:3000](http://localhost:3000) nel browser per visualizzarla.

* La pagina si **ricarica automaticamente** quando modifichi i file.
* Eventuali **errori di lint** compariranno nella console/terminal.

---

## HTTPS in sviluppo (consigliato)

Se vuoi eseguire il server di sviluppo su **HTTPS**, avvialo impostando queste variabili d'ambiente:

* `HTTPS=true`
* `SSL_CRT_FILE=PATH TO FILE.crt`
* `SSL_KEY_FILE=PATH TO FILE.key`

Puoi usare percorsi relativi (es. `./localhost.crt`, `./localhost.key`).

**Alternativa via `package.json`**: modifica lo script `start` in questo modo:

```json
"start": "set HTTPS=true&&set SSL_CRT_FILE=PATH TO FILE.crt&&set SSL_KEY_FILE=PATH TO FILE.key&&react-scripts start"
```

Per farlo funzionare serve un **certificato server**. Vedi la sezione **"Ottenere un certificato di sviluppo"** qui sotto.

---

## Demo web

È disponibile una demo ospitata online:
[https://touchdesigner.github.io/WebRTC-Remote-Panel-Web-Demo/](https://touchdesigner.github.io/WebRTC-Remote-Panel-Web-Demo/)

Per connettere la demo al tuo **signalingServer** locale, è necessario un certificato server (vedi **"Ottenere un certificato di sviluppo"**).

> Nota: i certificati di sviluppo **non sono considerati affidabili** dai browser. Se hai dimestichezza con le CA, puoi installare un certificato valido sul signalingServer.

Se usi un certificato di sviluppo, aggiungilo al **trust store** del browser (es. Chrome → **Settings → Privacy and security → Security → Manage device certificates**) oppure visita prima l'URL **HTTPS** del signalingServer per accettare temporaneamente il certificato.

---

## Configurazione in TouchDesigner

* In **TouchDesigner**, aggiungi un **signalingServer COMP** (Palette → WebRTC).
  Con TLS, abilita **Secure** e imposta i parametri **certificate** e **key**.

* Avvia un **signalingClient** (in locale o su un'altra macchina) e abilita **Forward to subscribers**.

* Configura il **WebRTCRemotePanel COMP** impostando il parametro `signalingClient` (quello creato in precedenza) e collegando un pannello compatibile.

---

## Limitazioni note

Non tutti i componenti si comportano perfettamente con questa configurazione, ma molti **setup semplici** sono coperti.

* Potresti notare **click non registrati** o **dialoghi che non compaiono**.
* Regola pratica: **evita i dialog** nei componenti custom. Espone invece i parametri direttamente nell'interfaccia (widget / Parameter COMPs).
* **Input da tastiera** al momento non supportato (in parte previsto nel codice).
* **Touch** da smartphone supportato parzialmente.

---

## Perfect negotiation (note progettuali)

Il progetto segue in larga parte lo schema di **"perfect negotiation"**, con piccole modifiche per adeguarsi agli schemi dei messaggi di **Signaling**.

Differenza principale: la scelta di quale **signaling client** debba essere "polite".
Con il signaling server di TouchDesigner, il **signalingServer COMP** restituisce un **"join time"** quando conferma l'ingresso di un client in sessione. Questo **join time** determina quale client sarà polite (o meno) durante la negoziazione.

Riferimenti utili:

* Mozilla — Perfect Negotiation
* Google — Esempio WebRTC
* Fondamenti di WebRTC

---

## Ottenere un certificato di sviluppo

Consigliamo **mkcert**: [https://github.com/FiloSottile/mkcert](https://github.com/FiloSottile/mkcert)

1. Installa mkcert seguendo le istruzioni del repository.
2. Apri un terminale **nella cartella del progetto TouchDesigner** e genera un certificato locale con:

```bash
mkcert -install
mkcert -cert-file tdServer.crt -key-file tdServer.key localhost 127.0.0.1
```

Questo creerà `tdServer.crt` e `tdServer.key` nella root del progetto.

Imposta questi file nei parametri **certificate/key** del tuo **signalingServer COMP**.

---

## JSON Schemas

Gli schemi dell'API di Signaling di TouchDesigner sono disponibili qui:
[https://github.com/TouchDesigner/SignalingAPI](https://github.com/TouchDesigner/SignalingAPI)

---

## Pubblicazione (per maintainer/contributor)

Per aggiornare la pagina ospitata su GitHub Pages (servono permessi di scrittura):

```bash
npm run deploy
```

Riferimento Create React App: [https://create-react-app.dev/docs/deployment/](https://create-react-app.dev/docs/deployment/)

Assicurati che in `package.json` sia presente un campo `homepage`, ad esempio:

```json
"homepage": "https://myusername.github.io/my-app"
```
