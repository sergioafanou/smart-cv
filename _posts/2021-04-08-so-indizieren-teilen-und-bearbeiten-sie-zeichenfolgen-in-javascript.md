---
title : "So indizieren, teilen und bearbeiten Sie Zeichenfolgen in JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-split-and-manipulate-strings-in-javascript-de
image: "https://sergio.afanou.com/assets/images/image-midres-43.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Eine <strong>Zeichenfolge</strong> ist eine Sequenz von einem oder mehreren Zeichen, die aus Buchstaben, Zahlen oder Symbolen bestehen kann. Auf jedes Zeichen in einer JavaScript-Zeichenfolge kann über eine Indexnummer zugegriffen werden, und allen Zeichenfolgen stehen Methoden und Eigenschaften zur Verfügung.</p>

<p>In diesem Tutorial lernen wir den Unterschied zwischen Zeichenfolgenprimitiven und dem <code>Zeichenfolgenobjekt</code> kennen, wie Zeichenfolgen indiziert werden, wie auf Zeichen in einer Zeichenfolge zugegriffen wird und welche allgemeinen Eigenschaften und Methoden für Zeichenfolgen verwendet werden.</p>

<h2 id="zeichenfolgenprimitive-und-zeichenfolgenobjekte">Zeichenfolgenprimitive und Zeichenfolgenobjekte</h2>

<p>Zuerst werden wir die beiden Arten von Zeichenfolgen klären. JavaScript unterscheidet zwischen der <strong>Zeichenfolgenprimitive</strong>, einem unveränderlichen Datentyp und dem <code>Zeichenfolgenobjekt</code>.</p>

<p>Um den Unterschied zwischen den beiden zu testen, initialisieren wir eine Zeichenfolgenprimitive und ein Zeichenfolgenobjekt.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Initializing a new string primitive
const stringPrimitive = 'A new string.'

// Initializing a new String object
const stringObject = new String('A new string.')
</code></pre>
<p>Wir können den Operator <code>typeof</code> verwenden, um den Typ eines Werts zu bestimmen. Im ersten Beispiel haben wir einer Variable einfach eine Zeichenfolge zugewiesen.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringPrimitive
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>string
</code></pre>
<p>Im zweiten Beispiel haben wir eine <code>neue Zeichenfolge()</code> verwendet, um ein Zeichenfolgenobjekt zu erstellen und einer Variable zuzuweisen.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringObject
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>object
</code></pre>
<p>Meistens erstellen Sie Zeichenfolgenprimitive. JavaScript kann auf die integrierten Eigenschaften und Methoden des Zeichenfolgen``objekt-Wrappers zugreifen und diese verwenden, ohne die von Ihnen erstellte Zeichenfolgenprimitive tatsächlich in ein Objekt zu ändern.</p>

<p>Während dieses Konzepts zunächst etwas anspruchsvoll ist, sollten Sie sich der Unterscheidung zwischen Primitive und Objekt bewusst sein. Im Wesentlichen stehen allen Zeichenfolgen Methoden und Eigenschaften zur Verfügung. Im Hintergrund führt JavaScript bei jedem Aufruf einer Methode oder Eigenschaft eine Konvertierung in ein Objekt und zurück in eine Primitive durch.</p>

<h2 id="wie-zeichenfolgen-indiziert-sind">Wie Zeichenfolgen indiziert sind</h2>

<p>Jedes der Zeichen in einer Zeichenfolge entspricht einer Indexnummer, beginnend mit <code>0</code>.</p>

<p>Zur Demonstration erstellen wir eine Zeichenfolge mit dem Wert <code>How are you?</code>.</p>

<table><thead>
<tr>
<th style="text-align: center">H</th>
<th style="text-align: center">o</th>
<th style="text-align: center">w</th>
<th style="text-align: center"></th>
<th style="text-align: center">a</th>
<th style="text-align: center">r</th>
<th style="text-align: center">e</th>
<th style="text-align: center"></th>
<th style="text-align: center">y</th>
<th style="text-align: center">o</th>
<th style="text-align: center">u</th>
<th style="text-align: center">?</th>
</tr>
</thead><tbody>
<tr>
<td style="text-align: center">0</td>
<td style="text-align: center">1</td>
<td style="text-align: center">2</td>
<td style="text-align: center">3</td>
<td style="text-align: center">4</td>
<td style="text-align: center">5</td>
<td style="text-align: center">6</td>
<td style="text-align: center">7</td>
<td style="text-align: center">8</td>
<td style="text-align: center">9</td>
<td style="text-align: center">10</td>
<td style="text-align: center">11</td>
</tr>
</tbody></table>

