[Osservazioni sui test passati]
# Termini:
1. "PO", "Analyzer", "Agent", o "Analy": Product Analyzer Agent


# Test 1
- Nel primo test, la prima cosa che si nota é che il bot ripete a pappagallo quel che l'utente gli ha comunicato sul suo prodotto; non ne abbiamo bisogno, difatti rovina l'esperienza. 
  - Ripetere lo stretto necessario, tentando di rielaborare quindi, invece di fare da pappagallo.

- La seconda cosa notata é che il PO ripeteva la parola "Boss" ad ogni paragrafo.
  - Mai ripetere "Boss" piú volte a inizio paragrafo nello stesso messaggio

- La terza falla osservata é la ripetizione di ció che il bot stesso ha giá detto.
  - Non ripetere lo stesso concetto, fare riferimento ad esso senza ripetizione delle parole.
  - [Alzare frequency e presence penalty se necessario]
  
# Test 2
- I problemi nel test 2 erano tutti relativi a parte dei cambiamenti apportati dopo il primo test nel system prompt. Dato che, per evitare di avere un'agente che si ripete troppo, gli abbiamo dato istruzione di concludere in fretta, eseguire alcuni passaggi in modo concatenato, passando lui stesso a inferire le keyword della fase 2 subito dopo aver ricevuto descrizione del prodotto, e proponendole insieme anche all'idea che si era fatto dell'industria di appartenenza dell'utente, abbiamo ottenuto l'effetto estremo di quel che volevamo, ossia; l'agente ormai pur di concludere in fretta scriveva non-sense da subito, cercando di correre a svolgere il compto finale ossia formulare la USP, nonostante non avesse idea di cosa dire perché ovviamente non avevamo scavato molto sul prodotto in se per se, e quindi scriveva una USP piena di frasi fatte e vuote, difatti ha detto che l'offerta si basava su "soluzioni complete e personalizzate, focalizzate su efficienza, sicurezza e scalabilità", che é molto generico.

# Test 3
- Qui vediamo come dopo aver istruiuto l'agente su come scavare piú a fondo nel prodotto presentato dall'utente, esso abbia effettivamente rispsto positivamente e fatto diverse domande per approfondire una risposta generica da parte dell'utente.

# Test 4
- Nel test 4 il summarizer ha inferito le keyword perché il suo system prompt era troppo povero di dettagli che contestualizzassero che si trattasse di un agente esterno alla conversazione e che dovesse attendere conferme e trattarle come se lui fosse un notaio che trascrive e non parte dell'interazione. Per questo motivo avendo inferito le keyword durante il primo turno, nel secondo, quando l'Analyzer le ha lette, ha deciso di saltare direttamente alla USP. Di base qua, visto che comunque i due agenti sono in relazione dato che ció che il summarizer salva finisce nel contesto dell'analyzer, questo ha propagato l'errore da uno all'altro, bypassando il bisogno della conferma dell'utente.

# Test 5
Nel test 5 vediamo l'agente arrivare a chiedere di parlare della USP e fare alcune domande neanche troppo generiche riguardo a quali potrebbero essere i punti di forza. 
Il problema di questo test era la scarsa lungimiranza dell'agente, difatti ha chiuso molto in fretta le interazioni, due volte su tre ha usato il tool Save prima di una qualsiasi conferma
  - Attendere approvazione definitiva dei dati prima di salvare

# Test 6
- Il summarizer qui non segnava bene i dati, difatti ha dimenticato di compilare la chiave `products`
  - System prompt corretto

- L'analyzer non distingueva bene tra potenziale target di chi usa il prodotto e target dichiarato del venditore del prodotto. Nei commenti giá ipotizzavo di cambiare modello.
  - Spiegata la differenza nel system prompt e aggiornato il modello successivamente da 03mini a Gemini 3 Flash

# Test 7
- Si osservava come gli agenti erano impazienti di concludere il lavoro, poiché la logica impartitagli non comprendeva ancora una checklist chiara.
  - Corretto system prompt analyzer, aggiunta direttiva di hardlock per processare secondo checklist mentale e non come un normale discorso
  - Fatto presente il concetto di Target del venditore (utente Boss) e target potenziale dell'user (per i casi in cui si tratta di un prodotto che permette di arrivare ad altre persone target o ottenere outcome specifici), per permettere di fare distinzione tra le due cose
  
