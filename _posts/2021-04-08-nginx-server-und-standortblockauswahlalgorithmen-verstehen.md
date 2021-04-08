---
title : "Nginx Server- und Standortblockauswahlalgorithmen verstehen"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms-de
image: "https://sergio.afanou.com/assets/images/image-midres-11.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Nginx ist einer der beliebtesten Webserver der Welt. Er kann hohe Lasten mit vielen gleichzeitigen Clientverbindungen erfolgreich bewältigen und kann problemlos als Webserver, Mailserver oder Reverse-Proxy-Server fungieren.</p>

<p>In diesem Leitfaden werden wir einige der Details hinter den Kulissen erörtern, die bestimmen, wie Nginx Client-Abfragen verarbeitet. Das Verständnis dieser Ideen kann das Rätselraten beim Entwerfen von Server- und Standortblöcken erleichtern und die Bearbeitung von Abfragen weniger unvorhersehbar erscheinen lassen.</p>

<h2 id="nginx-blockkonfigurationen">Nginx-Blockkonfigurationen</h2>

<p>Nginx unterteilt die Konfigurationen, die unterschiedliche Inhalte bereitstellen sollen, logisch in Blöcke, die in einer hierarchischen Struktur leben. Bei jedem Abfragen einer Client-Abfrage beginnt Nginx einen Prozess zur Feststellung, welche Konfigurationsblöcke zur Bearbeitung der Abfrage verwendet werden sollen. Dieser Entscheidungsprozess ist das, was wir in diesem Leitfaden diskutieren werden.</p>

<p>Die Hauptblöcke, die wir diskutieren werden, sind der <strong>Server-Block</strong> und der <strong>Standort-Block</strong>.</p>

<p>Ein Serverblock ist eine Teilmenge der Nginx-Konfiguration, die einen virtuellen Server definiert, der zur Verarbeitung von Abfragen eines definierten Typs verwendet wird. Administratoren konfigurieren häufig mehrere Serverblöcke und entscheiden anhand des angeforderten Domainnamens, Ports und der IP-Adresse, welcher Block welche Verbindung verarbeiten soll.</p>

<p>Ein Standortblock befindet sich in einem Serverblock und wird verwendet, um zu definieren, wie Nginx Abfragen für verschiedene Ressourcen und URIs für den übergeordneten Server verarbeiten soll. Der URI-Bereich kann nach Belieben des Administrators mithilfe dieser Blöcke unterteilt werden. Es ist ein extrem flexibles Modell.</p>

<h2 id="wie-nginx-entscheidet-welcher-serverblock-eine-abfrage-verarbeiten-wird">Wie Nginx entscheidet, welcher Serverblock eine Abfrage verarbeiten wird</h2>

<p>Da der Administrator mit Nginx mehrere Serverblöcke definieren kann, die als separate virtuelle Webserverinstanzen fungieren, muss festgelegt werden, welcher dieser Serverblöcke zur Erfüllung einer Abfrage verwendet wird.</p>

<p>Dies geschieht durch ein definiertes System von Überprüfungen, mit denen die bestmögliche Übereinstimmung gefunden wird. Die wichtigsten Serverblock-Direktiven, mit denen sich Nginx während dieses Prozesses befasst, sind die Direktive <code>listen</code> und <code>server_name</code>.</p>

<h3 id="analysieren-der-„listen“-direktive-um-mögliche-Übereinstimmungen-zu-finden">Analysieren der „listen“-Direktive, um mögliche Übereinstimmungen zu finden</h3>

<p>Zunächst überprüft Nginx die IP-Adresse und den Port der Abfrage. Dies wird mit der <code>listen</code>-Direktive jedes Servers verglichen, um eine Liste der Serverblöcke zu erstellen, die möglicherweise die Abfrage auflösen können.</p>

<p>Die <code>listen</code>-Direktive definiert typischerweise, auf welche IP-Adresse und welchen Port der Serverblock reagieren wird. Standardmäßig erhält jeder Serverblock, der keine <code>listen</code>-Direktive enthält, die listen-Parameter <code>0.0.0.0:80</code> (oder <code>0.0.0.0:8080</code>, wenn Nginx von einem normalen non-root-Benutzer ausgeführt wird). Auf diese Weise können diese Blöcke auf Abfragen an einer beliebigen Schnittstelle an Port 80 antworten, aber dieser Standardwert hat im Serverauswahlprozess nicht viel Gewicht.</p>

