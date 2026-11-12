# ReteLab

Laboratorio in italiano per una quinta di telecomunicazioni: ipotesi sul carico, IPv4, CIDR, VLSM e VLAN. Frontend statico HTML/CSS/JavaScript senza framework, build o dipendenze esterne. Hosting previsto: GitHub Pages. Raccolta verifiche: Supabase (piano Free).

Layout aggiornato il 12 novembre 2026: tema blu notte con accenti ciano e lime, schema di rete SVG animato, navigazione con pagina corrente evidenziata, schede attività e moduli adattati a desktop e smartphone. `modern.css` definisce il nuovo tema sopra gli stili di base; `ui.js` contiene la home e la navigazione. Motore didattico e database conservano la versione precedente. Le animazioni rispettano la preferenza di riduzione del movimento.

## Stato della consegna

Il laboratorio è configurato per il progetto Supabase ReteLab nella regione UE Francoforte. `config.js` contiene solo l’URL API e una chiave pubblica publishable. Schema e permessi sono installati; il test transazionale `tests/backend-smoke.sql` ha verificato sul server RLS, accesso docente, PIN, ripresa, bozze, scadenza, immutabilità della consegna e cancellazione. Tutti i dati di quel test sono stati annullati con ROLLBACK.

Repository del sito: https://github.com/ProfAlbertoPignatoJCMaxwell/Indirizzamento-IP. Per usare l’area docente serve un utente Supabase Auth abilitato nella tabella teachers, distinto dall’account di gestione Supabase. Le istruzioni di installazione seguenti servono per riprodurre il progetto su un nuovo account; non rieseguire schema.sql sul progetto già installato.

## Aprire il sito

Aprire `index.html` con un browser, oppure, con Node.js installato, eseguire dalla cartella del sito:

```text
node server.cjs
```

Aprire http://127.0.0.1:4173. L’anteprima usa un server locale, non pubblica il sito. In alternativa basta l’apertura diretta del file HTML per l’allenamento. Le verifiche online vanno usate dal sito HTTPS pubblicato.

## Come funziona il laboratorio

- Cinque contesti: albergo, scuola, festival, centro di progettazione e museo. Il codice traccia, il livello e la versione del motore riproducono esattamente la stessa situazione. Nuove missioni cambiano contesto, dimensioni e rete madre; il contesto può ripetersi, i dati vengono rigenerati.
- Tre livelli da 3, 4 o 5 VLAN. Le reti madri sono /20 privati nel blocco 10.0.0.0/8. Non vengono richiesti Internet pubblico, IPv6 o link WAN.
- Prima si sceglie un numero di dispositivi per unità dentro i limiti della situazione. Una stima più prudente è accettata: tutti i calcoli successivi e la soluzione di riferimento si adattano a quella scelta. La motivazione scritta resta una valutazione umana.
- Dispositivi iniziali = unità × ipotesi; riserva = arrotondamento per eccesso della percentuale sui dispositivi iniziali; host necessari = dispositivi + riserva + indirizzi fissi + un gateway.
- Il prefisso deve essere minimo; rete e broadcast non sono host. Il correttore accetta allocazioni alternative nella rete madre e gateway su qualsiasi host valido. Richiede VLAN distinte da 2 a 4094 per convenzione didattica.
- Le scelte VLAN coprono access, trunk 802.1Q, routing inter-VLAN e ACL/firewall. È presente un ripasso con esempio svolto.
- Salvataggio locale automatico; JSON di sicurezza e stampa/PDF dal riepilogo. Non ci sono analitiche, font remoti o tracciamento.
- Ogni parametro e domanda chiusa vale un punto nell’indicatore automatico. I campi di range sono valutati rispetto al blocco inserito, anche per evidenziare competenze parziali. Il punteggio non sostituisce il voto del docente e non valuta la qualità della motivazione.

## Attivare Supabase (una volta)

