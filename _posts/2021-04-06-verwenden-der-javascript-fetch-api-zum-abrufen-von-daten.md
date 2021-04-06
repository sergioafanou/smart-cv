---
title : "Verwenden der JavaScript-Fetch-API zum Abrufen von Daten"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-fetch-api-to-get-data-de
image: "https://sergio.afanou.com/assets/images/image-midres-11.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Es gab eine Zeit, als <code>XMLHttpRequest</code> verwendet wurde, um API-Anfragen zu erstellen. Es enthielt keine Promises und sorgte nicht für einen sauberen JavaScript-Code. Mit jQuery verwendeten Sie die saubere Syntax mit <code>jQuery.ajax()</code>.</p>

<p>Jetzt verfügt JavaScript über eine eigene integrierte Möglichkeit, API-Anfragen zu erstellen. Dies ist die Fetch-API, ein neuer Standard, um Serveranfragen mit Promises zu erstellen, der aber noch viele andere Funktionen enthält.</p>

<p>In diesem Tutorial erstellen Sie mit der Fetch-API sowohl GET- als auch POST-Anfragen.</p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Um dieses Tutorial zu absolvieren, benötigen Sie Folgendes:</p>

<ul>
<li>Auf Ihrem Rechner installierte neueste Version von Node. Zur Installation von Node unter macOS folgen Sie den im Tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">Installieren von Node.js und Erstellen einer lokalen Entwicklungsumgebung unter macOS</a> beschriebenen Schritten.</li>
<li>Ein grundlegendes Verständnis der Programmierung in JavaScript, über das Sie <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">in der Reihe Programmieren in JavaScript</a> mehr erfahren können.</li>
<li>Ein Verständnis von Promises in JavaScript. Lesen Sie den <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">Abschnitt Promises</a> dieses Artikels über <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">die Ereignisschleife, Callbacks, Promises und async/await</a> in JavaScript.</li>
</ul>

<h2 id="schritt-1-—-erste-schritte-mit-der-fetch-api-syntax">Schritt 1 — Erste Schritte mit der Fetch-API-Syntax</h2>

<p>Um die Fetch-API zu verwenden, rufen Sie die Methode <code>fetch</code> auf, die die URL der API als Parameter akzeptiert:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre>
<p>Fügen Sie nach der Methode <code>fetch()</code> die Promise-Methode <code>then()</code> ein:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function() {

})
</code></pre>
<p>Die Methode <code>fetch()</code> gibt ein Promise zurück. Wenn das zurückgegebene Promise <code>resolve</code> ist, wird die Funktion innerhalb der Methode <code>then()</code> ausgeführt. Diese Funktion enthält den Code für die Handhabung der von der API empfangenen Daten.</p>

<p>Fügen Sie unter der Methode <code>then()</code> die Methode <code>catch()</code> ein:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function() {

});
</code></pre>
<p>Die API, die Sie mit <code>fetch()</code> aufrufen, kann außer Betrieb sein oder es können andere Fehler auftreten. Wenn dies geschieht, wird das Promise <code>reject</code> zurückgegeben. Die Methode <code>catch</code> wird verwendet, um <code>reject</code> zu handhaben. Der Code innerhalb von <code>catch()</code> wird ausgeführt, wenn ein Fehler beim Aufruf der API Ihrer Wahl auftritt.</p>

<p>Zusammengefasst sieht die Verwendung der Fetch-API wie folgt aus:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then(function() {

})
.catch(function() {

});
</code></pre>
<p>Mit einem Verständnis der Syntax zur Verwendung der Fetch-API können Sie nun mit der Verwendung von <code>fetch()</code> für eine echte API fortfahren.</p>

<h2 id="schritt-2-—-verwenden-von-fetch-zum-abrufen-von-daten-aus-einer-api">Schritt 2 — Verwenden von Fetch zum Abrufen von Daten aus einer API</h2>

<p>Die folgenden Codebeispiele basieren auf der <a href="https://randomuser.me">Random User API</a>. Mithilfe der API erhalten Sie zehn Benutzer und zeigen sie auf der Seite mit Vanilla JavaScript an.</p>

<p>Die Idee ist, alle Daten von der Random User API zu erhalten und in Listenelementen innerhalb der Liste des Autors anzuzeigen. Beginnen Sie mit der Erstellung einer HTML-Datei und dem Hinzufügen einer Überschrift und einer ungeordneten Liste mit der <code>id</code> der <code>authors</code>:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;h1&gt;Authors&lt;/h1&gt;
&lt;ul id="authors"&gt;&lt;/ul&gt;
</code></pre>
<p>Fügen Sie nun <code>script</code>-Tags am Ende Ihrer HTML-Datei und verwenden Sie einen DOM-Selektor zum Ergreifen von <code>ul</code>. Verwenden Sie <code>getElementById</code> mit <code>authors</code> als Argument. Denken Sie daran: <code>authors</code> ist die <code>id</code> für die zuvor erstellte <code>ul</code>:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;

    const ul = document.getElementById('authors');

