# Collegare il centralino all'agente vocale AI di Aptiva

Esempi di configurazione per collegare il tuo centralino ad [Aptiva](https://www.aptiva.cloud), la piattaforma italiana di agenti vocali AI per le chiamate aziendali. L'agente risponde alle chiamate e chiama i contatti, 24 ore su 24, in italiano, e passa la chiamata a una persona presentandogliela a voce.

Il centralino si collega con un trunk IAX2 o SIP su TLS con audio SRTP, entrambi cifrati: SIP in chiaro non viene accettato. Ogni cartella contiene il riassunto della guida, i blocchi di configurazione, quando la guida li riporta, e il link alla guida completa su aptiva.cloud, che spiega ogni passaggio.

| Centralino | Cartella | Guida completa |
|---|---|---|
| Asterisk | [asterisk](asterisk/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/asterisk-agente-vocale-ai) |
| FreePBX | [freepbx](freepbx/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/freepbx-agente-vocale-ai) |
| NethVoice | [nethvoice](nethvoice/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/nethvoice-agente-vocale-ai) |
| 3CX | [3cx](3cx/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/3cx-agente-vocale-ai) |
| Grandstream UCM | [grandstream-ucm](grandstream-ucm/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/grandstream-ucm-agente-vocale-ai) |
| Wildix | [wildix](wildix/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/wildix-agente-vocale-ai) |
| Yeastar P-Series | [yeastar](yeastar/) | [aptiva.cloud](https://www.aptiva.cloud/risorse/centralino/yeastar-agente-vocale-ai) |

Il collegamento è già stato provato sul campo con FreePBX; per gli altri centralini le guide si basano sulla documentazione ufficiale.

Per far presentare le chiamate dall'agente prima di passarle, nella cartella [trasferimento-assistito](trasferimento-assistito/) trovi esempi di istruzioni di presentazione.

## Credenziali

Il collegamento si attiva con le credenziali che emettiamo noi. Richiedile scrivendo a info@aptiva.cloud o dalla [pagina Contatti](https://www.aptiva.cloud/contatti), con i dati indicati nella cartella del tuo centralino. L'attivazione non ha costi. Requisiti, sicurezza e costi sono nella [guida generale al collegamento](https://www.aptiva.cloud/risorse/centralino).

Estratti il 13/11/2026 dalle guide di aptiva.cloud aggiornate il 13 ottobre 2026.
