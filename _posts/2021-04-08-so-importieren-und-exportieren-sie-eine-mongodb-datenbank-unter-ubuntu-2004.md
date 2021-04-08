---
title : "So importieren und exportieren Sie eine MongoDB-Datenbank unter Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04-de
image: "https://sergio.afanou.com/assets/images/image-midres-49.jpg"
---

<p><em>Der Autor hat den <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> dazu ausgewählt, eine Spende im Rahmen des Programms <a href="https://do.co/w4do-cta">Write for DOnations</a> zu erhalten.</em></p>

<h3 id="einführung">Einführung</h3>

<p>MongoDB ist eine der beliebtesten NoSQL-Datenbank-Engines. Es ist berühmt für seine Skalierbarkeit, Leistung, Zuverlässigkeit und Benutzerfreundlichkeit. In diesem Artikel zeigen wir Ihnen, wie Sie Ihre MongoDB-Datenbanken importieren und exportieren.</p>

<p>Wir sollten klarstellen, dass unter Import und Export diejenigen Vorgänge zu verstehen sind, die Daten in einem für Menschen lesbaren Format verarbeiten, das mit anderen Softwareprodukten kompatibel ist. Im Gegensatz dazu erstellen oder verwenden die Sicherungs- und Wiederherstellungsvorgänge MongoDB-spezifische Binärdaten, wodurch die Konsistenz und Integrität Ihrer Daten sowie deren spezifische MongoDB-Attribute erhalten bleiben. Daher ist es für die Migration normalerweise vorzuziehen, Backup und Wiederherstellung zu verwenden, solange das Quell- und das Zielsystem kompatibel sind.</p>

<p>Backup-, Wiederherstellungs- und Migrationsaufgaben gehen über den Rahmen dieses Artikels hinaus. Weitere Informationen finden Sie unter <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">So sichern Sie eine MongoDB-Datenbank unter Ubuntu 20.04</a>.</p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Um dieses Tutorial zu absolvieren, benötigen Sie Folgendes:</p>

<ul>
<li>Ein Ubuntu 20.04-Droplet, das gemäß dem <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Ubuntu 20.04-Handbuch zur Ersteinrichtung des Servers</a> eingerichtet wurde, einschließlich eines Sudo-Nicht-Root-Benutzers und einer Firewall.</li>
<li>MongoDB installiert und konfiguriert mithilfe des Artikels „<a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Installieren von MongoDB unter Ubuntu 20.04</a>“.</li>
<li>Ein Verständnis der Unterschiede zwischen JSON- und BSON-Daten in MongoDB. Für eine ausführliche Diskussion lesen Sie <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04#step-1-%E2%80%94-using-json-and-bson-in-mongodb"><strong>Schritt eins — Verwenden von JSON und JSON in MongoDB</strong> in unserem Tutorial zum Sichern, Wiederherstellen und Migrieren einer MongoDB-Datenbank unter Ubuntu 20.04</a>.</li>
</ul>

<h2 id="schritt-1-mdash-importieren-von-informationen-in-mongodb">Schritt 1 — Importieren von Informationen in MongoDB</h2>

<p>Um zu erfahren, wie Informationen in MongoDB importiert werden, verwenden wir eine beliebte Beispiel-MongoDB-Datenbank über Restaurants. Sie ist im .json-Format und kann mit <code>wget</code> wie folgt heruntergeladen werden:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json
</li></ul></code></pre>
<p>Sobald der Download abgeschlossen ist, sollten Sie eine Datei mit dem Namen <code>primer-dataset.json</code> (12 MB Größe) im aktuellen Verzeichnis haben. Importieren wir die Daten aus dieser Datei in eine neue Datenbank namens <code>newdb</code> und in eine Sammlung mit dem Namen <code>Restaurants</code>.</p>

<p>Verwenden Sie den Befehl <code>mongoimport</code> wie folgt:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoimport --db newdb --collection restaurants --file primer-dataset.json
</li></ul></code></pre>
<p>Das Ergebnis sieht folgendermaßen aus:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:37:55.607+0000    connected to: mongodb://localhost/
2020-11-11T19:37:57.841+0000    25359 document(s) imported successfully. 0 document(s) failed to import
</code></pre>
<p>Wie der obige Befehl zeigt, wurden 25359 Dokumente importiert. Da wir keine Datenbank mit dem Namen <code>newdb</code> haben, hat MongoDB sie automatisch erstellt.</p>