<p>Die <code>listen</code>-Direktive kann auf Folgendes eingestellt werden:</p>

<ul>
<li>Eine Kombination aus IP-Adresse und Port.</li>
<li>Eine einzelne IP-Adresse, die dann den Standardport 80 überwacht.</li>
<li>Ein einzelner Port, der jede Schnittstelle an diesem Port überwacht.</li>
<li>Der Pfad zu einem Unix-Socket.</li>
</ul>

<p>Die letzte Option hat im Allgemeinen nur Auswirkungen beim Übergeben von Abfragen zwischen verschiedenen Servern.</p>

<p>Wenn Sie versuchen, zu bestimmen, an welchen Serverblock eine Abfrage gesendet wird, wird Nginx zunächst versuchen, anhand der Spezifität der <code>listen</code>-Direktive mit den folgenden Regeln zu entscheiden:</p>

<ul>
<li>Nginx übersetzt alle „unvollständigen“ <code>listen</code>-Direktiven, indem fehlende Werte mit den Standardwerten ersetzt werden, sodass jeder Block durch seine IP-Adresse und den Port bewertet werden kann. Einige Beispiele für diese Übersetzungen sind:

<ul>
<li>Ein Block ohne <code>listen</code>-Direktive verwendet den Wert <code>0.0.0.0:80</code>.</li>
<li>Ein Block, der auf eine IP-Adresse <code>111.111.111.111</code> ohne Port festgelegt ist, wird zu <code>111.111.111.111:80</code></li>
<li>Ein Block, der auf Port <code>8888</code> ohne IP-Adresse festgelegt ist, wird zu <code>0.0.0.0:8888</code></li>
</ul></li>
<li>Nginx versucht dann, eine Liste der Serverblöcke zu sammeln, die der Abfrage am spezifischsten entsprechen, basierend auf der IP-Adresse und dem Port. Dies bedeutet, dass ein Block, der funktional <code>0.0.0.0</code> als IP-Adresse verwendet (um mit einer beliebigen Schnittstelle übereinzustimmen), nicht ausgewählt wird, wenn übereinstimmende Blöcke vorhanden sind, in denen eine bestimmte IP-Adresse aufgeführt ist. In jedem Fall muss der Port genau übereinstimmen.</li>
<li>Wenn nur eine spezifischste Übereinstimmung vorhanden ist, wird dieser Serverblock verwendet, um die Abfrage zu bedienen. Wenn mehrere Serverblöcke mit derselben Spezifitätsübereinstimmung vorhanden sind, beginnt Nginx mit der Auswertung der Direktive <code>server_name</code> jedes Serverblocks.</li>
</ul>

<p>Es ist wichtig, zu verstehen, dass Nginx die Direktive <code>server_name</code> nur dann auswertet, wenn zwischen Serverblöcken unterschieden werden muss, die der gleichen Spezifitätsstufe in der <code>listen</code>-Direktive entsprechen. Wenn beispielsweise <code>example.com</code> auf Port <code>80</code> von <code>192.168.1.10</code> gehostet wird, wird eine Abfrage für <code>example.com</code> in diesem Beispiel trotz der Direktive <code>server_name</code> im zweiten Block immer vom ersten Block bedient.</p>
<pre class="code-pre "><code>server {
    listen 192.168.1.10;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}
</code></pre>
<p>Für den Fall, dass mehr als ein Serverblock mit gleicher Spezifität übereinstimmt, besteht der nächste Schritt darin, die Direktive <code>server_name</code> zu überprüfen.</p>

<h3 id="analysieren-der-direktive-„server_name“-um-eine-Übereinstimmung-auszuwählen">Analysieren der Direktive „server_name“, um eine Übereinstimmung auszuwählen</h3>

<p>Um Abfragen mit gleichermaßen spezifischen <code>listen</code>-Direktiven weiter auszuwerten, überprüft Nginx die „Host“-Überschrift der Abfrage. Dieser Wert enthält die Domain oder IP-Adresse, die der Client tatsächlich versucht hat, zu erreichen.</p>

<p>Nginx versucht, die beste Übereinstimmung für den gefundenen Wert zu finden, indem es sich die Direktive <code>server_name</code> in jedem der Serverblöcke ansieht, die noch Auswahlkandidaten sind. Nginx bewertet diese durch die folgende Formel:</p>

