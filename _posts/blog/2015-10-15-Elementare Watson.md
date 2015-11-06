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

##  Tipi ... croce e delizia


> <span class="teaser">Da un grande potere derivano grandi responsabilità
</span><cite> Spiderman </cite>

Come avevamo detto la scorsa puntata, la tipizzazione è un affare serio; dà grande robustezza al linguaggio ma dà anche svantaggi, per questo vedo una presa di responsabilità necessaria ad ogni buon sw developer, capire con quali regole funziona il compilatore quando fà del lavoro al posto nostro.

# ... nei template

Come sappiamo un costrutto molto potente del C++ è i template, similare ai <i>Generic Class</i> del Java.


<pre>
template &lt;typename T&gt; 
inline T const& Max (T const& a, T const& b) 
{ 
    return a &lt; b ? b:a; 
} 
</pre>

La semantica è ovvia, la "[pragmatica]" [1] un'pò meno.
Proviamo ad usare il template di cui sopra in un software come il seguente.

<pre>
...
int main ()
{
    int i = 39;
    int j = 20;
    cout &lt;&lt; "Max(i, j): " &lt;&lt; Max(i, j) &lt;&lt; endl;

    double f1 = 13.5;
    double f2 = 20.7;
    cout &lt;&lt; "Max(f1, f2): " &lt;&lt; Max(f1, f2) &lt;&lt; endl;

    string s1 = "Hello"; 
    string s2 = "World"; 
    cout &lt;&lt; "Max(s1, s2): " &lt;&lt; Max(s1, s2) &lt;&lt; endl; 
	
	return 0;
}
</pre>

Compiliamo e sbirciamo:

<pre>
[root@localhost template]# g++ prova.cpp
[root@localhost template]# nm a.out |grep Max
08048ba2 W _Z3MaxINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEERKT_S8_S8_
08048b58 W _Z3MaxIdERKT_S2_S2_
08048b30 W _Z3MaxIiERKT_S2_S2_
[root@localhost template]#
</pre>

Il risultato della compilazione è stato quindi la creazione di 3 funzioni che prendono tipi diversi, per l'appunto <code>T const&amp; </code>.

A
<pre>
[root@localhost template]# c++filt -n _Z3MaxIiERKT_S2_S2_ _Z3MaxIdERKT_S2_S2_
int const& Max&lt;int>(int const&, int const&)
double const& Max&lt;double>(double const&, double const&)
</pre>

Evitiamo per semplicità, il reverse engineering del template quando T è di tipo string lo riprenderemo in fondo al post.

In C++11 sono stati aggiunti i template sugli <code>rvalue</code>.

<pre>
#include &lt;iostream>

using namespace std;
template&lt;typename T>
void funzione(T&& param)
{
    cout&lt;&lt; param &lt;&lt;"\n";
};	// param ora è un riferimento universale

int main(){
int x = 27;	// come prima
const int cx = x;	// come prima
const int& rx = x;	// come prima

funzione(x);	// x è un lvalue, pertanto T è int&, il tipo di param è int&

funzione(cx);	// cx è un lvalue, pertanto T è const int&, il tipo di param è const int&

funzione(rx);	// rx è un lvalue, pertanto T è const int&, anche il tipo di param è const int&

funzione(27);	// 27 è un rvalue, pertanto T è int, quindi il tipo di param è int&&

}
</pre>
che istanzia a compile-time i simboli :
<pre>
....
... W _Z8funzioneIRiEvOT_
... W _Z8funzioneIRKiEvOT_
... W _Z8funzioneIiEvOT_
...
void funzione&lt;int&>(int&)
void funzione&lt;int const&>(int const&)
void funzione&lt;int>(int&&)
</pre>

Prima osservazione : abbiamo istanziato 3 simboli, perchè <pre>funzione(cx)</pre> ed <pre>funzione(rx)</pre> vengono "mappati" tramite 
<pre>void funzione&lt;int&>(const int)</pre>

Seconda osservazione: in C++98 non esistevano i template sugli rvalue ma avremmo dovuto usare un template tipo:

<pre>
template<typename T>
void funzione(T const & param)
</pre>

il codice era compilabile e avremmo istanziato 1 solo simbolo di "tipo":
<pre>
void funzione&lt;int>(int const&)
</pre>

# Array o puntatori

La differenza tra un array e un puntatore è che il primo ha un contenuto informativo maggiore, il numero di elementi dell'array.

<pre>
template <typename T, std::size_t N>
 std::size_t arraySize(T (&)[N])
{
return N;	
}	

