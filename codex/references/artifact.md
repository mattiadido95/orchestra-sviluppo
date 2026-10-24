# Artifact e scrittori

Gli artifact vivono nella copia principale, in `.planning/filoni/<slug>/`. Se il repository impone
un percorso diverso, concordalo in `CONTESTO.md` e usa quello in tutti i piani e le assegnazioni.
Ogni versione della skill contiene tutti i riferimenti e i template necessari.

```text
.planning/filoni/<slug>/
  CONTESTO.md
  ANALYSIS.md
  CONTRATTO.md
  PLAN.md
  AVVIO.md
  piani/NN-nome.md
  piani/test.md
  test/esito-ondata-k.md
  test/esito-checkpoint-n.md
```

Le copie isolate leggono questi file dalla copia principale con percorsi assoluti. La copia degli
artifact eventualmente presente nel loro checkout è una fotografia: non va usata come stato corrente.
Se l'harness non consente tale lettura, risolvi l'accesso prima di assegnare, senza ampliare i
permessi autonomamente.

## Ownership

| Artifact | Scrittore | Durante l'esecuzione |
| --- | --- | --- |
| `CONTESTO.md` | analista; poi orchestratore | aggiorna fatti e deleghe effettive, non inventa autorizzazioni |
| `ANALYSIS.md`, `CONTRATTO.md` | analista | modifiche sostanziali solo dopo decisione dell'utente |
| `PLAN.md` | analista; poi orchestratore | stato, decisioni, evidenze e blocchi |
| `AVVIO.md`, `piani/*` | analista | specifica stabile; cambiamenti approvati con uno scrittore designato |
| `test/*` e test automatici | tester designato | evidenze e verifiche, senza modifiche al codice applicativo |
| codice applicativo | worker owner | solo file del package, nella copia assegnata |

Ogni file ha un solo scrittore alla volta. Un passaggio di ownership richiede di fermare lo
scrittore precedente, registrare il nuovo owner e poi riassegnare.

Per cambiare scope, contratto, ownership, ordine delle ondate o checkpoint, l'orchestratore ferma
il lavoro coinvolto e presenta la modifica all'utente. Dopo l'approvazione una sola sessione o un
solo agente aggiorna i documenti interessati, poi l'orchestratore registra e riassegna.
Gli artifact non prevalgono mai sulle istruzioni applicabili o sui limiti dell'harness.

## Contenuto dei file

### `CONTESTO.md`

Dal modello [CONTESTO.md](../templates/CONTESTO.md): fonti, strumenti, ambiente, comandi con directory
e risultato atteso, workflow di integrazione, matrice delle autorizzazioni e informazioni mancanti.

### `ANALYSIS.md`

Obiettivo, perimetro, non-obiettivi, stato attuale con fonti, requisiti `R<n>` con sorgente,
checkpoint, grafo dei package, ownership preliminare, critical path, modalità di lavoro,
limiti del parallelismo, rischi e decisioni aperte.

### `CONTRATTO.md`

Comportamenti verificabili per checkpoint: scenari, input/output, interfacce, regole, errori,
permessi, dati e criteri legati agli `R<n>`. Sezione `Mock` con file, simulazione, checkpoint di
introduzione e sostituzione. Le parti aperte restano esplicite.

### `PLAN.md`

Dal modello [PLAN.md](../templates/PLAN.md). Durante l'esecuzione l'orchestratore aggiorna header
di stato, checkbox, stato dei checkpoint, assegnazioni, copie aperte, decisioni, blocchi e registro
eventi. Può aggiungere evidenze alla matrice dei requisiti e gap dichiarati man mano che emergono.
La pianificazione cambia soltanto con l'approvazione descritta sopra.

### `piani/NN-nome.md` e `piani/test.md`

Dal modello [piano.md](../templates/piano.md), che contiene una variante tester da usare al posto
di quella worker. Ogni piano basta a sé stesso: ruolo, percorsi assoluti, copia, revisione base se
nota, file posseduti, input, contratto, specifica, dipendenze e criteri osservabili.
Il destinatario non deve conoscere la chat in cui il piano è nato.
I piani non sono file di avanzamento: il worker non li aggiorna.

### `AVVIO.md`

Dal modello [AVVIO.md](../templates/AVVIO.md) specifico dell'harness: prerequisiti, capacità
disponibili, assegnazioni, prompt e, se servono, comandi per la shell reale. Deve essere eseguibile
nel contesto raccolto, non un elenco di flag ipotetici.

### `test/esito-*.md`

Dal modello [esito.md](../templates/esito.md): revisione o fotografia del codice verificato,
ambiente, verifiche eseguite, non eseguite e fallite, evidenze, requisiti coperti e rischio residuo.
Distingui esecuzione diretta da risultati riferiti dall'utente. Se il tester non può scrivere il
file, registra `BLOCCATO` e risolvi l'accesso o concorda un nuovo scrittore prima della chiusura.

## Conservazione

Non eliminare automaticamente la cartella alla chiusura. Concorda conservazione o trasferimento
del record in una destinazione disponibile. La pulizia richiede un'autorizzazione specifica e non
fa parte del semplice completamento dei package.
