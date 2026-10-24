# Protocollo — Codex

La modalità predefinita usa subagent nativi. Se è stata selezionata esplicitamente Paseo,
[paseo.md](paseo.md) sostituisce soltanto creazione, trasporto dei messaggi, attesa e recupero:
le regole di piano, ownership, autorizzazioni, ondate ed esiti qui sotto restano applicabili.
In Paseo i permessi sono quelli di ciascuna sessione Codex configurata sul daemon: non assumere
l'eredità dei permessi dei subagent nativi. Non usare contemporaneamente due meccanismi di delega.

## Capacità e ruoli

La chat principale coordina worker e tester usando i tool di delega disponibili nel runtime.
Non apre una chat utente per ogni package. Registra in `CONTESTO.md` capacità di creazione,
comunicazione, attesa, recupero e arresto effettivamente esposte e i relativi limiti.

I nomi dei tool dipendono dal client. Se disponibili, le capacità possono corrispondere a
`spawn_agent`, `send_message`, `followup_task`, `wait_agent`, `list_agents`, `interrupt_agent`.
Usa soltanto nomi e schemi presenti nel contesto corrente: questa lista non prova disponibilità e
non è un'API universale. Una notifica o un elenco non provano che un package sia completato.

| Ruolo | Dove | Scrive |
| --- | --- | --- |
| analista | chat principale, prima dell'esecuzione | contesto, analisi, contratto e piani |
| orchestratore | chat principale | stato, decisioni e assegnazioni |
| worker | subagent nella copia assegnata | file del package |
| tester | subagent separato nella copia principale | test ed esiti |

Durante `pianifica`, compila [AVVIO.md](../templates/AVVIO.md). Dopo l'approvazione, in `esegui`,
l'orchestratore presenta stato e prossimi package, poi crea soltanto i collaboratori utili alle
dipendenze soddisfatte. Considera i limiti di concorrenza effettivi, incluso l'eventuale conteggio
del parent; non assumere un numero fisso. Riusa gli agenti disponibili quando appropriato.

Non assegnare automaticamente modelli o livelli di ragionamento diversi: conserva quelli
ereditati, salvo richiesta dell'utente o istruzioni applicabili.

## Permessi e filesystem

I subagent ereditano sandbox e approvazioni del parent. Un ruolo personalizzato non costituisce
un sandbox distinto. Non creare configurazioni, profili o modalità senza permessi per far partire
il workflow. I limiti di `CONTESTO.md` valgono anche quando un tool sarebbe tecnicamente disponibile.

Per ogni assegnazione passa directory di lavoro e artifact correnti con percorsi assoluti, elenco
dei file posseduti e vincoli sui comandi. Non assumere un filesystem separato o una working directory
indipendente: esplicita la directory in ogni operazione che la supporta. Se non puoi garantire il
perimetro di una copia isolata, non assegnarla e concorda la serializzazione.

Creare un subagent non crea un worktree, non integra il suo lavoro e non autorizza Git. Prima di
assegnare una copia isolata, deve esistere ed essere accessibile secondo le autorizzazioni valide.
L'accesso agli artifact deve permettere la lettura dalla copia principale; non usare copie obsolete.

## Assegnazioni e risposte

La prima richiesta al subagent è già un'assegnazione: non serve un handshake da terminale.
Passa un compito concreto, piano assoluto, contratto, contesto, dipendenze verificate, directory,
ownership e formato del risultato. I subagent non delegano ad altri agenti.

| Tipo | Uso |
| --- | --- |
| `ASSEGNATO` | package e piano, copia, output e limiti |
| `TESTA` | ondata/checkpoint, fase `unit`, `verifica` o `checkpoint`, evidenze disponibili |
| `BLOCCATO` | ostacolo, impatto e decisione richiesta, prima dell'azione non coperta |
| `FINITO` | piano, copia, file modificati, dettagli scelti, verifiche e limiti |
| `ESITO` | `OK`, `KO` o `PARZIALE`, revisione e file scritto dal tester |
| `CORREGGI` | difetto dimostrato, piano, evidenza e confine della correzione |

Queste etichette descrivono il contenuto, non nuovi comandi o tool. L'orchestratore usa i risultati
e le risposte native: non chiedere ai subagent di contattare altre chat tramite plugin.
Le comunicazioni sono limitate ai collaboratori di questo sviluppo; non autorizzano messaggi a terzi.

Solo `FINITO` consegna il package. Un agente può terminare il turno con una domanda o un limite:
leggi il risultato prima di avanzare lo stato. `FINITO` non accetta scelte né sostituisce il tester.
Per correzioni, usa il tool di follow-up che avvia un turno quando disponibile: un semplice messaggio
può non risvegliare un agente inattivo. Attendi con le primitive native, evitando polling serrato.

## Ciclo di un'ondata

1. Presenta package, copie, ownership e prerequisiti. Avvia solo package pronti, con contratto stabile.
2. Il tester può preparare casi in parallelo sui suoi file. Non competere con i worker sugli stessi
   file, lockfile, schema o risorse di esecuzione condivise.
3. Raccogli `FINITO` o `BLOCCATO`. Porta all'utente le scelte materiali prima di incorporarle.
   L'orchestratore non modifica il codice al posto del worker per aggirare un blocco.