int main(){
char x[] = {2,3,5,7,11};
cout &lt;&lt; arraySize(x)&lt;&lt;"\n";

}
</pre>

Quindi tramite l'operatore  T (&amp;)[N] abbiamo estratto il tipo T e la cardinalità N dalla dichiarazione ed infatti:

<pre>
unsigned long arraySize<char, 5ul>(char (&) [5ul])
</pre>

unsigned long perchè size_t viene implementato con tale tipo.

La deduzione dei valori N nei template permette di costruire anche strutture in modo ricorsivo.

<pre>
// Stiamo definendo la struttura fattoriale<N>
template &lt;int N> 
struct fattoriale {
  static const int val = N * fattoriale<N - 1>::val;
};

// Definiaimo fattoriale<0>
template <>
struct fattoriale<0> {
  static const int val = 1;
};

int main(){
 std::cout &lt;&lt; fattoriale<3>::val&lt;&lt;"\n"; 
}
</pre>

Un'osservazione dovrebbe nascere; perchè quando il compilatore deve risolvere <code>fattoriale&lt;0>::val</code> non cerca di istanziare ancora la prima dichiarazione di template?
Perchè l'istanza del template <code>fattoriale&lt;0></code>è stata fatta a monte prima della deduzione di <code>fattoriale&lt;1>::val</code> .

# Argomenti dei template

Abbiamo visto che gli argomenti di un template possono essere, tipi T (identificabili anche con typename) o valori V, entrambi conosciuti a compile-time .
Altri "tipi" di parametri possono essere:
<ul>
<ue>classi</ue>
<ue>template</ue>
<ue>template o classi con valore di default</ue>
</ul>

dal C++11 si può definire anche una lista <i>variadic</i> di parametri detta [parameter pack][2] .

Ci sono però delle eccezioni.
<pre>
template<const char *V>
void funzione(){
    std::cout&lt;&lt;V&lt;&lt;"\n";
}

int main(){ 
  funzione<"Hello">();
}
....
main.cpp: In function 'int main()':

main.cpp:10:21: error: no matching function for call to 'funzione()'

   funzione<"Hello">();

                     ^
main.cpp:5:6: note: candidate: template<const char* V> void funzione()

 void funzione(){

      ^
main.cpp:5:6: note:   template argument deduction/substitution failed:

main.cpp:10:21: error: '"Hello"' is not a valid template argument for type 'const char*' because string literals can never be used in this context

   funzione<"Hello">();
</pre>

<em>because string literals can never be used in this context</em>
il messaggio è chiaro ed esplicito.

Altri vincoli sui parametri di un template non di tipo è che non siano:
<ul>
<el> il risultato dell'operazione typeid</el>
<el> un oggetto temporaneo</el>
<el> un porzione componente di un oggetto (classe base, membri di classe etc) </el>
<el> la variabile __func__ , che translittera in formato const char* il nome della funzione</el>
</ul>

# Template di template

<pre>
#include &lt;iostream>

template&lt;typename T> 
class B{ 
    public:
    T x ;
    void print(){    std::cout &lt;&lt; "Hello\n"; } 
};

int main(){
  B&lt;B&lt;int>> b ;
  b.x.print();
}
</pre>

# Alias di template 

<b>template &lt;&lt;/b><em>lista parametri template</em>
<b>></b>
<b>using</b> <em>identificatore</em> <b>=</b>  <em>tipo</em><b>;</b>

ci permette di definire delle abbreviazioni per il template 

<pre>
template<class T> struct Alloc {};
template<class T> using Vec = vector<T, Alloc<T>>; // tipo è un modo abbreviato per riferirsi al template vector<T, Alloc<T>>
Vec<int> v; // Stiamo in realtà definendo: vector<int, Alloc<int>> v
</pre>

riprendiamo
<pre>
#include <iostream>

template<typename T>  
 T const& Max (T const& a, T const& b) 
{
 return ( a < b ? b : a)   ;
}

int main(){ 
    string s1 = "Hello"; 
    string s2 = "World"; 
    cout &lt;&lt; "Max(s1, s2): " &lt;&lt; Max(s1, s2) &lt;&lt; endl;   
}
</pre>

il simbolo generato è :
<pre>
_Z3MaxINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEET_S6_S6_

std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const& 
Max<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >
(
std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, 
std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&
)
</pre>

[1]: https://it.wikipedia.org/wiki/Pragmatica
[2]: http://en.cppreference.com/w/cpp/language/parameter_pack
 
</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


