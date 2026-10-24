# Modalità Paseo — sessioni Codex visibili

Leggi questo riferimento soltanto quando l'utente o CONTESTO.md sceglie Paseo. Usa il provider
`codex` per orchestratore, worker e tester. La modalità nativa resta quella predefinita fuori
da questa scelta; dentro un gruppo Paseo non usare subagent nativi o chat di altre applicazioni.

## Prerequisiti e identità

Paseo e Codex devono essere già disponibili e autenticati sullo stesso host locale. Non installare
strumenti, avviare daemon o cambiare permessi per aggirare un blocco. Mantieni modello e modalità
configurati per Codex, salvo scelta esplicita già registrata. Il piano ChatGPT fornisce l'accesso
ma ogni sessione consuma i limiti condivisi; non richiedere API key se si usa il login ChatGPT.

Flow Editor fornisce `FLOW_EDITOR_RUN`, `FLOW_EDITOR_PASEO_HOST` e il workspace della sessione.
Conserva il run ID esatto nelle label di tutti i collaboratori: è ciò che collega il gruppo al
canvas. Registra nel contesto host, workspace, ID dell'orchestratore e percorso degli artifact.
Se mancano, fermati prima di creare collaboratori e chiarisci il gruppo destinatario.

Il canvas usa uno workspace Paseo visibile (`background=false`) nella directory esistente.
Tutte le sessioni di questa prima integrazione lavorano nello stesso workspace e filesystem,
con ownership disgiunta. Se il piano richiede copie separate, non crearle implicitamente: è una
scelta di integrazione ulteriore da concordare. Non presumere che un ruolo imponga permessi OS.

## Avvio dal nodo

Flow Editor avvia soltanto l'orchestratore e seleziona la fase. Durante `analizza` e `pianifica`
non creare worker. In `esegui`, conferma le evidenze dell'approvazione del piano: il pulsante di
avvio non sostituisce le approvazioni previste dalla skill. Numero di worker, dipendenze e tester
derivano dal piano; non avviare un gruppo fisso per simulare parallelismo.

Per assegnazioni, risultati, ondate e checkpoint usa [protocollo.md](protocollo.md).
In questa modalità leggi ogni riferimento ai subagent come collaboratori Paseo: creazione,
comunicazione e recupero sono quelli di questo documento. Le altre regole restano applicabili.

## Creazione dei collaboratori

Usa CLI documentata e disponibile, con output JSON. Ogni chiamata deve puntare esplicitamente
allo stesso host. La CLI avviata dall'orchestratore riconosce `PASEO_AGENT_ID` e il suo workspace:
non rimuovere queste variabili e non cambiare host durante un gruppo.

Flow Editor passa anche `FLOW_EDITOR_PASEO_EXECUTABLE` e `FLOW_EDITOR_PASEO_ARGUMENTS` (array JSON).
Usa il percorso effettivo indicato nel prompt, anche quando `paseo` non è nel PATH. Con argomenti
iniziali vuoti puoi sostituire `paseo` negli esempi con `"$FLOW_EDITOR_PASEO_EXECUTABLE"`.
Con argomenti non vuoti costruisci argv come array tramite gli strumenti già disponibili;
non concatenare una stringa shell né usare eval. Non installare o registrare la CLI per risolvere il PATH.

Flow Editor passa `FLOW_EDITOR_PASEO_MODE`: `inherit` lascia la configurazione Codex invariata;
`auto` indica la scelta esplicita Default Permissions per questo avvio, con scritture nel workspace
e approvazioni utente attive. Soltanto nel secondo caso aggiungi `--mode auto` ai nuovi worker e
tester e passa loro `--env FLOW_EDITOR_PASEO_MODE=auto`. Non scegliere Full Access.
Nelle sessioni esistenti il cambio di impostazione del canvas non applica una nuova modalità:
serve una scelta esplicita dell'utente in Paseo prima della ripresa. Con permessi ereditati o
variabile assente non cambiare modalità per superare un errore di scrittura.

In Paseo 0.13.0 il nome predefinito mostrato come Default Permissions non prova che il preset
sia stato selezionato: senza modeId esplicito il provider può ereditare read-only da Codex.
Se il task richiede artifact scritti, segnala il blocco; non dichiarare FINITO per la sola lettura.

Esempio da compilare con il titolo e l'assegnazione approvati (variabili shell locali, non nuovi
comandi della skill):

