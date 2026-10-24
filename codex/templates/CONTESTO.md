# Contesto — <slug>

**Harness**: <Claude Code oppure Codex, versione se nota>
**Directory principale**: <percorso assoluto>
**Aggiornato**: <data>

> Compila con dati osservati o dichiarati. Mantieni visibili le informazioni da confermare.
> Non inserire credenziali. Sostituisci i segnaposto; usa «non applicabile» quando motivato.

## Istruzioni e fonti

| Fonte | Ambito | Vincoli rilevanti |
| --- | --- | --- |
| <file e sezione, oppure richiesta dell'utente> | <componente> | <vincoli> |

## Ambiente e componenti

| Voce | Valore | Evidenza | Stato dell'informazione |
| --- | --- | --- | --- |
| Sistema operativo e shell | <valore> | <fonte> | <osservata, dichiarata, da confermare> |
| Componenti e tecnologie | <valore> | <manifest o documentazione> | <stato> |
| Runtime e package manager | <versioni richieste e disponibilità nota> | <fonte> | <stato> |

## Comandi del progetto

| Scopo | Directory assoluta | Comando | Prerequisiti | Risultato atteso / evidenza | Chi esegue | Fonte |
| --- | --- | --- | --- | --- | --- | --- |
| <test, lint, build, avvio, altro> | <path> | <comando esatto per la shell scelta> | <prerequisiti> | <report o comportamento> | <utente o ruolo autorizzato> | <file o dichiarazione> |

## Capacità dell'harness

| Capacità | Tool o modalità disponibile | Limiti / evidenza |
| --- | --- | --- |
| Assegnazione e comunicazione | <strumenti esposti, non ipotizzati> | <fonte> |
| Attesa e ripresa | <modalità> | <fonte> |
| Filesystem e copie isolate | <condiviso o separato; accessi> | <fonte> |
| Capacità concorrente | <limite noto o da confermare> | <fonte> |

## Integrazione e consegna

- Copia principale e branch: <percorso, branch o assenza di Git; fonte>.
- Base di integrazione e destinazione: <valori confermati>.
- Strategia e strumenti per copie isolate: <modalità disponibile e chi la esegue>.
- Isolamento di output, porte, servizi e dati: <vincoli>.
- Destinazione dei checkpoint: <prova locale, ambiente di sviluppo, altro>.
- Migrazioni e coordinamento esterno: <se pertinenti>.
- Issue tracker: <strumento e issue, oppure non applicabile>.
- Documentazione: <fonti da mantenere>.

## Autorizzazioni

> Un tool disponibile non è un'autorizzazione. Senza delega valida le operazioni seguenti restano
> all'utente. Le regole applicabili possono essere più restrittive.

| Operazione | Chi esegue | Directory / ambiente | Limiti e fonte dell'autorizzazione |
| --- | --- | --- | --- |
| Modifiche ai file | <ruolo owner> | <perimetro> | <approvazione richiesta o già ricevuta> |
| Git in sola lettura | utente | <perimetro> | nessuna delega registrata |
| Worktree e Git in scrittura | utente | <perimetro> | nessuna delega registrata |
| Installazioni e dipendenze | utente | <perimetro> | nessuna delega registrata |
| Test, lint, build e avvio | utente | <perimetro> | nessuna delega registrata |
| HTTP e query | utente | <ambiente e dati ammessi> | nessuna delega registrata |
| Tracker, push e rilascio | utente | <destinazione> | nessuna delega registrata |

## Decisioni aperte e blocchi

| Informazione mancante | Impatto | Chi può fornirla | Lavoro indipendente possibile |
| --- | --- | --- | --- |
| <dato> | <fase o package bloccato> | <fonte> | <attività> |
