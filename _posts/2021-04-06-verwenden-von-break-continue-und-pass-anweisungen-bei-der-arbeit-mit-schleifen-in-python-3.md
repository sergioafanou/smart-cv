---
title : "Verwenden von Break-, Continue- und Pass-Anweisungen bei der Arbeit mit Schleifen in Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3-de
image: "https://sergio.afanou.com/assets/images/image-midres-51.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Die Verwendung von <strong>for-Schleifen</strong> und <strong><a href="https://www.digitalocean.com/community/tutorials/how-to-construct-while-loops-in-python-3">while-Schleifen</a></strong> in Python ermöglicht Ihnen die Automatisierung und Wiederholung von Aufgaben in effizienter Weise.</p>

<p>Jedoch kann ein externer Faktor die Ausführung Ihres Programms manchmal beeinflussen. Wenn dies der Fall ist, möchten Sie vielleicht, dass Ihr Programm eine Schleife vollständig verlässt, vor dem Fortfahren einen Teil einer Schleife überspringt oder diesen externen Faktor ignoriert. Sie können diese Aktionen mit den Anweisungen <code>break</code>, <code>continue</code> und <code>pass</code> durchführen.</p>

<h2 id="break-anweisung">Break-Anweisung</h2>

<p>In Python bietet Ihnen die <code>break</code>-Anweisung die Möglichkeit, eine Schleife zu verlassen, wenn eine externe Bedingung ausgelöst wird. Sie setzen die <code>break</code>-Anweisung innerhalb des Codeblocks unter Ihrer Schleifenanweisung ein, normalerweise nach einer bedingten <code>if</code>-Anweisung.</p>

<p>Sehen wir uns ein Beispiel an, das die Anweisung <code>break</code> in einer Schleife <code>for</code> verwendet:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        break    # break here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>In diesem kleinen Programm wird Variable <code>number</code> bei 0 initialisiert. Dann erstellt eine Anweisung <code>for</code> die Schleife so lange, bis die Variable <code>number</code> kleiner als 10 ist.</p>

<p>Innerhalb der Schleife <code>for</code> gibt es eine Anweisung <code>if</code>, die die Bedingung stellt, dass, <em>wenn</em> die Variable <code>number</code> gleich der Ganzzahl 5 ist, <em>dann</em> die Schleife abbricht.</p>

<p>Innerhalb der Schleife gibt es auch eine Anweisung <code>print()</code>, die bei jeder Iteration der Schleife <code>for</code> ausgeführt wird, bis die Schleife abbricht, da sie nach der Anweisung <code>break</code> steht.</p>

<p>Um zu wissen, wann wir aus der Schleife heraus sind, haben wir eine abschließende Anweisung <code>print()</code> außerhalb der Schleife <code>for</code> eingefügt.</p>

<p>Wenn wir diesen Code ausführen, wird unsere Ausgabe wie folgt aussehen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Out of loop
</code></pre>
<p>Dies zeigt, dass, sobald die Ganzzahl <code>number</code> als gleichwertig zu 5 ausgewertet wird, die Schleife abbricht, da das Programm mit der Anweisung <code>break</code> dazu aufgefordert wird.</p>

<p>Die Anweisung <code>break</code> veranlasst ein Programm, aus einer Schleife auszubrechen.</p>

<h2 id="continue-anweisung">Continue-Anweisung</h2>

<p>Die Anweisung <code>continue</code> gibt Ihnen die Möglichkeit, den Teil einer Schleife zu überspringen, in der eine externe Bedingung ausgelöst wird, aber den Rest der Schleife zu Ende zu führen. Das heißt, die aktuelle Iteration der Schleife wird unterbrochen, aber das Programm kehrt an den Anfang der Schleife zurück.</p>

<p>Die Anweisung <code>continue</code> befindet sich innerhalb des Codeblocks unter der Schleifenanweisung, normalerweise nach einer bedingten <code>if</code>-Anweisung.</p>

