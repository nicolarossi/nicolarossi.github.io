---
layout: page-fullwidth
title: " C++ ed espressioni λ"
subheadline: "C++ come linguaggio funzionale"
meta_teaser: "Come il C++11,C++14 sono un linguaggi funzionali ..."
teaser: "Come il C++11,C++14 sono un linguaggi funzionali ..."
header:
    image: arrow.jpg
    background-color: "#262930"
    caption: Image by Matthew Smith
    caption_url: https://unsplash.com/whale
image:
    thumb:  arrow_thumb.jpg
    homepage: arrow.jpg
    caption: Image by Matthew Smith
    caption_url: "https://unsplash.com/whale"
categories:
    - Programming
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

##  Espressioni λ e linguaggio funzionale

>  <span class="teaser">In informatica si dice che una funzione produce un effetto collaterale quando modifica un valore o uno stato al di fuori del proprio scoping locale.</span>
<cite> https://it.wikipedia.org/wiki/Effetto_collaterale_(informatica)</cite>

Un linguaggio funzionale non è solo un linguaggio che ha una funzione tra i suoi tipi, altrimenti anche il C potrebbe essere tale, ma un linguaggio in cui le funzioni non hanno effetti collaterali.

I linguaggi funzionali puri sono nati negli anni '50 dall'informatica teorica e dallo studio della correttezza formale dei programmi.

I più famosi sono sicuramente LISP e Haskell, il primo è usato come interprete in emacs per i plugin e le estensioni; se sbirciate nel file <code>~/.emacs.d</code> troverete linguaggio LISP.

<b>Curiosità:</b> In LISP è stato scritto il primo linguaggio ad auto-compilare se stesso, sbirciate [questa][2].

Nei linguaggi funzionali esiste una ulteriore classificazione dovuta alla semantica dell'istanza del linguaggio.
<ul>
<li>Eager (o Call-by-value)</li>
<li>Lazy  (o Call-by-name o Left-most o Call-by-need)</li>
</ul>

Come il nome suggerisce (Eager → Impaziente , Lazy → Pigro ) la differenza è nel momento della valutazione dei parametri delle funzioni.
Semplificando nelle semantiche Lazy, i parametri vengono calcolati solo quando servono e questo cambia fortemente il comportamento di un programma.

Esempio (scritto con errori in pseudo linguaggio λ ):

<pre>
F=λx.1
G=λg.λn.n*g(n-1)

H=λf.λg.λx 
</pre>

Abbiamo definito F come una funzione che prende x e torna sempre 1 .
Abbiamo definito G come una funzione che prende n e torna il prodotto tra n e G' dove G' è una funzione che ... 

Definiamo poi H come l'applicazione di F su G.

Se procediamo con il calcolo della semantica noteremo che G non termina, o detto meglio, in lambda-calculus non si riduce.
F invece non dipende dall'input.

Nella semantica Lazy non si tenterà di ridurre il parametro di F e quindi la funzione terminerà sempre.

A questo punto appare ovvio che i linguaggi funzionali moderni (Java-8, Scala, C++11 etc ...) NON sono di tipo Lazy .

I linguaggi funzionali puri sono un potente strumento per il [Theorem-proving][1] e utili nella creazione di modellazioni "sicure" di sistemi mission-critical; 
ma nella attività di sviluppo, IMHO, non possono essere visti come la soluzione per impedire allo sviluppatore di scrivere errori, come qualcuno crede si possa fare, visto che i linguaggi funzionali (puri) sono privi di <em>side-effect</em> by design .

# Tornando al C++

Dal C++11 è stato inserito l'espressioni lamda e le funzioni anonime .

<pre>
    auto f= [](int x){return 2*x;} ; 
    
    cout << f(1) << endl;
    cout << f(2) << endl;
</pre>

Abbiamo definito una funzione, che si chiama f, che và dagli <code>int</code> agli <code>int</code> o meglio una approssimazione di quella <b>f</b> nell'espressione <b>f:</b>ℕ → ℕ  .

Arricchiamo la nostra funzione e diciamo che:

<pre>
auto f = [](int x){
    cout << x << "\t" ;
    return 2*x;
} ;
</pre>

Questa funzione non è più [side-effect] [] perchè modifica l'oggetto <code>std::cout</code> e quindi lo stato in cui è eseguito.

Se guardiamo il binario generato e l'assembly, rinominandola <em>funzione</em>, troveremo:

<pre>
0000000000600f15 b funzione
...
	movl	$2, %esi
	movl	$funzione, %edi
	call	_ZNKUliE_clEi
...	
 	
</pre>	
	
Quindi :
<ul>
<li> metti 2 in <code>%esi</code> </li>
<li> metti funzione in <code>%edi</code> </li>
</ul>
e richiama la funzione 	<code>_ZNKUliE_clEi</code> per gli amici:
<pre>
	{lambda(int)#1}::operator()(int) const
</pre>

o più semplicemente :
<pre>
	<lambda(int)>::operator() (&funzione, 1)
</pre>


[1]: https://en.wikipedia.org/wiki/Automated_theorem_proving
[2]: ftp://publications.ai.mit.edu/ai-publications/pdf/AIM-039.pdf
[3]: https://it.wikipedia.org/wiki/Effetto_collaterale_%28informatica%29

</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


