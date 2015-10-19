---
layout: page-fullwidth
title: "DIY memory management"
subheadline: "Memory management"
meta_teaser: "Faster dynamic memory manager created to increase speed in a chess engine"
teaser: "<em>Faster dynamic memory manager</em> created to increase speed in a chess engine"
header: no
image:
    thumb:  bicycle_thumb.jpg
    homepage: bicycle.jpg
    caption: Image by David Marcu
    caption_url: "https://unsplash.com/davidmarcu"
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

## Lato – A dynamic memory manager.

Un gestore di memoria "memory manager" è semplicemente una porzione di codice che per "contratto" gestira la memoria RAM a vostra disposizione.

Il memory manager di cui è fornito il kernel del vostro sistema operativo è capace di gestire tutta la memoria hardware di cui dispone il vostro pc/server .

Per fare questo utilizza una serie di layer software per fornire i servizi di gestione della RAM come:

- Ogni processo dispone in linea teorica dell'intero spazio di indirizzamento<sub>logico</sub>, che su macchine da 32bit è di 4Gbyte; quindi come fanno più processi a condividere uno stesso spazio la cui somma totale può superare quella dello spazio degli indirizzi fisici di una macchina? (C'era un tempo, quando eravate piccoli, che i desktop avevano solo pochi Gbyte di Ram ).
La risposta è nel servizio di <strong>Traduzione da indirizzi logici a fisici</strong>

- Esiste un concetto nella architettura  dei calcolatori che si chiama "gerarchia di memoria", che si può spiegare con una metafora descritta in un post apposito.

La gestione della memoria "virtuale" (o di swap) divide lo spazio d'indirizzamento in "pagine" e gestisce la loro posizione nella gerarchia di memoria.

- Gestione della memoria logica "allocando"/"deallocando" memoria al processo che la richiede/libera.

I primi 2 punti vengono effettuati dal sistema operativo insieme alla collaborazione dei firmware dei vari device sottostanti (dischi rigidi, RAM, Cache) .

Il terzo punto invece richiede la collaborazione del processo che usa la memoria, tramite l'uso di 2 API fondamentali .
malloc
free 

La prima richiede al sistema operativo l'allocazione dinamica (ossia a durante l'esecuzione del programma) di un certo quantitativo  di memoria richiesta per lavorare.
La seconda invece informa il sistema operativo che un'area di memoria prima richiesta non è più necessaria.

Perciò basta anteporsi al kernel, durante la fornitura di queste API per fare un proprio gestore della memoria.

Quali sono i vantaggi?
<ul>
<il>
Maggiore controllo per il debugging
</il>
<il>
Performance (in alcuni casi specifici) maggiori.
Minor numero di salti tra user-mode e kernel-mode (questi "salti" creano dei sovraccarichi alla CPU per switchare in kernel mode.
</il>
</ul>

Il primo punto può implementare (per esempio) un sistema di controllo per impedire che vengano effettuate 2 free sulla stessa area di memoria.

Il secondo puntò è vero in casi rari ed eccezionali, un esempio che è quello che mi ha portato a scrivere questo memory manager è accaduto all'autore quando per diletto ha progettato un motore di scacchi; tale software faceva un vastissimo uso di malloc() e free() (occupavano il 60% delle operazioni) e per questo invece di riscrivere il codice in una forma in cui non usasse tali operazioni, ha riscritto le malloc() e free() in modo che fossero più performanti.

Il MM era utilizzato in modo molto specifico.





</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


