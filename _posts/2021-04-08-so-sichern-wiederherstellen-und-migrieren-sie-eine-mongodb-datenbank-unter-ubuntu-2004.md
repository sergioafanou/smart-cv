---
title : "So sichern, wiederherstellen und migrieren Sie eine MongoDB-Datenbank unter Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04-de
image: "https://sergio.afanou.com/assets/images/image-midres-8.jpg"
---

<p><em>Der Autor hat den <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> dazu ausgewählt, eine Spende im Rahmen des Programms <a href="https://do.co/w4do-cta">Write for DOnations</a> zu erhalten.</em></p>

<h2 id="einführung">Einführung</h2>

<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">MongoDB</a> ist eine der beliebtesten NoSQL-Datenbank-Engines. Es ist bekannt für seine Skalierbarkeit, Robustheit, Zuverlässigkeit und Benutzerfreundlichkeit. In diesem Artikel werden Sie eine Beispiel-MongoDB-Datenbank sichern, wiederherstellen und migrieren.</p>

<p>Beim Importieren und Exportieren einer Datenbank werden Daten in einem für Menschen lesbaren Format verarbeitet, das mit anderen Softwareprodukten kompatibel ist. Im Gegensatz dazu erstellen oder verwenden die Sicherungs- und Wiederherstellungsvorgänge von MongoDB MongoDB-spezifische Binärdaten, wodurch nicht nur die Konsistenz und Integrität Ihrer Daten, sondern auch die spezifischen MongoDB-Attribute erhalten bleiben. Daher ist es für die Migration normalerweise vorzuziehen, Backup und Wiederherstellung zu verwenden, solange das Quell- und das Zielsystem kompatibel sind.</p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Bevor Sie diesem Tutorial folgen, stellen Sie bitte sicher, dass Sie die folgenden Voraussetzungen erfüllen:</p>

<ul>
<li><a href="https://www.digitalocean.com/products/droplets/">Ein Ubuntu 20.04-Droplet</a>, das gemäß dem <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Ubuntu 20.04-Handbuch zur Ersteinrichtung des Servers</a> eingerichtet wurde, einschließlich eines Sudo-Nicht-Root-Benutzers und einer Firewall.</li>
<li>MongoDB installiert und konfiguriert mithilfe des Artikels „<a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Installieren von MongoDB unter Ubuntu 20.04</a>“.</li>
<li>Beispiel MongoDB-Datenbank importiert mit den Anweisungen in <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04">How To Import and Export a MongoDB Database</a></li>
</ul>

<p>Sofern nicht anders angegeben, sollten alle Befehle, für die in diesem Tutorial-Root-Berechtigungen erforderlich sind, als Nicht-Root-Benutzer mit Sudo-Berechtigungen ausgeführt werden.</p>

<h2 id="schritt-1-mdash-verwenden-von-json-und-bson-in-mongodb">Schritt 1 — Verwenden von JSON und BSON in MongoDB</h2>

<p>Bevor Sie mit diesem Artikel fortfahren, ist ein grundlegendes Verständnis der Angelegenheit erforderlich. Wenn Sie Erfahrung mit anderen NoSQL-Datenbanksystemen wie Redis haben, können Sie bei der Arbeit mit MongoDB einige Ähnlichkeiten feststellen.</p>

<p>MongoDB verwendet die Formate <a href="http://json.org/">JSON</a> und BSON (binäres JSON) zum Speichern seiner Informationen. JSON ist das für Menschen lesbare Format, das sich perfekt zum Exportieren und eventuell zum Importieren Ihrer Daten eignet. Sie können Ihre exportierten Daten mit jedem Tool, das JSON unterstützt, einschließlich eines einfachen Texteditors, weiter verwalten.</p>

<p>Ein Beispiel-JSON-Dokument sieht folgendermaßen aus:</p>
<div class="code-label " title="Example of JSON Format">Example of JSON Format</div><pre class="code-pre "><code class="code-highlight language-bash">{"address":[
    {"building":"1007", "street":"Park Ave"},
    {"building":"1008", "street":"New Ave"},
]}
</code></pre>
<p>JSON ist zum Arbeiten praktisch, unterstützt jedoch nicht alle in BSON verfügbaren Datentypen. Dies bedeutet, dass es bei Verwendung von JSON zu einem sogenannten „Verlust der Wiedergabetreue“ der Informationen kommt. Zum Sichern und Wiederherstellen ist es besser, das binäre BSON zu verwenden.</p>

<p>Zweitens müssen Sie sich nicht um die explizite Erstellung einer MongoDB-Datenbank kümmern. Wenn die für den Import angegebene Datenbank noch nicht vorhanden ist, wird sie automatisch erstellt. Noch besser ist die Struktur der Sammlungen (Datenbanktabellen). Im Gegensatz zu anderen Datenbank-Engines wird in MongoDB die Struktur beim Einfügen des ersten Dokuments (Datenbankzeile) automatisch neu erstellt.</p>