- Si osservava tool call ingiustificata dell'analyzer verso il tool `Save`

# Test 8 

- Si osservava nuovamente tool call ingiustificata dell'analyzer verso il tool `Save`, anche se vuota, denotava un errore di logica
  - É stata aggiunta direttiva per imporre una sola chiamata al tool alla fine di tutti i passaggi quando si hanno tutti i dati

- Si osservava un'errore di valutazione da parte del summarizer, che difatto segnava il target del venditore come caratteristica del prodotto stesso, il che é fallace, visto che segnava che il prodotto era pensato per il Fintech, quando in realtá gli era solo stato detto che il prodotto veniva venduto preferibilmente a quel settore perché piú reditizio, quindi di per se il prodotto non era limitato ad esso.
  - Aggiunto ragionamento piú preciso nell'analyzer per poter fare si che lui in primis sbagli molto meno di frequente, e non mandi in errore il summarizer

- Si osservava che il summarizer non riusciva a comprendere accuratamente lo scorrere del tempo, sia a causa di una spiegazione non troppo ben dettagliata di ció che aveva davanti, che a causa della scarsa capacitá del modello di distinguere tra l'input nuovo e quello che invece era stato recuperato dal sub-node Simple Memory
  - Rimosso il Simple Memory, aggiunto salvataggio del messaggio corrente dell'Analyzer in database da ridare al ciclo successivo al summarizer come contesto insieme ai messaggi di quel ciclo perché ne abbia 3 e possa fare u -> v -> w, dove u é il messaggio salvato inizialmente in db, e v e w sono i messaggi del ciclo successivo (o dal punto di vista del ciclo corrente, v e w sono i messaggi dell'utente e la risposta dell'agente, e u invece é ció che precede v, dove é il messaggio attuale dell'utente); in questo modo puó ogni volta avere un'idea chiara, combinato con lo stato salvato del DB, e sapere che modifica svolgere

# Test 9
  - É stata eseguita una chiamata prematura dal tool `Save` dall'Analyzer; ha compilato i campi type_and_industry con la descrizione sintetizzata del prodotto, ed il campo products con una descrizione piú estesa.
    - Rafforzato il system prompt per usare il tool solo a fine lavoro

  - É stato osservato che adesso l'Analyzer rispetta la logica di distinzione tra target e funzioni del prodotto
  
  - É stato osservato che il modello tendesse a cercare comunque di correre a concludere il lavoro anche quando veniva messo in discussione ed erano ancora necessarie delle modifiche, di fatto non rispettava il concetto di chiedere una conferma finale correttamente

  - É stato osservato che l'Analyzer non chiamava piú il tool `Save` durante le fasi intermedie, ma solo alla fine

# Test 10 
  - L'analisi del prodotto e la raccolta delle keyword é andata bene
  - Il salvataggio della summary é stato discreto, nonostante si poteva fare di meglio
  - Arrivati all'USP, quando ho nominato la dicitura sulla nicchia immobiliare di lusso negli Emirati, l'Analyzer ha fatto notare che essa appariva in contrasto con la natura universale del prodotto, e chiedeva se questa peculiaritá era una limitazione tecnica del sistema di Outsmart oppure rappresentava solo l'obiettivo di vendita. Poi chiedeva conferma. Questo denota un ottimo ragionamento sulla direttiva di distinzione tra target di vendita e caratteristiche del prodotto; ottimo risultato. Subito dopo avergli chiarito la questione lui ha chiesto la conferma ma ha comunque poi eseguito il tool call a `Save`. 
    - Aggiunta direttiva che spiega che serve conferma atomica dell'USP prima di andare ad eseguiro il `Save`.

# Test 11
  - Qui il summarizer si é fondamentalmente dimenticato di aggiornare lo stato per riflettere il fatto che le keywords erano state confermate.
    - Implementato uno step count che si aggiorna con l'andare avanti delle interazioni e viene salvato nel db; il conteggio viene dato nel contesto del summarizer e gli viene anche detto che adesso siamo a quello step + 1 per fargli comprendere lo scorrere del tempo, e ad ogni turno di salvataggio dello stato nel DB si incrementa il conteggio.

