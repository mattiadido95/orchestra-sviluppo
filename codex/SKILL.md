---
name: orchestra-sviluppo
description: Pianifica ed esegue sviluppi complessi con checkpoint verificabili e package indipendenti, coordinando subagent nativi di Codex. Usa per un workflow orchestrato esplicitamente richiesto, non per modifiche piccole o attività interamente seriali.
---

# Orchestra sviluppo — Codex

Ricava fase (`raccogli`, `analizza`, `pianifica`, `esegui`, `riprendi`, `chiudi`) e slug dal testo
che accompagna l'invocazione `$orchestra-sviluppo` e dagli artifact esistenti. Se il contesto
identifica un solo sviluppo, riusalo; chiedi lo slug se manca o è ambiguo. Non assumere una variabile
di argomenti o un comando slash di un altro harness.

## Modello

- **Copia principale (cappello)**: directory di integrazione e sede degli artifact in
  `.planning/filoni/<slug>/`. Non presumere branch, sistema operativo o script di gestione.
- **Checkpoint**: stato utile e verificabile della copia principale, con una destinazione concordata.
- **Ondate**: package con dipendenze soddisfatte e ownership disgiunta, assegnati a worker.
- **Copie isolate**: opzionali; un subagent non crea automaticamente un worktree o un ambiente.

La chat principale è l'orchestratore. Durante `esegui`, dopo l'approvazione del piano, usa subagent
nativi per i package indipendenti e un tester separato. Questa è un'istruzione di delega per
l'esecuzione dello sviluppo, non per la sola raccolta o per la manutenzione di questa skill.
Se il grafo è seriale, raccomanda uno sviluppo seriale e non creare agenti per simulare parallelismo.

## Fasi

| Fase | Riferimento | Risultato e passaggio successivo |
| --- | --- | --- |
| `raccogli` | [contesto.md](references/contesto.md) | `CONTESTO.md`: strumenti, workflow, capacità e autorizzazioni; risolvi i blocchi materiali |
| `analizza` | [analisi.md](references/analisi.md) | `ANALYSIS.md`, `CONTRATTO.md`; approvazione esplicita dell'utente |
| `pianifica` | [artifact.md](references/artifact.md) e [protocollo.md](references/protocollo.md) | `PLAN.md`, `piani/*`, `AVVIO.md`; approvazione esplicita prima dell'esecuzione |
| `esegui` | [protocollo.md](references/protocollo.md) | delega, ondate, esiti e checkpoint, rispettando le capacità e le autorizzazioni |
| `riprendi` | protocollo, sezione Ripresa | ricostruisci lo stato e riparti dal primo passo senza evidenza |
| `chiudi` | protocollo, sezione Chiusura | copertura requisiti, documentazione, gap e consegna finale |

La ricognizione è un prerequisito anche se l'utente parte da `analizza` o `pianifica`.
Non ripeterla quando `CONTESTO.md` è aggiornato. Usa gli artifact per mantenere la continuità oltre
compattazioni o nuove chat; non promettere la sopravvivenza dei subagent. Le approvazioni già valide
non si richiedono nuovamente.

## Regole operative

- Segui `AGENTS.md`, le altre istruzioni applicabili e le autorizzazioni dell'utente. Rispetta le
  eventuali regole locali di piano e approvazione anche per la creazione degli artifact.
- Usa solo strumenti realmente esposti. Rileva capacità e schema dei tool nel contesto corrente;
  non inventare flag CLI per ruoli, API di messaggistica o file di configurazione necessari.
- I subagent ereditano sandbox e approvazioni del parent. I limiti di ruolo nel prompt e l'ownership
  non creano isolamento tecnico: non dichiarare permessi separati o accessi in sola lettura.
- La matrice di `CONTESTO.md` stabilisce chi esegue i comandi. Senza delega valida Git,
  installazioni, test, lint, build, avvii e azioni esterne restano all'utente.
- Ogni file ha un solo scrittore: codice al worker, test ed esiti al tester, stato all'orchestratore.
  I subagent non delegano a loro volta; la concorrenza resta sotto il controllo dell'orchestratore.
- Scelte materiali non previste diventano `BLOCCATO`; i dettagli compatibili col piano si dichiarano
  nel `FINITO`. Un risultato del subagent non è un'approvazione dell'utente.
- Dall'OK alla consegna di un checkpoint, congela la copia principale. Il lavoro successivo usa
  solo copie isolate autorizzate, oppure attende.
- Non creare chat utente o mandare messaggi ad altre chat per sostituire i subagent. Se i tool
  necessari mancano, segnala il limite e concorda una modalità supportata, senza cambiare settings.
- Niente segreti negli artifact o nei messaggi. Segnala problemi fuori scope senza correggerli.

## Risorse e limiti

I modelli in [templates/](templates/) sono sorgenti da compilare. Consulta soltanto i riferimenti
pertinenti alla fase. Il protocollo contiene le fonti ufficiali dell'adattamento; non applicare
`ListAgents`, `SendMessage` o flag di Claude Code a questa versione.

Questa versione richiede un primo collaudo reale delle capacità raccolte. Non dichiara eseguiti
test, integrazioni o rilasci solo perché il piano ne descrive i passi.
