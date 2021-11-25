# Convex_Hull (prolog and lisp)

## Obiettivo
Il progetto consiste nel determinare, data una serie di punti, la corrispondente
chiglia convessa. Una chiglia convessa di un insieme di punti ha le seguenti proprietà:
- è insieme convesso
- è la forma più semplice che "approssima" l'insieme di punti
- è il perimetro più corto che delimita l'insieme di punti
- è il poligono di area minore che copre l'insieme di punti
L'algoritmo da implementare, tuttavia, non corrisponde completamente a quanto detto 
sopra poiché le specifiche indicate imponevano il mentenimento dei punti collineari,
che, di norma, non andrebbero inclusi.

## Descrizione Algoritmo
L'algoritmo funziona nel seguente modo:
1) Si ha una lista di punti in ingresso; se non lo è già, si procedere a rendere
   tale lista un insieme(vengono eliminati eventuali punti duplicati).
2) Si determina il punto, che denominiamo p0, con ordinata minore nell'insieme di punti; 
   in caso di parità tra due o più punti, si sceglie quello con ascissa minore.
3) Si ordina tutti i restanti punti in ordine crescente secondo l'angolo che essi 
   formano con p0 e la direzione orizzontale: il possibile angolo formato è compreso 
   tra 0° (il punto in cosiderazione si trova alla medesima ordinata di p0 e alla sua
   destra) e 180° (il punto in cosiderazione si trova alla medesima ordinata di p0 e 
   alla sua sinistra). Se l'angolo formato da più punti è il medesimo si posiziona prima 
   il punto più vinino a p0.
4) Viene effettuato il push di p0 e del primo punto della lista ottenuta precedentemente,
   nello stack che rappresenta la nostra CH. A questo punto di procede nel seguente modo
   finchè i punti della lista ordinata non finiscono:
   se, in ordine, il punto immediatemente sotto al punto più in alto dello stack,
   il punto più in alto dello stack e il punto considerato della lista:
	a)sono in senso antiorario o (come richiesto dalle specifiche del progetto e 
	  diversamente dall'algoritmo originale) sono collineari allora si aggiunge 
	  il punto considerato della lista allo stack e si passa a considerare il 
	  seguente punto della lista.
	b)sono in senso orario allora si fa un pop dello stack e si considera nuovamente
	  lo stesso punto della lista.
   questa parte dell'algoritmo si conclude dopo aver passato in rassegna tutti punti
   della lista ordinata secondo l'angolo con p0. Ci si ritrova quindi quasi(vedi punto 5)
   tutti i punti che formano la CH in senso orario a partire dal punto a sinistra di p0 
   fino ad arrivare a p0, mantenendo i punti collineari.
5) ATTENZIONE! 
   A questo punto bisogna effettuare una piccola correzione che nell'algoritmo originale
   non era necessario effettuare in quanto i punti collineari non erano manenuti.
   Nello specifico si nota che, come fino ad ora proceduto, tutti i punti collineari 
   vengono mantenuti ma c'è un caso particolare che fa eccezione: prendiamo in
   considerazione la retta per la quale passano p0 e l'ultimo punto aggiunto alla
   CH (il punto che con p0 forma l'angolo maggiore). Se ci sono punti collineari su 
   questa retta allora verrà mantenuto solamente il più lontano! Volendo invece noi 
   mantendere TUTTI i punti collineari abbiamo deciso di procedere nel seguente modo:
   
	5.1) si rimuove l'ultimo punto inserito nello stack CH, che chiamiamo Z;
	     Z era il top(CH).
	     
	5.2) si trova nell'insieme di punti iniziale quelli che formano con p0 lo 
	     stesso angolo di Z. 
	     
	5.3) si ordina l'insieme ottenuto secondo la stessa definizione del punto 3)
	     
	5.4) si procede con un append tra l'insieme ordinato del punto precedente
	     e la CH (che ricordiamo era stata privata del suo top).
	     
   il procedimento descritto è generale e può essere applicato anche quando non si
   presenta il caso particolare sopra citato poiché non si farebbe altro che togliere 
   e rimettere l'ultimo punto della CH (Z, quello che con p0 forma l'angolo maggiore).
6) Come da consegna, si provvede a ribaltare CH per avere come primo punto p0 e i
   restanti punti ordinati in senso antiorario.


Dell'implementazione di Prolog vogliamo esplicitare i seguenti passaggi:
- new_point/2 consente l'inserimento di nuovi punti nella base di conoscenza. Secondo le
  istruzioni del progetto l'inserimento dei punti deve essere possibile tramite file, noi
  abbiamo reso possibile anche questa alternativa.
- abbiamo propositamente scelto di effettuare il set di p0 poichè è sfruttato un numero
  considerevole di volte: si tenga conto che viene utilizzato nella funzione smaller_angle/2
  e quindi utilizzato di continuo durante l'ordinamento quick_sort_by_angle/2.
- come si intuisce dal punto precedente, senza una definizione, tramite set, di p0 le 
  funzioni smaller_angle/2 e quick_sort_by_angle/2 non possono funzionare.
- la funzione smaller_angle/2 fa quanto dichiarato solo nel contesto dell'algoritmo, ossia
  quando p0 è definito secondo la definizione di get_p0/2. Se ad esempio p0 non fosse
  a parità di y il punto più a sinistra allora la logica di smaller_angle/2 cadrebbe
  (si pensi al caso in cui si prenda in considerazione, da sinistra a destra
  sulla medesima ordinata, i punti A B e p0; allora secondo la nostra funzione il punto
  A sarebbe considerato più vicino a p0 rispetto a B e non è ciò che si vuole).
- nella funzione adjust_last_coll/3 si provvede ad "aggiustare" la CH ottenuta fino a quel
  momento facendo ciò che è spiegato nel punto 5).
- la funzione convexh/2 non fa altro che eseguire la funzione convexh_without_list/1
  dopo aver eseguito l'assert dei punti presenti nella lista "In" passata. Questo approccio
  è molto comodo perchè consente di eseguire il nostro algoritmo su alcuni punti creati
  lato utente tramite new_point/2(usando convexh_without_list/1) o alternativamente su una 
  lista di punti passata come parametro(usando convexh/2), e quindi anche su un file grazie 
  alla funzione read_points/2.
- abbiamo costruito l'automa che riconosce e accetta il formato del file di punti così come è
  stato descritto nella consegna(x, tab, y ed eventualmente a capo). Per fare questo abbiamo
  usufruito della funzione built-in read_file_to_codes/3 e siamo andati a vedere quindi quale
  fosse il codice corrispondente ai caratteri numerici, al segno tab, al segno meno e
  all'inidicazione di nuova riga. Talora il file di punti in input non fosse, ANCHE SOLO PER 
  UNO SPAZIO BIANCO, come dichiarato, allora esso non verrà accettato e la funzione
  file_check/1 stamperà un messaggio di errore.