# Test 12 prima parte
  - Test andato a buon fine; l'analyzer ha dedotto correttamente delle ottime keyword in base ad una mia descrizione dettagliata del prodotto, il summarizer ha estratto correttamente i dati, l'analyzer ha proposto in automatica dopo la mia conferma delle keyword una porzione di USP interessante alla quale gli ho fatto aggiungere ulteriori dettagli che lui ha magistralmente amalgamato a ció che aveva giá proposto inizialmente, il summarizer ha nuovamente riportato i dati correttamente in base allo scambio tra me e l'analyzer, dopo che ho aggiunto un'ulteriore porzione molto densa di dettagli tra cui il concetto filosofico di tatemae per spiegare come il sistema riesce a tener traccia di quello che é il rapporto tra il lead e appunto la facciata digitale del venditore--quel che ho definito tatemae in questo caso--l'analyzer ha preso e unito correttamente tutti i pezzi, amalgamando di nuovo tutti i dettagli. Del resto non potevo aspettarmi un risultato negativo data la considerevole mole di energia cognitiva impiegata nel dargli un input di qualitá. Gli ho chiesto anche di aggiungere oltre a tutto quello che gli ho detto qualcosa di creativo e geniale in piú e lo ha fato, suggerendo che la "la tecnologia si auto-ottimizza continuamente grazie a meccanismi di apprendimento in tempo reale, garantendo un adattamento dinamico alle mutevoli esigenze del mercato e ai feedback dei clienti, posizionandosi cosí come la scelta obbligata per chi cerca performance d'eccellenza nel sales enablement", il che é stato sbalorditivo visto che consideravo io stesso di aggiungere proprio questa caratteristica piú in avanti, questo dimostra che c'é probabilmente una correlazione agghiacciante tra linguaggio e "ovvio corso degli eventi."

# Test 12 seconda parte
  - In questo testo osserviamo per la prima volta delle falle per quanto riguarda richieste relative alle potenziali obiezioni. Diedi al modello un dato durante la descrizione del prodotto per formulare la USP, e cioé che si risparmiano 2/3 del tempo amministrativo usando Outsmart, e dissi anche che il sistema é ``conscio`` e che ricorda perfettamente i vari touch point multicanale, e chiesi, dopo aver ripetuto la stessa sequenza del test precedente, di andare piú a fondo e, provare a mettersi nei panni di un CEO che riceve richieste ogni giorno, e che vuole piú certezza tecnica riguardo a quei dati per far si che non creda siano solo promesse, e abbattere il suo scetticismo, gli ho chiesto quindi di aggiungere questo scudo anti-scetticismo, su suggerimento di Gemini, alla bozza precedente, e farmi vedere cosa tira fuori. Inutile dire che, si ci ha provato, ma il risultato, data l'assenza di chiare indicazioni su un caso simile, é stato abbastanza scarso. Ha subito usato la chiusura tramite l'output della parola `STOP` e "affrontato" l'obiezione direttamente nei dati salvati con il tool `Save`, ha interpretato cosa gli é stato detto come un incoraggiamento a poter aggiungere qualcosa per "contrattaccare" e concludere cosí, bypassando la conferma atomica finale.
    - Aggiunto divieto di autoconferma per fare si che non chiami il tool nello stesso messaggio in cui presenta una bozza, e istruito a chiamare il tool `Save` se il Boos gli dice di finire, blindare, confermare o migliorare la bozza, ma di ripresentarla nuovamente per ottenere conferma atomica, e aggiunta istruzione a scavare di piú in una meccanica tecnica quando é necessario blindare la USP contro le obiezioni.

# Test 13 prima parte
    - Si osserva per prima cosa che nell'output 2 l'Analyzer ha fatto una ripetizione indesiderata della parola boss, e ha prodotto due liste di keywords
    
    - L'Analyzer ha seguito la direttiva sul chiedere se la bozza della USP "distrugge l'obiezione", chiedendolo senza che fosse stata nominata alcuna obiezione
      - Migliorata la direttiva per diventare condizionale e non imperativa
    
    - In questo test 3 ho definito come verdetto finale di aver imparato dal mio personale fallimento di essere stato troppo indulgente e di non aver letto le istruzioni fornitemi da Gemini e averle usate senza essermi soffermato abbastanza, motivo per cui abbiamo avuto quel comportamento indesiderato, poiché, se invece fossi stato attento e mi fossi fermato a leggere tutto per filo e per segno non avremmo avuto quella direttiva volta all'imperativo e non avrebbe quindi ripetuto per ogni proposta di USP se la proposta abbatteva un'obiezione che non era stata fatta. Ho aggiunto che serve costruirsi, per capire cosa conta davvero e non usare male il proprio tempo, una buona bussola che ci guidi nella vita attraverso i nostri obiettivi.

