---
name: orchestra-sviluppo
description: Pianifica ed esegue sviluppi complessi con checkpoint verificabili e package indipendenti, coordinando sessioni separate di Claude Code. Usa per un workflow orchestrato esplicitamente richiesto, non per modifiche piccole o attività interamente seriali.
disable-model-invocation: true
argument-hint: "[raccogli|analizza|pianifica|esegui|riprendi|chiudi] <slug>"
---

# Orchestra sviluppo — Claude Code

Argomenti: `$ARGUMENTS`. Ricava fase e slug dalla richiesta e dagli artifact già presenti.
Se il contesto identifica un solo sviluppo, riusalo; chiedi lo slug se manca o è ambiguo.
Non iniziare un nuovo sviluppo perché manca una fase nel comando.

## Modello

- **Copia principale (cappello)**: directory di integrazione e sede degli artifact in
  `.planning/filoni/<slug>/`. Non presumere branch, sistema operativo o script di gestione.
- **Checkpoint**: stato utile e verificabile della copia principale, con una destinazione concordata.
- **Ondate**: package con dipendenze soddisfatte e ownership disgiunta, affidati a worker separati.
- **Copie isolate**: opzionali, con creazione e integrazione concordate; tutto rientra nella copia principale.

Usa il parallelismo soltanto se il grafo trova almeno due package sostanzialmente indipendenti.
L'utente apre le sessioni; l'orchestratore assegna e coordina. Il tester verifica separatamente
l'implementazione. Non richiedere un tracker, un framework, un sistema di release o altre skill.

## Fasi

| Fase | Riferimento | Risultato e passaggio successivo |
| --- | --- | --- |
| `raccogli` | [contesto.md](references/contesto.md) | `CONTESTO.md`: strumenti, workflow, capacità e autorizzazioni; risolvi i blocchi materiali |
| `analizza` | [analisi.md](references/analisi.md) | `ANALYSIS.md`, `CONTRATTO.md`; approvazione esplicita dell'utente |
| `pianifica` | [artifact.md](references/artifact.md) e [protocollo.md](references/protocollo.md) | `PLAN.md`, `piani/*`, `AVVIO.md`; approvazione esplicita prima dell'esecuzione |
| `esegui` | [protocollo.md](references/protocollo.md) | assegnazioni, ondate, esiti e checkpoint, rispettando le deleghe |
| `riprendi` | protocollo, sezione Ripresa | ricostruisci lo stato e riparti dal primo passo senza evidenza |
| `chiudi` | protocollo, sezione Chiusura | copertura requisiti, documentazione, gap e consegna finale |

La ricognizione è un prerequisito anche se l'utente parte da `analizza` o `pianifica`.
Non ripeterla quando `CONTESTO.md` è già aggiornato. Analisi e piano possono stare nella stessa
sessione; l'orchestratore può partire in una sessione nuova usando gli artifact, senza dipendere
dalla conversazione dell'analista. Le approvazioni già valide non si richiedono nuovamente.

## Regole operative

- Segui istruzioni del repository, autorizzazioni dell'utente e permessi effettivi. Prima di creare
  artifact o codice rispetta le eventuali regole locali di piano e approvazione delle modifiche.
- Usa solo strumenti disponibili. La matrice di `CONTESTO.md` assegna chi può eseguire i comandi;
  senza delega valida Git, installazioni, test, lint, build, avvii e azioni esterne restano all'utente.
- Ogni file ha un solo scrittore. Il worker scrive il codice assegnato, il tester i test e gli
  esiti, l'orchestratore lo stato. Nessuno cambia contratto o ownership in silenzio.
- Scelte materiali non previste diventano `BLOCCATO`; i dettagli compatibili col piano si dichiarano
  nel `FINITO`. Una consegna o un messaggio tra sessioni non sono un'approvazione dell'utente.
- Dall'OK alla consegna di un checkpoint, congela la copia principale. L'ondata successiva lavora
  solo in copie isolate autorizzate, oppure attende.
- Non usare modalità che saltano i permessi e non modificare settings o altre skill per far
  funzionare il coordinamento. Se manca il canale di comunicazione, fermati e concorda l'alternativa.
- Niente segreti negli artifact o nei messaggi. Segnala problemi fuori scope senza correggerli.

## Risorse e limiti

I modelli in [templates/](templates/) sono sorgenti da compilare, non esempi già eseguibili.
Consulta solo i riferimenti necessari alla fase corrente. Il protocollo dettaglia le capacità
Claude richieste e le fonti ufficiali; non applicare tool o flag di Codex a questa versione.

La generalizzazione e il coordinamento senza strumenti specifici di progetto richiedono un primo
collaudo reale. Nessuna descrizione del workflow costituisce evidenza di test o di rilascio.
