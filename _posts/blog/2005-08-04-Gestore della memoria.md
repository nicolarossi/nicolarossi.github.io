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

Costruiamo una piramide in cui negli strati più bassi della piramide sono presenti le memorie più economiche e quindi disponibili in maggiore quantità (nastri, memorie esterne, storage etc etc) e nella parte più alta invece troviamo le memorie più veloci e performanti ( Ram, cache, registri della CPU etc etc ).
Supponiamo che su questa piramide sia adagiata una puntina che legge/scrive sui/nei mattoni della piramide.
Se vogliamo leggere/scrivere da/su un mattone dobbiamo portarlo vicino la nostra puntina.

Il sogno di un calcolatore performante è di poter usare le memorie più disponibili (parte bassa della piramide) alla velocità di quelle più performante (parte bassa della piramide) per fare questo esisterà un sistema software (o hardware) che avrà la responsabilità di spostare i mattoni tra i piani della piramide per farli leggere alla puntina.
Tale gestore



### &lt;blockquote&gt; Quotation
<blockquote>Everything happens for a reason. (Britney Spears)</blockquote>

### &lt;blockquote&gt; and &lt;cite&gt;  together

> Age is an issue of mind over matter. If you don't mind, it doesn't matter.
<cite>Mark Twain</cite>


### &lt;ol&gt; Ordered Lists

1. Ordered List
2. Second List Item
3. Third List Item
    4. Second Level First Item
    4. Second Level Second Item
    4. Second Level Third Item
        5. And a third level First Item
        5. And a third level Second Item
        5. And a third level Third Item
4. Fourth List Item
5. Fifth List Item


### &lt;ul&gt; Unordered Lists

- Unordered List
- Second List Item
- Third List Item
    + Second Level First Item
    + Second Level Second Item
    + Second Level Third Item
        * And a third level First Item
        * And a third level Second Item
        * And a third level Third Item
- Fourth List Item
- Fifth List Item

### &lt;dl&gt; Definition Lists

Definition List
:   Bacon ipsum dolor sit amet spare ribs brisket ribeye, andouille sirloin bresaola frankfurter corned beef capicola bacon. Salami beef ribs sirloin, short loin hamburger shoulder t-bone.

Beef ribs jowl swine porchetta
:   Sirloin tenderloin swine frankfurter pork loin pork capicola ham hock strip steak ribeye beef ribs. Hamburger t-bone ribeye ham prosciutto bresaola.

Pancetta flank sirloin pork
:   short ribs shankle prosciutto landjaeger. Beef ribs turkey shoulder drumstick. Leberkas pork belly ribeye, bresaola jerky strip steak tenderloin bacon landjaeger short ribs beef ribs. Flank pork chop fatback tail kielbasa filet mignon jowl landjaeger bresaola tongue corned beef biltong.
:   Landjaeger spare ribs fatback corned beef tenderloin drumstick, swine chicken beef turkey biltong doner tri-tip filet mignon. 


### &lt;a&gt;
[Links][2] make the web exceptional.


### &lt;em&gt;
Let's *emphasize* how important responsive webdesign is.



### &lt;strong&gt;
This looks like **bold** text.



### &lt;small&gt;
<small>This is small text.</small>



### &lt;s&gt;

It's nice getting things done. Just strike through <s>finished tasks</s>.



### &lt;cite&gt;

<cite>Albert Einstein</cite>



### &lt;q&gt;

If you use &lt;q&gt; your text gets <q>automagically quotes around the text passage</q>.



### &lt;dfn&gt;

The &lt;dfn&gt; tag is a phrase tag. It defines a <dfn>definition term</dfn>.



### &lt;abbr&gt;

The <abbr title="World Health Organization">WHO</abbr> was founded in 1948.



### &lt;time&gt;

The concert took place on <time datetime="2001-05-15 19:00">May 15</time>.


### &lt;code&gt;

Some `code: lucida console` displayed.



### &lt;var&gt;

