---
title : "So bearbeiten Sie eingehende Anfragedaten in Flask"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/processing-incoming-request-data-in-flask-de
image: "https://sergio.afanou.com/assets/images/image-midres-52.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Webanwendungen erfordern häufig die Verarbeitung eingehender Anforderungsdaten von Benutzern. Diese Nutzdaten können in Form von Abfragezeichenfolgen, Formulardaten und JSON-Objekten vorliegen. Mit <a href="https://flask.palletsprojects.com/">Flask</a> können Sie wie mit jedem anderen Webframework auf die Anforderungsdaten zugreifen.</p>

<p>In diesem Tutorial erstellen Sie eine Flask-Anwendung mit drei Routen, die entweder Abfragezeichenfolgen, Formulardaten oder JSON-Objekte akzeptieren.</p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Um diesem Tutorial zu folgen, benötigen Sie:</p>

<ul>
<li>Dieses Projekt erfordert <a href="https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3">die Installation von Python in einer lokalen Umgebung</a>.</li>
<li>In diesem Projekt wird <a href="https://pypi.org/project/pipenv/">Pipenv</a> verwendet, ein produktionsfähiges Tool, mit dem das Beste aus allen Verpackungswelten in die Python-Welt gebracht werden soll. Es nutzt Pipfile, pip und <a href="https://virtualenv.pypa.io/en/latest/">virtualenv</a> in einem einzigen Befehl.</li>
<li>Das Herunterladen und Installieren eines Tools wie <a href="https://www.getpostman.com/">Postman</a> wird benötigt, um API-Endpunkte zu testen.</li>
</ul>

<p>Dieses Tutorial wurde mit Pipenv v2020.11.15, Python v3.9.0 und Flask v1.1.2 verifiziert.</p>

<h2 id="einrichten-des-projekts">Einrichten des Projekts</h2>

<p>Um die verschiedenen Verwendungsmöglichkeiten von Anforderungen zu demonstrieren, müssen Sie eine Flask-App erstellen. Obwohl die Beispiel-App eine vereinfachte Struktur für die Ansichtsfunktionen und -routen verwendet, kann das, was Sie in diesem Tutorial lernen, auf jede Methode zum Organisieren Ihrer Ansichten angewendet werden, z. B. auf klassenbasierte Ansichten, Blaupausen oder eine Erweiterung wie Flask-Via.</p>

<p>Zuerst müssen Sie ein Projektverzeichnis erstellen. Öffnen Sie Ihren Terminal und führen Sie folgenden Befehl aus:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Navigieren Sie dann zum neuen Verzeichnis:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd <span class="highlight">flask_request_example</span>
</li></ul></code></pre>
<p>Installieren Sie als nächstes Flask. Öffnen Sie Ihren Terminal und führen Sie folgenden Befehl aus:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv install Flask
</li></ul></code></pre>
<p>Der Befehl <code>pipenv</code> erstellt eine virtuelle Umgebung für dieses Projekt, eine Pipfile, eine Installations-<code>flask</code> und eine Pipfile.lock.</p>

<p>Führen Sie den folgenden Befehl aus, um virtualenv des Projekts zu aktivieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pipenv shell
</li></ul></code></pre>
<p>Um auf die eingehenden Daten in Flask zuzugreifen, müssen Sie das <code>Anforderungsobjekt</code> verwenden. Das <code>Anforderungsobjekt</code> enthält alle eingehenden Daten aus der Anforderung, einschließlich Mimetyp, Referrer, IP-Adresse, Rohdaten, HTTP-Methode und Überschriften.</p>

<p>Obwohl alle Informationen, die das <code>Anforderungsobjekt</code> enthält, nützlich sein können, konzentrieren Sie sich für die Zwecke dieses Artikels auf die Daten, die normalerweise direkt vom Aufrufer des Endpunkts bereitgestellt werden.</p>

<p>Um Zugriff auf das Anforderungsobjekt in Flask zu erhalten, müssen Sie es aus der Flask-Bibliothek importieren:</p>
<pre class="code-pre "><code class="code-highlight language-python">from flask import request
</code></pre>
<p>Sie können es dann in jeder Ihrer Ansichtsfunktionen verwenden.</p>

<p>Verwenden Sie Ihren Code-Editor, um eine Datei <code>app.py</code> zu erstellen. Importieren Sie <code>Flask</code> und das <code>Anforderungsobjekt</code>. Und erstellen Sie auch Routen für <code>query-example</code>, <code>form-example</code> und <code>json-example</code>:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># import main Flask class and request object
from flask import Flask, request