<ul>
<li>Nginx versucht zunächst, einen Serverblock mit einem <code>server_name</code> zu finden, der dem Wert in der „Host“-Überschrift der Abfrage <em>genau</em> entspricht. Wenn dieser gefunden wird, wird der zugeordnete Block verwendet, um die Abfrage zu bedienen. Wenn mehrere genaue Übereinstimmungen gefunden werden, wird die <strong>erste</strong> verwendet.</li>
<li>Wenn keine genaue Übereinstimmung gefunden wird, versucht Nginx, einen Serverblock mit einem <code>server_name</code> zu finden, der mit einem führenden Platzhalter übereinstimmt (angezeigt durch ein <code>*</code> am Anfang des Namens in der Konfiguration). Wenn eine gefunden wird, wird der zugeordnete Block verwendet, um die Abfrage zu bedienen. Wenn mehrere Übereinstimmungen gefunden werden, wird die <strong>längste</strong> Übereinstimmung verwendet, um die Abfrage zu bedienen.</li>
<li>Wenn mit einem führenden Platzhalter keine Übereinstimmung gefunden wird, sucht Nginx nach einem Serverblock mit einem <code>server_name</code>, der mit einem nachfolgenden Platzhalter übereinstimmt (angezeigt durch einen Servernamen, der in der Konfiguration mit einem <code>*</code> endet). Wenn eine gefunden wird, wird dieser Block verwendet, um die Abfrage zu bedienen. Wenn mehrere Übereinstimmungen gefunden werden, wird die <strong>längste</strong> Übereinstimmung verwendet, um die Abfrage zu bedienen.</li>
<li>Wenn mit einem nachgestellten Platzhalter keine Übereinstimmung gefunden wird, wertet Nginx Serverblöcke aus, die den <code>server_name</code> mithilfe regulärer Ausdrücke definieren (angezeigt durch ein <code>~</code> vor dem Namen). Der <strong>erste</strong> <code>server_name</code> mit einem regulären Ausdruck, der der „Host“-Überschrift entspricht, wird zur Bearbeitung der Abfrage verwendet.</li>
<li>Wenn keine Übereinstimmung mit regulären Ausdrücken gefunden wird, wählt Nginx den Standardserverblock für diese IP-Adresse und diesen Port aus.</li>
</ul>

<p>Jede Kombination aus IP-Adresse und Port verfügt über einen Standardserverblock, der verwendet wird, wenn mit den oben genannten Methoden keine Vorgehensweise festgelegt werden kann. Bei einer Kombination aus IP-Adresse und Port ist dies entweder der erste Block in der Konfiguration oder der Block, der die Option <code>default_server</code> als Teil der <code>listen</code>-Direktive enthält (die den zuerst gefundenen Algorithmus überschreiben würde). Pro IP-Adresse/Port-Kombination kann nur eine <code>default_server</code>-Deklaration vorhanden sein.</p>

<h3 id="beispiele">Beispiele</h3>

<p>Wenn ein <code>server_name</code> definiert ist, der genau mit dem „Host“-Überschriftswert übereinstimmt, wird dieser Serverblock ausgewählt, um die Abfrage zu verarbeiten.</p>

<p>Wenn in diesem Beispiel die „Host“-Überschrift der Abfrage auf „host1.example.com“ gesetzt wäre, würde der zweite Server ausgewählt:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name *.example.com;

    . . .

}

server {
    listen 80;
    server_name host1.example.com;

    . . .

}
</code></pre>
<p>Wenn keine genaue Übereinstimmung gefunden wird, prüft Nginx, ob ein <code>server_name</code> mit einem passenden Start-Platzhalter vorhanden ist. Die längste Übereinstimmung mit einem Platzhalter wird ausgewählt, um die Abfrage zu erfüllen.</p>

<p>Wenn in diesem Beispiel die Abfrage einer „Host“-Überschrift „www.example.org“ hat, würde der zweite Serverblock ausgewählt:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name www.example.*;

    . . .

}

server {
    listen 80;
    server_name *.example.org;

    . . .

}