&lt;/script&gt;
</code></pre>
<p>Erstellen Sie eine konstante Variable namens <code>url</code>, die die API enthält, die zehn zufällige Benutzer zurückgibt:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api/?results=10';
</code></pre>
<p>Mit erstellten <code>ul</code> und <code>url</code> ist es an der Zeit, die Funktionen zu erstellen, die zur Erstellung der Listenelemente verwendet werden. Erstellen Sie eine Funktion namens <code>createNode</code>, die einen Parameter namens <code>element</code> aufnimmt:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {

}
</code></pre>
<p>Wenn <code>createNode</code> später aufgerufen wird, müssen Sie den Namen eines tatsächlichen HTML-Elements übergeben, das erstellt werden soll.</p>

<p>Fügen Sie innerhalb der Funktion eine Anweisung <code>return</code> hinzu, die <code>element</code> unter Verwendung von <code>document.createElement()</code> zurückgibt:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}
</code></pre>
<p>Sie müssen auch eine Funktion namens <code>append</code> erstellen, die zwei Parameter aufnimmt: <code>parent</code> und <code>el</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {

}
</code></pre>
<p>Diese Funktion fügt <code>el</code> an <code>parent</code> mit <code>document.createElement</code> an:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {
    return parent.appendChild(el);
}
</code></pre>
<p>Sowohl <code>createNode</code> als auch <code>append</code> sind einsatzbereit. Rufen Sie nun mit der Fetch-API die Random User API unter Verwendung von <code>fetch()</code> mit <code>url</code> als Argument auf:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre><pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
  .then(function(data) {

    })
  })
  .catch(function(error) {

  });
</code></pre>
<p>Im obigen Code rufen Sie die Fetch-API auf, und übergeben die URL an die Random User API. Dann wird eine Antwort empfangen. Die Antwort, die Sie erhalten, ist jedoch nicht JSON, sondern ein Objekt mit einer Reihe von Methoden, die je nachdem, was Sie mit den Informationen tun möchten, verwendet werden können. Um das zurückgegebene Objekt in JSON zu konvertieren, verwenden Sie die Methode <code>json()</code>.</p>

<p>Fügen Sie die Methode <code>then()</code> hinzu, die eine Funktion mit einem Parameter namens <code>resp</code> enthält:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; )
</code></pre>
<p>Der Parameter <code>resp</code> nimmt den Wert des von <code>fetch(url)</code> zurückgegebenen Objekts an. Verwenden Sie die Methode <code>json()</code>, um <code>resp</code> in JSON-Daten zu konvertieren:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; resp.json())
</code></pre>
<p>Die JSON-Daten müssen noch verarbeitet werden. Fügen Sie eine weitere Anweisung <code>then()</code> mit einer Funktion hinzu, die ein Argument namens <code>data</code> aufweist:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {

    })
})
</code></pre>
<p>Erstellen Sie innerhalb dieser Funktion eine Variable namens <code>authors</code>, die gleich <code>data.results</code> gesetzt wird:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {
    let authors = data.results;
</code></pre>
<p>Sie sollten für jeden Autor in <code>authors</code> ein Listenelement erstellen, das ein Bild und den Namen des Autors anzeigt. Dafür eignet sich die Methode <code>map()</code> hervorragend:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let authors = data.results;
return authors.map(function(author) {

})
</code></pre>
<p>Erstellen Sie innerhalb Ihrer Funktion <code>map</code> eine Variable namens <code>li</code>, die gleich <code>createNode</code> mit <code>li</code> (dem HTML-Element) als Argument gesetzt wird:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">return authors.map(function(author) {
    let li = createNode('li');
})
</code></pre>
<p>Wiederholen Sie dies, um ein Element <code>span</code> und ein Element <code>img</code> zu erstellen:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let li = createNode('li');
let img = createNode('img');
let span = createNode('span');
</code></pre>
<p>Die API bietet einen Namen für den Autor und ein Bild, das zu diesem Namen gehört. Setzen Sie <code>img.src</code> auf das Bild des Autors:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let img = createNode('img');
let span = createNode('span');

img.src = author.picture.medium;
</code></pre>
<p>Das Element <code>span</code> sollte den Vor- und Nachnamen des <code>author</code> enthalten. Mit der Eigenschaft <code>innerHTML</code> und der String-Interpolation können Sie dies tun:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">img.src = author.picture.medium;
span.innerHTML = `${author.name.first} ${author.name.last}`;
</code></pre>
<p>Mit dem Bild und dem Listenelement, die zusammen mit dem Element <code>span</code> erstellt wurden, können Sie die zuvor erstellte Funktion <code>append</code> verwenden, um diese Elemente auf der Seite anzuzeigen:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">append(li, img);
append(li, span);
append(ul, li);
</code></pre>
<p>Da beide Funktionen <code>then()</code> abgeschlossen sind, können Sie nun die Funktion <code>catch()</code> hinzufügen. Diese Funktion protokolliert den potenziellen Fehler auf der Konsole:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Dies ist der vollständige Code der von Ihnen erstellten Anfrage:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}