The &lt;var&gt; tag is a phrase tag. It defines a <var>variable</var>.



### &lt;samp&gt;

Text surrounded by &lt;samp&gt; <samp>looks like this in monospace</samp>.



### &lt;kbd&gt;

Copycats enjoy pressing <kbd>CMD</kbd> + <kbd>c</kbd> and <kbd>CMD</kbd> + <kbd>v</kbd>.



### &lt;sub&gt;

This text <sub>lays low</sub> and chills a bit.


### &lt;sup&gt;

This text <sup>gets high</sup> above the clouds.



### &lt;i&gt;

This looks <i>italic</i>.



### &lt;b&gt;

This looks <b>bold</b>, too.



### &lt;u&gt;

<div><p><u>Underlining</u> content for emphasize is not the best choice. You can't read it so well.</p></div>



### &lt;mark&gt;
Let's <mark>mark this hint</mark> to give you a clue.




### &lt;br&gt;

Need a break? I give you three!<br><br><br>


 [1]: https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5/HTML5_element_list
 [2]: http://phlow.de/
 [3]: http://en.wikipedia.org/wiki/Ed_Benguiat
 [4]: https://www.google.com/fonts/specimen/Lato
 [5]: https://www.google.com/fonts/specimen/Volkhov
 [6]: http://www.latofonts.com/
 [7]: http://modularscale.com/
 [8]: #
 [9]: #
 [10]: #


# &lt;h1&gt;-Heading in Volkhov

## &lt;h2&gt;-Heading in Volkhov

### &lt;h3&gt;-Heading in Volkhov

#### &lt;h4&gt;-Heading in Volkhov

##### &lt;h5&gt;-Heading in Volkhov

##### &lt;h6&gt;-Heading in Volkhov


## Tables

Even tables are responsive thanks to foundation. A table can consist of these elements.

<table>
  <caption>&lt;table&gt; defines an HTML table</caption>
  <colgroup>
    <col span="1" style="width: 15%;"></col>
    <col span="1" style="width: 50%;"></col>
    <col span="1" style="width: 35%;"></col>
  </colgroup>
  <thead>
    <tr>
      <th>HTML Tag</th>
      <th>Defintion</th>
      <th>Style</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>&lt;caption&gt;</td>
      <td>defines a table caption</td>
      <td><code>font-weight: bold;</code></td>
    </tr>
    <tr>
      <td>&lt;colgroup&gt;</td>
      <td>specifies a group of one or more columns in a table for 
formatting. The &lt;colgroup&gt; tag is useful for applying styles to entire columns, instead of repeating the styles for each cell, for each row.</td>
      <td>no styling needed</td>
    </tr>
    <tr>
      <td>&lt;col&gt;</td>
      <td>specifies column properties for each column within a `&lt;colgroup&gt;` 
element</td>
      <td>no styling needed</td>
    </tr>
    <tr>
      <td>&lt;thead&gt;</td>
      <td>is used to group header content in an HTML table</td>
      <td><code>font-weight: bold;</code></td>
    </tr>
    <tr>
      <td>&lt;tbody&gt;</td>
      <td>is used to group the body content in an HTML table</td>
      <td>no styling needed</td>
    </tr>
    <tr>
      <td>&lt;tr&gt;</td>
      <td>defines a row in an HTML table</td>
      <td>no styling needed</td>
    </tr>
    <tr>
      <td>&lt;th&gt;</td>
      <td>defines a header cell in an HTML table</td>
      <td><code>font-weight: bold;</code></td>
    </tr>
    <tr>
      <td>&lt;td&gt;</td>
      <td>defines a standard cell in an HTML table</td>
      <td><code>font-weight: normal;</code></td>
    </tr>
    <tr>
      <td>&lt;tfoot&gt;</td>
      <td>is used to group footer content in an HTML table</td>
      <td>no styling needed</td>
    </tr>
</table>







</div><!-- /.medium-8.columns -->
</div><!-- /.row -->


