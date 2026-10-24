# Avvio — <slug> — Claude Code

## Prerequisiti raccolti

- Copia principale: <percorso assoluto>.
- Artifact correnti: <percorso assoluto>.
- Contesto, analisi, contratto e piano approvati: <riferimenti>.
- Claude Code: <versione nota e disponibilità di ListAgents/SendMessage>.
- Sistema operativo e shell: <valori da CONTESTO.md>.
- Copie isolate: <già disponibili o creazione a carico del responsabile registrato>.
- Permessi e comandi: <sezione di CONTESTO.md; eventuali flag verificati>.

> Genera comandi concreti per la shell effettiva. Non eseguire i comandi di questo modello senza
> compilazione. I flag non sostituiscono l'approvazione delle operazioni. Nessun bypass dei permessi.

## Sessioni per ondata

| Ondata | Sessioni necessarie | Directory | Motivo |
| --- | --- | --- | --- |
| <k> | <orchestratore, tester e worker utili> | <path per sessione> | <parallelismo effettivo> |

Ordine: orchestratore, tester, worker. Le sessioni le apre l'utente.

## Comandi da compilare

Per ciascuna sessione indica prima la directory assoluta in cui aprire il terminale. Esempi del
solo avvio, da adattare ai permessi registrati:

```text
claude --name orchestratore-<slug>
claude --name tester-<slug>
claude --name worker-<n>-<slug> --disallowedTools "Bash" "PowerShell"
```

Il tester e l'orchestratore possono avere tool tecnicamente disponibili che non sono autorizzati
a usare: riportane esplicitamente i limiti nei prompt e genera eventuali flag supportati.
Per un worker isolato aggiungi `--add-dir "<percorso-assoluto-artifact>"` se necessario e approvato:
questo accesso non è di sola lettura; il divieto di scrivere gli artifact resta una regola di ruolo.
Non aggiungere strumenti o ampliare directory per aggirare un blocco.

## Primo messaggio dell'orchestratore

```text
/orchestra-sviluppo esegui <slug>
```

## Primo messaggio del tester

```text
Sei tester-<slug>. Leggi CONTESTO.md, PLAN.md, CONTRATTO.md e piani/test.md da
<percorso assoluto degli artifact>. La tua directory è <copia principale>.
Scrivi soltanto test ed esiti posseduti. Non cambiare codice applicativo, piani o stato.
Non delegare il ruolo. Rispetta le autorizzazioni di CONTESTO.md anche se il tool è disponibile.
Manda DISPONIBILE a orchestratore-<slug> con identità e directory, poi attendi TESTA.
Per unit consegna FINITO; per verifica e checkpoint consegna ESITO con file ed evidenza.
Le credenziali restano fuori da messaggi e artifact.
```

## Primo messaggio del worker

```text
Sei worker-<n>-<slug>. La tua directory è <copia assegnata>.
Gli artifact correnti sono in <percorso assoluto nella copia principale>.
Leggi CONTESTO.md, PLAN.md e le istruzioni applicabili, poi manda DISPONIBILE a
orchestratore-<slug> con identità e directory e attendi ASSEGNATO.
Leggi piano e contratto prima di implementare. Scrivi solo i file posseduti nella copia assegnata.
Non scrivere test o artifact di stato e non delegare il package.
Per scelte materiali, file fuori ownership o permessi mancanti manda BLOCCATO prima di agire.
A conclusione manda FINITO con piano, copia, file, dettagli scelti, verifiche ed eventuali limiti.
Poi resta disponibile per CORREGGI o una nuova assegnazione.
```

## Verifica del canale e ripresa

L'utente può usare `/list-agents`; l'orchestratore verifica i destinatari con `ListAgents` prima
di assegnare. Un invio trattenuto o rifiutato blocca l'assegnazione, non autorizza a cambiare settings.

Per riprendere: stessa directory, stesso nome e flag, usando la modalità di resume supportata.
Nella sessione dell'orchestratore:

```text
/orchestra-sviluppo riprendi <slug>
```

Se una capacità necessaria manca, concorda una modalità diversa prima di procedere.