<p>Unter Verwendung des gleichen Schleifenprogramms <code>for</code> wie im vorstehenden Abschnitt <a href="https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3#break-statement">Break-Anweisung</a>, verwenden wir eine Anweisung <code>continue</code> anstelle einer Anweisung <code>break</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        continue    # continue here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Der Unterschied bei der Verwendung der Anweisung <code>continue</code> anstelle einer Anweisung <code>break</code> besteht darin, dass unser Code trotz der Unterbrechung fortgesetzt wird, wenn die Variable <code>number</code> als äquivalent zu 5 ausgewertet wird. Schauen wir uns unsere Ausgabe an:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Number is 6
Number is 7
Number is 8
Number is 9
Out of loop
</code></pre>
<p>Hier tritt <code>Nuber is 5</code> nie in der Ausgabe auf, aber die Schleife fährt nach diesem Punkt fort, um Zeilen für die Zahlen 6–10 auszugeben, bevor sie die Schleife verlässt.</p>

<p>Sie können die Anweisung <code>continue</code> verwenden, um tief verschachtelten bedingten Code zu vermeiden oder eine Schleife zu optimieren, indem Sie häufig auftretende Fälle, die Sie verwerfen möchten, eliminieren.</p>

<p>Die Anweisung <code>continue</code> veranlasst ein Programm, bestimmte Faktoren zu überspringen, die innerhalb einer Schleife auftreten, dann aber den Rest der Schleife zu durchlaufen.</p>

<h2 id="pass-anweisung">Pass-Anweisung</h2>

<p>Wenn eine externe Bedingung ausgelöst wird, können Sie mit der Anweisung <code>pass</code> die Bedingung behandeln, ohne dass die Schleife in irgendeiner Weise beeinflusst wird; der gesamte Code wird weiterhin gelesen, bis eine <code>break</code>- oder eine andere Anweisung auftritt.</p>

<p>Wie bei den anderen Anweisungen, befindet sich die Anweisung <code>pass</code> innerhalb des Codeblocks unter der Schleifenanweisung, normalerweise nach einer bedingten <code>if</code>-Anweisung.</p>

<p>Unter Verwendung des gleichen Codeblocks wie vorstehend, ersetzen wir die Anweisung <code>break</code> oder <code>continue</code> durch eine Anweisung <code>pass</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        pass    # pass here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Die Anweisung <code>pass</code>, die nach der bedingten <code>if</code>-Anweisung steht, teilt dem Programm mit, dass es die Schleife weiter ausführen und die Tatsache ignorieren soll, dass die Variable <code>number</code> während einer ihrer Iterationen als gleichwertig zu 5 ausgewertet wird.</p>

<p>Wir führen das Programm aus und betrachten die Ausgabe:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Number is 5
Number is 6
Number is 7
Number is 8
Number is 9
Out of loop
</code></pre>
<p>Durch die Verwendung der Anweisung <code>pass</code> in diesem Programm stellen wir fest, dass das Programm genau so abläuft, wie es ablaufen würde, wenn es keine bedingte Anweisung in dem Programm gäbe. Die Anweisung <code>pass</code> weist das Programm an, diese Bedingung zu ignorieren und das Programm wie gewohnt weiter auszuführen.</p>

<p>Die Anweisung <code>pass</code> kann minimale Klassen erstellen oder als Platzhalter dienen, wenn Sie an einem neuen Code arbeiten und auf einer algorithmischen Ebene denken, bevor Sie die Details ausarbeiten.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Mit den Anweisungen <code>break</code>, <code>continue</code> und <code>pass</code> in Python können Sie <code>for</code>-Schleifen und <code>while</code>-Schleifen effektiver in Ihrem Code verwenden.</p>

<p>Um mehr mit den Anweisungen <code>break</code> und <code>pass</code> zu arbeiten, können Sie unserem Projekttutorial „<a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-twitterbot-with-python-3-and-the-tweepy-library">Erstellen eines Twitterbots mit Python 3 und der Tweepy-Bibliothek</a>“ folgen.</p>
