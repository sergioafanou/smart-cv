---
title : "So indexieren und trennen Sie Zeichenfolgen in Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3-de
image: "https://sergio.afanou.com/assets/images/image-midres-35.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Der Python-Zeichenfolgen-Datentyp ist eine Sequenz, die aus einem oder mehreren einzelnen Zeichen besteht, die aus Buchstaben, Zahlen, Leerzeichen oder Symbolen bestehen können. Da es sich bei einer Zeichenfolge um eine Sequenz handelt, kann durch Indizieren und Schneiden auf dieselbe Weise wie bei anderen sequenzbasierten Datentypen auf sie zugegriffen werden.</p>

<p>Dieses Tutorial führt Sie durch den Zugriff auf Zeichenfolgen durch Indizieren, sie durch ihre Zeichenfolgen schneiden und einige Zähl- und Zeichenortungsmethoden durchgehen.</p>

<h2 id="wie-zeichenfolgen-indiziert-sind">Wie Zeichenfolgen indiziert sind</h2>

<p>Wie der <a href="https://www.digitalocean.com/community/tutorials/understanding-lists-in-python-3">Listendatentyp</a> mit Elementen, die einer Indexnummer entsprechen, entspricht auch jedes Zeichen einer Zeichenfolge einer Indexnummer, beginnend mit der Indexnummer 0.</p>

<p>Für die Zeichenfolge <code>Sammy Shark!</code> die Indexaufschlüsselung sieht folgendermaßen aus:</p>

<table><thead>
<tr>
<th>S</th>
<th>a</th>
<th>m</th>
<th>m</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>a</th>
<th>r</th>
<th>k</th>
<th>!</th>
</tr>
</thead><tbody>
<tr>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
<td>5</td>
<td>6</td>
<td>7</td>
<td>8</td>
<td>9</td>
<td>10</td>
<td>11</td>
</tr>
</tbody></table>

<p>Wie Sie sehen können, beginnt das erste <code>S</code> bei Index 0 und die Zeichenfolge endet bei Index 11 mit dem <code>!</code> -Symbol.</p>

<p>Wir bemerken auch, dass das Leerzeichen zwischen <code>Sammy</code> und <code>Shark</code> auch seiner eigenen Indexnummer entspricht. In diesem Fall beträgt die dem Leerzeichen zugeordnete Indexnummer 5.</p>

<p>Das Ausrufezeichen (<code>!</code>) hat auch eine Indexnummer zugeordnet. Alle anderen Symbole oder Satzzeichen wie <code>*#$&amp;. ;?</code>, ist ebenfalls ein Zeichen und würde mit einer eigenen Indexnummer verknüpft.</p>

<p>Die Tatsache, dass jedes Zeichen in einer Python-Zeichenfolge eine entsprechende Indexnummer hat, ermöglicht es uns, auf Zeichenfolgen auf dieselbe Weise wie bei anderen sequentiellen Datentypen zuzugreifen und diese zu bearbeiten.</p>

<h2 id="zugriff-auf-zeichen-nach-positiver-indexnummer">Zugriff auf Zeichen nach positiver Indexnummer</h2>

