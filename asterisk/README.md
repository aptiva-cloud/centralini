# Asterisk e agente vocale AI: IAX2 o PJSIP a mano

Collegare Asterisk 20/22 LTS a un agente vocale AI: iax.conf e pjsip.conf con segnaposto, contesto dedicato agli interni, Dial verso il trunk, comandi CLI.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/asterisk-agente-vocale-ai

## In breve

- Asterisk si collega all'agente vocale AI di Aptiva con una registrazione in uscita: su IAX2 basta una sezione friend in iax.conf con cifratura forzata; su SIP servono transport TLS, registration, auth, aor, endpoint con SRTP e un identify sull'indirizzo che ti forniamo.
- Le chiamate da Aptiva (i trasferimenti verso i tuoi interni) atterrano in un contesto dedicato che raggiunge gli interni previsti, come si fa con il trunk di qualunque operatore. L'agente può anche chiamare in uscita dal numero della tua azienda, attraverso il tuo operatore (campagne, richiami): serve un contesto dedicato di poche righe, una per lunghezza di numero, che trovi nella guida o configuriamo insieme.
- Per mandare una chiamata all'agente basta un Dial verso il trunk: Dial(IAX2/<UTENTE>/8000) oppure Dial(PJSIP/8000@aptiva). Il numero composto non è vincolante e il caller ID non va toccato.
- Riferimento: Asterisk 20 e 22 LTS con chan_iax2 e chan_pjsip. chan_sip è stato rimosso in Asterisk 21 e non è coperto. Istruzioni scritte sulla documentazione ufficiale e sul sample di configurazione di Asterisk.

## Configurazione

### iax.conf: il trunk IAX2 (consigliato)

```ini
; /etc/asterisk/iax.conf

[general]
; La porta locale resta quella predefinita: non va aperta in ingresso,
; perché la registrazione la apre Asterisk verso Aptiva.
register => <UTENTE>:<PASSWORD>@<SERVER>:<PORTA>

; Lascia attive le protezioni predefinite descritte in "IAX2 Security"
; (validazione dei call token e limiti di chiamata): non serve toccarle.

; Il sample di Asterisk contiene una sezione [guest] per chiamate non autenticate:
; se è presente nel tuo file, rimuovila o assegnale un contesto che non fa nulla.

[<UTENTE>]
; Il nome della sezione DEVE essere l'utente che ti abbiamo emesso:
; è l'identificativo con cui Aptiva autentica il tuo Asterisk in entrambe le direzioni.
type = friend
host = <SERVER>
port = <PORTA>
username = <UTENTE>
secret = <PASSWORD>

; Contesto dedicato ai trasferimenti dell'agente: vedi extensions.conf più sotto.
context = from-aptiva

; La cifratura è forzata dalla parte di Aptiva: senza queste due righe
; il trunk non si collega in chiaro, non si collega affatto.
encryption = yes
forceencryption = yes
requirecalltoken = yes

; Solo G.711, A-law per primo (Italia).
disallow = all
allow = alaw
allow = ulaw

; Tiene viva la mappatura NAT e ti dice se il peer risponde.
qualify = yes
```

### pjsip.conf: il trunk SIP su TLS con SRTP

```ini
; /etc/asterisk/pjsip.conf

;--- Trasporto TLS -------------------------------------------------------
; PJSIP crea un trasporto TLS solo se gli indichi un certificato. Aptiva NON
; richiede un certificato client: un certificato autofirmato va benissimo.
; La porta di bind è locale e non va aperta in ingresso.
[transport-tls]
type = transport
protocol = tls
bind = 0.0.0.0:5061
cert_file = /etc/asterisk/keys/asterisk.crt
priv_key_file = /etc/asterisk/keys/asterisk.key
; sslv23: nonostante il nome negozia la versione TLS più alta disponibile
; (TLS 1.2 o 1.3, le due che il collegamento accetta). tlsv1_2 funziona ma
; esclude TLS 1.3; il predefinito si ferma a TLS 1.0, che Aptiva rifiuta.
method = sslv23
; Verifica il certificato pubblico di Aptiva con la CA di sistema (percorso Debian).
; Se preferisci un file singolo, usa ca_list_file: il file deve contenere la
; radice ISRG Root X1 di Let's Encrypt (per esempio il bundle delle CA di
; sistema), non il certificato del nostro server, che si rinnova da solo.
verify_server = yes
ca_list_path = /etc/ssl/certs
; Dietro NAT, decommenta e adatta (vedi la guida ufficiale sul NAT):
; local_net = 192.168.1.0/24
; external_signaling_address = <IL_TUO_IP_PUBBLICO_O_FQDN>
; external_media_address = <IL_TUO_IP_PUBBLICO_O_FQDN>

;--- Registrazione in uscita ---------------------------------------------
[aptiva]
type = registration
transport = transport-tls
outbound_auth = aptiva
server_uri = sip:<SERVER>:<PORTA>;transport=tls
client_uri = sip:<UTENTE>@<SERVER>
retry_interval = 60
forbidden_retry_interval = 60
expiration = 3600

;--- Credenziali ---------------------------------------------------------
[aptiva]
type = auth
auth_type = userpass
username = <UTENTE>
password = <PASSWORD>

;--- Dove mandare le chiamate verso Aptiva ------------------------------
; La parte utente del contatto la imposta il Dial: Dial(PJSIP/8000@aptiva).
[aptiva]
type = aor
contact = sip:<SERVER>:<PORTA>;transport=tls
qualify_frequency = 60

;--- L'endpoint ----------------------------------------------------------
[aptiva]
type = endpoint
transport = transport-tls
; Contesto dedicato ai trasferimenti dell'agente: vedi extensions.conf più sotto.
context = from-aptiva
outbound_auth = aptiva
aors = aptiva

disallow = all
allow = alaw
allow = ulaw

; Audio cifrato obbligatorio. Con la cifratura "opzionale" (optimistic=yes)
; la chiamata fallisce: sul collegamento non esiste un ripiego in chiaro.
media_encryption = sdes
media_encryption_optimistic = no

; NAT: quasi sempre corretti per un trunk verso un servizio su Internet.
rtp_symmetric = yes
force_rport = yes
rewrite_contact = yes
direct_media = no

; NON impostare from_user né riscrivere il caller ID: nel From viaggia
; il numero di chi chiama, ed è ciò che Aptiva deve ricevere intatto.

;--- Riconoscere le chiamate che arrivano da Aptiva ---------------------
; Il trunk deve accettare anche le chiamate che arrivano da Aptiva, cioè i
; trasferimenti dell'agente. Portano nel From il numero del chiamante originale,
; non il nostro utente: Asterisk deve associarli all'endpoint per indirizzo,
; altrimenti li scarta come "No matching endpoint found".
[aptiva]
type = identify
endpoint = aptiva
match = <INDIRIZZO_APTIVA>
```

