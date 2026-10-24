# Sviluppo — <nome>

**Slug**: <slug>
**Stato**: pianificato
**Ondata attiva**: nessuna
**Ultimo checkpoint consegnato**: nessuno
**Copia principale**: <percorso assoluto e branch se applicabile>
**Contesto**: [CONTESTO.md](CONTESTO.md)
**Contratto**: [CONTRATTO.md](CONTRATTO.md)
**Avvio**: [AVVIO.md](AVVIO.md)
**Issue o record esterno**: <riferimento, oppure non applicabile>

> Stati dello sviluppo: pianificato, in-corso, completato.
> L'orchestratore è l'unico scrittore dello stato. I piani rimangono specifica.

## Obiettivo e invariante

<Valore per l'utente e comportamento che deve restare funzionante tra le ondate.>

## Sintesi funzionale

- Cosa fa: <comportamento>.
- Per chi: <destinatario>.
- Entry point: <UI, API, CLI, libreria o altro>.
- Non-obiettivi: <perimetro escluso>.

## Checkpoint

| # | Percorso verificabile | Ondate | Reale e simulato | Prerequisiti / migrazioni | Revisione verificata | Consegnato il | Verifica a destinazione |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | <percorso> | <numeri> | <mock e uscita, oppure tutto reale> | <prerequisiti> | — | — | — |

Destinazione e modalità di consegna: <da CONTESTO.md>.
L'ultimo checkpoint non contiene mock temporanei. Dall'OK alla consegna la copia principale resta
congelata; lavoro successivo soltanto in copie isolate autorizzate.

## Package

- [ ] <NN-nome> — ondata <k> — <modalità scelta> — [piano](piani/NN-nome.md)

`[x]` solo dopo un `ESITO OK` che copre il package, dopo l'integrazione nella copia principale.

## Ondate

| Ondata | Package | Modalità e copia | Dipende da | Motivo dell'ordinamento | Checkpoint |
| --- | --- | --- | --- | --- | --- |
| 1 | <NN-nome> | <principale o isolata, con path> | <package o nessuno> | <dipendenza, conflitto, capacità, checkpoint> | <n> |

Concorrenza utile: <numero e motivo, considerando harness, strumenti e disponibilità dell'utente>.
Critical path: <catena di dipendenze>.

## Ownership e ruoli

| Ruolo / identificativo | Copia e percorso | File posseduti | Vincoli |
| --- | --- | --- | --- |
| orchestratore | <principale> | stato e decisioni | non implementa né si attribuisce le verifiche del tester |
| tester | <principale> | <test ed esiti> | non modifica il codice applicativo |
| <worker> | <copia assegnata> | <file o piano> | non modifica test o artifact di stato |

## Regole operative

- Comandi, strumenti e deleghe: `CONTESTO.md`; la disponibilità tecnica non è un'autorizzazione.
- Un file, un solo scrittore. Gli artifact si leggono dal percorso assoluto della copia principale.
- `FINITO` è una consegna, non un'accettazione; inattività e timeout non attestano completamento.
- Scelte materiali non coperte dal contratto: `BLOCCATO` prima di applicarle.
- Niente segreti nei messaggi o negli artifact; usa riferimenti alle variabili necessarie.
- Fuori perimetro: segnala, non correggere senza una nuova autorizzazione.

## Prerequisiti

- [ ] <dati, ambiente, verifiche dell'utente, accessi, migrazioni o coordinamento esterno>

## Decisioni

| # | Data | Ambito | Decisione e fonte dell'approvazione | Documenti aggiornati |
| --- | --- | --- | --- | --- |

## Copertura requisiti

| Requisito e sorgente | Checkpoint | Package | Caso di verifica | Evidenza |
| --- | --- | --- | --- | --- |
| R1 — <comportamento e sorgente> | <n> | <NN> | <caso del piano test> | <esito e caso, oppure non ancora disponibile> |

## Gap dichiarati

<Requisiti o verifiche mancanti, motivi e decisioni dell'utente; nessuno se non ne emergono.>

## Fuori perimetro

<Difetti o opportunità non inclusi nello sviluppo.>

## Stato dell'esecuzione

| Package | Assegnato a | Copia | Stato | Evidenza / note |
| --- | --- | --- | --- | --- |
| <NN-nome> | — | <percorso> | da assegnare | — |

Stati: da assegnare, assegnato, finito, rientrato, in test, in correzione, bloccato,
collaudato (ESITO OK). Usa «rientrato» solo per un'integrazione effettiva.

Copie isolate aperte: <percorso, revisione base, branch se presente, owner e package; oppure nessuna>.

Allineamento con la base: <evidenza e data, oppure non necessario con motivo>.

Ripresa: <primo passo senza evidenza, agenti/sessioni da recuperare, autorizzazioni ancora valide>.

Registro eventi, dal più vecchio:

- <data> — <evento, evidenza, prossimo passo>
