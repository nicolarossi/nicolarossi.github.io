---
layout: page-fullwidth
title: "DIY memory management"
subheadline: "Memory management"
meta_teaser: "Un gestore della memoria dinamica performante creato per incrementare la velocità di un motore di scacchi"
teaser: "Un gestore della memoria dinamica performante creato per incrementare la velocità di un motore di scacchi"
header:
    image: bicycle.jpg
    background-color: "#0183c4"
    caption: Image by David Marcu
    caption_url: "https://unsplash.com/davidmarcu"
image:
    thumb:  bicycle_thumb.jpg
    homepage: bicycle.jpg
    caption: Image by David Marcu
    caption_url: "https://unsplash.com/davidmarcu"
categories:
    - Programming,Optimization
---
<div class="row">
<div class="medium-4 medium-push-8 columns" markdown="1">
<div class="panel radius" markdown="1">
**Table of Contents**
{: #toc }
*  TOC
{:toc}
</div>
</div><!-- /.medium-4.columns -->

<div class="medium-8 medium-pull-4 columns" markdown="1">

## Lato - A dynamic memory manager.

Un gestore di memoria è semplicemente una porzione di codice che gestirà la memoria RAM del device e la metterà a disposizione dei processi che ne fanno uso.

Il memory manager di cui è fornito il kernel del vostro sistema operativo preferito è capace di gestire tutta la memoria hardware di cui dispone il vostro pc/server .

Per fare questo utilizza una serie di layer software per fornire i servizi di gestione della RAM come risorsa condivisa tra più processi.

- Traduzione da indirizzi logici a fisici
	Ogni processo dispone in linea teorica dell'intero spazio di indirizzamento logico, che su macchine a 32bit è di 4Gbyte; quindi come fanno più processi a condividere uno stesso spazio la cui somma totale può superare quella dello spazio degli indirizzi fisici di una macchina?.
	La risposta è nel servizio di Traduzione da indirizzi logici a fisici.

- Gestione della gerarchia di memoria

- Gestione della memoria "virtuale" (di cui fà parte anche la famosa memoria di swap) divide lo spazio d'indirizzamento in "pagine" e gestisce la loro "posizione" nella gerarchia di memoria.

- Gestione della memoria logica allocando/deallocando memoria al processo che la richiede/libera.

I primi punti di questa lista vengono effettuati dal sistema operativo insieme alla collaborazione dei firmware dei vari device sottostanti (dischi rigidi, RAM, Cache) .

L'ultimo punto invece richiede la collaborazione del processo che usa la memoria, tramite l'uso di 2 API fondamentali: <code>malloc</code> e <code>free</code>

La prima richiede al sistema operativo l'allocazione dinamica, ossia durante l'esecuzione del programma, di un certo quantitativo  di memoria richiesta per lavorare.

La seconda invece informa il sistema operativo che un'area di memoria prima richiesta non è più necessaria.

Perciò basta anteporsi al kernel, durante la fornitura di queste API per fare un proprio gestore della memoria.

Quali possono essere i vantaggi?

- Maggiore controllo per il debugging
	(eg: in un sistema di controllo per impedire che vengano effettuate 2 free sulla stessa area di memoria.

- Profiling

- Performance maggiori.
 Dovuti al minor numero di salti tra user-mode e kernel-mode (questi "salti" creano dei sovraccarichi alla CPU per switchare in kernel mode.

Un esempio che è quello che mi ha portato a scrivere questo memory manager è accaduto all'autore quando per diletto ha progettato un motore di scacchi[^chessengine]; tale software faceva un vastissimo uso di <code>malloc</code> e <code>free</code> (occupavano il 60% delle operazioni) e per questo invece di riscrivere il codice in una forma in cui non usasse tali operazioni, ha riscritto tali API in modo che fossero più performanti.

L'idea è semplice e ricopia quello che già fà il kernel, ci teniamo in memoria un albero binario per sapere se un'area lineare da <em>controllare</em> è libera o allocata.

Se il bit alla radice dell'albero è 0 ciò indica che l'area <em>controllata</em> è totalmente occupata, altrimenti se 1 l'albero ha almeno 1 figlio che controlla un'area allocabile, quindi si passa a cercare in questo figlio dell'albero ad effettuare la ricerca ricorsivamente fino a che non arriveremo ad una foglia che verrà messa a 0 per indicare lo slot occupato.

Quando si effettua una <code>free()</code> di una area controllata da una foglia si metteranno ad 1 tutta la discendenza verticale della foglia fino alla radice (o fino a trovare il primo padre ad 1).

Un improvement sostanziale è stato effettuato usando invece che un'albero binario un albero con 32 figli.

<em>Perchè 32 ?</em>

Perchè data una maschera di 32 bit posso sapere quale è il primo bit a 1 tramite l'istruzione <code>ffs()</code> o <code>RSB</code> su architettura Intel X86 .


## Code explained 

# Struttura dati dell'albero :

<pre>
typedef struct nodeFreeHandle{
  int_32 mask; //--- Maschera per sapere chi e' libero e chi no.
  struct nodeFreeHandle **child; //--- Vettore di figli
  struct nodeFreeHandle *parent; //--- Padre (nullo per il root node dell'albero)
  int offSet;  //--- Nell'area lineare da controllare che indirizzi stiamo controllando ? da offSet a offSet + ...
  int nLevel;  //--- Livello nell'albero aka: Distanza dal padre 
  int nChild;  //--- Che figlio e' del padre?
} nodeFreeHandle_t ;
</pre>

# Struttura dati per indicizzare un area lineare
<pre>
typedef struct aMemArea{
  nodeFreeHandle_t *frH ; //---- Albero per trovare elementi liberi

  nodeFreeHandle_t *lastBlockFree; //--- Ultimo blocco che ha tornato un valore allocabile (vedi sezione "Caching")
  void *workArea; //--- area di memoria da controllare
  int nFree; //--- Numero di elementi liberi

} aMemArea_t;
</pre>

# Costruzione iniziale

Nel programma per usare un "oggetto" <code>aMemArea_t</code> che controlla la gestione dinamica <code>malloc/free</code> di <code>nElem<code> elementi di dimensione <code>size</code> useremo lo statement:
 
<pre>
  ptrWA=(aMemArea_t*) createWorkArea(sizeArea,sizeof(board_t));
</pre>

Nella libreria l'API è implementata così:

<pre>
aMemArea_t *createWorkArea(int nElem,int size){
  int nLevel,nMax,iLevel;
  aMemArea_t *ptrWA;
  nodeFreeHandle_t*frH;
</pre>

Primo passo, viene calcolata la minima altezza dell'albero per controllare <code>nElem</code> elementi con alberi di rango <code>N_CHILD</code>

<pre>
  for (nLevel=1,nMax=N_CHILD; nMax<nElem ;nMax*=N_CHILD) {
    nLevel++;
  }
</pre>

Poi vengono create e riempite alcune strutture 

<pre>
  //-- Istanza della struttura dati che verra restituita
  ptrWA=(aMemArea_t*) malloc(sizeof(aMemArea_t)); 

  //--- Viene richiesta al kernel l'allocazione di un'area di lavoro contigua.
  ptrWA->workArea=(void *) malloc(nElem*size);

...

  //-- Istanza dell'albero per la gestione dei posti liberi
  ptrWA->frH=(nodeFreeHandle_t*) malloc(sizeof(nodeFreeHandle_t));

  //- Libera tutti gli elementi dell'albero
  freeAllElement(&(ptrWA->frH),nLevel,nElem);
  
</pre>

Un punto che merita un'pò più attenzione è quando...

<pre>
  //-- ...scorre l'albero per sapere quale è il primo blocco libero
  for (frH=ptrWA->frH,iLevel=nLevel; iLevel!=1; iLevel--) {
    frH=frH->child[0];
  }
  ptrWA->lastBlockFree=frH;
</pre>

Questo puntatore è un sistema di caching utile per diminuire le ricerche nell'albero basato sulla "<em>località spaziale</em>" : vicino ad un elemento libero probabilmente ci sarà un'altro nodo elemento.

<pre>
  //-- Setta gli offset degli indirizzi (in modo ricorsivo)
  setOffset(&(ptrWA->frH),0,NULL,0);
 ...
  return ptrWA;
}
</pre>

# Utilizzo:

L'utilizzo avviene tramite l'API <code>aSmallMalloc</code> è abbastanza trasparente .

<pre>
       start[i]=aSmallMalloc(ptrWA,sizeof(board_t));
//--- Al posto di :
//     start[i]=(board_t*) malloc(sizeof(board_t));

</pre>

Analizzando l'implementazione della <code>aSmallMalloc</code> si capisce l'utilizzo del puntatore <code>lastBlockFree</code> .

Prima viene interrogato il puntatore all'ultimo albero, in ordine cronologico, che ha restituito un elemento libero e se questo non ritorna un valore utile, si ricomincia la ricerca dal root node dell'albero<code>ptrWA->frH</code>.

<pre>
void *aSmallMalloc(aMemArea_t *ptrWA,int size){
...
  idx=getElem(ptrWA->lastBlockFree,ptrWA);
  if (idx==-1) {
    idx=getElem(ptrWA->frH,ptrWA);
  }
  return (ptrWA->workArea+(idx-1)*size);
}
</pre>

# Ricerca nell'albero

Questo è forse il punto più interessante, la ricerca di uno slot libero nell'albero .

Si ponga attenzione

<pre>
int getElem(nodeFreeHandle_t*frH,aMemArea_t*ptrWA){
  int idx,idxFree;

  if (frH->child==NULL) {
    //--- Siamo in una foglia
    idx=ffs(frH->mask);
</pre>

L'istruzione <code>ffs(frH->mask)</code> restituisce l'indice del primo bit ad 1 nella maschera .

Se non ci sono slot liberi, la maschera del padre viene aggiornata e viene effettuata una richiesta al padre.

<pre>
    if (idx==0) { //--- Questo figlio non ha slot liberi 
      //--- Aggiorna la maschera del padre
      frH->parent->mask=frH->parent->mask & (~(1<<frH->nChild));

      return getElem(frH->parent,ptrWA);
</pre>

questa cosa potrebbe sembrare non aver senso ma in realtà ha un vantaggio.

Ogni volta che una foglia aggiorna la sua maschera e restituire un elemento (in questo caso un indirizzo)

<pre>
    } else {
      //--- Toglie il bit
      frH->mask=frH->mask&(~(1<<(idx-1)));
      ptrWA->lastBlockFree=frH;
      return idx+frH->offSet;
    }
</pre>

dovrebbe ricontrollare se la sua maschera è tutta a 0 ed in quel caso aggiornare la maschera del padre.

Procastinando questo test si risparmiano N_CHILD test (di cui N_CHILD -1 tutti con lo stesso risultato) a discapito di 2 annidamenti di chiamata in più.

Nel caso in cui la maschera del figlio è a "0" il flusso diventa:

<pre>
f(parent) &RightArrow; f(child[i]) &RightArrow; f(parent) &RightArrow; f(child[i+1])
</pre>

invece che:

<pre>
f(parent) &RightArrow; f(child[i])
</pre>

Ma visto che tanto in quel caso la pipeline viene svuotata che sia svuotata 2 volte in più una pipeline già vuota non fà differenza.
Invece il risparmio di quei N_CHILD è oggettivo.

Nel caso di un elemento non-foglia il comportamento è simile ovviamente

<pre>
  } else {
    idx=ffs(frH->mask);
    if (idx==0) {
      //--- Non ci sono piu' blocchi liberi aggiorna la maschera del padre e ripassagli il task
      frH->parent->mask=frH->parent->mask & (~(1<<frH->nChild));
      return getElem(frH->parent,ptrWA);
    }  else {
      idxFree=getElem(frH->child[idx-1],ptrWA);
      return idxFree;
    }
  }

}
</pre>

Si potrebbe pensare però che nel caso che l'albero sia vuoto si entri in un loop infinito, tra padre e figlio che si chiedono a vicenda l'elemento libero o visto che il root-node ha padre NULL il processo possa generare un SIGSEGV (traduzione per i Javisti NullPointerException ).

In realtà la <code>getElem</code> è una funzione interna, lo sviluppatore usa <code>aSmallMalloc</code>  che essa contiene il check sul numero di elementi liberi, quindi <code>getElem</code> viene richiamata se e solo se c'è almeno un elemento libero.

# Free

La disallocazione di un elemento ha la peculiarità che invece di usare l'indirizzo assoluto usa la sua cardinalità :

<pre>
#ifdef USE_MALLOC
      free(start[i]);
#else
      freeElem(ptrWA,i);
#endif
</pre>

Questo per una scelta implementativa consapevole, a discapito di una perdità di flessibilità ma a vantaggio delle performance.

Il calcolo da indirizzo assoluto a relativo sarebbe stato un doppio costo:
1. Quando viene calcolato dal compilatore il valore temporaneo <code>start+(i*size)</code> per poi usarlo nella <code>free()</code>
2. Quando dall'API bisogna tradurre da indirizzo assoluto <code>start+(i*size)</code> ad <code>i</code> per identificare lo slot da "liberare"

L'implementazione di <code>freeElem()</code> ha delle sorprese.

<pre>
void freeElem(aMemArea_t *ptrWA,int nElem){
  int idx,val,iLevel;
  nodeFreeHandle_t *frH;

  frH=ptrWA->frH;

  //--- 
  for (iLevel=frH->nLevel;iLevel!=1;iLevel--) {
    val=(nElem - (frH->offSet)) ;
</pre>

Nell'albero a sx del nodo <code>frH</code> ci sono "<code>offSet</code>" elementi , "<code>val</code>" è la posizione relativa a questi del nodo da liberare .
Il valore di "<code></code>idx" indica quale è il sottoalbero che lo contiene per saperlo bisognerebbe dividere "<code></code>val" per il numero di elementi contenuti in ogni sotto albero all'altezza "<code>L</code>" e sarebbe <code>32^L = (2^5)^L = (2^(5*L))</code> e dividere per <code>(2^m)</code> si usa ( <code>>>m</code>)

Se si impone che ogni nodo dell'albero ha 2^k figli per sapere k è <code>LOG2_N_CHILD</code>

<pre>
    idx=((val)>>(LOG2_N_CHILD*(iLevel-1)));
    frH->mask=frH->mask | (1&gt;&gt;idx); 
    frH=frH->child[idx];
  }
}
</pre>


# Gerarchia di memoria

Esiste un concetto nella architettura  dei calcolatori che si chiama "gerarchia di memoria", che si può spiegare efficacemente con questa figura onirica.

Costruiamo una piramide in cui negli strati più bassi della piramide sono presenti le memorie più economiche e quindi disponibili in maggiore quantità <em>(dischi rigidi, nastri, cassette DAT, servizi di cloud storage etc etc )</em> e nella parte più alta invece troviamo le memorie più veloci e performanti <em> Ram, cache, registri della CPU etc etc </em>.

Supponiamo che in cima a questa piramide sia adagiata una puntina che legge e scrive sui mattoni della piramide i dati di nostro interesse.

Se vogliamo leggere/scrivere da/su un mattone, deve essere trasportato fisicamente sotto la nostra puntina.

Il sogno di chi progetta un calcolatore performante è di poter usare le memorie più largamente disponibili (situate nella parte bassa della piramide) alla velocità di quelle più performanti (situate nella parte alta della piramide) per fare questo esisterà una combinazione di sistemi software e hardware che hanno la responsabilità di spostare i mattoni tra i piani della piramide per farli leggere alla puntina.

# Bitboard

Visto che stiamo parlando di motori di scacchi:
<pre>
typedef struct {
  int low;int high;
} board_t;
</pre>

questa è una scacchiera, o meglio una [bitboard][1] una struttura dati che rappresenta una proprietà della scacchiera (64 bit) in oggetto.
Per esempio se vogliamo identificare la posizione dei pedoni bianchi all'inizio delle partita essi saranno tutti nella seconda traversa della scacchiera e quindi i bit dal 9 al 16 saranno messi a 1 e gli altri bit saranno a 0 .
Se volessimo indicare la posizione di un pezzo che è stato appena mangiato la bitboard sarà costituita da tutti 0 .

Se vogliamo sapere i pedoni in presa[^presa] basta fare la AND bit-a-bit tra la bitboard dei pedoni e la bitboard delle caselle attaccate.

Se vogliamo invece da una bitboard sapere quale è la posizione del primo bit a 1 useremo l'istruzione <code>ffs()</pre> messa a disposizione dalle Glibc >= 2.12 implementata usando l'istruzione Assembly <code>BSR</code>.

I più intelligenti avranno detto ma perchè non usare un <code>long int</code> che è già di 64 bit invece di un <int>? così invece di fare 2 operazioni di AND se ne fà 1 sola .
Il problema è che nella versione iniziale in cui fu scritto, il codice era su una macchina con registri a 32bit, quindi il <code>long</code> sarebbe stato implementato sempre con 2 AND.


[kernel]: a meno di quella che il kernel riserva per se
[chessengine]: Se analizzate l'architettura di programma che gioca a scacchi questo può essere diviso in 2 parti:
1. l'interfaccia grafica
2. il backend logico che calcola la mossa migliore da fare
	quest'ultimo è un motore di scacchi

[bitboard]: https://en.wikipedia.org/wiki/Bitboard
[presa]: che rischiano di essere mangiati


</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