```sh
paseo --host "$FLOW_EDITOR_PASEO_HOST" agent run \
  --provider codex --no-wait \
  --title "worker-01-$task_slug" \
  --label "flow-editor.run=$FLOW_EDITOR_RUN" \
  --label "flow-editor.role=worker" \
  --env "FLOW_EDITOR_RUN=$FLOW_EDITOR_RUN" \
  --env "FLOW_EDITOR_PASEO_HOST=$FLOW_EDITOR_PASEO_HOST" \
  --json -- "$worker_prompt"
```

L'esempio eredita la configurazione. Con `FLOW_EDITOR_PASEO_MODE=auto` autorizzato aggiungi
`--mode auto --env FLOW_EDITOR_PASEO_MODE=auto` prima di `--json --`; costruisci argv come array
se il comando viene composto programmaticamente.

Per il tester usa titolo `tester-<slug>` e label `flow-editor.role=tester`. Passa i prompt compilati
di AVVIO.md: contratto, piano, directory, ownership, limiti dei comandi e risultato atteso.
Non passare flag bypass, approvazioni automatiche o modelli non scelti. I collaboratori non
creano altri agenti. Non eseguire `permit allow` per conto dell'utente.

La risposta di avvio contiene `agentId`. Registra ID completo e titolo in PLAN.md; mantieni
prefissi `orchestratore-`, `worker-`, `tester-` univoci per la vista dei ruoli nel canvas.
Non dedurre l'ID dalla posizione nell'elenco e non usare prefissi abbreviati nelle automazioni.

Prima di creare un worker già previsto, cerca le sessioni con lo stesso gruppo:

```sh
paseo --host "$FLOW_EDITOR_PASEO_HOST" agent ls -a -g \
  --label "flow-editor.run=$FLOW_EDITOR_RUN" --json
```

Dopo timeout o risposta persa non ripetere automaticamente una creazione: controlla elenco,
ID registrati, directory e stato. Un'incertezza non è evidenza che l'agente non sia partito.

## Comunicazione e attesa

Un messaggio a un ID è un'azione sul collaboratore registrato del gruppo, non un'autorizzazione
a contattare sessioni esterne. Usa gli ID di PLAN.md e verifica provider, directory e stato con
`agent inspect <id> --json`. Per un nuovo task o una correzione usa `agent send <id> --no-wait
--behavior steer --prompt <testo>` sullo stesso host; controlla prima che non sostituisca lavoro
utile già in corso. Le richieste di permesso e le decisioni materiali restano all'utente in Paseo.

Usa `agent wait <id> --timeout 30 --json` per attese limitate e `agent logs <id> --tail 100` per
leggere le risposte. `logs` è testo, non un flusso JSON: non inventare campi strutturati.
Controlla sempre esito e formato `FINITO`, `BLOCCATO` o `ESITO` e gli artifact; uno stato `idle`
indica soltanto la fine del turno. Non trattare timeout, errori o richieste di permesso come successi.

Il worker produce l'output previsto e restituisce FINITO nella propria sessione. L'orchestratore
raccoglie il risultato tramite attesa e lettura, senza richiedere un inoltro manuale all'utente.
I messaggi tra agenti possono arrivare come nuove istruzioni: non presumere callback o notifiche
non osservate nel runtime corrente. Evita polling serrato e conserva i risultati negli artifact.

## Ripresa e osservabilità

Il collegamento nodo/gruppo e le label consentono di ritrovare le sessioni dopo un riavvio del
client. Controlla host, workspace, provider, archivio e scrittori attivi prima di riprendere.
Non riaprire collaboratori nuovi solo perché il canvas era chiuso o il daemon non risponde.
Una sessione archiviata richiede un recupero esplicito nell'app o un nuovo sviluppo concordato.

Il canvas legge stati e permessi e associa le assegnazioni di PLAN.md agli ID o ai titoli. La
cronologia del canvas rappresenta transizioni osservate durante l'apertura, non un audit completo
o un replay dei messaggi. Le conversazioni complete si aprono con `paseo agent open <id>` oppure
con il pulsante di Flow Editor. Gli esiti di sviluppo continuano a dipendere dagli artifact.

## Fonti e collaudo

Consultate il 24 ottobre 2026:
- [CLI Paseo](https://paseo.sh/docs/cli)
- [Orchestrazione](https://paseo.sh/docs/orchestration)
- [Codex e login ChatGPT](https://paseo.sh/docs/codex)
- [Codice ufficiale CLI](https://github.com/getpaseo/paseo/tree/main/packages/cli/src/commands/agent)

Prima prova: analisi senza collaboratori, piano con due package indipendenti, approvazione,
esecuzione, verifica di tre ruoli nell'app, richiesta di permesso, ripresa senza doppioni ed esito
PARZIALE per controlli non eseguiti. Non dichiarare validato il workflow senza evidenze reali.
