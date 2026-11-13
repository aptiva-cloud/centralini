# Grandstream UCM e agente vocale AI: trunk SIP

Collegare Grandstream UCM6300 a un agente vocale AI: trunk SIP su TLS con SRTP, Inbound Route «By DID» per gli interni, Outbound Route, Fail2ban e Geo-IP.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/grandstream-ucm-agente-vocale-ai

## In breve

- Un Grandstream UCM6300 si collega all'agente vocale AI di Aptiva con un Register SIP Trunk su TLS con audio cifrato SRTP: il centralino si registra da noi con le credenziali che emettiamo, senza aprire porte né avere un IP statico.
- I trasferimenti dell'agente arrivano sull'UCM come nuove chiamate sul trunk, con il numero dell'interno come numero chiamato: li accoglie una Inbound Route con Default Destination «By DID», un pattern che elenca solo gli interni e «Dial Trunk» disattivato.
- Le chiamate verso l'agente partono da una Outbound Route dedicata al trunk Aptiva, raggiunta come «External Number» dalla Inbound Route del tuo operatore, da una Time Condition, da un tasto dell'IVR o dalla destinazione di Max Wait Time di una Call Queue.
- Sull'UCM le chiamate SRTP simultanee sono meno di quelle in chiaro (50 su 75 per un UCM6301): tienilo presente nel dimensionamento. L'indirizzo Aptiva va nella Allowlist di Fail2ban e, se usi il filtro Geo-IP, il suo Paese va ammesso.

## Credenziali del collegamento

[Richiedi le credenziali](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando «SIP», il modello di UCM e il firmware, il numero da collegare e gli interni o gruppi a cui trasferire.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