<p>Durch Verweisen von Indexnummern können wir eins der Zeichen in einer Zeichenfolge isolieren. Wir tun dies durch Einfügen der Indexnummern in quadratische Klammern. Lassen Sie uns eine Zeichenfolge deklarieren, drucken und die Indexnummer in quadratischen Klammern aufrufen:</p>
<pre class="code-pre "><code class="code-highlight language-python">ss = "Sammy Shark!"
print(ss[4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>y
</code></pre>
<p>Wenn wir auf eine bestimmte Indexnummer einer Zeichenfolge verweisen, gibt Python das Zeichen zurück, das sich in dieser Position befindet. Da sich der Buchstabe <code>y</code> an der Indexnummer 4 der Zeichenfolge <code>ss = „Sammy Shark!“</code> befindet, erhalten wir beim Drucken von <code>ss [4]</code> <code>y</code> als Ausgabe.</p>

<p>Indexnummern ermöglichen es uns, auf bestimmte Zeichen innerhalb einer Zeichenfolge zuzugreifen.</p>

<h2 id="zugriff-auf-zeichen-nach-negativer-indexnummer">Zugriff auf Zeichen nach negativer Indexnummer</h2>

<p>Wenn wir eine lange Zeichenfolge haben und ein Element am Ende anlegen möchten, können wir auch am Ende der Zeichenfolge rückwärts zählen, beginnend mit der Indexnummer <code>-1</code>.</p>

<p>Für die gleiche Zeichenfolge <code>Sammy Shark!</code> sieht die negative Indexaufschlüsselung folgendermaßen aus:</p>

<table><thead>
<tr>
<th>S</th>
<th>a</th>
<th>m</th>
<th>m</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>a</th>
<th>r</th>
<th>k</th>
<th>!</th>
</tr>
</thead><tbody>
<tr>
<td>-12</td>
<td>-11</td>
<td>-10</td>
<td>-9</td>
<td>-8</td>
<td>-7</td>
<td>-6</td>
<td>-5</td>
<td>-4</td>
<td>-3</td>
<td>-2</td>
<td>-1</td>
</tr>
</tbody></table>

<p>Durch die Verwendung negativer Indexzahlen können wir das Zeichen <code>r</code> ausdrucken, indem wir uns wie folgt auf seine Position am -3-Index beziehen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-3])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Die Verwendung negativer Indexnummern kann nützlich sein, um ein einzelnes Zeichen am Ende einer langen Zeichenfolge zu isolieren.</p>

<h2 id="schneiden-von-zeichenfolgen">Schneiden von Zeichenfolgen</h2>

<p>Wir können auch eine Reihe von Zeichen aus der Zeichenfolge aufrufen. Angenommen, wir möchten nur das Wort <code>Shark</code> drucken. Wir können dies tun, indem wir eine <strong>Scheibe</strong>erstellen, die eine Folge von Zeichen innerhalb einer ursprünglichen Zeichenfolge ist. Mit Scheiben können wir mehrere Zeichenwerte aufrufen, indem wir einen Bereich von Indexnummern erstellen, die durch einen Doppelpunkt <code>[x:y]</code> getrennt sind:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Beim Erstellen einer Scheibe wie in <code>[6:11]</code> beginnt bei der ersten Indexnummer die Scheibe (einschließlich), und bei der zweiten Indexnummer endet die Scheibe (exklusiv), weshalb in unserem obigen Beispiel der Bereich der Indexnummer gilt, der unmittelbar nach dem Ende der Zeichenfolge auftreten würde.</p>

<p>Beim Schneiden von Zeichenfolgen erstellen wir eine <strong>Teilzeichenfolge</strong>, bei der es sich im Wesentlichen um eine Zeichenfolge handelt, die in einer anderen Zeichenfolge vorhanden ist. Wenn wir <code>ss[6:11]</code> aufrufen, rufen wir den Teilstring <code>Shark</code> auf, der in der Zeichenfolge <code>Sammy Shark!</code> vorhanden ist.</p>

<p>Wenn wir eines der Enden einer Zeichenfolge einschließen möchten, können wir eine der Zahlen in der ZeichenfolgenSyntax <code>[n:n]</code> auslassen. Wenn wir beispielsweise das erste Wort der Zeichenfolge <code>ss</code> - „Sammy“ - drucken möchten, können Sie dies tun, indem Sie Folgendes eingeben:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[:5])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy
</code></pre>
<p>Dazu haben wir die Indexnummer vor dem Doppelpunkt in der Scheiben-Syntax weggelassen und nur die Indexnummer nach dem Doppelpunkt eingefügt, die sich auf das Ende der Teilzeichenfolge bezieht.</p>

<p>Um eine Teilzeichenfolge zu drucken, die in der Mitte einer Zeichenfolge beginnt und bis zum Ende druckt, können Sie dazu nur die Indexnummer vor dem Doppelpunkt einfügen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[7:])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>hark!
</code></pre>
<p>Indem nur die Indexnummer vor dem Doppelpunkt eingefügt und die zweite Indexnummer aus der Syntax herausgelassen wird, wechselt die Teilzeichenfolge vom Zeichen der aufgerufenen Indexnummer zum Ende der Zeichenfolge.</p>