4. Integra eventuali copie isolate con il workflow registrato. Se l'operazione spetta all'utente,
   prepara i comandi e attendi l'evidenza. Non includere file estranei e non risolvere conflitti
   automaticamente: riporta i file e concorda il passo successivo.
5. `TESTA <k> unit`: il tester prepara i test pertinenti e restituisce `FINITO` con i file.
6. Richiedi test, lint e build applicabili a chi è autorizzato a eseguirli. Raccogli comando,
   directory, ambiente, revisione e report. Distingui risultati osservati da quelli riferiti.
7. `TESTA <k> verifica`: il tester esamina le evidenze, effettua solo le verifiche delegate e
   scrive sempre `test/esito-ondata-<k>.md` con `ESITO`. **Anche un'ondata con soli unit test passa
   da questa fase**. HTTP, UI e CLI si aggiungono se pertinenti e autorizzati.
8. Con `OK`, aggiorna stato e checkbox e gestisci le copie come concordato. Con `KO`, passa
   `CORREGGI` allo stesso agente se recuperabile, nella stessa copia, poi ripeti integrazione e
   verifiche pertinenti. Con `PARZIALE`, mantieni visibili le verifiche mancanti: l'utente può
   accettare un rischio, ma l'esito non diventa `OK` e le checkbox legate all'OK restano aperte.

## Copie isolate e checkpoint

Registra per ogni copia percorso, revisione base, branch se presente, package e owner. Creazione,
commit, integrazione e rimozione seguono la matrice delle autorizzazioni, senza presumere script o
tool specifici. Mantieni la copia fino all'esito dell'ondata. Se si eseguono processi separati,
isola anche output, porte, servizi e dati come pianificato.

Al raggiungimento di un checkpoint:

1. Valuta l'allineamento con la base da evidenze autorizzate; un'integrazione necessaria precede
   il gate e viene eseguita dal responsabile registrato.
2. `TESTA <n> checkpoint`: percorso completo, regressioni, integrazione e mock attesi sulla copia
   principale; esito in `test/esito-checkpoint-<n>.md`.
3. Registra il riferimento del codice verificato, distinguendo eventuali commit successivi dei
   soli artifact. Modifiche applicative successive richiedono una nuova verifica pertinente.
4. Presenta esito, parti simulate, limiti e prerequisiti. L'OK tecnico non autorizza la consegna:
   attendi il via dell'utente per merge, pubblicazione o rilascio.
5. Usa la destinazione concordata: prova locale, merge, deploy o altro. Migrazioni e notifiche
   necessarie fanno parte del piano, con responsabili e autorizzazioni espliciti.
6. Raccogli la verifica a destinazione e aggiorna esito e tabella. Poi sblocca il lavoro successivo
   nella copia principale; prima, solo copie isolate autorizzate. Tracker solo se presente e delegato.

## Blocchi e ripresa

File fuori ownership, nuove dipendenze, decisioni di prodotto e azioni non delegate producono
`BLOCCATO` prima della parte interessata. Si può proseguire soltanto su parti indipendenti già
autorizzate. L'orchestratore registra le decisioni dell'utente come `D<n>` prima di riassegnare.

In `riprendi`, leggi contesto, stato, decisioni, esiti e registro. Conferma quali agenti esistono
e sono recuperabili usando i tool disponibili: gli ID non sono una garanzia di persistenza.
Non ricreare un worker soltanto per un timeout. Se un agente non è recuperabile, accertati che
non ci siano scrittori ancora attivi prima di assegnarne uno nuovo. Passagli piano e lavoro
esistente, registra la sostituzione e fagli dichiarare cosa ha trovato già fatto.

Per lo stato Git raccogli l'evidenza dall'utente se non delegato. Non ripetere integrazioni o
consegne documentate; riparti dal primo passo senza evidenza. Una nuova chat usa gli artifact,
senza presumere di poter contattare gli agenti della precedente.

## Chiusura

Accerta dalle evidenze che i package siano integrati, il contratto dell'ultimo checkpoint sia
soddisfatto e i mock temporanei siano rimossi. Completa la matrice requisiti; i gap richiedono una
decisione dell'utente per una chiusura ridotta e non vanno presentati come completamento integrale.
Allinea la documentazione con gli strumenti disponibili, senza dipendere da altre skill, e
riporta nel contratto le decisioni approvate con uno scrittore unico.

Accerta che nessun subagent stia ancora scrivendo prima di consegnare. Non interrompere lavoro
utile o eliminare copie, artifact e branch per fare pulizia implicita. Concorda conservazione del
record e azioni finali; issue, push e rilasci richiedono l'autorizzazione pertinente.
Consegna sintesi, file coinvolti, decisioni, verifiche eseguite/non eseguite e passi manuali.

## Fonti dell'adattamento

Consultate il 24 ottobre 2026; capacità e permessi del runtime corrente restano la fonte operativa:

- [Subagent e permessi ereditati](https://learn.chatgpt.com/docs/agent-configuration/subagents).
- [Skill e invocazione](https://learn.chatgpt.com/docs/build-skills).
