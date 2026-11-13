# FreePBX e agente vocale AI: guida al collegamento

Collegare FreePBX a un agente vocale AI con un trunk IAX2 o SIP su TLS: campi del trunk, rotte in entrata per gli interni, instradamento, errori tipici.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/freepbx-agente-vocale-ai

## In breve

- FreePBX si collega all'agente vocale AI di Aptiva con un trunk che si registra verso di noi: nessuna porta da aprire, nessun IP statico. Il protocollo consigliato è IAX2 (Add IAX2 Trunk); in alternativa SIP con chan_pjsip su TLS e SRTP. Il collegamento — trunk, registrazione, chiamata all'agente e trasferimento a un interno — è provato sul campo con FreePBX.
- Le righe che contano nel trunk sono poche: il contesto d'ingresso predefinito (from-trunk su IAX2, quello proposto da FreePBX su SIP), la cifratura obbligatoria, i codec A-law e μ-law selezionati esplicitamente, From User e Outbound CallerID vuoti, e su SIP il campo Match (Permit) con l'indirizzo che ti forniamo, perché il trunk deve accettare anche le chiamate che arrivano da Aptiva.
- Per ogni interno che l'agente deve poter far squillare crei una Inbound Route dedicata con DID Number uguale all'interno: è così che il trasferimento arriva alla persona giusta con il numero del chiamante sul display.
- Le chiamate arrivano all'agente con una Inbound Route, una Misc Destination su una Outbound Route dedicata, una Time Condition o il fallback di un Ring Group: sono le rotte normali di FreePBX, che scegli in base a come vuoi usare l'agente. L'agente può anche chiamare in uscita dal numero della tua azienda, attraverso il tuo operatore (campagne, richiami): serve una Outbound Route dedicata e un contesto di due righe per il trunk Aptiva, che trovi nella guida o configuriamo insieme.

## Configurazione

### PEER Details e Register String

```text
host=<SERVER>
port=<PORTA>
username=<UTENTE>
secret=<PASSWORD>
type=friend
context=from-trunk
qualify=yes
encryption=yes
forceencryption=yes
requirecalltoken=yes
disallow=all
allow=alaw
allow=ulaw
```

```text
<UTENTE>:<PASSWORD>@<SERVER>:<PORTA>
```

### Il contesto del trunk Aptiva

Il contesto manda i numeri +39 alla Outbound Route dedicata Aptiva-uscita, che lascia uscire solo cellulari, fissi e VoIP italiani: si crea dall'interfaccia come spiega la guida completa.

```ini
; /etc/asterisk/extensions_custom.conf
[from-aptiva]
; I numeri +39 vanno alla rotta Aptiva-uscita (N = id della rotta).
exten => _+39X.,1,Goto(outrt-N,${EXTEN},1)
; Tutto il resto, cioè i trasferimenti agli interni, segue le Inbound Routes come prima.
exten => _X!,1,Goto(from-trunk,${EXTEN},1)
```

```text
dialplan show from-aptiva
dialplan show outrt-N
```

## Credenziali del collegamento

[Richiedi le credenziali del collegamento](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando il protocollo (IAX2 consigliato), la versione di FreePBX, il numero da collegare e gli interni o i gruppi a cui l'agente deve poter trasferire. L'attivazione del collegamento non ha costi.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