server {
    listen 80;
    server_name *.org;

    . . .

}
</code></pre>
<p>Wenn mit einem Start-Platzhalter keine Übereinstimmung gefunden wird, prüft Nginx anhand eines Platzhalters am Ende des Ausdrucks, ob eine Übereinstimmung vorliegt. An diesem Punkt wird die längste Übereinstimmung mit einem Platzhalter ausgewählt, um die Abfrage zu bedienen.</p>

<p>Wenn beispielsweise die Abfrage eine „Host“-Überschrift auf „www.example.com“ festgelegt hat, wird der dritte Serverblock ausgewählt:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name host1.example.com;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name www.example.*;

    . . .

}
</code></pre>
<p>Wenn keine Platzhalterübereinstimmungen gefunden werden können, versucht Nginx, Übereinstimmungen mit den Direktiven <code>server_name</code> zuzuordnen, die reguläre Ausdrücke verwenden. Der <em>erste</em> übereinstimmende reguläre Ausdruck wird ausgewählt, um auf die Abfrage zu antworten.</p>

<p>Wenn beispielsweise die „Host“-Überschrift der Abfrage auf „www.example.com“ gesetzt ist, wird der zweite Serverblock ausgewählt, um die Abfrage zu erfüllen:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name ~^(www|host1).*\.example\.com$;

    . . .

}

server {
    listen 80;
    server_name ~^(subdomain|set|www|host1).*\.example\.com$;

    . . .

}
</code></pre>
<p>Wenn keiner der oben genannten Schritte die Abfrage erfüllen kann, wird die Abfrage an den <em>Standardserver</em> für die übereinstimmende IP-Adresse und den passenden Port weitergeleitet.</p>

<h2 id="Übereinstimmung-von-standortblöcken">Übereinstimmung von Standortblöcken</h2>

<p>Ähnlich wie bei dem Prozess, mit dem Nginx den Serverblock auswählt, der eine Abfrage verarbeitet, verfügt Nginx auch über einen etablierten Algorithmus zur Entscheidung, welcher Standortblock innerhalb des Servers zur Verarbeitung von Abfragen verwendet werden soll.</p>

<h3 id="standortblock-syntax">Standortblock-Syntax</h3>

<p>Bevor wir uns damit befassen, wie Nginx entscheidet, welcher Standortblock zur Verarbeitung von Abfragen verwendet werden soll, gehen wir einen Teil der Syntax durch, der möglicherweise in Standortblockdefinitionen angezeigt wird. Standortblöcke befinden sich in Serverblöcken (oder anderen Standortblöcken) und werden verwendet, um zu entscheiden, wie der Abfrage-URI (der Teil der Abfrage, der nach dem Domainnamen oder der IP-Adresse/dem IP-Port kommt) verarbeitet werden soll.</p>

<p>Standortblöcke haben im Allgemeinen die folgende Form:</p>
<pre class="code-pre "><code>location <span class="highlight">optional_modifier</span> <span class="highlight">location_match</span> {

    . . .

}
</code></pre>
<p>Das oben angezeigte <code><span class="highlight">location_match</span></code> definiert, gegen was Nginx den Abfrage-URI prüfen soll. Das Vorhandensein oder Nichtvorhandensein des Modifikators im obigen Beispiel beeinflusst die Art und Weise, wie der Nginx versucht, mit dem Standortblock übereinzustimmen. Die folgenden Modifikatoren bewirken, dass der zugehörige Standortblock wie folgt interpretiert wird:</p>

<ul>
<li><strong>(none)</strong>: Wenn keine Modifikatoren vorhanden sind, wird der Speicherort als <em>Präfix</em>-Übereinstimmung interpretiert. Dies bedeutet, dass der angegebene Speicherort mit dem Beginn des Abfrage-URIs abgeglichen wird, um eine Übereinstimmung zu ermitteln.</li>
<li><strong><code>=</code></strong>: Wenn ein Gleichheitszeichen verwendet wird, wird dieser Block als Übereinstimmung betrachtet, wenn der Abfrage-URI genau mit dem angegebenen Standort übereinstimmt.</li>
<li><strong><code>~</code></strong>: Wenn ein Tilde-Modifikator vorhanden ist, wird dieser Speicherort als Übereinstimmung zwischen regulären Ausdrücken und Groß- und Kleinschreibung interpretiert.</li>
<li><strong><code>~*</code></strong>: Wenn ein Tilde- und ein Sternchen-Modifikator verwendet werden, wird der Positionsblock als Übereinstimmung zwischen regulären Ausdrücken ohne Berücksichtigung der Groß- und Kleinschreibung interpretiert.</li>
<li><strong><code>^~</code></strong>: Wenn ein Karat- und Tilde-Modifikator vorhanden ist und dieser Block als beste Übereinstimmung mit nicht regulären Ausdrücken ausgewählt ist, findet keine Übereinstimmung mit regulären Ausdrücken statt.</li>
</ul>