# create the Flask app
app = Flask(__name__)

@app.route('/query-example')
def query_example():
    return 'Query String Example'

@app.route('/form-example')
def form_example():
    return 'Form Data Example'

@app.route('/json-example')
def json_example():
    return 'JSON Object Example'

if __name__ == '__main__':
    # run app in debug mode on port 5000
    app.run(debug=True, port=5000)
</code></pre>
<p>Öffnen Sie als nächstes Ihr Terminal und starten Sie die App mit dem folgenden Befehl:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="(flask_request_example)">python app.py
</li></ul></code></pre>
<p>Die App wird auf Port 5000 gestartet, sodass Sie jede Route in Ihrem Browser über die folgenden Links anzeigen können:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example (or localhost:5000/query-example)
http://127.0.0.1:5000/form-example (or localhost:5000/form-example)
http://127.0.0.1:5000/json-example (or localhost:5000/json-example)
</code></pre>
<p>Der Code erstellt drei Routen und zeigt die Nachrichten <code>„Beispiel für Abfragezeichenfolge“</code>,<code>„Beispiel für Formulardaten“</code> bzw. <code>„Beispiel für JSON-Objekt“</code> an.</p>

<h2 id="verwenden-von-abfrageargumenten">Verwenden von Abfrageargumenten</h2>

<p>URL-Argumente, die Sie einer Abfragezeichenfolge hinzufügen, sind eine übliche Methode, um Daten an eine Webanwendung zu übergeben. Beim Surfen im Internet sind Sie wahrscheinlich schon einmal auf eine Abfragezeichenfolge gestoßen.</p>

<p>Eine Abfragezeichenfolge ähnelt der folgenden:</p>
<pre class="code-pre "><code>example.com?arg1=value1&amp;arg2=value2
</code></pre>
<p>Die Abfragezeichenfolge beginnt nach dem Fragezeichen (<code>?</code>) Zeichen:</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1&amp;arg2=value2</span>
</code></pre>
<p>Und hat Schlüssel-Wert-Paare, die durch ein kaufmännisches Und (<code>&amp;</code>) getrennt sind:</p>
<pre class="code-pre "><code>example.com?<span class="highlight">arg1=value1</span>&amp;<span class="highlight">arg2=value2</span>
</code></pre>
<p>Für jedes Paar folgt auf den Schlüssel ein Gleichheitszeichen (<code>=</code>) und dann der Wert.</p>
<pre class="code-pre "><code>arg1 : value1
arg2 : value2
</code></pre>
<p>Abfragezeichenfolgen sind nützlich, um Daten zu übergeben, für die der Benutzer keine Maßnahmen ergreifen muss. Sie können irgendwo in Ihrer App eine Abfragezeichenfolge generieren und an eine URL anhängen. Wenn ein Benutzer eine Anfrage stellt, werden die Daten automatisch für ihn übergeben. Eine Abfragezeichenfolge kann auch von Formularen generiert werden, deren Methode GET ist.</p>

<p>Fügen wir der <code>Abfragebeispielroute</code> eine Abfragezeichenfolge hinzu. In diesem hypothetischen Beispiel geben Sie den Namen einer Programmiersprache an, die auf dem Bildschirm angezeigt wird. Erstellen Sie einen Schlüssel für <code>„Sprache“</code> und einen Wert für <code>„Python“</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example<span class="highlight">?language=Python</span>
</code></pre>
<p>Wenn Sie die App ausführen und zu dieser URL navigieren, wird weiterhin die Meldung <code>„Beispiel für eine Abfragezeichenfolge“</code> angezeigt.</p>

<p>Sie müssen den Teil programmieren, der die Abfrageargumente verarbeitet. Dieser Code liest den Schlüssel <code>Sprache</code> durch Verwendung von <code>request.args.get('language')</code> oder <code>request.args.get('language')</code>.</p>

<p>Durch den Aufruf von <code>request.args.get('language')</code> wird die Anwendung weiterhin ausgeführt, wenn der Schlüssel <code>Sprache</code> nicht in der URL vorhanden ist. In diesem Fall ist das Ergebnis der Methode <code>Keine</code>.</p>

<p>Durch den Aufruf von <code>request.args['language']</code> gibt die App einen 400-Fehler zurück, wenn der Schlüssel <code>Sprache</code> nicht in der URL vorhanden ist.</p>