<p>Überprüfen wir den Import.</p>

<p>Stellen Sie eine Verbindung mit der neu erstellten <code>newdb</code>-Datenbank her:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Sie sind nun mit der <code>newdb</code>-Datenbankinstanz verbunden. Beachten Sie, dass sich Ihre Eingabeaufforderung geändert hat, was anzeigt, dass Sie mit der Datenbank verbunden sind.</p>

<p>Zählen Sie die Dokumente in der Sammlung Restaurants mit dem Befehl:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.count()
</li></ul></code></pre>
<p>Das Ergebnis zeigt <code>25359</code>, was die Anzahl importierter Dokumente ist. Für eine noch bessere Überprüfung können Sie das erste Dokument aus der Sammlung Restaurants wie folgt auswählen:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.findOne()
</li></ul></code></pre>
<p>Das Ergebnis sieht folgendermaßen aus:</p>
<pre class="code-pre "><code>[secondary label Output]
{
    "_id" : ObjectId("5fac3d937f12c471b3f26733"),
    "address" : {
        "building" : "1007",
        "coord" : [
            -73.856077,
            40.848447
        ],
        "street" : "Morris Park Ave",
        "zipcode" : "10462"
    },
    "borough" : "Bronx",
    "cuisine" : "Bakery",
    "grades" : [
        {
            "date" : ISODate("2014-03-03T00:00:00Z"),
            "grade" : "A",
            "score" : 2
        },
...
    ],
    "name" : "Morris Park Bake Shop",
    "restaurant_id" : "30075445"
}
</code></pre>
<p>Eine solche detaillierte Überprüfung kann Probleme mit den Dokumenten wie ihrem Inhalt, der Codierung usw. enthüllen. Das Format json verwendet <code>UTF-8</code>-Codierung und Ihre Exporte und Importe sollten in dieser Codierung vorhanden sein. Denken Sie daran, wenn Sie die json-Dateien manuell bearbeiten. Andernfalls verarbeitet MongoDB dies automatisch für Sie.</p>

<p>Um die MongoDB-Eingabeaufforderung zu beenden, geben Sie <code>exit</code> an die Eingabeaufforderung ein:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Sie werden als non-root user zur normalen Befehlszeilenaufforderung zurückgegeben.</p>

<h2 id="schritt-2-mdash-exportieren-von-informationen-aus-mongodb">Schritt 2 — Exportieren von Informationen aus MongoDB</h2>

<p>Wie bereits erwähnt, können Sie durch den Export von MongoDB-Informationen eine für Menschen lesbare Textdatei mit Ihren Daten abrufen. Standardmäßig werden Informationen im JSON-Format exportiert. Sie können sie jedoch auch nach CSV exportieren (durch Kommas getrennter Wert).</p>

<p>Um Informationen aus MongoDB zu exportieren, verwenden Sie den Befehl <code>mongoexport</code>. Es ermöglicht Ihnen, einen sehr feinkörnigen Export zu exportieren, sodass Sie eine Datenbank, eine Sammlung, ein Feld und sogar eine Abfrage für den Export angeben können.</p>

<p>Ein einfaches <code>mongoexport</code>-Beispiel wäre die Sammlung Restaurants aus der <code>newdb</code>-Datenbank, die wir zuvor importiert haben. Dies kann wie folgt erfolgen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants --out newdbexport.json
</li></ul></code></pre>
<p>Im obigen Befehl verwenden wir <code>--db</code>, um die Datenbank <code>-c</code> für die Sammlung und <code>--out</code> für die Datei zu spezifizieren, in der die Daten gespeichert werden.</p>

