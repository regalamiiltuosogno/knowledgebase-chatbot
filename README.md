# Pubblicazione automatica della Knowledge Base del chatbot

Documentazione del processo che porta i contenuti preparati dal team su un sito pubblico, da cui il chatbot interno legge la sua Knowledge Base.

Ultimo aggiornamento: 30 ottobre 2026

---

## 1. Obiettivo

Chi prepara i contenuti (oggi Francesca) deve limitarsi a spostare un file dal proprio Drive nella cartella condivisa "WebPages". Tutto il resto avviene in automatico: il file viene pubblicato su un sito con URL stabile e il chatbot lo legge da lì.

Nessun client FTP, nessuna copia manuale, nessun passaggio tecnico per chi scrive i contenuti. L'unico gesto manuale è lo spostamento del file nella cartella, che è anche il punto di controllo prima della pubblicazione.

---

## 2. Come funziona (in sintesi)

```
Gemini genera il contenuto (HTML o Google Doc)
        |
        v
Il file viene salvato nel Drive personale di Francesca ("Il mio Drive")
        |
        v
Francesca controlla il contenuto e lo SPOSTA a mano
nella cartella condivisa "WebPages" (unico passaggio manuale)
        |
        v   (ogni 15 minuti)
Script Google Apps Script "Pubblica KB"
  - legge i file nuovi o modificati
  - li invia al repository GitHub tramite API
  - rigenera indice (index.html) e sitemap.xml
        |
        v   (1-2 minuti)
GitHub Pages pubblica il sito
        |
        v   (una volta al giorno, oppure subito se si forza la sincronizzazione)
Botmama rilegge gli URL inseriti in Topics
        |
        v
Il chatbot risponde con la documentazione aggiornata
```

Tempi: dal momento in cui il file entra nella cartella alla pubblicazione sul sito passano circa 15-20 minuti. Dalla pubblicazione all'aggiornamento del chatbot passano al massimo circa 24 ore (rilettura giornaliera di Botmama), oppure pochi minuti se si forza la sincronizzazione (sezione 5, "Correzione urgente").

---

## 3. Componenti e indirizzi

| Componente | Dettaglio |
|---|---|
| Cartella Drive | "WebPages" (ID cartella: `13cZckTG5azpMYN4qFL7GgupX_MQDhC26`), nel Drive dell'account aziendale e condivisa con Francesca come Editor. È la cartella letta dallo script |
| Script | Google Apps Script, progetto "Pubblica KB", creato con l'account aziendale |
| Trigger | Uno solo, su `publishKB`, basato sul tempo, ogni 15 minuti |
| Repository GitHub | `github.com/regalamiiltuosogno/knowledgebase-chatbot` (pubblico) |
| Sito pubblicato | `https://regalamiiltuosogno.github.io/knowledgebase-chatbot/` |
| Sitemap | `https://regalamiiltuosogno.github.io/knowledgebase-chatbot/sitemap.xml` |
| Pagine | `https://regalamiiltuosogno.github.io/knowledgebase-chatbot/pages/<nome-file>.html` |

**Chatbot (Botmama):** in **Botmama, Topics** sono inseriti gli **URL delle singole pagine** (`.../pages/<nome-file>.html`), non l'indice né la sitemap. Questo significa che ogni pagina nuova va aggiunta a mano in Topics (vedi sezione 5).

---

## 4. Credenziali e dove si trovano

Le credenziali non sono nel codice. Sono nelle **Proprietà script** del progetto (Impostazioni progetto, in fondo alla pagina):

| Proprietà | Contenuto |
|---|---|
| `FOLDER_ID` | ID della cartella Drive condivisa "WebPages" |
| `GH_TOKEN` | Token GitHub fine-grained, permesso **Contents: Read and write** solo sul repository `knowledgebase-chatbot` |

Il token non va mai incollato in chat, email o documenti, né mostrato negli screenshot.

---

## 5. Istruzioni per chi pubblica i contenuti

### Cosa fare

