# Piano <NN> — <nome>

**Ruolo**: worker
**Sviluppo**: <slug>
**Ondata / checkpoint**: <k> / <n>
**Dipende da**: <package e output atteso, oppure nessuno>
**Directory di lavoro**: <percorso assoluto della copia assegnata>
**Modalità**: <principale o isolata; revisione base e branch se applicabili>
**Artifact correnti**: <percorso assoluto nella copia principale>
**Istruzioni applicabili**: <file e ambito>
**Contratto**: <percorso assoluto di CONTRATTO.md e sezioni>

> Leggi CONTESTO.md, PLAN.md e il contratto dalla copia principale, anche se lavori in una copia
> isolata. Non aggiornare i piani o lo stato. Non delegare ad altri agenti questo package.

## Obiettivo

<Comportamento osservabile a fine package.>

## File posseduti

| File nella copia assegnata | Nuovo o modificato | Responsabilità |
| --- | --- | --- |
| <path> | <azione> | <contenuto> |

## File da usare senza modificarli

<Interfacce, componenti e dati da consultare. Tutti i file non posseduti restano fuori perimetro.>

## Specifica

<Input, output, firme, flussi, regole, errori e riferimenti al codice. Per i mock: file dedicato,
checkpoint di introduzione e rimozione. Punti che richiedono documentazione ufficiale.>

## Strumenti e limiti

<Comandi eventualmente delegati da CONTESTO.md, directory e ambiente; altrimenti nessuna esecuzione.
La shell disponibile non cambia le autorizzazioni. Accessi mancanti o file fuori ownership: BLOCCATO.>

## Criteri di completamento

- <Comportamenti attesi collegati agli R<n>.>
- Solo file posseduti modificati nella copia assegnata.
- Scelte di prodotto, contratto, dipendenze o scope non previste: `BLOCCATO` prima della modifica.
- Consegna `FINITO`: piano, copia, file modificati, scelte di dettaglio, verifiche eseguite e non
  eseguite con evidenza, limiti e note per il tester.

## Requisiti e gap

- R<n>: <requisito con sorgente>.
- Gap: <eventuali limiti, oppure nessuno>.

---

<!-- Per piani/test.md usa soltanto la variante seguente, sostituendo quella worker. -->

# Piano test — <slug>

**Ruolo**: tester
**Directory di lavoro**: <percorso assoluto della copia principale>
**Artifact correnti**: <percorso assoluto>
**Istruzioni applicabili**: <file e ambito>
**Contratto di riferimento**: <percorso assoluto e sezioni>

## Principio e ownership

I casi e i risultati attesi derivano dal contratto. Il codice aiuta a costruire il test, non a
giustificare un comportamento difforme. Si verifica la copia principale dopo l'integrazione.

- File di test posseduti: <path>.
- Esiti posseduti: <path assoluto>/test/esito-ondata-<k>.md e esito-checkpoint-<n>.md.
- Mai codice applicativo, piani o stato dell'orchestratore. Non delegare il ruolo ad altri agenti.

## Fasi su assegnazione dell'orchestratore

1. `unit`: prepara i test e i casi pertinenti; manda `FINITO` con file e comandi da eseguire.
   Puoi preparare i casi in parallelo al worker, solo sui tuoi file.
2. `verifica`: dopo l'integrazione raccogli l'evidenza di test, lint e build applicabili. Esegui
   solo ciò che CONTESTO.md delega al tester; per il resto usa risultati forniti dall'utente con
   fonte e revisione. Effettua verifiche HTTP/UI/CLI solo se pertinenti e autorizzate.
   Scrivi l'esito e manda `ESITO`, anche quando l'ondata richiede soltanto unit test.
3. `checkpoint`: verifica il percorso completo, integrazione, regressioni e mock attesi.
   Dopo la consegna aggiungi l'evidenza a destinazione ricevuta dal responsabile.

## Comandi e dati

<Comandi esatti e directory da CONTESTO.md; framework già adottati e report attesi. Dati di prova,
ambiente ammesso e limiti delle operazioni che cambiano dati. Nomi delle variabili necessarie,
mai credenziali. Se manca l'autorizzazione all'esecuzione, prepara i passi per l'utente.>

## Casi per ondata e checkpoint

| Caso | R<n> | Ondata / checkpoint | Scenario | Risultato atteso | Tipo di controllo |
| --- | --- | --- | --- | --- | --- |
| T1 | <requisito> | <destinazione> | <azioni e dati> | <osservabile> | <unit, integrazione, HTTP, UI, CLI, manuale> |

## Esito

`OK` soltanto con evidenza per tutti i controlli richiesti. `KO` per difetti dimostrati;
`PARZIALE` per verifiche mancanti, con causa e rischio residuo. Non trasformare una build verde
nella prova di tutti i requisiti.
