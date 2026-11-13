# NethVoice e agente vocale AI: collegare il trunk

Collegare NethVoice su NethServer 8 a un agente vocale AI: trunk IAX2 dall'interfaccia avanzata, NethVoice Proxy, rinomina Custom-, percorsi in entrata.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/nethvoice-agente-vocale-ai

## In breve

- NethVoice su NethServer 8 è FreePBX 16 con un'interfaccia propria e un proxy SIP obbligatorio davanti (NethVoice Proxy): il trunk verso l'agente vocale AI di Aptiva si configura dall'interfaccia avanzata, cioè FreePBX, perché Aptiva non è tra i provider della procedura guidata.
- Per NethVoice la via indicata è IAX2: non passa dal NethVoice Proxy, usa una sola porta UDP dell'istanza per segnalazione e audio ed è cifrato in modo nativo. Se ti serve SIP, scrivici: lo verifichiamo insieme sulla tua installazione.
- Chiama il trunk Custom-Aptiva: la documentazione usa il prefisso Custom- per proteggere dagli aggiornamenti i trunk modificati dall'interfaccia avanzata, e conviene usarlo anche qui.
- Per ogni interno che l'agente deve poter far squillare crei un percorso in entrata dedicato con il numero dell'interno come numero chiamato. L'agente può anche chiamare in uscita dal numero della tua azienda, attraverso il tuo operatore (campagne, richiami): la configurazione è quella di FreePBX, fatta dall'interfaccia avanzata, con le poche differenze di NethVoice spiegate nella guida, oppure la facciamo insieme. Istruzioni scritte sulla documentazione ufficiale di NethVoice per NethServer 8 (modulo 1.7); se la tua interfaccia è diversa, scrivici.

## Credenziali del collegamento

[Richiedi le credenziali del collegamento](https://www.aptiva.cloud/contatti) (o scrivi a info@aptiva.cloud) indicando il protocollo (IAX2), la versione di NethVoice, il numero da collegare e gli interni o i gruppi a cui l'agente deve poter trasferire. L'attivazione non ha costi.

Estratto il 13/11/2026 dalla guida aggiornata il 13 ottobre 2026.