1. Generare il contenuto con Gemini.
2. Salvarlo come file in formato `.html` (oppure come Google Doc) nel **proprio Drive** ("Il mio Drive").
3. Dare al file un **nome chiaro e unico**, in minuscolo, senza spazi (esempio: `recupero-password.html`).
4. Rileggere il contenuto: da qui in poi diventa pubblico.
5. **Spostare il file nella cartella condivisa "WebPages"** (trascinandolo, oppure tasto destro, Organizza, Sposta). È l'unico passaggio manuale e coincide con la decisione di pubblicare.
6. Attendere circa 15-20 minuti e controllare che la pagina compaia nell'indice del sito.
7. **Solo per pagine nuove:** aggiungere l'URL della pagina (`https://regalamiiltuosogno.github.io/knowledgebase-chatbot/pages/<nome-file>.html`) in **Botmama, Topics**. Le pagine già inserite e poi modificate non richiedono questo passaggio, perché l'URL non cambia.

Finché il file resta nel Drive personale di Francesca non viene pubblicato. Per aggiornare una pagina già pubblicata basta sostituire o modificare il file nella cartella "WebPages": viene ripubblicato al giro successivo.

Nota: il file va **spostato**, non copiato né collegato. Un collegamento (Maiusc+Z) a un file `.html` può non essere visto dallo script; per i Google Doc il collegamento funziona, ma lo spostamento resta la scelta più semplice.

### Correzione urgente (sincronizzazione forzata di Botmama)

Di norma Botmama rilegge gli URL una volta al giorno. Se un contenuto sbagliato va corretto subito:

1. Correggere il file e spostarlo (o sostituirlo) in "WebPages".
2. Nell'editor dello script eseguire a mano `publishKB`, per non aspettare i 15 minuti del trigger.
3. Dopo 1-2 minuti aprire la pagina sul sito (Ctrl+F5) e verificare che mostri il testo corretto.
4. **Solo dopo**, in **Botmama, Topics**, forzare la sincronizzazione. Se si forza prima che la pagina sia aggiornata, Botmama rilegge la versione vecchia.
5. Fare al chatbot la domanda interessata e verificare la risposta.

### Regole

- **Nome file unico.** Il nome del file diventa l'indirizzo della pagina. Due file con lo stesso nome si sovrascrivono.
- **HTML semplice.** Per il chatbot funziona meglio un HTML con titoli, paragrafi ed elenchi, senza CSS elaborati, JavaScript o link esterni. Gli `.html` vengono pubblicati senza filtri, esattamente come sono.
- **Tag `<title>`.** Il titolo mostrato nell'indice viene preso dal tag `<title>` dell'HTML; se manca si usa il nome del file.
- **Solo contenuti pubblici.** Tutto ciò che viene spostato nella cartella "WebPages" diventa visibile a chiunque su internet. Mai inserire dati riservati, di clienti, credenziali o prezzi interni.
- **Google Doc.** Sono supportati: vengono convertiti usando titoli, paragrafi, elenchi e tabelle. Immagini e link non vengono portati sul sito. Usare gli stili Titolo/Titolo 1/Titolo 2 per le intestazioni.
- **Controllo prima di pubblicare.** Il contenuto generato da un LLM può contenere errori: va riletto prima di spostarlo in "WebPages", perché una volta nella cartella finisce online e il chatbot lo userà per rispondere.

---

## 6. Rimozione di una pagina

La rimozione **non è automatica**: eliminare un file da Drive non lo toglie dal sito.

Procedura:

1. Eliminare (o spostare fuori) il file dalla cartella "WebPages", altrimenti una sua modifica lo ripubblica.
2. Su GitHub aprire il repository, cartella `pages/`, cliccare il file.
3. Tre puntini in alto a destra, **Delete file**, poi **Commit changes**.
   Scorciatoia: aprire `https://github.com/regalamiiltuosogno/knowledgebase-chatbot/delete/main/pages/NOMEFILE.html`
4. La pagina sparisce dal sito in 1-2 minuti.
5. Togliere l'URL della pagina da **Botmama, Topics** e, se serve che la risposta cambi subito, forzare la sincronizzazione. Finché l'URL resta in Topics, il chatbot può continuare a usare l'ultima versione letta.