<h3 id="beispiele-zur-demonstration-der-standortblock-syntax">Beispiele zur Demonstration der Standortblock-Syntax</h3>

<p>Als Beispiel für die Präfixübereinstimmung kann der folgende Standortblock ausgewählt werden, um auf Abfrage-URIs zu antworten, die wie <code>/site</code>, <code>/site/page1/index.html</code> oder <code>/site/index.html</code> aussehen:</p>
<pre class="code-pre "><code>location /site {

    . . .

}
</code></pre>
<p>Zur Demonstration der genauen Übereinstimmung der Abfrage-URI wird dieser Block immer verwendet, um auf eine Abfrage-URI zu antworten, die wie <code>/page1</code> aussieht. Es wird <strong>nicht</strong> verwendet, um auf eine <code>/page1/index.html</code>-Abfrage-URI zu antworten. Beachten Sie, dass bei Auswahl dieses Blocks und Erfüllung der Abfrage über eine Indexseite eine interne Umleitung an einen anderen Speicherort erfolgt, der der eigentliche Handhaber der Abfrage ist:</p>
<pre class="code-pre "><code>location = /page1 {

    . . .

}
</code></pre>
<p>Als Beispiel für einen Speicherort, der als regulärer Ausdruck mit Groß- und Kleinschreibung interpretiert werden sollte, kann dieser Block verwendet werden, um Abfragen für <code>/tortoise.jpg</code> zu verarbeiten, <strong>nicht</strong> jedoch für <code>/FLOWER.PNG</code>:</p>
<pre class="code-pre "><code>location ~ \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Ein Block, der eine Übereinstimmung ohne Berücksichtigung der Groß- und Kleinschreibung ähnlich wie oben ermöglichen würde, ist unten dargestellt. Hier könnten sowohl <code>/tortoise.jpg</code> <em>als auch</em> <code>/FLOWER.PNG</code> von diesem Block verarbeitet werden:</p>
<pre class="code-pre "><code>location ~* \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Schließlich würde dieser Block verhindern, dass eine Übereinstimmung mit regulären Ausdrücken auftritt, wenn festgestellt wird, dass dies die beste Übereinstimmung mit nicht regulären Ausdrücken ist. Er könnte Abfragen für <code>/costumes/ninja.html</code> verarbeiten:</p>
<pre class="code-pre "><code>location ^~ /costumes {

    . . .

}
</code></pre>
<p>Wie Sie sehen, geben die Modifikatoren an, wie der Standortblock interpretiert werden soll. Dies sagt uns jedoch <em>nicht</em>, welchen Algorithmus Nginx verwendet, um zu entscheiden, an welchen Standortblock die Abfrage gesendet werden soll. Wir werden das als nächstes durchgehen.</p>

<h3 id="wie-nginx-wählt-welcher-standort-zur-bearbeitung-von-abfragen-verwendet-werden-soll">Wie Nginx wählt, welcher Standort zur Bearbeitung von Abfragen verwendet werden soll</h3>

<p>Nginx wählt den Speicherort aus, an dem eine Abfrage bearbeitet wird, ähnlich wie bei der Auswahl eines Serverblocks. Es wird ein Prozess durchlaufen, der den besten Standortblock für eine bestimmte Abfrage ermittelt. Das Verständnis dieses Prozesses ist eine entscheidende Anforderung, um Nginx zuverlässig und korrekt konfigurieren zu können.</p>

<p>Unter Berücksichtigung der oben beschriebenen Arten von Standortdeklarationen bewertet Nginx die möglichen Standortkontexte, indem der Abfrage-URI mit jedem der Standorte verglichen wird. Dies geschieht mit dem folgenden Algorithmus:</p>

