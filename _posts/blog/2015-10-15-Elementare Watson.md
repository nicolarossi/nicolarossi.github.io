---
layout: page-fullwidth
title: "Elementare Watson"
subheadline: "Deduzione dei tipi"
meta_teaser: "Come il C++14 e più in generale il C++ deduce i tipi nei template ..."
teaser: "Come il C++14 e più in generale il C++ deduce i tipi nei template ..."
header: no
image:
    thumb:  pipe_smoker_band_thumb.jpg
    homepage: pipe_smoker_band.jpg
    caption: Image by Andrew Welch
    caption_url: "https://unsplash.com/andrewwelch3"
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

##  Perchè?
Si è vero, sono il primo a dire che i linguaggi di programmazione essendo linguaggi in senso matematico tutti [Touring completi][8] non dovrebbero essere capaci di aggiungere potenza alle capacità comunicative dell'autore.
Beh si in effetti stiamo parlando di comunicazione tra due entità.
Un software è un poema, non è solo una visione romantica ma un affare serio.
Pensate ai software di milioni di LOC. Non è un Odissea forse trovare un bug in essi?
Non è forse un piacere leggere un software "ben" fatto?
Lasciando da parte queste speculazioni comiche riflettete su questo fatto reale:
[Noam Chomsky] [1][2] è un [linguista] [4] e ha descritto una struttura matematica alla base dell'informatica teorica, la [<em>Chomsky's hierarchy</em>][3]

Questa gerarchia può essere utile per quantificare se un linguaggio sia più potente di un'altro, ma in realtà dopo un'pò di esperienza si nota che i linguaggi iniziano un'pò tutti a copiarsi e non esiste la <em>silver bullet</em>.
Pensate alla potenza della [programmazione logica e del prolog] [10] , quando è risultata utile alla comunità, cosa è stato fatto? Si è cercato di inglobarla sviluppando una serie di librerie e interfacce in Java per usarla[5] [6] e la stessa cosa in C++ [9].

> "Si attacca con la forza frontale, ma si vince con quelle laterali."
<cite> Sun Tzu (The art of war)

## Primo statement (Auto)

<code>
	auto sum = 0.; // sum è un double
	auto n = 10; //  n è un int
	auto c = "hello this is a right place"; // c è un const char *
</code>

Il codice è autoesplicativo.
Il compilatore definisce il tipo delle variabili al tempo di compilazione ma facciamoci un'pò di domande.

<code>
auto d = c + n; // Questa è facile.... const char * 
</code>

Un indirizzo più un qualcosa (<code>int</code>)che può essere castato ad "indirizzo" è un indirizzo.

<code>
auto e = c + sum; 
// Errore	C2111	'+': l'addizione di puntatori richiede un operando integrale	
</code>

Sicuramente l'operatore <code>auto</code> sarà molto utile quando si utilizzano strutture più complesse per identificare automaticamente il tipo permettendo di risparmiare tempo.
Ma attenzione che l'insidia è dietro l'angolo, come si è visto con la somma di const char e int il compilatore accetta lo statement perchè la sua semantica lo prevede sta a voi farne buon uso.

<code>
+		c	0x00e88e30 "hello world this is a right place"	const char *
+		d	0x00e88e3a "d this is a right place"	const char *
</code>

Altra domanda, si può usare tale operatore con le funzioni? 