# Test 13 seconda parte 

   Nel test 13 seconda parte ho usato gli stessi system prompt della prima parte e sono ripartito dall'input 5 in poi.
  
  - In questo test sono andato piú a fondo, parlando negli ultimi scambi con l'Analyzer dell'idea dell'ATLAS MEMORIALE 
  
  - Il test si é concentrato sulla USP, attorno a cui ho aggiunto e modificato porzioni molto lunghe, ed é stato osservato che non é stato compilato dall'analyzer il campo ``products``
    - Rafforzato il system prompt per insistere sulla corretta compilazione di tutti i campi, per intero, senza tralasciare dettagli

# Test 14

  - L'Analyzer ha distinto perfettamente il prodotto, il target, il settore di riferimento e le keywords
  
  - Abbiamo trattato la ricerca forense (che raccoglie dati personali di comportamento non solo mera firmografia) e la personalizzazione approfondita dell'USP per poter rendere il rapporto tra l'utente Boss ed i suoi lead estremamente viva e personale, nonché umana
  
  - Abbiamo trattato lo statement di cose apparentemente ovvie ma che vengono cercate di evitare da chi si trova in stati di crisi come metodo per validare l'emozione che prova l'utente, emozione che cerchiamo di capire ed analizzare ogni volta che ci approcciamo ai lead
  
  - Abbiamo preso il concetto dell'ATLAS MULTICANALE e lo abbiamo utilizzato per dire che esso viene consultato ad ogni decisione che bisogna prendere da due entitá/personalitá che sono in dibattito tra loro per cercare una di portare avanti la sua opinione sul quella dell'altra, e al termine, giunti ad una decisione, il concetto espresso viene poi validato o non da una terza entitá scettica che fa da controllore.
  
  - Osservato errore di conta negli step count, ma non importa davvero, anche se ho corretto l'errore, ció che contava era che il modelle capisse che il dato che gli diamo come contesto nel prompt sullo stato attuale del DB si trava allo step ``n`` mentre invece l'analisi che lui sta facendo e sulla base della quale deve aggiornare il db é a ``n + 1``.
  
  - Questa prova, come detto nel verdetto finale, é degna di nota. Molti input e quindi anche gli output sono molto lunghi perció si rischia che il messaggio non venga inviato, bisogna implementare qualcosa per fare caso a questa eventualitá.

# Test 15
  - Ho testato il sistema con il caso `Trame di Paesaggio`
  
  - L'analisi iniziale del prodotto é stata buona, tuttavia dato che il system prompt non comprendeva anche direttive su superlativi, parole blacklistate come ad esempio "pluriennale", l'agente ha scritto alcune parti riportando la pigrizia dell'input.
    - Aggiunte direttive di negazione dei termini di marketing generico, con tanto di balcklist

  - Osservato che il summarizer ha fatto il suo dovere mentre la descrizione era ancora in fase di elaborazione, e difatti é rimasta in fase di conferma fin quando non abbiamo confermato e siamo passati alle keyword e solo allora lo stato é cambiato da `Descrizione in fase di conferma` a `Keyword in fase di conferma`

  - L'analyzer ha invitato ad approfondire eventualmente termini tecnici o numeri nel messaggio dell'Output 7, dove mostrava la bozza della USP, il che é apprezzabile.

  - Nel verdetto ho dichiarato che il bug secondo cui il prodotto non veniva compilato dall'analyzer, cosa che é capitata almeno una volta, sembrerebbe non avvenga piú. Questo era accaduto in un test precedente in cui mi sono concentrato sulla USP, ed era venuta molto lunga; si tratta del test 13 seconda parte
  
  - Ho fornito pochi dettagli all'agente e lui non ha inventato nulla partendo da essi, bene 