<ul>
<li>Nginx überprüft zunächst alle Präfix-basierten Standortübereinstimmungen (alle Standorttypen, die keinen regulären Ausdruck enthalten). Es vergleicht jeden Standort mit dem vollständigen Abfrage-URI.</li>
<li>Zunächst sucht Nginx nach einer genauen Übereinstimmung Wenn ein Standortblock mit dem Modifikator <code>=</code> gefunden wird, der genau mit dem Abfrage-URI übereinstimmt, wird dieser Standortblock sofort ausgewählt, um die Abfrage zu bedienen.</li>
<li>Wenn keine genauen (mit dem Modifikator <code>=</code>) Positionsblockübereinstimmungen gefunden werden, fährt Nginx mit der Auswertung nicht exakter Präfixe fort. Es ermittelt den am längsten übereinstimmenden Präfixspeicherort für den angegebenen Abfrage-URI, den es dann wie folgt auswertet:

<ul>
<li>Wenn der am längsten übereinstimmende Präfixstandort den Modifikator <code>^ ~</code> hat, beendet Nginx die Suche sofort und wählt diesen Standort aus, um die Abfrage zu bearbeiten.</li>
<li>Wenn die Position mit dem längsten übereinstimmenden Präfix <em>nicht</em> den Modifikator <code>^ ~</code> verwendet, wird die Übereinstimmung für den Moment von Nginx gespeichert, damit der Fokus der Suche verschoben werden kann.</li>
</ul></li>
<li>Nachdem die Position mit der längsten Übereinstimmung ermittelt und gespeichert wurde, fährt Nginx mit der Auswertung der Positionen für reguläre Ausdrücke fort (sowohl Groß- und Kleinschreibung als auch Nicht-Groß-/Kleinschreibung beachten). Wenn sich Positionen <em>mit regulären Ausdrücken innerhalb</em> der am längsten übereinstimmenden Präfixposition befinden, verschiebt Nginx diese an den Anfang der Liste der zu überprüfenden Regex-Positionen. Nginx versucht dann, nacheinander mit den Positionen der regulären Ausdrücke abzugleichen. Der <strong>erste</strong> reguläre Ausdruck des Standortes wird sofort ausgewählt, um die Abfrage zu bedienen.</li>
<li>Wenn keine Standorte für reguläre Ausdrücke gefunden werden, die mit dem Abfrage-URI übereinstimmen, wird der zuvor gespeicherte Präfixstandort ausgewählt, um die Abfrage zu bedienen.</li>
</ul>

<p>Es ist wichtig, zu verstehen, dass Nginx standardmäßig Übereinstimmungen mit regulären Ausdrücken anstelle von Präfixübereinstimmungen bereitstellt. Es werden jedoch zuerst Präfixpositionen <em>ausgewertet</em>, sodass die Verwaltung diese Tendenz überschreiben kann, indPositionen mit den Modifikatoren <code>=</code> und <code>^ ~</code> angegeben werden.</p>

<p>Es ist auch wichtig, zu beachten, dass, während Präfixpositionen im Allgemeinen basierend auf der längsten, spezifischsten Übereinstimmung ausgewählt werden, die Auswertung regulärer Ausdrücke gestoppt wird, wenn die erste übereinstimmende Position gefunden wird. Dies bedeutet, dass die Positionierung innerhalb der Konfiguration enorme Auswirkungen auf die Positionen regulärer Ausdrücke hat.</p>

<p>Schließlich ist es wichtig, zu verstehen, dass Übereinstimmungen <em>mit regulären Ausdrücken innerhalb</em> der längsten Präfixübereinstimmung „die Zeile überspringen“, wenn Nginx Regex-Positionen auswertet. Diese werden der Reihe nach ausgewertet, bevor andere Übereinstimmungen mit regulären Ausdrücken berücksichtigt werden. Maxim Dounin, ein unglaublich hilfreicher Nginx-Entwickler, erklärt in <a href="https://www.ruby-forum.com/topic/4422812#1136698">diesem Beitrag</a> diesen Teil des Auswahlalgorithmus.</p>

<h3 id="wann-springt-die-standortblockbewertung-zu-anderen-standorten">Wann springt die Standortblockbewertung zu anderen Standorten?</h3>

<p>Wenn ein Standortblock ausgewählt wird, um eine Abfrage zu bedienen, wird die Abfrage im Allgemeinen von diesem Punkt an vollständig in diesem Kontext behandelt. Nur der ausgewählte Standort und die geerbten Anweisungen bestimmen, wie die Abfrage verarbeitet wird, ohne dass Geschwisterstandortblöcke eingreifen.</p>