<p>Drittens kann das Lesen oder Einfügen großer Datenmengen, wie z. B. die Aufgaben dieses Artikels, in MongoDB ressourcenintensiv sein und einen Großteil Ihrer CPU, Ihres Arbeitsspeichers und Ihres Speicherplatzes beanspruchen. Dies ist entscheidend, da MongoDB häufig für große Datenbanken und Big Data verwendet wird. Die einfachste Lösung für dieses Problem besteht darin, die Exporte und Sicherungen während der Nacht oder außerhalb der Stoßzeiten auszuführen.</p>

<p>Viertens kann die Informationskonsistenz problematisch sein, wenn Sie einen ausgelasteten MongoDB-Server haben, auf dem sich die Informationen während des Datenbankexports oder -sicherungsprozesses ändern. Eine mögliche Lösung für dieses Problem ist <a href="https://docs.mongodb.com/manual/faq/replica-sets/">die Replikation</a>, die Sie möglicherweise in Betracht ziehen, wenn Sie mit dem Thema MongoDB fortfahren.</p>

<p>Während Sie die <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-14-04">Import- und Exportfunktionen</a> zum Sichern und Wiederherstellen Ihrer Daten verwenden können, gibt es bessere Möglichkeiten, um die vollständige Integrität Ihrer MongoDB-Datenbanken sicherzustellen. Um Ihre Daten zu sichern, sollten Sie den Befehl <code>mongodump</code> verwenden. Zum Wiederherstellen verwenden Sie <code>mongorestore</code>. Sehen wir uns an, wie sie funktionieren.</p>

<h2 id="schritt-2-ndash-verwenden-von-mongodump-zum-sichern-einer-mongodb-datenbank">Schritt 2 – Verwenden von <code>mongodump</code> zum Sichern einer MongoDB-Datenbank</h2>

<p>Lassen Sie uns zuerst das Sichern Ihrer MongoDB-Datenbank behandeln.</p>

<p>Ein wesentliches Argument für <code>mongodump</code> ist <code>--db</code>, das den Namen der Datenbank angibt, die Sie sichern möchten. Wenn Sie keinen Datenbanknamen angeben, sichert <code>mongodump</code> alle Ihre Datenbanken. Das zweite wichtige Argument ist <code>--out</code>, das das Verzeichnis definiert, in das die Daten gespeichert werden. Lassen Sie uns beispielsweise die <code>newdb</code>-Datenbank sichern und im Verzeichnis <code>/var/backups/mongobackups</code> speichern. Im Idealfall haben wir jedes unserer Backups in einem Verzeichnis mit dem aktuellen Datum wie <code>/var/backups/mongobackups/10-29-20</code>.</p>

<p>Erstellen Sie zuerst dieses Verzeichnis <code>/var/backups/mongobackups</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir /var/backups/mongobackups
</li></ul></code></pre>
<p>Führen Sie dann <code>mongodump</code> aus:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</li></ul></code></pre>
<p>Sie werden eine Ausgabe wie diese sehen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:22:36.886+0000    writing newdb.restaurants to
2020-10-29T19:22:36.969+0000    done dumping newdb.restaurants (25359 documents)
</code></pre>
<p>Beachten Sie, dass wir im obigen Verzeichnispfad das <code>Datum + „%m-%d-%y“</code> verwendet haben, das automatisch das aktuelle Datum abruft. Auf diese Weise können wir Backups im Verzeichnis wie <code>/var/backups/<span class="highlight">10-29-20</span>/</code> erstellen. Dies ist besonders praktisch, wenn wir die Backups automatisieren.</p>

<p>An diesem Punkt verfügen Sie über eine vollständige Sicherung der <code>newdb</code>-Datenbank im Verzeichnis <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>. Dieses Backup verfügt über alles, um <code>newdb</code> richtig zu restaurieren und ihre sogenannte „Treue“ zu erhalten.</p>

<p>In der Regel sollten Sie regelmäßige Sicherungen durchführen, vorzugsweise wenn der Server am wenigsten ausgelastet ist. Auf diese Weise können Sie den Befehl <code>mongodump</code> als Cron-Job festlegen, sodass er regelmäßig ausgeführt wird, z. B. jeden Tag um 03:03 Uhr.</p>

<p>Um dies zu erreichen, öffnen Sie Crontab, Cron&rsquo;s Editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Beachten Sie, dass Sie beim Ausführen von <code>sudo crontab</code> die Cron-Jobs für den Root-Benutzer bearbeiten. Dies wird empfohlen, da die Crons für Ihren Benutzer möglicherweise nicht ordnungsgemäß ausgeführt werden, insbesondere wenn für Ihr Sudo-Profil eine Passwortüberprüfung erforderlich ist.</p>