# Test 16

  - Preso in causa nuovamente il caso `Trame di Paesaggio`

  - Da qui si evinceva che l'Analyzer non stava scavando abbastanza e che dovevo imporgli di mettere in discussione l'utente e farlo faticare perché questo abbia un senso e non sia una perdita di tempo, dato che se sbagliamo a lasciar passare dati `poveri` qui, allora piú avanti nella catena avremo una comunicazione povera; al contrario, scavando qui, si potrá ottenere arrivare al nocciolo del prodotto del Boss, potendo usare i dettagli che lo riguardano per costruire i legami tra i painpoint del lead e i punti di forza delle soluzioni del prodotto del Boss.

# Test 17

  - Trattiamo nuovamente il caso `Outsmart`

  - Il sistema ha subito risposto con tono inquisitorio non appena ho genericamente dichiarato di occuparmi di "outreach automatizzata"

  - Dopo aver aggiunto dei dettaagli il sistema chiede di scavare piú a fondo

  - Nel messaggio di risposta ho erroneamente premuto `Enter` mentre scrivevo quindi l'input é venuto troncato; l'agente ha chiesto una precisazione dicendo "hai accennato che in fase di onboarding `scendiamo molto a fondo` per comprendere il prodotto dell'utente e personal;izzare le mail, ma il messaggio si interrompe. Puoi completare la descrizione?" Poi chiede conferma sull'integrazione di dati comportamentali provenienti da vari canali come Google, Twitter, siti web, e social media per ottenere un profilo approfondito dei lead
  
  - Usando parole presenti in blacklist, l'Agent adesso ci fa presente che non significano nulla e ci "bacchetta".
  
  - L'Analyzer chiede dettagli precisi sulla meccanica secondo cui riusciamo a risparmiare 2 terzi del tempo ai venditori con questo sistema, applicando ufficialmente tutte le direttive di inquisizione che gli sono state imposte.

  - Sono state rispettate le direttive di contrasto e inquisizione ma il sistema ha dato per buona una spiegazione debole sul perché del risparmio del tempo, non notando che abbiamo commesso un errore logico nell'affidarci a quel dato alla cieca senza fornire nessun tipo di altra spiegazione o verifica; non gli ho giustificato il dato ma solo comunicato che arrivasse da un Report fornito da Perplexity; al che l'agente ha accettato tacitamente, fornendo direttamente la nuova bozza della USP senza mettere piú nulla in discussione. Il test qui é terminato con l'agente che ha ceduto invece di continuare a fare pressione a causa di un dato ottenuto da fonte esterna senza applicare scetticismo.
    - Aggiunte direttive per la messa in discussione dei dati numerici e dei superlativi assoluti in generale
    - Aggiunta direttiva che impone non non accettare dati ingiustificati 

# Test 18

  - L'agente non ha nuovamente inquisito sul dato fornito
    - É stata resa piú incisiva la nuova direttiva di scetticismo inquisitorio nei confronti dei dati numerici privi di contesto tecnico

# Test 19

  - Il caso questa volta é un business Saas di prevenzione delle frodi bancarie ad agenzie creditizie commerciali a Dubai

  - Ho sostituito GPT o3 mini con Gemini 3 Flash

  - Ho ragionato sulla differenza tra formulare una semplice USP e scavare profondamente nel business come facciamo noi, costruendo un qualcosa che é potenzialmente molto piú consistente di una normale USP. In questo test prima ancora di arrivare alla USP abbiamo subito iniziato a mettere in discussione ció che l'utente ha detto inizialmente, questo perché ho aperto lasciando qualche buco nella speranza che lui attaccasse, come ha fatto di consueto 
    - Migliorata l'interazione iniziale per riflettere l'importanza di questo passaggio inquisitivo, dove se l'utente é preparato si fará in fretta, altrimenti se non si esprime in maniera dettagliata ma fa il vago ci vorranno almeno 15 minuti per completare la procedura

# Test 20

    - É stato osservato che facendo passare diverse ore tra un messaggio e l'altro, la memoria semplice di n8n, che si basa sulla ram del sistema ospitante l'istanza di n8n in uso, viene svuotata, quindi si perde il filo della conversazione
      - La soluzione é usare un sistema piú avanzato per la memoria della chat. Ho deciso di usare come memoria Postgre gestito da Supabase
        