<p>Beim Umgang mit Abfragezeichenfolgen wird empfohlen, <code>request.args.get ()</code> zu verwenden, um zu verhindern, dass die App fehlschlägt.</p>

<p>Lesen wir den Schlüssel <code>Sprache</code> und zeigen ihn als Ausgabe an.</p>

<p>Ändern Sie die Route <code>query-example</code> in <code>app.py</code> mit dem folgenden Code:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    <span class="highlight">language = request.args.get('language')</span>

    <span class="highlight">return '''&lt;h1&gt;The language value is: {}&lt;/h1&gt;'''.format(language)</span>
</code></pre>
<p>Führen Sie dann die App aus und navigieren Sie zur URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python
</code></pre>
<p>Der Browser sollte die folgende Nachricht anzeigen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
</code></pre>
<p>Das Argument aus der URL wird der Variable <code>Sprache</code> zugewiesen und dann an den Browser zurückgegeben.</p>

<p>Um weitere Parameter für Abfragezeichenfolgen hinzuzufügen, können Sie ein kaufmännisches Und und die neuen Schlüssel-Wert-Paare an das Ende der URL anhängen. Erstellen Sie einen Schlüssel für <code>„Framework“</code> und einen Wert für <code>„Flask“</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python<span class="highlight">&amp;framework=Flask</span>
</code></pre>
<p>Wenn Sie mehr möchten, fügen Sie weiterhin ein kaufmännisches Und und Schlüssel-Wert-Paare hinzu. Erstellen Sie einen Schlüssel für <code>„Framework“</code> und einen Wert für <code>„Flask“</code>:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask<span class="highlight">&amp;website=DigitalOcean</span>
</code></pre>
<p>Um Zugriff auf diese Werte zu erhalten, verwenden Sie weiterhin entweder <code>request.args.get()</code> oder <code>request.args[]</code>. Verwenden wir beide, um zu demonstrieren, was passiert, wenn ein Schlüssel fehlt. Ändern Sie die Route <code>query_example</code>, um den Wert der Ergebnisse in Variablen zu zuweisen und sie dann anzuzeigen:</p>
<pre class="code-pre "><code class="code-highlight language-python">@app.route('/query-example')
def query_example():
    # if key doesn't exist, returns None
    language = request.args.get('language')

    # if key doesn't exist, returns a 400, bad request error
    <span class="highlight">framework = request.args['framework']</span>

    # if key doesn't exist, returns None
    <span class="highlight">website = request.args.get('website')</span>

    <span class="highlight">return '''</span>
              <span class="highlight">&lt;h1&gt;The language value is: {}&lt;/h1&gt;</span>
              <span class="highlight">&lt;h1&gt;The framework value is: {}&lt;/h1&gt;</span>
              <span class="highlight">&lt;h1&gt;The website value is: {}'''.format(language, framework, website)</span>
</code></pre>
<p>Führen Sie dann die App aus und navigieren Sie zur URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>Der Browser sollte die folgende Nachricht anzeigen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Entfernen Sie den Schlüssel <code>Sprache</code> aus der URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?framework=Flask&amp;website=DigitalOcean
</code></pre>
<p>Der Browser sollte die folgende Nachricht mit <code>Keine</code> anzeigen, wenn ein Wert nicht für <code>Sprache</code> bereitgestellt wird:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: None
The framework value is: Flask
The website value is: DigitalOcean
</code></pre>
<p>Entfernen Sie den Schlüssel <code>Framework</code> aus der URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/query-example?language=Python&amp;website=DigitalOcean
</code></pre>
<p>Der Browser sollte auf einen Fehler stoßen, da er einen Wert für <code>Framework</code> erwartet:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>werkzeug.exceptions.BadRequestKeyError
werkzeug.exceptions.BadRequestKeyError: 400 Bad Request: The browser (or proxy) sent a request that this server could not understand.
KeyError: 'framework'
</code></pre>
<p>Jetzt verstehen Sie den Umgang mit Abfragezeichenfolgen. Fahren wir mit dem nächsten Typ eingehender Daten fort.</p>

<h2 id="verwenden-von-formulardaten">Verwenden von Formulardaten</h2>

