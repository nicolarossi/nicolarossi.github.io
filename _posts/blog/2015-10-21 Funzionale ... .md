---
layout: page-fullwidth
title: " C++ ed espressioni λ"
subheadline: "C++ come linguaggio funzionale"
meta_teaser: "Come il C++11,C++14 sono un linguaggi funzionali ..."
teaser: "Come il C++11,C++14 sono un linguaggi funzionali ..."
header: no
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

> In informatica si dice che una funzione produce un effetto collaterale quando modifica un valore o uno stato al di fuori del proprio scoping locale.
<cide> https://it.wikipedia.org/wiki/Effetto_collaterale_%28informatica%29  

Un linguaggio funzionale non è solo un linguaggio che ha una funzione tra i suoi tipi, altrimenti anche il C potrebbe essere tale, ma un linguaggio in cui le funzioni non hanno effetti collaterali.
I linguaggi funzionali puri sono nati negli anni '50 dall'informatica teorica e dallo studio della correttezza formale dei programmi.

I più famosi sono sicuramente LISP e Haskell, il primo è usato come interprete in emacs per i plugin e le estensioni; se sbirciate nel file <code>~/.emacs.d</code> troverete linguaggio LISP.

Nei linguaggi funzionali esiste una ulteriore classificazione dovuta alla semantica dell'istanza del linguaggio.
<ul>
<el>Eager (o Call-by-value)</el>
<el>Lazy  (o Call-by-name o Left-most o Call-by-need)</el>
</ul>

Come il nome suggerisce (Eager -> Impaziente , Lazy -> Pigro ) la differenza è nel momento della valutazione dei parametri delle funzioni.
Semplificando nelle semantiche Lazy, i parametri vengono calcolati solo quando servono e questo cambia fortemente il comportamento di un programma.

Esempio (scritto con errori in pseudo linguaggio λ ):
<code>
F=λx.1
G=λg.λn.n*g(n-1)

H=λf.λg.λx 
</code>

Abbiamo definito F come una funzione che prende x e torna sempre 1 .
Abbiamo definito G come una funzione che prende n e torna il prodotto tra n e G' dove G' è una funzione che ... 

Definiamo poi H come l'applicazione di F su G.

Se procediamo con il calcolo della semantica noteremo che G non termina, o detto meglio, in lambda-calculus non si riduce.
F invece non dipende dall'input.

Nella semantica Lazy non si tenterà di ridurre il parametro di F e quindi la funzione terminerà sempre.

A questo punto appare ovvio che i linguaggi funzionali moderni (Java-8, Scala, C++11 etc ...) NON sono di tipo Lazy .

I linguaggi funzionali puri sono uno strumento per il [Theorem-proving][1] e nella creazione di modellazioni "sicure" ;
ma nella attività di sviluppo, IMHO, non possono essere visti come la soluzione per impedire allo sviluppatore di scrivere errori;
come qualcuno crede si possa fare visto che i linguaggi funzionali sono privi di <em>side-effect</em> by design .

# Tornando al C++

Dal C++11 è stato inserito l'espressioni lamda e le funzioni anonime .

<code>
    auto f= [](int x){return 2*x;} ; 
    
    cout << f(1) << endl;
    cout << f(2) << endl;
</code>

Abbiamo definito una funzione, che si chiama f, che và dagli int agli int o meglio abbiamo quella f nell'espressione f:ℕ → ℕ  .

Arricchiamo la nostra funzione e diciamo che:

<code>
auto f = [](int x){
    cout << x << "\t" ;
    return 2*x;
} ;
</code>

Questa funzione non è più side-effect perchè modifica l'oggetto std::cout e quindi lo stato in cui è eseguito.

Se guardiamo il binario generato e l'assembly (richiamandola funzione per ) che vediamo?

<code>
0000000000600f15 b funzione
...
	movl	$2, %esi
	movl	$funzione, %edi
	call	_ZNKUliE_clEi
...	
 	
</code>	
	
Quindi :
- metti 2 in %esi 
- metti funzione in %edi

e richiama la funzione 	_ZNKUliE_clEi per gli amici:
<code>
	{lambda(int)#1}::operator()(int) const
</code>
o più semplicemente :
<code>
	<lambda(int)>::operator() (&funzione, 1)
</code>

Curiosità:
In LISP è stato scritto il primo linguaggio ad auto-compilare se stesso, sbirciate [questa][2].

[1] https://en.wikipedia.org/wiki/Automated_theorem_proving
[2] ftp://publications.ai.mit.edu/ai-publications/pdf/AIM-039.pdf


</div><!-- /.medium-8.columns -->
</div><!-- /.row -->