<p>Das erste Zeichen in der Zeichenfolge ist <code>H</code>, was dem Index <code>0</code> entspricht. Das letzte Zeichen ist <code>?</code>, was <code>11</code> entspricht. Die Leerzeichen haben auch einen Index bei <code>3</code> und <code>7</code>.</p>

<p>Wenn wir auf jedes Zeichen in einer Zeichenfolge zugreifen können, haben wir verschiedene Möglichkeiten, mit Zeichenfolgen zu arbeiten und diese zu bearbeiten.</p>

<h2 id="zugriff-auf-zeichen">Zugriff auf Zeichen</h2>

<p>Wir zeigen Ihnen, wie Sie auf Zeichen und Indizes mit der Zeichenfolge <code>How are you?</code> zugreifen können.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'

</code></pre>
<p>Mit der quadratischen Klammernotation können wir auf jedes Zeichen in der Zeichenfolge zugreifen.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'[5]
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Wir können auch die <code>charAt()</code>-Methode verwenden, um das Zeichen mit der Indexnummer als Parameter zurückzugeben.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.charAt(5)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Alternativ können wir <code>indexOf()</code> verwenden, um die Indexnummer durch die erste Instanz eines Zeichens zurückzugeben.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.indexOf('o')
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<p>Obwohl „o“ zweimal in der Zeichenfolge <code>How are you?</code> erscheint, erhält <code>indexOf()</code> die erste Instanz.</p>

<p><code>lastIndexOf()</code> wird verwendet, um die letzte Instanz zu finden.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.lastIndexOf('o')
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>9
</code></pre>
<p>Für beide Methoden können Sie auch nach mehreren Zeichen in der Zeichenfolge suchen. Sie gibt die Indexnummer des ersten Zeichens in der Instanz zurück.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.indexOf('are')
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>4
</code></pre>
<p>Die <code>slice()</code>-Methode hingegen gibt die Zeichen zwischen zwei Indexnummern zurück. Der erste Parameter ist die Startindexnummer und der zweite Parameter ist die Indexnummer, an der sie enden soll.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.slice(8, 11)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you
</code></pre>
<p>Beachten Sie, dass <code>11</code> <code>?</code> ist, aber <code>?</code> ist nicht Teil der zurückgegebenen Ausgabe. <code>slice()</code> gibt zurück, was zwischen dem letzten Parameter liegt, schließt ihn jedoch nicht ein.</p>

<p>Wenn ein zweiter Parameter nicht enthalten ist, gibt <code>slice()</code> alles vom Parameter bis zum Ende der Zeichenfolge zurück.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.slice(8)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you?
</code></pre>
<p>Zusammenfassend helfen <code>charAt()</code> und <code>slice()</code> dabei, Zeichenfolgenwerte basierend auf Indexnummern zurückzugeben, und <code>indexOf()</code> und <code>lastIndexOf()</code> machen das Gegenteil und geben Indexnummern basierend auf den angegebenen Zeichenfolgen zurück.</p>

<h2 id="die-länge-einer-zeichenfolge-finden">Die Länge einer Zeichenfolge finden</h2>

<p>Mit der Eigenschaft <code>length</code> können wir die Anzahl der Zeichen in einer Zeichenfolge zurückgeben.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.length
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>Denken Sie daran, dass die Eigenschaft <code>length</code> die tatsächliche Anzahl von Zeichen zurückgibt, die mit 1 beginnen, was 12 ergibt, und nicht die endgültige Indexnummer, die bei <code>0</code> beginnt und bei <code>11</code> endet.</p>

<h2 id="konvertieren-in-groß-oder-kleinschreibung">Konvertieren in Groß- oder Kleinschreibung</h2>

<p>Die beiden integrierten Methoden <code>toUpperCase()</code> und <code>toLowerCase()</code> sind hilfreiche Möglichkeiten, um Text zu formatieren und Textvergleiche in JavaScript zu erstellen.</p>

<p><code>toUpperCase()</code> konvertiert alle Zeichen in Großbuchstaben.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.toUpperCase()
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HOW ARE YOU?
</code></pre>
<p><code>toLowerCase()</code> konvertiert alle Zeichen in Kleinbuchstaben.</p>
<pre class="code-pre "><code class="code-highlight language-js">'How are you?'.toLowerCase()
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>how are you?
</code></pre>
<p>Diese beiden Formatierungsmethoden nehmen keine zusätzlichen Parameter.</p>

<p>Es lohnt sich, zu beachten, dass diese Methoden die ursprüngliche Zeichenfolge nicht ändern.</p>