<code>
auto fattoriale(int k) {
	if (k <= 1) {
		return 1;
	} else {
		return fattoriale(k - 1)*k;		
	}
}
....
main.cpp:16:22: error: 'fattoriale' function uses 'auto' type specifier without trailing return type
 auto fattoriale(int k) {
                      ^
main.cpp:16:22: note: deduced return type only available with -std=c++14 or -std=gnu++14
</code>

Nel C++11 no, tale funzionalità è stata inserita nel C++14 ma con un comportamento che dipende dall'implementazione del codice.
In questa implementazione, si riesce ad inferire il tipo e non si sollevano problemi, ma basta cambiare l'ordine del if then else per avere problemi perchè il compilatore non riesce a dedurre il tipo della funzione.

<code>
auto fattoriale(int k) {
	if (k > 1) {
		return k*fattoriale(k - 1);
	} else {
		return 1;
	}
}
...
main.cpp: In function 'auto fattoriale(int)':
main.cpp:18:12: error: use of 'auto fattoriale(int)' before deduction of 'auto'
   return k*fattoriale(k - 1);     
</code>


> Da un grande potere derivano grandi responsabilità
<cide> Spiderman 

La tipizzazione è un affare serio, da grande robustezza al linguaggio ma da anche svantaggi.
L'operatore auto ci permette di rimanere in un limbo, risparmiando tempo perchè non dobbiamo ogni volta definire il tipo.
Ma da altra parte se la deduzione del tipo deriva dal software si rischia di cadere in errori.


## Tipo null 

Finalmente hanno dato a null un tipo.
A che serve secondo voi ?

<code>
void foo(int k){
	std::cerr << "Eseguita foo(int)" << std::endl;	
}
void foo(char *) {
	std::cerr << "Eseguita foo(char*)" << std::endl;
}

int main()
{	
	foo(0);
	foo(NULL);
    return 0;
}
</code>

Che vi aspettate come output ?

Se state utilizzando Visual Studio C++ 2015, otterrete:
<code>
Eseguita foo(int)
Eseguita foo(int)
</code>

Se state utilizzando una vecchia versione di g++ (ie.: gcc 4.1.2)

Vi buscate un semplice warning
<code>
main.cpp: In function ‘int main()’:
main.cpp:14: warning: passing NULL to non-pointer argument 1 of ‘void foo(int)’
</code>

Se invece usate un g++ più avanzato (gcc  5.2.0 ) vi dà degli errori di compilazione.
<code>
main.cpp: In function 'int main()':
main.cpp:26:10: error: call of overloaded 'foo(NULL)' is ambiguous
  foo(NULL);
          ^
main.cpp:16:6: note: candidate: void foo(int)
 void foo(int k){
      ^
main.cpp:19:6: note: candidate: void foo(char*)
 void foo(char *) {
      ^
</code>

Nel C++11 è stato aggiunto il valore "nullptr".
Se chiamiamo foo() passando come argomento "nullptr" il comportamento cambia e abbiamo un errore a tempo di compilazione.

<code>
Eseguita foo(int)
Eseguita foo(char*)
</code>

[Watching under-the-hood] [12] scopriamo che il letterale "nullptr" è implementato tramite il tipo std::nullptr_t .

Rimane ancora valido l'uso di NULL per retro-compatibilità, ma se volete divertirvi potete ridefinire la macro <code>NULL</code> e vedere l'effetto che fà al vostro software

<code>
#define NULL nullptr
</code>

## Prossime puntate 
Da questa prima tappa abbiamo confermato quello che già avevamo scoperto in gioventù.
La semantica di un linguaggio è delicata e sottile, per esempio si racconta questo aneddoto.

L'autore scrisse in gioventù un software di tante linee di codice in Fortran 95 .
Compilando con la versione 8.0 del compilatore Intel IFC il codice sorgente si otteneva un eseguibile stabile utilizzato in molte ore di produzione.
Compilandolo con la versione 9.0 del compilatore IFC l'eseguibile risultava inutilizzabile per crash dovuti ad accessi alla memoria non validi (il classico SIGSEGV aka crash del binario)

Analizzando, si trovò che la linea di codice incriminata era 

if v <> null .and. v(1)> 0  then

volendo indicare:
<b><em>Se il vettore non era nullo e il primo elemento dello stesso era positivo allora ...</em></b>

Sfruttando la [short circuit evaluation][13] per garantire che venga testato il valore di v(1) solo se v<>null .

La trappola è che il Fortran90 ha la short-circuit evaluation ma non assicura che l'ordine di valutazione dei termini sia mantenuto ;)


[1] http://www.chomsky.info/
[2] https://it.wikipedia.org/wiki/Noam_Chomsky
[3] https://it.wikipedia.org/wiki/Gerarchia_di_Chomsky
[4] https://it.wikipedia.org/wiki/Linguistica
[5] http://www.swi-prolog.org/packages/jpl/
[6] https://www.gnu.org/software/gnuprologjava/
[7] http://www.swi-prolog.org/packages/jpl/java_api/
[8] https://it.wikipedia.org/wiki/Turing_equivalenza
[9] http://www.swi-prolog.org/pldoc/doc_for?object=section%28%27packages/pl2cpp.html%27%29
[10] http://homes.di.unimi.it/~logica/logimat/Prolog/Furlan_Lanzarone_PROLOG.pdf
[11] https://www.clear.rice.edu/mech517/F90_docs/EC_oop_f90.pdf
[12] http://en.cppreference.com/w/cpp/types/nullptr_t
[13] https://en.wikipedia.org/wiki/Short-circuit_evaluation

</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