<p>Formulardaten stammen aus einem Formular, das als POST-Abfrage an eine Route gesendet wurde. Anstatt die Daten in der URL anzuzeigen (außer in Fällen, in denen das Formular mit einer GET-Abfrage gesendet wird), werden die Formulardaten hinter den Kulissen an die App übergeben. Obwohl Sie die Formulardaten nicht einfach sehen können, die übergeben werden, kann Ihre App sie weiterhin lesen.</p>

<p>Um dies zu demonstrieren, ändern Sie die <code>Formularbeispielroute</code> in <code>app.py</code>, um sowohl GET- als auch POST-Abfragen zu akzeptieren, und geben Sie ein Formular zurück:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># allow both GET and POST requests
@app.route('/form-example'<span class="highlight">, methods=['GET', 'POST']</span>)
def form_example():
    <span class="highlight">return '''</span>
              <span class="highlight">&lt;form method="POST"&gt;</span>
                  <span class="highlight">&lt;div&gt;&lt;label&gt;Language: &lt;input type="text" name="language"&gt;&lt;/label&gt;&lt;/div&gt;</span>
                  <span class="highlight">&lt;div&gt;&lt;label&gt;Framework: &lt;input type="text" name="framework"&gt;&lt;/label&gt;&lt;/div&gt;</span>
                  <span class="highlight">&lt;input type="submit" value="Submit"&gt;</span>
              <span class="highlight">&lt;/form&gt;'''</span>
</code></pre>
<p>Führen Sie dann die App aus und navigieren Sie zur URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Der Browser sollte ein Formular mit zwei Eingabefeldern - einem für <code>Sprache</code> und einem für <code>Framework</code> - und eine Senden-Taste übergeben.</p>

<p>Das Wichtigste, was Sie über dieses Formular wissen müssen, ist, dass es eine POST-Abfrage an dieselbe Route ausführt, die das Formular generiert hat. Die Schlüssel, die in der App gelesen werden, stammen alle aus den <code>Namensattributen</code> in unseren Formulareingaben. In diesem Fall sind <code>Sprache</code> und <code>Framework</code> die Namen der Eingaben, sodass Sie Zugriff auf die in der App haben.</p>

<p>Innerhalb der Ansichtsfunktion müssen Sie überprüfen, ob die Abfragemethode GET oder POST ist. Wenn es sich um eine GET-Abfrage handelt, können Sie das Formular anzeigen. Wenn es sich um eine POST-Abfrage handelt, möchten Sie die eingehenden Daten verarbeiten.</p>

<p>Ändern Sie die Route <code>form-example</code> in <code>app.py</code> mit dem folgenden Code:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># allow both GET and POST requests
@app.route('/form-example', methods=['GET', 'POST'])
def form_example():
    # handle the POST request
    <span class="highlight">if request.method == 'POST':</span>
        <span class="highlight">language = request.form.get('language')</span>
        <span class="highlight">framework = request.form.get('framework')</span>
        <span class="highlight">return '''</span>
                  <span class="highlight">&lt;h1&gt;The language value is: {}&lt;/h1&gt;</span>
                  <span class="highlight">&lt;h1&gt;The framework value is: {}&lt;/h1&gt;'''.format(language, framework)</span>

    # otherwise handle the GET request
    return '''
           &lt;form method="POST"&gt;
               &lt;div&gt;&lt;label&gt;Language: &lt;input type="text" name="language"&gt;&lt;/label&gt;&lt;/div&gt;
               &lt;div&gt;&lt;label&gt;Framework: &lt;input type="text" name="framework"&gt;&lt;/label&gt;&lt;/div&gt;
               &lt;input type="submit" value="Submit"&gt;
           &lt;/form&gt;'''
</code></pre>
<p>Führen Sie dann die App aus und navigieren Sie zur URL:</p>
<pre class="code-pre "><code>http://127.0.0.1:5000/form-example
</code></pre>
<p>Füllen Sie das Feld <code>Sprache</code> mit dem Wert von <code>Python</code> und das Feld <code>Framework</code> mit dem Wert von <code>Flask</code> aus. Drücken Sie dann <strong>Senden</strong>.</p>

<p>Der Browser sollte die folgende Nachricht anzeigen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
</code></pre>
<p>Jetzt verstehen Sie den Umgang mit Formulardaten. Fahren wir mit dem nächsten Typ eingehender Daten fort.</p>

<h2 id="verwenden-von-json-daten">Verwenden von JSON-Daten</h2>

<p>JSON-Daten werden normalerweise von einem Prozess erstellt, der die Route aufruft.</p>