<h2 id="zeichenfolgen-teilen">Zeichenfolgen teilen</h2>

<p>JavaScript bietet eine sehr nützliche Methode, um eine Zeichenfolge durch ein Zeichen zu teilen und aus den Abschnitten eine neue <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">Anordnung</a> zu erstellen. Wir verwenden die <code>split()</code>-Methode, um die Anordnung durch ein Leerzeichen zu trennen, das durch <code>„“</code> dargestellt ist.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = 'How are you?'

// Split string by whitespace character
const splitString = originalString.split(' ')

console.log(splitString)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[ 'How', 'are', 'you?' ]
</code></pre>
<p>Nachdem wir eine neue Anordnung in der <code>splitString</code>-Variablen haben, können wir auf jeden Abschnitt mit einer Indexnummer zugreifen.</p>
<pre class="code-pre "><code class="code-highlight language-js">splitString[1]
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>are
</code></pre>
<p>Wenn ein leerer Parameter angegeben ist, erstellt <code>split()</code> eine durch Komma getrennte Anordnung mit jedem Zeichen in der Zeichenfolge.</p>

<p>Durch die Trennung von Zeichenfolgen können Sie bestimmen, wie viele Wörter sich in einer Satz befinden und die Methode als Möglichkeit verwenden, um beispielsweise die Vornamen und die Nachnamen von Leuten zu bestimmen.</p>

<h2 id="leerzeichen-kürzen">Leerzeichen kürzen</h2>

<p>Die JavaScript-Methode <code>trim()</code> entfernt Leerzeichen an beiden Enden einer Zeichenfolge, jedoch nicht irgendwo dazwischen. Leerzeichen können Tabulatoren oder Leerzeichen sein.</p>
<pre class="code-pre "><code class="code-highlight language-js">const tooMuchWhitespace = '     How are you?     '

const trimmed = tooMuchWhitespace.trim()

console.log(trimmed)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>How are you?
</code></pre>
<p>Die <code>trim()</code>-Methode ist eine einfache Möglichkeit, die gemeinsame Aufgabe auszuführen, um überschüssige Leerzeichen zu entfernen.</p>

<h2 id="zeichenfolgenwerte-suchen-und-ersetzen">Zeichenfolgenwerte suchen und ersetzen</h2>

<p>Wir können eine Zeichenfolge nach einem Wert durchsuchen und ihn mithilfe der <code>replace()</code> -Methode durch einen neuen Wert ersetzen. Der erste Parameter ist der zu findende Wert und der zweite Parameter ist der Wert, der ihn ersetzen kann.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = 'How are you?'

// Replace the first instance of "How" with "Where"
const newString = originalString.replace('How', 'Where')

console.log(newString)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Where are you?
</code></pre>
<p>Wir können nicht nur einen Wert durch einen anderen Zeichenfolgenwert ersetzen, sondern auch <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">reguläre Ausdrücke</a> verwenden, um <code>replace()</code> leistungsfähiger zu machen. Zum Beispiel wirkt sich <code>replace()</code> nur auf den ersten Wert aus, aber wir können das Flag <code>g</code> (global) verwenden, um alle Instanzen eines Werts abzufangen, und das Flag <code>i</code> (ohne Berücksichtigung der Groß- und Kleinschreibung), um Groß- und Kleinschreibung zu ignorieren.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString =
  "Javascript is a programming language. I'm learning javascript."

// Search string for "javascript" and replace with "JavaScript"
const newString = originalString.replace(/javascript/gi, 'JavaScript')

console.log(newString)
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>JavaScript is a programming language. I'm learning JavaScript.
</code></pre>
<p>Dies ist eine sehr häufige Aufgabe, die reguläre Ausdrücke verwendet. Besuchen Sie <a href="http://regexr.com/">Regexr</a>, um weitere Beispiele für RegEx zu üben.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Zeichenfolgen sind einer der am häufigsten verwendeten Datentypen, und es gibt eine Menge, was wir mit ihnen tun können.</p>

<p>In diesem Tutorial haben wir den Unterschied zwischen der Zeichenfolgenprimitive und dem <code>Zeichenfolgenobjekt</code> kennengelernt, wie Zeichenfolgen indiziert werden und wie die integrierten Methoden und Eigenschaften von Zeichenfolgen verwendet werden, um auf Zeichen zuzugreifen, Text zu formatieren und Werte zu suchen und zu ersetzen.</p>

<p>Eine allgemeinere Übersicht über Zeichenfolgen finden Sie im Lernprogramm „<a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-javascript">So arbeiten Sie mit Zeichenfolgen in JavaScript</a>“.</p>
