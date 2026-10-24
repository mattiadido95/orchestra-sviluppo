# Orchestra sviluppo

[Italiano](README.md) | [English](README.en.md)

Skill per pianificare e coordinare sviluppi complessi con package indipendenti, ownership dei file,
un tester separato e checkpoint verificabili. Il repository contiene due versioni autonome per
**Claude Code** e **Codex**. Si sceglie la versione per il proprio harness, cioè l'ambiente che
fornisce all'agente strumenti, contesto e permessi di esecuzione.

Il workflow parte dalla raccolta dell'ambiente reale: non presuppone linguaggi, framework, sistema
operativo, nomi dei branch, issue tracker o script di progetto. Non richiede librerie aggiuntive.

## Struttura

```text
claude/
  SKILL.md
  references/
    contesto.md
    analisi.md
    artifact.md
    protocollo.md
  templates/
    CONTESTO.md
    PLAN.md
    piano.md
    AVVIO.md
    esito.md
codex/
  SKILL.md
  agents/openai.yaml
  references/
  templates/
```

Le due cartelle contengono ciascuna tutti i propri riferimenti e template. Non servono collegamenti
alla versione dell'altro harness dopo l'installazione. `codex/agents/openai.yaml` conserva
l'invocazione esplicita della skill; Claude Code usa `disable-model-invocation: true`.

## Differenze tra harness

| Aspetto | Claude Code | Codex |
| --- | --- | --- |
| Invocazione | `/orchestra-sviluppo <fase> <slug>` | `$orchestra-sviluppo <fase> <slug>` |
| Orchestratore | sessione dedicata | chat principale |
| Worker e tester | sessioni separate aperte dall'utente | subagent nativi creati dall'orchestratore |
| Coordinamento | `ListAgents` e `SendMessage`, se disponibili | capacità native di delega, messaggistica, attesa e follow-up effettivamente esposte |
| Permessi | configurazione di ogni sessione e flag supportati | sandbox e approvazioni ereditati dal parent |
| Ripresa | sessioni recuperabili con il meccanismo di resume | agenti recuperabili se supportato; altrimenti ricostruzione dagli artifact |

L'ownership è una regola di coordinamento, non una barriera tecnica sul filesystem. In particolare,
un subagent Codex non ottiene un sandbox diverso perché gli si assegna un ruolo. Nessuna versione
crea automaticamente una copia isolata solo perché crea o usa un collaboratore.

## Installazione manuale

Scegli una sola variante per ciascun harness. Copia **l'intera cartella della variante** nella
destinazione con nome `orchestra-sviluppo`, mantenendo riferimenti e template. Non copiare soltanto
`SKILL.md` e non lasciare un livello intermedio `claude/` o `codex/` nella destinazione.

| Harness | Nel progetto destinatario | Personale, per più progetti |
| --- | --- | --- |
| Claude Code | `<progetto>/.claude/skills/orchestra-sviluppo/` | `~/.claude/skills/orchestra-sviluppo/` |
| Codex | `<progetto>/.agents/skills/orchestra-sviluppo/` | `~/.agents/skills/orchestra-sviluppo/` |

Su Windows `~` indica la home dell'utente; adatta i separatori al sistema. Il risultato deve essere,
per esempio, `<progetto>/.agents/skills/orchestra-sviluppo/SKILL.md` con `references/`, `templates/`
e, per Codex, `agents/` accanto.

Se una destinazione esiste già, confrontala prima di sostituirla e conserva le personalizzazioni.
Evita copie omonime personali e di progetto se non vuoi che compaiano più voci della stessa skill.
Queste istruzioni non installano nulla automaticamente e non modificano configurazioni dell'harness.