<p>Obwohl dies eine allgemeine Regel ist, mit der Sie Ihre Standortblöcke auf vorhersehbare Weise entwerfen können, ist es wichtig zu wissen, dass es manchmal Zeiten gibt, in denen eine neue Standortsuche durch bestimmte Anweisungen innerhalb des ausgewählten Standortes ausgelöst wird. Die Ausnahmen von der Regel „Nur ein Standortblock“ können Auswirkungen auf die tatsächliche Zustellung der Abfrage haben und stimmen möglicherweise nicht mit den Erwartungen überein, die Sie beim Entwerfen Ihrer Standortblöcke hatten.</p>

<p>Einige Direktiven, die zu dieser Art der internen Weiterleitung führen können, sind:</p>

<ul>
<li><strong>index</strong></li>
<li><strong>try_files</strong></li>
<li><strong>rewrite</strong></li>
<li><strong>error_page</strong></li>
</ul>

<p>Gehen wir diese kurz durch.</p>

<p>Die Direktive <code>index</code> führt immer zu einer internen Weiterleitung, wenn sie verwendet wird, um die Abfrage zu verarbeiten. Genaue Standortübereinstimmungen werden häufig verwendet, um den Auswahlprozess zu beschleunigen, indem die Ausführung des Algorithmus sofort beendet wird. Wenn Sie jedoch eine genaue Standortübereinstimmung mit einem <em>Verzeichnis</em> vornehmen, besteht eine gute Chance, dass die Abfrage zur tatsächlichen Verarbeitung an einen anderen Standort umgeleitet wird.</p>

<p>In diesem Beispiel wird der erste Standort mit einem Abfrage-URI von <code>/exact</code> abgeglichen. Um die Abfrage zu verarbeiten, initiiert die vom Block geerbte <code>Indexanweisung</code> eine interne Umleitung zum zweiten Block:</p>
<pre class="code-pre "><code>index index.html;

location = /exact {

    . . .

}

location / {

    . . .

}
</code></pre>
<p>Wenn Sie im obigen Fall wirklich die Ausführung benötigen, um im ersten Block zu bleiben, müssen Sie eine andere Methode finden, um die Abfrage an das Verzeichnis zu erfüllen. Sie können beispielsweise einen ungültigen <code>index</code> für diesen Block festlegen und den <code>autoindex</code> aktivieren:</p>
<pre class="code-pre "><code>location = /exact {
    index nothing_will_match;
    autoindex on;
}

location  / {

    . . .

}
</code></pre>
<p>Dies ist eine Möglichkeit, um zu verhindern, dass ein <code>index</code> den Kontext wechselt, ist jedoch für die meisten Konfigurationen wahrscheinlich nicht hilfreich. Meistens kann eine genaue Übereinstimmung mit Verzeichnissen hilfreich sein, um beispielsweise die Abfrage neu zu schreiben (was auch zu einer neuen Standortsuche führt).</p>

<p>Eine andere Instanz, in der der Verarbeitungsort neu bewertet werden kann, ist die Direktive <code>try_files</code>. Diese Direktive weist Nginx an, das Vorhandensein einer benannten Gruppe von Dateien oder Verzeichnissen zu überprüfen. Der letzte Parameter kann ein URI sein, zu dem Nginx eine interne Umleitung vornimmt.</p>

<p>Erwägen Sie folgende Konfiguration:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>Wenn im obigen Beispiel eine Anfrage für <code>/blahblah</code> gestellt wird, erhält der erste Standort zunächst die Abfrage. Er wird versuchen, eine Datei namens <code>blahblah</code> im Verzeichnis <code>/var/www/main</code> zu finden. Wenn gefunden werden kann, wird anschließend nach einer Datei mit dem Namen <code>blahblah.html</code> gesucht. Anschließend wird versucht, festzustellen, ob sich im Verzeichnis <code>/var/www/main</code> ein Verzeichnis mit dem Namen <code>blahblah/</code> befindet. Wenn alle diese Versuche fehlschlagen, wird zu <code>/fallback/index.html</code> umgeleitet. Dies löst eine weitere Standortsuche aus, die vom zweiten Standortblock abgefangen wird. Dies wird die Datei <code>/var/www/anderen/fallback/index.html</code> bereitstellen.</p>