<p>Fügen Sie in die Crontab-Eingabeaufforderung den folgenden <code>mongodump</code>-Befehl ein:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">3 3 * * * mongodump --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</code></pre>
<p>Im obigen Befehl lassen wir das Argument <code>--db</code> absichtlich weg, da Sie typischerweise alle Ihre Datenbanken gesichert werden möchten.</p>

<p>Abhängig von Ihrer MongoDB-Datenbankgröße wird Ihnen möglicherweise bald der Speicherplatz mit zu vielen Backups ausgehen. Aus diesem Grund wird auch empfohlen, die alten Backups regelmäßig zu bereinigen oder zu komprimieren.</p>

<p>Um beispielsweise alle Backups zu löschen, die älter als sieben Tage sind, können Sie den folgenden Bash-Befehl verwenden:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</li></ul></code></pre>
<p>Ähnlich wie beim vorherigen <code>mongodump</code>-Befehl können Sie diesen auch als Cron-Job hinzufügen. Er sollte kurz vor dem Start des nächsten Backups ausgeführt werden, z. B. um 03:01 Uhr. Öffnen Sie zu diesem Zweck crontab erneut:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Fügen Sie danach die folgende Zeile ein:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</code></pre>
<p>Speichern und schließen Sie die Datei.</p>

<p>Wenn Sie alle Aufgaben in diesem Schritt ausführen, wird eine ordnungsgemäße Backup-Lösung für Ihre MongoDB-Datenbanken sichergestellt.</p>

<h2 id="schritt-3-ndash-verwenden-von-mongorestore-zum-wiederherstellen-und-migrieren-einer-mongodb-datenbank">Schritt 3 – Verwenden von <code>mongorestore</code> zum Wiederherstellen und Migrieren einer MongoDB-Datenbank</h2>

<p>Wenn Sie Ihre MongoDB-Datenbank aus einem früheren Backup wiederherstellen, wird die genaue Kopie Ihrer MongoDB-Informationen zu einem bestimmten Zeitpunkt erstellt, einschließlich aller Indizes und Datentypen. Dies ist besonders nützlich, wenn Sie Ihre MongoDB-Datenbanken migrieren möchten. Zum Wiederherstellen von MongoDB verwenden wir den Befehl <code>mongorestore</code>, der mit den binären Backups funktioniert, die <code>mongodump</code> produziert.</p>

<p>Lassen Sie uns unsere Beispiele mit der <code>newdb</code>-Datenbank fortsetzen und sehen, wie wir sie aus dem zuvor erstellten Backup wiederherstellen können. Wir geben zuerst den Namen der Datenbank mit dem Argument <code>--nsInclude</code> an. Wir verwenden <code>newdb.*</code> zum Wiederherstellen aller Sammlungen. Um eine einzelne Sammlung wie <code>Restaurant</code>s wiederherzustellen, verwenden Sie stattdessen <code>newdb.restaurants</code>.</p>

<p>Wenn wir dann <code>--drop</code> verwenden, stellen wir sicher, dass die Zieldatenbank zuerst weggelassen wird, sodass das Backup in einer sauberen Datenbank wiederhergestellt wird. Als letztes Argument geben wir das Verzeichnis des letzten Backups an, das in etwa so aussieht: <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<p>Sobald Sie ein zeitgestempeltes Backup haben, können Sie dieses mit folgendem Befehl wiederherstellen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongorestore --db newdb --drop /var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/
</li></ul></code></pre>
<p>Sie werden eine Ausgabe wie diese sehen:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:25:45.825+0000    the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --nsInclude instead
2020-10-29T19:25:45.826+0000    building a list of collections to restore from /var/backups/mongobackups/10-29-20/newdb dir
2020-10-29T19:25:45.829+0000    reading metadata for newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.metadata.json
2020-10-29T19:25:45.834+0000    restoring newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.bson
2020-10-29T19:25:46.130+0000    no indexes to restore
2020-10-29T19:25:46.130+0000    finished restoring newdb.restaurants (25359 documents)
2020-10-29T19:25:46.130+0000    done
</code></pre>
<p>Im obigen Fall stellen wir die Daten auf demselben Server wieder her, auf dem wir das Backup erstellt haben. Wenn Sie die Daten auf einen anderen Server migrieren und dieselbe Technik verwenden möchten, sollten Sie das Backup-Verzeichnis, in unserem Fall <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>, auf den anderen Server kopieren.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Sie haben jetzt einige wichtige Aufgaben im Zusammenhang mit dem Sichern, Wiederherstellen und Migrieren Ihrer MongoDB-Datenbanken ausgeführt. Kein Produktions-MongoDB-Server sollte jemals ohne eine zuverlässige Backup-Strategie wie die hier beschriebene ausgeführt werden.</p>