<p>Sie können auch negative Indexnummern verwenden, um eine Zeichenfolge zu trennen. Wie bereits erwähnt, beginnen negative Indexzahlen einer Zeichenfolge bei -1 und zählen von dort herunter, bis wir den Anfang der Zeichenfolge erreichen. Wenn man negative Indexzahlen verwendet, beginnen wir zuerst mit der niedrigeren Zahl, wie sie früher in der Zeichenfolge vorkommt.</p>

<p>Verwenden wir zwei negative Indexzahlen, um die Zeichenfolge <code>ss</code> zu schneiden:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-4:-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ark
</code></pre>
<p>Die Teilzeichenfolge „Arche“ ist aus der Zeichenfolge „Sammy Shark!“ , weil das Zeichen „a“ an der Position der Indexnummer -4 auftritt und das Zeichen „k“ unmittelbar vor der Position der Indexnummer -1 auftritt.</p>

<h2 id="festlegen-des-schrittes-beim-schneiden-von-zeichenfolgen">Festlegen des Schrittes beim Schneiden von Zeichenfolgen</h2>

<p>Das Schneiden von Zeichenfolgen kann zusätzlich zu zwei Indexnummern einen dritten Parameter akzeptieren. Der dritte Parameter gibt den <strong>Schritt</strong> an, der angibt, wie viele Zeichen vorwärts bewegt werden sollen, nachdem das erste Zeichen aus der Zeichenfolge abgerufen wurde. Bisher haben wir den Schrittparameter weggelassen, und Python verwendet standardmäßig den Schritt 1, sodass jedes Zeichen zwischen zwei Indexnummern abgerufen wird.</p>

<p>Schauen wir uns noch einmal das obige Beispiel an, in dem die Teilzeichenfolge „Shark“ ausgedruckt wird:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Wir können die gleichen Ergebnisse erzielen, indem wir einen dritten Parameter mit einem Schritt von 1 einfügen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11:1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Ein Schritt von 1 nimmt also jedes Zeichen zwischen zwei Indexnummern eines Schrittes auf. Wenn wir den Schrittparameter weglassen, wird Python standardmäßig mit 1 angegeben.</p>

<p>Wenn wir stattdessen den Schritt erhöhen, sehen wir, dass Zeichen übersprungen werden:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SmySak
</code></pre>
<p>Die Angabe des Schrittes 2 als letzter Parameter in der Python-Syntax <code>ss[0:12:2]</code> überspringt jedes zweite Zeichen. Schauen wir uns die Zeichen an, die in rot ausgedruckt werden:</p>

<p><span class="highlight">S</span>a<span class="highlight">m</span>m<span class="highlight">y</span> <span class="highlight">S</span>h<span class="highlight">a</span>r<span class="highlight">k</span>!</p>

<p>Beachten Sie, dass das Leerzeichen bei der Indexnummer 5 auch mit einem angegebenen Schritt von 2 übersprungen wird.</p>

<p>Wenn wir eine größere Zahl für unseren Schrittparameter verwenden, haben wir eine deutlich kleinere Teilzeichenfolge:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Die Angabe des Schrittes 4 als letzter Parameter in der Python-Syntax <code>ss[0:12:4]</code> druckt nur jedes vierte Zeichen. Schauen wir uns nochmals die Zeichen an, die in rot ausgedruckt werden:</p>

<p><span class="highlight">S</span>amm<span class="highlight">y</span> Sh<span class="highlight">a</span>rk!</p>

<p>In diesem Beispiel wird auch das Leerzeichen übersprungen.</p>

<p>Da wir die gesamte Zeichenfolge drucken, können wir die beiden Indexnummern weglassen und die beiden Doppelpunkte in der Syntax behalten, um das gleiche Ergebnis zu erzielen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Durch Weglassen der beiden Indexnummern und Beibehalten von Doppelpunkten bleibt die gesamte Zeichenfolge im Bereich, während durch Hinzufügen eines letzten Parameters für den Schritt die Anzahl der zu überspringenden Zeichen angegeben wird.</p>