Nota: la voce nell'indice e nella sitemap resta (il link porta a un 404) finché non si rimuove anche la proprietà `doc_...` corrispondente dalle Proprietà script e non si esegue la funzione `rigeneraIndice`. Se le rimozioni diventano frequenti, conviene aggiungere allo script la rimozione automatica.

---

## 7. Manutenzione periodica

| Attività | Quando | Come |
|---|---|---|
| Rinnovo del token GitHub | Entro 1 anno dalla creazione (creato a fine ottobre 2026: scadenza circa ottobre 2027). Mettere un promemoria in agenda. | GitHub, Settings, Developer settings, Fine-grained tokens, rigenerare; poi aggiornare `GH_TOKEN` nelle Proprietà script |
| Controllo esecuzioni | Ogni tanto, o se le pagine non compaiono | Apps Script, menu **Esecuzioni**: cercare stati "Non riuscito" |
| Verifica trigger | Dopo ogni modifica allo script | Apps Script, **Attivatori**: deve esserci un solo trigger su `publishKB` |

Importante:

- Lo script deve restare nell'account aziendale, non in quello di una persona.
- Non rieseguire `createTrigger`: crea un secondo trigger e causa conflitti nella scrittura su GitHub.
- Se si cambia `OWNER` o `SITE` nello script (per esempio per un nuovo nome account), eseguire una volta `rigeneraIndice` per correggere indice e sitemap.

---

## 8. Funzioni dello script

| Funzione | Scopo |
|---|---|
| `publishKB` | Funzione principale, lanciata dal trigger ogni 15 minuti. Pubblica Google Doc e file `.html` nuovi o modificati |
| `rigeneraIndice` | Riscrive indice e sitemap anche senza file nuovi (utile dopo cambi di indirizzo) |
| `createTrigger` | Crea il trigger ogni 15 minuti. Da eseguire una sola volta, già fatto |

L'elenco delle pagine pubblicate è tenuto nelle Proprietà script, in voci con prefisso `doc_`: da qui vengono generati indice e sitemap.

---

## 9. Problemi comuni

| Sintomo | Causa probabile | Soluzione |
|---|---|---|
| Errore "Non è stato trovato alcun elemento con l'ID dato" | Lo script gira con un account diverso da quello che possiede la cartella, oppure `FOLDER_ID` è errato | Usare l'account aziendale; controllare `FOLDER_ID` senza spazi |
| `ReferenceError: props_ is not defined` | Nel codice manca una funzione (incolla incompleta) | Sostituire l'intero file con la versione completa dello script |
| Il file non compare sul sito | Formato non gestito (sono supportati solo Google Doc e `.html`), oppure non sono passati 15-20 minuti | Controllare il tipo di file e le Esecuzioni |
| Errore GitHub 401 o 404 | Token scaduto, revocato o non valido dopo un cambio di nome account | Rigenerare il token e aggiornare `GH_TOKEN` |
| Sitemap con vecchi indirizzi | Indice non rigenerato dopo un cambio di `SITE` | Eseguire `rigeneraIndice` |
| Pagina rimossa da GitHub ma ancora nell'indice | Voce `doc_...` ancora presente | Vedi sezione 6 |

---

## 10. Limiti noti e rischi

- **Repository pubblico.** GitHub Pages gratuito richiede un repository pubblico: ogni contenuto è leggibile da chiunque. Per contenuti riservati serve un'altra soluzione.
- **Nessuna revisione automatica.** Il sistema pubblica ciò che trova nella cartella. Il controllo dei contenuti è affidato a chi li mette nella cartella.
- **Rimozione manuale** delle pagine (sezione 6).
- **Nome file uguale, pagina sovrascritta.**
- **Dipendenza da servizi esterni:** Google (Drive e Apps Script) e GitHub (repository e Pages). Costo attuale: zero. Lo storico di tutte le versioni resta su GitHub e permette di recuperare un contenuto sbagliato.
- **Account GitHub personale.** Se in futuro più persone dovranno gestire il repository, valutare il passaggio a un'Organizzazione GitHub (gratuita).
