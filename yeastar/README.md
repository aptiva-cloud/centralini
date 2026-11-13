# Yeastar P-Series e agente vocale AI: guida al trunk

Yeastar P-Series e agente vocale AI: Register Trunk su TLS e SRTP, Inbound Route verso gli interni, Outbound Route, Allowed IPs, Cloud e Appliance.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/yeastar-agente-vocale-ai

## In breve

- Un Yeastar P-Series si collega all'agente vocale AI di Aptiva con un Register Trunk (template General) su trasporto TLS e con Enable SRTP attivo: è il centralino a registrarsi da noi, quindi non servono porte aperte né un IP statico.
- Serve almeno il firmware V24.1: le release precedenti hanno un difetto per cui, sulle chiamate uscenti via trunk con SRTP, l'audio diventa a senso unico dopo 15 minuti.
- I trasferimenti dell'agente arrivano come nuove chiamate sul trunk con il numero dell'interno come DID: bastano una Inbound Route legata al solo trunk Aptiva con DID Pattern uguale all'interno, oppure una sola route con «Match DID Pattern to Extensions».
- Le chiamate arrivano all'agente con una Outbound Route dedicata al trunk Aptiva, usata da IVR, code, gruppi di squillo e Inbound Route con condizione oraria. Autorizza l'indirizzo Aptiva fra gli IP consentiti: Allowed IPs sulla Cloud Edition, una regola Static Defense con azione Accept su Appliance e Software Edition.

## Configurazione

### Come creare il Register Trunk verso Aptiva

```text
Basic
  Name                    Aptiva
  Trunk Status            Enabled
  Select ITSP Template    General

Detailed Configuration
  Trunk Type              Register Trunk
  Transport               TLS
  Hostname/IP             <SERVER>
  Port                    <PORTA>
  Domain                  <SERVER>
  Username                <UTENTE>
  Password                <PASSWORD>
  Authentication Name     <UTENTE>
  Enable Outbound Proxy   disattivato
```

### Le opzioni Advanced che contano

```text
VoIP Settings
  Qualify                     attivo
  Enable SRTP                 attivo
  T.38 Support                disattivato (come consiglia Yeastar)
  Support SIP REFER           disattivato

Call Restriction
  Call Restriction Type       Outbound Call oppure All (vedi sotto)
  Maximum Concurrent Calls    vedi sotto

Codec
  a-law, u-law                in quest'ordine; rimuovi tutti gli altri

SIP Headers > Outbound Parameters
  From User Part              Originator Caller ID

Get DID From                  Follow System (se i trasferimenti non arrivano: Invite oppure To)
Get Caller ID From            Follow System
```

Per «Call Restriction» vedi la sezione [Sicurezza: Allowed IPs e limiti sul trunk](https://www.aptiva.cloud/risorse/centralino/yeastar-agente-vocale-ai#sicurezza-allowed-ips-e-limiti-sul-trunk) della guida completa.

### L'Inbound Route che porta i trasferimenti agli interni

```text
Name                   Aptiva verso interno 201
Trunk                  solo il trunk Aptiva
DID Pattern            201
Caller ID Pattern      vuoto
Default Destination    Extension 201
```

```text
Name                   Aptiva verso gli interni
Trunk                  solo il trunk Aptiva
DID Matching Mode      Match DID Pattern to Extensions
DID Pattern            {{.Ext}}
Default Destination    Match Selected Extensions → gli interni che l'agente può raggiungere
```

### L'Outbound Route verso l'agente

```text
Name                          Verso l'agente Aptiva
Dial Pattern
  Pattern                     7701
  Strip                       vuoto
  Prepend                     vuoto
Trunks                        solo il trunk Aptiva
Outbound Caller ID            vuoto
Password                      Disable
Extension/Extension Group     tutti gli interni, o quelli che possono avviare la chiamata
Time Condition                Always
```

## Credenziali del collegamento

[Richiedi le credenziali del collegamento](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando protocollo SIP, edizione e firmware del tuo Yeastar, il numero da collegare e gli interni o le code a cui l'agente deve poter trasferire.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