<p>Zusätzlich können Sie einen negativen numerischen Wert für den Schritt angeben, mit dem wir die ursprüngliche Zeichenfolge in umgekehrter Reihenfolge drucken können, wenn wir den Schritt auf -1 setzen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!krahS ymmaS
</code></pre>
<p>Die beiden Doppelpunkte ohne angegebenen Parameter enthalten alle Zeichen aus der ursprünglichen Zeichenfolge. Ein Schritt von 1 enthält alle Zeichen ohne Überspringen. Wenn Sie diesen Schritt negieren, wird die Reihenfolge der Zeichen umgekehrt.</p>

<p>Lassen Sie uns dies erneut tun, aber mit einem Schritt von -2:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!rh ma
</code></pre>
<p>In diesem Beispiel, <code>ss[::-2]</code>, handelt es sich um die Gesamtheit der ursprünglichen Zeichenfolge, da in den Parametern keine Indexnummern enthalten sind, und die Umkehrung der Zeichenfolge durch den negativen Schritt. Außerdem überspringen wir mit einem Schritt von -2 jeden zweiten Buchstaben der umgekehrten Zeichenfolge:</p>

<p><span class="highlight">! </span>k<span class="highlight">r</span>a<span class="highlight">h</span>S<span class="highlight">[Leerzeichen]</span>y<span class="highlight">m</span>m<span class="highlight">a</span>S</p>

<p>Das Leerzeichen wird in diesem Beispiel ausgedruckt.</p>

<p>Indem Sie den dritten Parameter der Python-Scheiben-Syntax angeben, geben Sie den Schritt der Teilzeichenfolge an, den Sie aus der ursprünglichen Zeichenfolge ziehen.</p>

<h2 id="zählmethoden">Zählmethoden</h2>

<p>Während wir über die relevanten Indexnummern nachdenken, die Zeichen in Zeichenfolgen entsprechen, lohnt es sich, einige der Methoden durchzugehen, die Zeichenfolgen zählen oder Indexnummern zurückgeben. Dies kann nützlich sein, um die Anzahl der Zeichen zu begrenzen, die wir in einem Benutzereingabeformular akzeptieren möchten, oder um Zeichenfolgen zu vergleichen. Wie bei anderen sequentiellen Datentypen können Zeichenfolgen mit verschiedenen Methoden gezählt werden.</p>

<p>Wir werden uns zunächst die <code>len()</code> -Methode ansehen, mit der die Länge jedes Datentyps ermittelt werden kann, der eine geordnete oder ungeordnete Sequenz ist, einschließlich Zeichenfolgen, Listen, <a href="https://www.digitalocean.com/community/tutorials/understanding-tuples-in-python-3">Tupeln</a> und <a href="https://www.digitalocean.com/community/tutorials/understanding-dictionaries-in-python-3">Wörterbüchern</a>.</p>

<p>Drucken wir die Länge der Zeichenfolge <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len(ss))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>Die Länge der Zeichenfolge „Sammy Shark!“ ist 12 Zeichen lang, einschließlich des Leerzeichens und des Ausrufezeichens.</p>

<p>Anstatt eine Variable zu verwenden, können wir auch eine Zeichenfolge direkt an die <code>len()</code> -Methode übergeben:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len("Let's print the length of this string."))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>38
</code></pre>
<p>Die <code>len()</code> -Methode zählt die Gesamtzahl der Zeichen innerhalb einer Zeichenfolge.</p>

