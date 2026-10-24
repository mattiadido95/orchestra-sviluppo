# Avvio — <slug> — Codex

## Modalità selezionata

- Runtime: <nativo, oppure Paseo esplicitamente autorizzato>.
- Per Paseo: host locale, workspace ID, `FLOW_EDITOR_RUN`, CLI e autenticazione Codex,
  percorso della skill e autorizzazioni effettive per ciascuna sessione.
- Con Flow Editor: scegli **Paseo · solo Codex**, quindi **Avvia in Paseo** sul nodo.
  L'estensione prepara e apre l'orchestratore; il piano approvato determina worker e tester.
- Per Paseo consulta [il protocollo dedicato](../references/paseo.md) nella skill installata:
  i prompt dei ruoli qui sotto restano validi, ma si inviano a sessioni Paseo.

## Prerequisiti raccolti

- Chat principale: <contesto dello sviluppo>.
- Copia principale: <percorso assoluto>.
- Artifact correnti: <percorso assoluto>.
- Contesto, analisi, contratto e piano approvati: <riferimenti>.
- Capacità di delega del runtime selezionato: <tool e limiti effettivamente esposti>.
- Copie isolate: <disponibili oppure creazione a carico del responsabile registrato>.
- Autorizzazioni e comandi: <sezioni di CONTESTO.md>.

In modalità nativa non servono terminali separati: i subagent vengono creati dall'orchestratore
dopo l'approvazione del piano. In modalità Paseo ogni ruolo ha invece una sessione Codex visibile
nell'app; non aprire manualmente altre sessioni per sostituire quelle già registrate.

## Richiesta nella chat principale

```text
$orchestra-sviluppo esegui <slug>
```

## Collaboratori per ondata

| Ondata | Agente / ruolo | Copia assoluta | Piano assoluto | Dipendenze soddisfatte |
| --- | --- | --- | --- | --- |
| <k> | <worker o tester> | <path> | <path> | <evidenze> |

La capacità utile segue il grafo e il limite corrente; gli agenti non delegano a loro volta.
Registra in PLAN.md gli identificativi effettivi restituiti dall'harness dopo la creazione.

## Assegnazione al worker

```text
ASSEGNATO <NN-nome> dello sviluppo <slug>.
Lavora in <percorso assoluto della copia assegnata>.
Leggi il piano <percorso assoluto>, CONTESTO.md, PLAN.md, CONTRATTO.md e le istruzioni applicabili.
Gli artifact correnti sono in <percorso assoluto della copia principale>; non usare copie obsolete.
Scrivi solo <file posseduti>, nella copia assegnata. Non modificare test, piani o stato.
Rispetta i limiti dei comandi di CONTESTO.md anche se i tool sono disponibili. Non creare subagent.
Per scelte materiali, accessi mancanti o file fuori ownership, restituisci BLOCCATO prima di agire.
Concludi con FINITO: piano, copia, file, dettagli scelti, verifiche ed evidenze, limiti e note test.
```

## Assegnazione al tester

```text
TESTA <ondata o checkpoint> fase <unit, verifica o checkpoint> dello sviluppo <slug>.
Lavora nella copia principale <percorso assoluto>.
Leggi <percorso assoluto>/piani/test.md, CONTESTO.md, PLAN.md e CONTRATTO.md.
Scrivi soltanto <file di test ed esiti posseduti>. Non modificare codice applicativo, piani o stato.
I risultati attesi derivano dal contratto. Non creare subagent.
Esegui soltanto i controlli delegati in CONTESTO.md; per gli altri usa evidenze con fonte esplicita.
Evidenze disponibili: <comandi, directory, revisione e report o risultati dell'utente>.
Per unit restituisci FINITO con i file; per verifica e checkpoint scrivi l'esito e restituisci
ESITO OK, KO o PARZIALE con percorso e rischio residuo. Le credenziali non entrano negli artifact.
```

Il tool di creazione o follow-up riceve un'assegnazione compilata. Usa il suo schema effettivo;
questi prompt non sono chiamate a un'API. Per le correzioni passa evidenza e perimetro allo stesso
agente se disponibile, usando una capacità che ne avvii il turno.

## Attesa e ripresa

Attendi con i tool nativi e leggi i risultati prima di aggiornare lo stato. Non dedurre `FINITO`
dall'inattività. La sola disponibilità di un agente non prova che abbia la directory corretta.

Per riprendere nella chat principale:

```text
$orchestra-sviluppo riprendi <slug>
```

In una chat nuova, fornisci anche il percorso assoluto degli artifact. Recupera gli agenti solo
se l'harness lo consente; altrimenti ricostruisci dal piano e dal lavoro presente, dopo aver
escluso scrittori ancora attivi. Non inventare comandi di resume o permessi separati per ruolo.