1. Creare il proprio account su https://supabase.com e un progetto nel piano **Free**, preferibilmente nella regione UE scelta dalla scuola. Conservare la password del database personalmente: non va nel sito.
2. Aprire **SQL Editor**, incollare l’intero file `backend/schema.sql` ed eseguirlo una volta in un progetto nuovo. Se esistono già tabelle con questi nomi, non cancellarle: usare un progetto dedicato o predisporre una migrazione.
3. In **Authentication → Users**, creare l’utente docente con la propria email e password. Copiare il suo UUID. Nel SQL Editor eseguire, sostituendo l’UUID:

```sql
insert into public.teachers(user_id) values ('UUID-REALE-UTENTE-DOCENTE');
```

4. Disabilitare l’iscrizione pubblica se non necessaria. L’abilitazione nella tabella `teachers` è comunque richiesta per creare verifiche.
5. Dalle impostazioni API copiare **Project URL** e la chiave **publishable** (oppure la chiave legacy `anon`, se disponibile). Modificare `config.js`:

```js
window.RETELAB_CONFIG = {
  supabaseUrl: 'https://ID-DEL-PROGETTO.supabase.co',
  supabaseKey: 'CHIAVE-PUBBLICA-PUBLISHABLE-O-ANON'
};
```

Non utilizzare `service_role`, chiavi `sb_secret_…`, JWT docente o password: concederebbero accesso privilegiato dal sito pubblico. La chiave pubblica è prevista nel frontend; l’accesso ai dati è limitato dalle policy e dalle funzioni del database.

Il piano gratuito può mettere in pausa progetti poco attivi: verificare lo stato del progetto il giorno prima della prova e riattivarlo dalla dashboard se necessario. Limiti e condizioni possono cambiare: https://supabase.com/docs/guides/platform/free-project-pausing.

## Pubblicare gratis su GitHub Pages

1. Creare un account su https://github.com. Creare un repository **pubblico** chiamato, per esempio, `rete-lab`. GitHub Pages è disponibile per repository pubblici con GitHub Free.
2. Da **Add file → Upload files**, caricare il contenuto di questa cartella **direttamente nella radice del repository**: `index.html`, `style.css`, `modern.css`, `ui.js`, `engine.js`, `app.js`, `config.js` e `.nojekyll`. Per pubblicare basta questo gruppo di file. Si possono caricare anche README, test, server e schema SQL perché non contengono dati o segreti.
3. Non caricare la cartella `Materiali`, dati reali degli studenti, esportazioni delle verifiche, PIN, password o file con credenziali.
4. In **Settings → Pages**, scegliere **Deploy from a branch**, branch `main`, cartella `/ (root)`, poi **Save**.
5. Attendere il deploy. L’URL sarà `https://NOME-UTENTE.github.io/rete-lab/`. Non è un URL già attivato: dipende dal proprio account.
6. Aprire il sito, verificare allenamento e ripasso, poi eseguire il collaudo seguente. Tutti i percorsi degli asset sono relativi, quindi funziona anche sotto il nome del repository.

Istruzioni ufficiali: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site.

## Gestire una verifica di due ore

1. Aprire **Area docente**, accedere con l’utente abilitato.
2. Scegliere livello, titolo e durata (predefinita 120 minuti). Il codice traccia determina l’esercizio, mentre il codice verifica serve per accedere alla sessione.
3. Inserire il roster: `identificativo,PIN`, una riga per studente. Usare identificativi assegnati in aula (es. `5A-07`) e PIN casuali distinti di almeno 6 caratteri. Conservare l’associazione con i nomi fuori dal sito. Non usare dati reali nel repository.
4. Creare la prova, controllare traccia e soluzione docente, distribuire il codice verifica e ogni PIN solo al rispettivo studente.
5. Premere **Avvia per tutta la classe**: il server fissa la scadenza comune. L’ingresso tardivo non sposta la scadenza. Chiudere blocca accessi e salvataggi; riaprire mantiene la scadenza originale. Per una nuova prova creare una nuova sessione.
6. Gli studenti entrano da **Verifica**. Le risposte vengono salvate localmente a ogni modifica e sul server ogni 30 secondi mentre la pagina della verifica è attiva. L’area di allenamento rimane pubblica: il controllo degli strumenti ammessi si svolge in aula.
7. **Consegna definitivamente** blocca il tentativo sul server. Se la pagina è aperta allo scadere, tenta la conferma automatica dell’ultima bozza sul server. Il browser non può garantire l’esecuzione quando è chiuso o sospeso; il docente vede comunque l’ultima bozza come non consegnata.
8. Dopo la scadenza il server non accetta nuovi contenuti, nemmeno manipolando l’orologio locale. Può confermare solo la bozza precedente. Se manca rete, conservare anche la copia JSON e gestire l’eccezione con il docente: non c’è importazione automatica delle copie locali nel registro.
9. Da **Risposte e risultati** leggere le consegne, aprire i parametri, valutare il testo delle ipotesi, esportare JSON completo o CSV riepilogativo. I punteggi sono ricalcolati dal browser del docente sui dati salvati; nessun voto dichiarato dallo studente viene accettato come affidabile.
10. Terminato il periodo di conservazione deciso dalla scuola, **Elimina dati** cancella la sessione, roster e tentativi. Prima esportare gli elementi da conservare.

