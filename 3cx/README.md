# 3CX e agente vocale AI: collegare il trunk SIP

Collegare 3CX a un agente vocale AI con un trunk SIP su TLS: template generico, DID per gli interni, regola in uscita, licenze, confronto con l'AI nativa.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/3cx-agente-vocale-ai

## In breve

- Un centralino 3CX V20 si collega all'agente vocale AI di Aptiva con un trunk «Generic VoIP Provider (Registration)» su TLS con audio SRTP: è il tuo 3CX a registrarsi da noi, quindi non servono porte aperte né un IP statico.
- Ogni interno o coda che l'agente deve poter raggiungere va dichiarato come DID del trunk Aptiva e assegnato alla sua destinazione: 3CX instrada le chiamate dal trunk solo verso DID censiti, il resto finisce sulla Default route.
- Le chiamate arrivano all'agente tramite una regola in uscita dedicata al trunk Aptiva, non limitata agli interni: così la usano anche IVR, code e gruppi di squillo.
- Serve un'edizione che accetti il trunk generico (Basic, PRO o AI): SMB Free accetta solo trunk «3CX Tested». La licenza si paga per chiamate simultanee, e una chiamata affidata all'agente ne occupa più d'una.

## Configurazione

### Come creare il trunk verso Aptiva

```text
Name                       Aptiva
Default route              <una destinazione interna, per esempio la casella vocale>
Type of authentication     Registration/Account based
Registrar/Server           <SERVER>
Port                       <PORTA>        (Auto-discovery disattivato)
Outbound Proxy             vuoto
Authentication ID          <UTENTE>
Authentication Password    <PASSWORD>
Main Trunk Number          <il numero che userai per chiamare l'agente, es. 7701>
```

### Trasporto TLS e audio SRTP

```text
Transport Protocol     TLS
Certificato radice     ISRG Root X1 (la radice di Let's Encrypt)
SRTP Mode              Enforced
Number of SIM Calls    vedi «Licenze e chiamate simultanee»
Allow inbound calls    attivo
Allow outbound calls   attivo
```

Per «Number of SIM Calls» vedi la sezione [Licenze e chiamate simultanee](https://www.aptiva.cloud/risorse/centralino/3cx-agente-vocale-ai#licenze-e-chiamate-simultanee) della guida completa.

### La regola in uscita per raggiungere l'agente

```text
Name                                     Verso l'agente Aptiva
Calls to numbers starting with prefix    7701
Calls to numbers with a length of        4
Calls from extension(s)                  vuoto (tutti gli interni)
Calls from departments                   vuoto
Make outbound calls on – prima route     trunk Aptiva
  Strip Digits                           0
  Prepend                                vuoto
  Outbound Caller ID                     vuoto
Route successive                         Block Calls
```

## Credenziali del collegamento

[Richiedi le credenziali del collegamento](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando protocollo SIP, edizione e versione del tuo 3CX, il numero da collegare e gli interni o le code a cui l'agente deve poter trasferire.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