<p>Eine weitere Direktive, die dazu führen kann, dass ein Standortblock übergeben wird, ist die Direktive <code>rewrite</code>. Wenn Sie den <code>letzten</code> Parameter mit der Direktive <code>rewrite</code> zum Umschreiben oder überhaupt keinen Parameter verwenden, sucht Nginx basierend auf den Ergebnissen des Umschreibens nach einem neuen übereinstimmenden Standort.</p>

<p>Wenn wir beispielsweise das letzte Beispiel so ändern, dass es ein Umschreiben enthält, können wir feststellen, dass die Abfrage manchmal direkt an den zweiten Standort übergeben wird, ohne sich auf die Direktive <code>try_files</code> zu verlassen:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    rewrite ^/rewriteme/(.*)$ /$1 last;
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>Im obigen Beispiel wird eine Abfrage für <code>/rewriteme/hello</code> zunächst vom ersten Standortblock verarbeitet. Sie wird in <code>/hello</code> umgeschrieben und ein Standort gesucht. In diesem Fall stimmt sie wieder mit dem ersten Standort überein und wird wie gewohnt von den <code>try_files</code> verarbeitet. Wenn nichts gefunden wird, kehren Sie möglicherweise zu <code>/fallback/index.html</code> zurück (mithilfe der oben beschriebenen internen Umleitung <code>try_files</code>).</p>

<p>Wenn jedoch eine Abfrage für <code>/rewriteme/fallback/hello</code> gestellt wird, stimmt der erste Block erneut überein. Das Umschreiben wird erneut angewendet, diesmal mit <code>/fallback/hello</code>. Die Abfrage wird dann aus dem zweiten Standortblock heraus zugestellt.</p>

<p>Eine verwandte Situation tritt bei der Direktive <code>return</code> auf, wenn die Statuscodes <code>301</code> oder <code>302</code> gesendet werden. Der Unterschied in diesem Fall ist, dass es eine völlig neue Abfrage in Form einer extern sichtbaren Umleitung bildet. Dieselbe Situation kann bei der Direktive <code>rewrite</code> auftreten, wenn die Flags <code>redirect</code> oder <code>permanent</code> verwendet werden. Diese Standortsuche sollte jedoch nicht unerwartet sein, da extern sichtbare Weiterleitungen immer zu einer neuen Abfrage führen.</p>

<p>Die Direktive <code>error_page</code> kann zu einer internen Umleitung führen, die der von <code>try_files</code> erstellten ähnelt. Diese Direktive wird verwendet, um zu definieren, was passieren soll, wenn bestimmte Statuscodes aufgetreten sind. Dies wird wahrscheinlich nie ausgeführt, wenn <code>try_files</code> festgelegt ist, da diese Direktive den gesamten Lebenszyklus einer Abfrage behandelt.</p>

<p>Erwägen Sie dieses Beispiel:</p>
<pre class="code-pre "><code>root /var/www/main;

location / {
    error_page 404 /another/whoops.html;
}

location /another {
    root /var/www;
}
</code></pre>
<p>Jede Abfrage (außer denjenigen, die mit <code>/another</code> beginnen) wird vom ersten Block bearbeitet, der Dateien aus <code>/var/www/main</code> bereitstellt. Wenn jedoch keine Datei gefunden wird (Status 404), erfolgt eine interne Umleitung zu <code>/another/whoops.html</code>, die zu einer neuen Standortsuche führt, die schließlich im zweiten Block landet. Diese Datei wird aus <code>/var/www/another/whoops.html</code>. bereitgestellt.</p>

<p>Wie Sie sehen können, kann das Verständnis der Umstände, unter denen Nginx eine neue Standortsuche auslöst, dazu beitragen, das Verhalten vorherzusagen, das bei Abfragen auftreten wird.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Wenn Sie wissen, wie Nginx Client-Abfragen verarbeitet, können Sie Ihre Arbeit als Administrator erheblich vereinfachen. Sie können anhand jeder Client-Abfrage wissen, welchen Serverblock Nginx auswählt. Sie können auch anhand der Abfrage-URI festlegen, wie der Standortblock ausgewählt wird. Wenn Sie wissen, wie Nginx verschiedene Blöcke auswählt, können Sie die Kontexte verfolgen, die Nginx anwenden wird, um jede Abfrage zu bearbeiten.</p>