<p>Ein Beispiel-JSON-Objekt sieht folgendermaßen aus:</p>
<pre class="code-pre "><code class="code-highlight language-json">{
  "language": "Python",
  "framework": "Flask",
  "website": "Scotch",
  "version_info": {
    "python": "3.9.0",
    "flask": "1.1.2"
  },
  "examples": ["query", "form", "json"],
  "boolean_test": true
}
</code></pre>
<p>Diese Struktur kann die Übergabe von viel komplizierteren Daten im Gegensatz zu Abfragezeichenfolgen und Formulardaten ermöglichen. Im Beispiel sehen Sie verschachtelte JSON-Objekte und eine Anordnung von Elementen. Flask kann dieses Format von Daten verarbeiten.</p>

<p>Ändern Sie die Route <code>form-example</code> in <code>app.py</code>, um POST-Abfragen zu akzeptieren und andere Abfragen wie GET zu ignorieren:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python">@app.route('/json-example'<span class="highlight">, methods=['POST']</span>)
def json_example():
    return 'JSON Object Example'
</code></pre>
<p>Im Gegensatz zu dem Webbrowser, der für Abfragezeichenfolgen und Formulardaten zum Senden eines JSON-Objekts in diesem Artikel verwendet wird, verwenden Sie <a href="https://www.getpostman.com/">Postman</a>, um benutzerdefinierte Anforderungen an URLs zu senden.</p>

<p><span class='note'><strong>Hinweis</strong>: Wenn Sie Hilfe benötigen, um Postman für Abfragen zu navigieren, konsultieren Sie <a href="https://learning.postman.com/docs/postman/sending-api-requests/requests/">die offizielle Dokumentation</a>.<br></span></p>

<p>Fügen Sie in Postman die URL hinzu und ändern Sie den Typ in <strong>POST</strong>. Wechseln Sie auf der Registerkarte Body zu <strong>raw</strong> und wählen Sie <strong>JSON</strong> aus der Dropdown-Liste aus.</p>

<p>Diese Einstellungen sind erforderlich, sodass Postman JSON-Daten richtig senden kann und Ihre Flask-App versteht, dass sie JSON empfängt:</p>
<pre class="code-pre "><code>POST http://127.0.0.1:5000/json-example
Body
raw JSON
</code></pre>
<p>Kopieren Sie als Nächstes das frühere JSON-Beispiel in die Texteingabe.</p>

<p>Senden Sie die Abfrage und Sie sollten <code>„Beispiel eines JSON-Objekts“</code> als Antwort erhalten. Das ist ziemlich antiklimatisch, aber zu erwarten, da der Code für die Verarbeitung der JSON-Datenantwort noch nicht geschrieben wurde.</p>

<p>Um die Daten zu lesen, müssen Sie verstehen, wie Flask JSON-Daten in Python-Datenstrukturen übersetzt:</p>

