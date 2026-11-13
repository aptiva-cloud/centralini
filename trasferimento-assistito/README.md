# Trasferimento assistito con l'agente vocale AI

Trasferimento assistito con l'agente vocale AI: cosa sente chi risponde, cosa sente il chiamante, come scrivere la presentazione, rubrica e costi.

Guida completa, passo per passo: https://www.aptiva.cloud/risorse/centralino/trasferimento-assistito-agente-ai

## In breve

- Con il trasferimento assistito l'agente vocale AI fa squillare l'interno o il numero giusto mentre il chiamante resta in linea con lui; quando qualcuno risponde, l'AI presenta la chiamata a voce, chi chiama e perché, secondo l'istruzione che imposti, e poi lascia le due persone a parlare direttamente fra loro.
- La presentazione è un'istruzione data all'AI, non un messaggio registrato: il testo si adatta a ogni chiamata, e lo sentono sia chi risponde sia il chiamante. La configuriamo noi all'attivazione, oppure la imposti tu via API.
- Se nessuno risponde entro il tempo di squillo (30 secondi di default), l'agente riprende la conversazione con il chiamante e, se l'hai impostato, segue il messaggio di mancata risposta: prende un messaggio o propone un richiamo. Impostalo sempre, insieme alla presentazione.
- Funziona sia con il centralino collegato (trasferimento agli interni, con il numero del chiamante sul display) sia con un numero Aptiva (trasferimento a numeri fissi, mobili o VoIP). Dal momento in cui il trasferimento è attivo, la conversazione costa 0,25 crediti al minuto.

## Configurazione

### Se nessuno risponde

```text
Se {contact_name} non risponde ({reason}), scusati brevemente con il
chiamante, spiega che in questo momento nessuno è disponibile, e proponi
due alternative: lasciare un messaggio dettagliato che verrà consegnato,
oppure essere richiamato indicando una fascia oraria. Prendi nota di
nome, numero e motivo della chiamata prima di salutare.
```

### Studio professionale (commercialista, avvocato)

```text
Presenta la chiamata a {contact_name} in due frasi: chi chiama (nome e,
se lo ha detto, azienda o pratica di riferimento) e il motivo della
chiamata. Se il chiamante ha indicato una scadenza o un'urgenza, dilla
per prima. Chiudi con «vi lascio in linea».
```

### Studio medico

```text
Presenta la chiamata a {contact_name} indicando chi chiama, se ha detto
di essere già paziente dello studio, e cosa chiede: una visita, una
ricetta, un referto o un'informazione. Se il chiamante ha descritto un
problema di salute, riportalo con le sue parole, senza interpretarlo.
Se ha detto che è urgente, dillo per primo. Poi lascia la linea.
```

### Azienda con più uffici

```text
Presenta la chiamata all'ufficio {contact_name}: chi chiama e per quale
azienda, cosa chiede in una frase, e se ha già un numero d'ordine, di
ticket o di pratica. Se il chiamante aveva chiesto un altro ufficio e lo
hai indirizzato qui, dillo. Chiudi con «vi lascio in linea».
```

Estratto il 13/11/2026 dalla guida di aptiva.cloud.