### extensions.conf: il contesto che riceve i trasferimenti

```ini
; /etc/asterisk/extensions.conf

;--- Gli interni che l'agente può far squillare --------------------------
; Il context della sezione [<UTENTE>] in iax.conf e dell'endpoint in pjsip.conf.
[from-aptiva]
exten => _20[1-9],1,NoOp(Trasferimento dall'agente vocale AI verso ${EXTEN} per il chiamante ${CALLERID(num)})
 same => n,Dial(PJSIP/${EXTEN},30)
 same => n,Hangup()

; Un gruppo: la voce di rubrica "7" fa squillare insieme due telefoni.
exten => 7,1,NoOp(Trasferimento dall'agente vocale AI al gruppo segreteria)
 same => n,Dial(PJSIP/201&PJSIP/202,30)
 same => n,Hangup()
```

### Mandare una chiamata all'agente con Dial

```ini
; IAX2: tecnologia/peer/numero
exten => s,1,Dial(IAX2/<UTENTE>/8000,60)

; SIP: numero@endpoint (la parte utente sostituisce quella del contatto dell'AOR)
exten => s,1,Dial(PJSIP/8000@aptiva,60)
```

```ini
[from-operatore]

; 1) Tutte le chiamate al numero collegato vanno all'agente.
exten => 0432000000,1,NoOp(Chiamata da ${CALLERID(num)} all'agente vocale AI)
 same => n,Dial(IAX2/<UTENTE>/8000,60)
 same => n,Hangup()

; 2) Prima squillano i telefoni; se nessuno risponde entro 20 secondi, l'agente.
exten => 0432000001,1,Dial(PJSIP/201&PJSIP/202,20)
 same => n,Dial(IAX2/<UTENTE>/8000,60)
 same => n,Hangup()

; 3) In orario d'ufficio i telefoni, fuori orario l'agente.
exten => 0432000002,1,GotoIfTime(09:00-18:00,mon-fri,*,*?ufficio:agente)
 same => n(ufficio),Dial(PJSIP/201&PJSIP/202,30)
 same => n,Goto(agente)
 same => n(agente),Dial(IAX2/<UTENTE>/8000,60)
 same => n,Hangup()

; 4) Da un menu vocale: l'opzione 3 passa all'agente.
[ivr-principale]
exten => 3,1,Dial(IAX2/<UTENTE>/8000,60)
 same => n,Hangup()
```

### Chiamate in uscita dal tuo numero

```ini
; /etc/asterisk/extensions.conf

[from-aptiva]
; Le regole degli interni restano quelle della sezione precedente. Accanto, aggiungi:
; cellulari a 9 e 10 cifre
exten => _+393XXXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+393XXXXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
; fissi da 6 a 11 cifre
exten => _+390[1-9]XXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+390[1-9]XXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+390[1-9]XXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+390[1-9]XXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+390[1-9]XXXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
exten => _+390[1-9]XXXXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
; numeri VoIP (serie 55)
exten => _+3955XXXXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
; numeri verdi, facoltativi: decommenta solo se ti servono
;exten => _+39800XXXXXX,1,Goto(aptiva-uscita,${EXTEN},1)
;exten => _+39803XXX,1,Goto(aptiva-uscita,${EXTEN},1)

;--- L'uscita verso il trunk del tuo operatore -----------------------------
[aptiva-uscita]
exten => _+39X.,1,Set(GROUP()=aptiva-uscita)
 same => n,GotoIf($[${GROUP_COUNT(aptiva-uscita)} > 2]?pieno)
 same => n,Set(CALLERID(num)=<NUMERO_AZIENDA>)
 same => n,Set(CALLERID(name)=<NOME_AZIENDA>)
 same => n,Dial(PJSIP/${EXTEN:3}@<TRUNK_OPERATORE>,60)
 same => n,Hangup()
 same => n(pieno),Hangup(34)
```

## Credenziali del collegamento

[Richiedi le credenziali del collegamento](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando il protocollo (IAX2 consigliato), la versione di Asterisk, il numero da collegare e gli interni o i gruppi a cui l'agente deve poter trasferire. L'attivazione non ha costi.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