<ul>
<li>Alles, was ein Objekt ist, wird in ein Python-Diktat konvertiert. <code>{"key": value "}</code> in JSON entspricht <code>somedict['key']</code>, das in Python einen Wert zurückgibt.</li>
<li>Eine Anordnung in JSON wird in Python in eine Liste konvertiert. Da die Syntax die gleiche ist, ist hier eine Beispielliste: <code>[1,2,3,4,5]</code></li>
<li>Die Werte in Anführungszeichen im JSON-Objekt werden Zeichenfolgen in Python.</li>
<li>Boolean <code>wahr</code> und <code>falsch</code> werden in Python zu <code>Wahr</code> und <code>Falsch</code>.</li>
<li>Abschließend werden Zahlen ohne Anführungszeichen in Python zu Zahlen.</li>
</ul>

<p>Arbeiten wir nun an dem Code, um die eingehenden JSON-Daten zu lesen.</p>

<p>Zuerst weisen wir alles aus dem JSON-Objekt mit <code>request.get_json()</code> einer Variable zu.</p>

<p><code>request.get_json()</code> konvertiert das JSON-Objekt in Python-Daten. Weisen wir die eingehenden Abfragedaten den Variablen zu, und geben sie zurück, indem wir die folgenden Änderungen an der Route <code>json-example</code> vornehmen:</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># GET requests will be blocked
@app.route('/json-example', methods=['POST'])
def json_example():
    <span class="highlight">request_data = request.get_json()</span>

    <span class="highlight">language = request_data['language']</span>
    <span class="highlight">framework = request_data['framework']</span>

    # two keys are needed because of the nested object
    <span class="highlight">python_version = request_data['version_info']['python']</span>

    # an index is needed because of the array
    <span class="highlight">example = request_data['examples'][0]</span>

    <span class="highlight">boolean_test = request_data['boolean_test']</span>

    <span class="highlight">return '''</span>
           <span class="highlight">The language value is: {}</span>
           <span class="highlight">The framework value is: {}</span>
           <span class="highlight">The Python version is: {}</span>
           <span class="highlight">The item at index 0 in the example list is: {}</span>
           <span class="highlight">The boolean value is: {}'''.format(language, framework, python_version, example, boolean_test)</span>
</code></pre>
<p>Beachten Sie, wie Sie auf Elemente zugreifen, die nicht auf der oberen Ebene sind. <code>['version']['python']</code> wird verwendet, da Sie ein verschachteltes Objekt eingeben. Und <code>['examples'][0]</code> wird verwendet, um auf den 0. Index in der Anordnung der Beispiele zuzugreifen.</p>

<p>Wenn das mit der Abfrage gesendete JSON-Objekt keinen Schlüssel hat, auf den in Ihrer Ansichtsfunktion zugegriffen wird, wird die Abfrage fehlschlagen. Wenn Sie nicht möchten, dass es fehlschlägt, wenn ein Schlüssel nicht vorhanden ist, müssen Sie überprüfen, ob der Schlüssel vorhanden ist, bevor Sie versuchen, darauf zuzugreifen.</p>
<div class="code-label " title="app.py">app.py</div><pre class="code-pre "><code class="code-highlight language-python"># GET requests will be blocked
@app.route('/json-example', methods=['POST'])
def json_example():
    request_data = request.get_json()

    <span class="highlight">language = None</span>
    <span class="highlight">framework = None</span>
    <span class="highlight">python_version = None</span>
    <span class="highlight">example = None</span>
    <span class="highlight">boolean_test = None</span>

    <span class="highlight">if request_data:</span>
        <span class="highlight">if 'language' in request_data:</span>
            language = request_data['language']

        <span class="highlight">if 'framework' in request_data:</span>
            framework = request_data['framework']

        <span class="highlight">if 'version_info' in request_data:</span>
            <span class="highlight">if 'python' in request_data['version_info']:</span>
                python_version = request_data['version_info']['python']

        <span class="highlight">if 'examples' in request_data:</span>
            <span class="highlight">if (type(request_data['examples']) == list) and (len(request_data['examples']) &gt; 0):</span>
                example = request_data['examples'][0]

        <span class="highlight">if 'boolean_test' in request_data:</span>
            boolean_test = request_data['boolean_test']

    return '''
           The language value is: {}
           The framework value is: {}
           The Python version is: {}
           The item at index 0 in the example list is: {}
           The boolean value is: {}'''.format(language, framework, python_version, example, boolean_test)
</code></pre>
<p>Führen Sie die App aus und senden Sie die Beispiel-JSON-Abfrage mit Postman. In der Antwort erhalten Sie die folgende Ausgabe:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The language value is: Python
The framework value is: Flask
The Python version is: 3.9
The item at index 0 in the example list is: query
The boolean value is: false
</code></pre>
<p>Jetzt verstehen Sie die Verarbeitung von JSON-Objekten.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>In diesem Artikel haben Sie eine Flask-Anwendung mit drei Routen erstellt, die entweder Abfragezeichenfolgen, Formulardaten oder JSON-Objekte akzeptieren.</p>

<p>Denken Sie auch daran, dass alle Ansätze die wiederkehrende Überlegung berücksichtigen mussten, ob ein Schlüssel ordnungsgemäß fehlschlägt, wenn ein Schlüssel fehlt.</p>

<p><span class='warning'><strong>Warnung:</strong> ein Thema, das in diesem Artikel nicht behandelt wurde, war die Bereinigung von Benutzereingaben. Durch die Bereinigung von Benutzereingaben wird sichergestellt, dass von der Anwendung gelesene Daten nicht unerwartet fehlschlagen oder Sicherheitsmaßnahmen umgehen.<br></span></p>

<p>Wenn Sie mehr über Flask erfahren möchten, lesen Sie <a href="https://www.digitalocean.com/community/tags/flask">unsere Themenseite zu Flask</a> für Übungen und Programmierprojekte.</p>