function append(parent, el) {
  return parent.appendChild(el);
}

const ul = document.getElementById('authors');
const url = 'https://randomuser.me/api/?results=10';

fetch(url)
.then((resp) =&gt; resp.json())
.then(function(data) {
  let authors = data.results;
  return authors.map(function(author) {
    let li = createNode('li');
    let img = createNode('img');
    let span = createNode('span');
    img.src = author.picture.medium;
    span.innerHTML = `${author.name.first} ${author.name.last}`;
    append(li, img);
    append(li, span);
    append(ul, li);
  })
})
.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Sie haben gerade erfolgreich eine GET-Anfrage mit der Random User API und der Fetch-API durchgeführt. Im nächsten Schritt lernen Sie, wie Sie POST-Anfragen ausführen.</p>

<h2 id="schritt-3-—-handhaben-von-post-anfragen">Schritt 3 — Handhaben von POST-Anfragen</h2>

<p>Fetch ist standardmäßig auf GET-Anfragen eingestellt, doch Sie können auch alle anderen Arten von Anfragen verwenden, die Kopfzeilen ändern und Daten senden. Dazu müssen Sie Ihr Objekt festlegen und als zweites Argument der Funktion fetch übergeben.</p>

<p>Erstellen Sie vor der Erstellung einer POST-Anfrage die Daten, die Sie an die API senden möchten. Dies wird ein Objekt namens <code>data</code> mit dem Schlüssel <code>name</code> und dem Wert <code>Sammy</code> (oder Ihrem Namen) sein:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sammy'
}
</code></pre>
<p>Stellen Sie sicher, dass Sie eine konstante Variable einfügen, die den Link der Random User API enthält.</p>

<p>Da es sich um eine POST-Anfrage handelt, müssen Sie dies explizit angeben. Erstellen Sie ein Objekt namens <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {

}
</code></pre>
<p>Dieses Objekt muss drei Schlüssel enthalten: <code>method</code>, <code>body</code> und <code>headers</code>. Der Schlüssel <code>method</code> sollte den Wert <code>'POST'</code> haben. <code>body</code> sollte gleich dem gerade erstellten Objekt <code>data</code> gesetzt werden. <code>headers</code> sollte den Wert von <code>new Headers()</code> haben:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {
  method: 'POST',
  body: data,
  headers: new Headers()
}
</code></pre>
<p>Die Schnittstelle <code>Headers</code> ist eine Eigenschaft der Fetch-API, mit der Sie verschiedene Aktionen für HTTP-Anfragen und Antwort-Header durchführen können. Wenn Sie mehr darüber erfahren möchten, finden Sie in diesem Artikel namens <a href="https://www.digitalocean.com/community/tutorials/nodejs-express-routing">Definieren von Routen und HTTP-Anfragemethoden in Express</a> weitere Informationen.</p>

<p>Mit diesem Code kann die POST-Anfrage unter Verwendung der Fetch-API erstellt werden. Sie schließen <code>url</code> und <code>fetchData</code> als Argumente für Ihre POST-Anfrage <code>fetch</code> ein:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
</code></pre>
<p>Die Funktion <code>then()</code> enthält Code, der die vom Random User API-Server empfangene Antwort verarbeitet:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
.then(function() {
    // Handle response you get from the server
});
</code></pre>
<p>Es gibt auch eine andere Möglichkeit zur Erstellung eines Objekts und der Verwendung der Funktion <code>fetch()</code>. Anstatt ein Objekt wie <code>fetchData</code> zu erstellen, können Sie den Anfragekonstruktor verwenden, um Ihr Anfrageobjekt zu erstellen. Erstellen Sie dazu eine Variable namens <code>request</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request =
</code></pre>
<p>Die Variable <code>request</code> sollte gleich <code>new Request</code> gesetzt werden. Das Konstrukt <code>new Request</code> nimmt zwei Argumente an: die API-URL (<code>url</code>) und ein Objekt. Das Objekt sollte auch die Schlüssel <code>method</code>, <code>body</code> und <code>headers</code> enthalten, genau wie <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});
</code></pre>
<p>Jetzt kann <code>request</code> als einziges Argument für <code>fetch()</code> verwendet werden, da es auch die API-URL enthält:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>Insgesamt sieht Ihr Code wie folgt aus:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});

fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>Jetzt kennen Sie zwei Methoden zur Erstellung und Ausführung von POST-Anfragen mit der Fetch-API.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Obwohl die Fetch-API noch nicht von allen Browsern unterstützt wird, ist sie eine gute Alternative zu XMLHttpRequest. Wenn Sie lernen möchten, wie Sie Web-APIs mit React aufrufen, <a href="https://www.digitalocean.com/community/tutorials/how-to-call-web-apis-with-the-useeffect-hook-in-react">lesen Sie diesen Artikel</a> zu diesem Thema.</p>
