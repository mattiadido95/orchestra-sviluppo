# Fase `raccogli`

Ricostruisci l'ambiente prima di scegliere comandi, ruoli o modalità di esecuzione. Il risultato è
`.planning/filoni/<slug>/CONTESTO.md`, dal modello [CONTESTO.md](../templates/CONTESTO.md).
Se esiste già, aggiorna solo ciò che è cambiato: non rifare la ricognizione a ogni fase.

## Fonti e limiti

Parti dalla richiesta, dalle istruzioni applicabili del repository e dagli artifact esistenti.
Leggi documentazione, manifest, lockfile, configurazioni e script pertinenti senza eseguirli.
Usa le istruzioni proprie dell'harness, incluse quelle locali ai componenti interessati.
Non richiedere un percorso di documentazione convenzionale se il progetto ne usa un altro.

Distingui per ogni informazione:

- **osservata**: file, configurazione o tool effettivamente disponibile, con riferimento;
- **dichiarata**: informazione ricevuta dall'utente, con fonte;
- **da confermare**: deduzione o dato non ancora disponibile.

La presenza di uno script non prova che funzioni; un eseguibile disponibile non autorizza a usarlo.
Non eseguire Git, controlli di versione, test, lint, build o avvii solo per completare la tabella.
Se le istruzioni non ne consentono l'esecuzione, prepara il comando e raccogli l'esito dall'utente.
Non leggere valori di credenziali: registra soltanto nomi delle variabili e modalità di accesso.

## Cosa raccogliere

1. **Ambiente**: sistema operativo, shell, directory principale, componenti, runtime e versioni
   dichiarate dai file. Specifica ciò che non è stato confermato nell'ambiente reale.
2. **Strumenti di sviluppo**: package manager già adottato; comandi di installazione, test, lint,
   build, avvio e verifica manuale. Per ogni comando: directory, prerequisiti, risultato atteso,
   fonte e chi lo può eseguire. Non installare nulla durante la ricognizione.
3. **Collaborazione**: capacità effettivamente esposte dall'harness per assegnare, comunicare,
   attendere e riprendere; condivisione del filesystem; limite di concorrenza se noto; limiti di
   accesso alle directory. Registra la modalità scelta: nativa oppure Paseo. Per Paseo registra
   host locale, workspace ID e gruppo Flow Editor, CLI effettiva, provider Codex, autenticazione
   tramite ChatGPT e permessi di ogni sessione. Non installare o avviare servizi per scoprirli.
   Usa il protocollo specifico di questa versione della skill.
4. **Integrazione**: branch base e di consegna, strategia di integrazione, pulizia richiesta,
   strumenti per copie isolate e isolamento di porte, output di build, dati e servizi. Senza
   autorizzazione a Git, chiedi all'utente solo le informazioni che non emergono dai documenti.
5. **Consegna**: ambiente o destinatario dei checkpoint, eventuali migrazioni, issue tracker,
   documentazione da mantenere, responsabili e modalità di rilascio. Un tracker o un ambiente
   remoto non sono obbligatori: una prova locale può essere il punto di consegna concordato.
6. **Permessi**: registra la matrice operativa prima di formulare comandi di avvio o assegnazioni.

## Matrice delle autorizzazioni

In assenza di un'autorizzazione esplicita già valida, Git (anche letture), installazioni, test,
lint, build, avvio dell'app, query e azioni esterne restano a carico dell'utente. Gli agenti
preparano i comandi e registrano le evidenze ricevute. La scrittura dei test segue l'ownership;
non implica il permesso di eseguirli.

Una delega può cambiare questo assetto se le istruzioni applicabili lo consentono. Specifica
operazione, ruolo, directory, ambiente, limiti e fonte dell'autorizzazione. Non estenderla a
comandi diversi, ad altri agenti o ad altri ambienti. I limiti tecnici dell'harness restano validi.
Segui anche le regole locali sull'approvazione delle modifiche ai file: non farle riapprovare se
un'autorizzazione valida copre già il lavoro.

## Uscita

Presenta ciò che è disponibile, i blocchi e le sole decisioni che cambiano il piano. Indaga
autonomamente i dettagli leggibili; per le scelte significative fai una domanda alla volta.
Puoi procedere con l'analisi delle parti indipendenti dalle informazioni mancanti.

Non installare strumenti, cambiare settings, creare worktree o avviare collaboratori per scoprire
se il protocollo funziona. Se manca una capacità necessaria, segnala il limite e concorda una
modalità supportata; non simulare coordinamento automatico tramite inoltri manuali.
