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

> Da un grande potere derivano grandi responsabilità
<cide> Spiderman 

Come avevamo detto la scorsa puntata, la tipizzazione è un affare serio; da grande robustezza al linguaggio ma da anche svantaggi, per questo vedo una presa di responsabilità necessaria ad ogni buon sw developer, capire con quali regole funziona il compilatore quando fà del lavoro al posto nostro.

# ... nei template

Come sappiamo un costrutto del C++ sono i template (detta in Java  <i>Generic Class</i>).
<code>
template <typename T>
inline T const& Max (T const& a, T const& b) 
{ 
    return a < b ? b:a; 
} 
</code>

La semantica è ovvia, la "[pragmatica]" [1] un'pò meno.
Proviamo ad usare il template di cui sopra in un software come il seguente.
<code>
...
int main ()
{
    int i = 39;
    int j = 20;
    cout << "Max(i, j): " << Max(i, j) << endl;

    double f1 = 13.5;
    double f2 = 20.7;
    cout << "Max(f1, f2): " << Max(f1, f2) << endl;

    string s1 = "Hello"; 
    string s2 = "World"; 
    cout << "Max(s1, s2): " << Max(s1, s2) << endl; 
	
	return 0;
}
</code>

Compiliamo e sbirciamo:

<code>
[root@localhost template]# g++ prova.cpp
[root@localhost template]# nm a.out |grep Max
08048ba2 W _Z3MaxISsERKT_S2_S2_
08048b58 W _Z3MaxIdERKT_S2_S2_
08048b30 W _Z3MaxIiERKT_S2_S2_
[root@localhost template]#
</code>

Il risultato della compilazione è stato quindi la creazione di 3 funzioni che prendono tipi diversi.

<code>
[root@localhost template]# c++filt -n _Z3MaxIiERKT_S2_S2_ _Z3MaxIdERKT_S2_S2_
int const& Max<int>(int const&, int const&)
double const& Max<double>(double const&, double const&)
</code>

Adesso facciamo un esempio diverso.



[1] https://it.wikipedia.org/wiki/Pragmatica


</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


