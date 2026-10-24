# Fase `analizza`

Leggi `CONTESTO.md` compilato nello sviluppo, le istruzioni applicabili,
la documentazione funzionale e il codice pertinente. Riusa gli artifact esistenti. Issue e fonti
esterne si consultano solo con gli strumenti e i permessi registrati; non sono prerequisiti impliciti.

Produci `ANALYSIS.md` e `CONTRATTO.md` secondo [artifact.md](artifact.md). Non scrivere codice.

## Requisiti e contratto

Ricostruisci entry point, flussi, dati, componenti condivisi, test e vincoli di integrazione.
Registra le fonti con file e riga o riferimento al requisito. Distingui fatti, inferenze e dati
mancanti; segnala opportunità fuori perimetro senza trasformarle in prerequisiti.

Per ogni requisito `R<n>` indica sorgente, scenario, comportamento atteso, criteri osservabili,
errori e casi limite. Un requisito deriva dalla richiesta o da una fonte approvata, non dal codice
che si pensa di scrivere. Se non è verificabile, resta una decisione aperta.

Prima di separare package che comunicano, fissa il loro contratto: API, interfacce, eventi, formati,
regole di dominio, autorizzazioni e gestione degli errori pertinenti. Worker e tester usano lo stesso
contratto. Un'implementazione non diventa automaticamente la specifica dei test.

## Checkpoint

Un checkpoint è uno stato della copia principale che offre un percorso utile e verificabile.
La destinazione può essere locale o un ambiente concordato: non presumere un branch di release,
un deploy, un database o un frontend se il progetto non li richiede.

Pianifica prima i checkpoint, poi le ondate necessarie. Ordinali per valore del feedback e riduzione
del rischio. Per ciascuno indica:

- percorso completo da provare e destinatario;
- parti reali e simulate, criteri di accettazione e requisiti coperti;
- ondate necessarie, verifiche e prerequisiti;
- migrazioni, compatibilità dei dati e coordinamento esterno, se pertinenti;
- ciò che resta per i checkpoint successivi.

I mock stanno in file dedicati elencati in `CONTRATTO.md` con checkpoint d'ingresso e di uscita.
La loro sostituzione è un package del grafo. L'ultimo checkpoint non contiene mock temporanei.

Il checkpoint è pronto quando i controlli concordati hanno evidenza positiva, il percorso funziona,
le regressioni rilevanti sono coperte e il tester ha scritto `ESITO OK`. Se una build non è
pertinente, motivane l'assenza; se una verifica pertinente non è stata eseguita, l'esito è
`PARZIALE`, non `OK`. L'esito tecnico non autorizza il rilascio.

## Grafo e ownership

Per ogni package candidato: obiettivo, contratto necessario, output, file posseduti, dipendenze,
risorse condivise, checkpoint abilitato, rischio e verifica attesa.

Costruisci un grafo aciclico distinguendo:

1. **Dipendenza tecnica**: un package richiede un output verificato di un altro.
2. **Conflitto di scrittura**: file, schema o stato condiviso richiedono un owner unico o serializzazione.
3. **Capacità**: attività indipendenti rinviate per limiti di agenti, strumenti o disponibilità umana.
4. **Checkpoint**: vincolo sulla consegna di uno stato verificato.

Prepara i file condivisi indispensabili in un passo seriale. Non imporre refactoring solo per creare
parallelismo. Se la topologia rimane seriale o il coordinamento costa più del beneficio, raccomanda
uno sviluppo seriale e non avviare collaboratori.

## Modalità dei package

- **Copia principale (cappello)**: default con ownership disgiunta e nessun conflitto sui runtime.
- **Copia isolata**: worktree o altro meccanismo disponibile, scelto per contenere rischio o isolare
  build, porte e servizi. Non presumere che creare un agente crei una copia separata.

La copia isolata non permette di scrivere in parallelo sugli stessi file. Prima di assegnarla,
conferma creazione, revisione di partenza, accesso agli artifact e modalità di integrazione.
Se non è disponibile, serializza le attività incompatibili.

Dall'`OK` di un checkpoint alla sua consegna, la copia principale resta congelata. Il lavoro
successivo può procedere solo in copie isolate autorizzate; altrimenti attende.

Proponi le ondate dopo il grafo: critical path (catena che determina la durata minima), concorrenza
utile, limiti dell'harness, modalità dei package, preparazione parallela dei test e punti
d'integrazione. Considera anche la capacità dell'utente di fornire build, decisioni e dati.

## Consegna

Presenta checkpoint, grafo, ownership, rischi e decisioni aperte; attendi l'approvazione esplicita
dell'analisi e del contratto. Poi passa a `pianifica`. Il piano non può cambiare silenziosamente
requisiti o checkpoint approvati.
