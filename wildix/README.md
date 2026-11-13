# Wildix e agente vocale AI: collegare il trunk

Collegare Wildix WMS 7 a un agente vocale AI: trunk SIP su TLS con SDES-SRTP, procedura di Dialplan dedicata, Trusted IP list. Guida per il partner.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/wildix-agente-vocale-ai

## In breve

- Un centralino Wildix si collega all'agente vocale AI di Aptiva con un trunk SIP che si registra da noi (Enable registration) su Transport TLS con SDES-SRTP: nessuna porta da aprire, nessun IP statico richiesto.
- Al trunk Aptiva si associa una procedura di Dialplan dedicata, con i soli interni e gruppi previsti come Called number e «default» che riaggancia: così i trasferimenti dell'agente arrivano all'interno giusto e le chiamate dal trunk raggiungono solo quegli interni.
- Le chiamate verso l'agente si mandano con l'applicazione «Dial the trunk», da main, da un tasto IVR, dopo il timeout di un Call group o con «Verify timetable» per il fuori orario.
- L'indirizzo Aptiva va nella Trusted IP list (Settings → PBX → Security), che accetta solo IP singoli: da lì arrivano i trasferimenti. Su Wildix la configurazione è spesso in mano al partner: la guida completa è pensata per essere girata a lui.

## Credenziali del collegamento

[Richiedi le credenziali](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando «SIP», «Wildix WMS 7», il numero da collegare e gli interni o gruppi a cui trasferire; poi gira la [guida completa](https://www.aptiva.cloud/risorse/centralino/wildix-agente-vocale-ai) al tuo partner Wildix.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