<p>Wenn wir zählen möchten, wie oft entweder ein bestimmtes Zeichen oder eine Folge von Zeichen in einer Zeichenfolge angezeigt wird, können wir dies mit der <code>str.count()</code>-Methode tun. Arbeiten wir mit unserer Zeichenfolge <code>ss = „Sammy Shark!“</code> und zählen die Anzahl der erscheinenden Zeichen „a“:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("a"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2
</code></pre>
<p>Wir können nach einem anderen Zeichen suchen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("s"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>0
</code></pre>
<p>Obwohl sich der Buchstabe „S“ in der Zeichenfolge befindet, ist es wichtig zu beachten, dass bei jedem Zeichen zwischen Groß- und Kleinschreibung unterschieden wird. Wenn wir unabhängig von der Groß- und Kleinschreibung nach allen Buchstaben in einer Zeichenfolge suchen möchten, können wir die Zeichenfolge mit der <code>str.lower()</code> -Methode zuerst in Kleinbuchstaben konvertieren. Weitere Informationen zu dieser Methode finden Sie unter „<a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3#making-strings-upper-and-lower-case">Eine Einführung in Zeichenfolgenmethoden in Python 3</a>“.</p>

<p>Versuchen wir <code>str.count()</code> mit einer Sequenz von Zeichen:</p>
<pre class="code-pre "><code class="code-highlight language-python">likes = "Sammy likes to swim in the ocean, likes to spin up servers, and likes to smile."
print(likes.count("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>3
</code></pre>
<p>In den Zeichenfolgen <code>likes</code> kommt die Zeichenfolge, die „likes“ entspricht, in der ursprünglichen Zeichenfolge dreimal vor.</p>

<p>Wir können auch herausfinden, an welcher Position ein Zeichen oder eine Zeichenfolge in einer Zeichenfolge vorkommt. Wir können dies mit der <code>str.find()</code>-Methode tun, die die Position des Zeichens basierend auf der Indexnummer zurückgibt.</p>

<p>Wir können überprüfen, wo das erste „m“ in der Zeichenfolge <code>ss</code> vorkommt:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.find("m"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>2
</code></pre>
<p>Das erste Zeichen „m“ tritt an der Indexposition 2 in der Zeichenfolge „Sammy Shark!“ auf. Wir können die Indexnummerpositionen der <code>oben beschriebenen</code> Zeichenfolge <a href="https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3#how-strings-are-indexed">ss</a> überprüfen.</p>

<p>Überprüfen wir, wo die erste Zeichenfolge „likes“ in der Zeichenfolge <code>likes</code> vorkommt:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>6
</code></pre>
<p>Die erste Instanz der Zeichenfolge „likes“ beginnt an der Indexnummernposition 6, an der sich das Zeichen <code>l</code> der Sequenz <code>likes</code> befindet.</p>

<p>Was, wenn wir sehen möchten, wo die zweite Sequenz von „likes“ beginnt? Wir können dazu einen zweiten Parameter an die <code>str.find()</code>-Methode übergeben, die mit einer bestimmten Indexnummer beginnt. Anstatt also am Anfang der Zeichenfolge zu beginnen, beginnen wir nach der Indexnummer 9:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 9))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>34
</code></pre>
<p>In diesem zweiten Beispiel, das an der Indexnummer 9 beginnt, beginnt das erste Ereignis der Zeichenfolge „likes“ bei der Indexnummer 34.</p>

<p>Zusätzlich können wir ein Ende für den Bereich als dritten Parameter angeben. Wie beim Schneiden können wir dies tun, indem wir mit einer negativen Indexzahl rückwärts zählen:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 40, -6))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>64
</code></pre>
<p>Dieses letzte Beispiel sucht nach der Position der Sequenz „likes“ zwischen den Indexnummern 40 und -6. Da der letzte eingegebene Parameter eine negative Zahl ist, zählt er vom Ende der ursprünglichen Zeichenfolge.</p>

<p>Die Zeichenfolgemethoden <code>len()</code>, <code>str.count()</code> und <code>str.find()</code> können verwendet werden, um die Länge, die Zählungen von Zeichen oder Zeichenfolgen sowie Indexpositionen von Zeichen oder Zeichenfolgen in Zeichenfolgen zu bestimmen.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Die Möglichkeit, bestimmte Indexnummern von Zeichenfolgen oder eine bestimmte Scheibe einer Zeichenfolge aufzurufen, bietet uns eine größere Flexibilität bei der Arbeit mit diesem Datentyp. Da Zeichenfolgen wie Listen und Tupel ein sequenzbasierter Datentyp sind, kann durch Indizieren und Schneiden auf sie zugegriffen werden.</p>

<p>Sie können mehr über das <a href="https://www.digitalocean.com/community/tutorials/how-to-format-text-in-python-3">Formatieren von Zeichenfolgen</a> und <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3">Zeichenfolgenmethoden</a> lesen, um weitere Informationen zu Zeichenfolgen zu erhalten.</p>