Per cambiare studente su un PC condiviso premere **Esci dalla sessione su questo dispositivo** dopo aver verificato la consegna, oppure usare un profilo browser separato. Il token del tentativo rimane nel browser fino all’uscita per permettere il recupero. L’accesso docente vive solo in memoria e scade secondo Supabase; in caso di scadenza aggiornare la pagina e accedere nuovamente.

## Collaudo obbligatorio prima dell’uso in aula

Il test automatico `tests/backend-smoke.sql` copre sul database il flusso e i controlli di accesso con dati temporanei. Prima dell’uso in aula completare anche queste prove tramite il sito pubblicato, usando identificativi fittizi:

1. Creare due docenti di test, abilitare almeno il primo e verificare che un utente non abilitato non possa creare una prova.
2. Creare una prova con 2 studenti fittizi. Verificare codice e PIN errati, ingresso a prova chiusa, apertura con scadenza comune e traccia uguale sui due browser.
3. Controllare che le richieste anonime a `exams`, `roster`, `teachers` e `attempts` non leggano righe; il secondo docente non deve leggere la prova del primo. Il ruolo authenticated non può leggere la colonna `token` né `pin_hash`.
4. Compilare, attendere il salvataggio, riaprire con lo stesso codice/PIN in un altro browser: deve riprendere lo stesso tentativo. Il docente deve vedere la bozza.
5. Consegnare e tentare un secondo salvataggio: le risposte non devono cambiare. Ripetere con rete assente e verificare messaggio d’errore e copia locale.
6. Per testare la scadenza senza aspettare, impostare dal SQL Editor la scadenza di una prova fittizia nel passato. Nuovi contenuti devono essere respinti; la conferma tardiva deve conservare solo la bozza server.
7. Provare chiusura, riapertura, esportazioni e cancellazione di una verifica fittizia. Controllare che non rimangano roster o tentativi della prova eliminata.

Le funzioni `security definer` hanno `search_path` vuoto e permessi espliciti; RLS limita le letture al proprietario. Non è previsto un sistema anti-bot o di rate limiting applicativo. Usare PIN casuali e non divulgare codice e PIN fuori dalla classe. Non presentare questa piattaforma come ambiente per esami ad alta garanzia: la logica didattica è pubblica e gli identificativi con PIN non verificano l’identità fisica.

## Verifiche tecniche locali

```text
node --check app.js
node --test tests/engine.test.cjs
```

Il test include 900 scenari riproducibili e casi su ipotesi alternative, blocchi minimi, maschere, sovrapposizioni, VLAN duplicate, gateway e dati incompleti. Per i controlli del database fare riferimento al collaudo sopra e alla documentazione ufficiale Supabase su RLS e database functions.

Non cambiare il motore durante una verifica aperta: codice traccia, versione e livello devono continuare a produrre gli stessi requisiti anche quando il docente ricalcola i risultati. Per una futura versione aggiungere una migrazione e conservare il motore precedente per le verifiche archiviate.