<p>Die Ausgabe eines erfolgreichen <code>mongoexport</code> sollte folgendermaßen aussehen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:39:57.595+0000    connected to: mongodb://localhost/
2020-11-11T19:39:58.619+0000    [###############.........]  newdb.restaurants  16000/25359  (63.1%)
2020-11-11T19:39:58.871+0000    [########################]  newdb.restaurants  25359/25359  (100.0%)
2020-11-11T19:39:58.871+0000    exported 25359 records
</code></pre>
<p>Die obige Ausgabe zeigt, dass 25359 Dokumente importiert wurden, die gleiche Anzahl wie die importierten sind.</p>

<p>In einigen Fällen müssen Sie möglicherweise nur einen Teil Ihrer Sammlung exportieren. Angesichts der Struktur und den Inhalt der Restaurants-json-Datei exportieren wir alle Restaurants, die die Kriterien erfüllen, die sich in der Bronx befinden und chinesische Küche anbieten. Wenn wir diese Informationen direkt während der Verbindung mit MongoDB erhalten möchten, stellen Sie erneut eine Verbindung zur Datenbank her:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Verwenden Sie dann diese Abfrage:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.find( { "borough": "Bronx", "cuisine": "Chinese" } )
</li></ul></code></pre>
<p>Die Ergebnisse werden dem Terminal angezeigt:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">2020-12-03T01:35:25.366+0000    connected to: mongodb://localhost/
</li><li class="line" data-prefix="$">2020-12-03T01:35:25.410+0000    exported 323 records
</li></ul></code></pre>
<p>Um die MongoDB-Eingabeaufforderung zu beenden, geben Sie <code>exit</code> ein:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Wenn Sie die Daten von einer sudo-Befehlszeile exportieren möchten, anstatt mit der Datenbank verbunden zu sein, machen Sie die vorherige Abfrage zu einem Teil des <code>mongoexport</code>-Befehls, indem Sie sie für das Argument <code>-q</code> wie folgt angeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants -q "{\"borough\": \"Bronx\", \"cuisine\": \"Chinese\"}" --out Bronx_Chinese_retaurants.json
</li></ul></code></pre>
<p>Beachten Sie, dass wir die doppelten Anführungszeichen mit Backslash (<code>\</code>) in der Abfrage umgehen. Ähnlich müssen Sie alle anderen Sonderzeichen in der Abfrage umgehen.</p>

<p>Wenn der Export erfolgreich war, sollte das Ergebnis folgendermaßen aussehen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:49:21.727+0000    connected to: mongodb://localhost/
2020-11-11T19:49:21.765+0000    exported 323 records
</code></pre>
<p>Das obige Beispiel zeigt, dass 323 Datensätze exportiert wurden, und Sie können sie in der von uns angegebenen Datei <code>Bronx_Chinese_retaurants.json</code> finden.</p>

<p>Verwenden Sie <code>cat</code> und <code>less</code>, um die Daten zu durchsuchen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat Bronx_Chinese_retaurants.json | less
</li></ul></code></pre>
<p>Verwenden Sie <code>SPACE</code>, um die Daten zu übermitteln:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">date":{"$date":"2015-01-14T00:00:00Z"},"grade":"Z","score":36}],"na{"_id":{"$oid":"5fc8402d141f5e54f9054f8d"},"address":{"building":"1236","coord":[-73.8893654,40.81376179999999],"street":"238 Spofford Ave","zipcode":"10474"},"borough":"Bronx","cuisine":"Chinese","grades":[{"date":{"$date":"2013-12-30T00:00:00Z"},"grade":"A","score":8},{"date":{"$date":"2013-01-08T00:00:00Z"},"grade":"A","score":10},{"date":{"$date":"2012-06-12T00:00:00Z"},"grade":"B","score":15}],
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">. . .
</li></ul></code></pre>
<p>Drücken Sie <code>q</code>, um zu beenden. Sie können nun eine MongoDB-Datenbank importieren und exportieren.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Dieser Artikel hat Sie in die Grundlagen zum Importieren und Exportieren von Informationen zu und aus einer MongoDB-Datenbank eingeführt. Sie können weitere Informationen über <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">So sichern Sie eine MongoDB-Datenbank unter Ubuntu 20.04</a> lesen.</p>

<p>Sie können auch die Replikation berücksichtigen. Durch die Replikation können Sie Ihren MongoDB-Dienst ohne Unterbrechung von einem Slave-MongoDB-Server aus fortsetzen, während Sie den Master-Dienst nach einem Fehler wiederherstellen. Teil der Replikation ist das <a href="https://docs.mongodb.org/manual/core/replica-set-oplog/">Betriebsprotokoll (oplog)</a>, das alle Operationen aufzeichnet, die Ihre Daten ändern. Sie können dieses Protokoll genauso verwenden wie das Binärprotokoll in MySQL, um Ihre Daten nach dem letzten Backup wiederherzustellen. Denken Sie daran, dass Backups normalerweise nachts stattfinden. Wenn Sie sich entscheiden, abends einen Backup wiederherzustellen, fehlen Ihnen alle Aktualisierungen seit dem letzten Backup.</p>
