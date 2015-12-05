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

Un gestore di memoria è semplicemente una porzione di codice che gestirà la memoria RAM del device[^kernel] e la metterà a disposizione dei processi che ne fanno uso.

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

L'archiettura è 


# Gerarchia di memoria

Esiste un concetto nella architettura  dei calcolatori che si chiama "gerarchia di memoria", che si può spiegare efficacemente con questa figura onirica.

Costruiamo una piramide in cui negli strati più bassi della piramide sono presenti le memorie più economiche e quindi disponibili in maggiore quantità <em>(dischi rigidi, nastri, cassette DAT, servizi di cloud storage etc etc )</em> e nella parte più alta invece troviamo le memorie più veloci e performanti <em> Ram, cache, registri della CPU etc etc </em>.

Supponiamo che in cima a questa piramide sia adagiata una puntina che legge e scrive sui mattoni della piramide i dati di nostro interesse.

Se vogliamo leggere/scrivere da/su un mattone, deve essere trasportato fisicamente sotto la nostra puntina.

Il sogno di chi progetta un calcolatore performante è di poter usare le memorie più largamente disponibili (situate nella parte bassa della piramide) alla velocità di quelle più performanti (situate nella parte alta della piramide) per fare questo esisterà una combinazione di sistemi software e hardware che hanno la responsabilità di spostare i mattoni tra i piani della piramide per farli leggere alla puntina.


[kernel]: a meno di quella che il kernel riserva per se
[chessengine]: Se analizzate l'architettura di programma che gioca a scacchi questo può essere diviso in 2 parti:
1. l'interfaccia grafica
2. il backend logico che calcola la mossa migliore da fare
	quest'ultimo è un motore di scacchi


</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