Percorsi e meccanismi di caricamento sono descritti nelle fonti ufficiali:
[Claude Code skills](https://code.claude.com/docs/en/skills) e
[Codex skills](https://learn.chatgpt.com/docs/build-skills).

## Uso

Avvia l'harness nel progetto destinatario, con i normali permessi. Per iniziare, scrivi nella chat:

Claude Code:

```text
/orchestra-sviluppo raccogli nome-sviluppo
```

Codex:

```text
$orchestra-sviluppo raccogli nome-sviluppo
```

I comandi qui sopra sono prompt per l'harness, non comandi della shell. `nome-sviluppo` è lo slug,
un identificativo breve usato per ritrovare gli artifact di quel lavoro.

| Fase | Cosa produce | Passaggio |
| --- | --- | --- |
| `raccogli` | `CONTESTO.md`: strumenti, comandi, ambiente, integrazione e autorizzazioni | chiarisce i blocchi che influenzano il lavoro |
| `analizza` | `ANALYSIS.md`, `CONTRATTO.md`: requisiti, checkpoint e grafo | approvazione esplicita |
| `pianifica` | `PLAN.md`, piani dei package e test, `AVVIO.md` | approvazione esplicita prima dell'esecuzione |
| `esegui` | implementazione per ondate, verifiche ed esiti | decisioni e consegne secondo il piano |
| `riprendi` | ricostruzione dello stato dagli artifact | primo passo ancora senza evidenza |
| `chiudi` | copertura requisiti, documentazione, gap e consegna | azioni finali solo se autorizzate |

Se parti da `analizza` senza contesto, la skill esegue prima la ricognizione necessaria. La raccolta
legge istruzioni, documenti e manifest; non installa tool, non avvia collaboratori e non esegue
comandi solo per provarne la disponibilità. Le informazioni non confermate restano esplicite.

Gli artifact vivono normalmente in `.planning/filoni/<slug>/` nella copia principale. Il percorso
può essere concordato diversamente se il progetto ha già una convenzione. Le approvazioni delle
modifiche ai file seguono sempre le istruzioni locali e le autorizzazioni già ricevute.

## Principi del workflow

- Il parallelismo segue il grafo delle dipendenze: se il lavoro è seriale, la skill lo segnala.
- Ogni file ha un solo scrittore. I worker implementano; il tester possiede test ed esiti;
  l'orchestratore aggiorna lo stato e gestisce le decisioni.
- Il contratto approvato definisce il risultato atteso. Una build verde non prova tutti i requisiti.
- Ogni ondata riceve un esito documentato, anche con soli unit test: `OK`, `KO` o `PARZIALE`.
- I checkpoint offrono un percorso utile da provare; mock temporanei hanno un'uscita pianificata.
- Dall'OK alla consegna la copia principale resta congelata. Il lavoro successivo attende o usa
  copie isolate già autorizzate.
- La disponibilità di un tool non conferisce autorizzazione. Senza delega valida Git, installazioni,
  verifiche, avvii e azioni esterne restano all'utente; i comandi vengono preparati con directory,
  prerequisiti e risultato atteso.
- Non si modificano settings per aggirare limiti e non si registrano credenziali negli artifact.
- Gli artifact si conservano alla chiusura, salvo una scelta esplicita di trasferimento o pulizia.

## Prima prova manuale

Prerequisiti: variante installata e harness disponibile in un progetto di prova con istruzioni
note. Non usare una prova del caricamento per autorizzare modifiche al progetto reale.

1. Apri il progetto destinatario e controlla che la skill compaia nel selettore. Se necessario,
   riavvia l'harness; controlla che il percorso contenga direttamente `SKILL.md`.
2. Invoca `raccogli prova` con una piccola richiesta di sviluppo e chiedi di presentare il contesto
   prima di scriverlo. Deve riconoscere stack e comandi dalle fonti disponibili, indicare ciò che
   manca e rispettare le istruzioni locali senza installazioni o esecuzioni implicite.
3. Dopo la tua approvazione, controlla `CONTESTO.md`: comandi con directory e fonte, strumenti
   effettivamente disponibili, branch non presunti e matrice delle autorizzazioni.
4. Passa ad `analizza prova`. Il risultato deve distinguere requisiti, contratto, checkpoint e
   dipendenze e fermarsi all'approvazione. Un caso interamente seriale non deve produrre agenti.
5. Per un collaudo completo, usa un esempio con due package indipendenti, approva piano ed
   esecuzione e segui `AVVIO.md`. In Claude apri le sessioni previste; in Codex osserva i subagent
   dalla chat principale. Controlla ownership disgiunta, `FINITO`, esito del tester e ripresa.
6. Includi un'ondata con soli unit test e un caso con verifica mancante: la prima deve ricevere un
   esito, il secondo deve restare `PARZIALE`. Nessuna consegna esterna parte dal solo `OK` tecnico.

Il repository contiene istruzioni e template, senza codice eseguibile o una suite di test propria.
La lettura statica non dimostra che il workflow funzioni nella configurazione del tuo harness.
Queste versioni generiche richiedono un primo collaudo reale; non sono dichiarate validate sul campo.

## Manutenzione

Le risorse comuni sono duplicate intenzionalmente per permettere installazioni indipendenti:

- `references/contesto.md`, `references/analisi.md`, `references/artifact.md`;
- `templates/CONTESTO.md`, `templates/PLAN.md`, `templates/piano.md`, `templates/esito.md`.

Aggiorna entrambe le copie quando cambia il metodo comune. `SKILL.md`, `references/protocollo.md`
e `templates/AVVIO.md` sono specifici dell'harness. Per le capacità native usa la documentazione
corrente e i tool realmente esposti:
[Claude Code messaging](https://code.claude.com/docs/en/cross-session-messaging) e
[Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents).

## Integrazione sperimentale Flow Editor + Paseo (solo Codex)

La variante `codex/` supporta una modalità Paseo **opzionale ed esplicita**, distinta dalla delega
nativa. Flow Editor può aprire l'orchestratore dall'apposito nodo; dopo l'approvazione del piano,
questo crea worker e tester come sessioni Codex nello stesso daemon e workspace Paseo.
Il dettaglio è in [codex/references/paseo.md](codex/references/paseo.md).

La modalità richiede Paseo Desktop, la CLI compatibile e Codex già autenticato con ChatGPT sullo
stesso host locale. Nessun tool viene installato automaticamente. Le sessioni condividono i limiti
del piano; i ruoli non creano sandbox separati. Le regole di autorizzazione e gli artifact restano
validi. La prima integrazione usa directory locali già esistenti, senza creare worktree.

La variante Claude resta autonoma. Il riferimento Paseo è parte della cartella `codex/`: copia
sempre la variante completa. L'integrazione richiede collaudo manuale e non è dichiarata validata.
