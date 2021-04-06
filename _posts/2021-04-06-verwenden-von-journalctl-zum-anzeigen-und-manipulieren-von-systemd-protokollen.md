---
title : "Verwenden von Journalctl zum Anzeigen und Manipulieren von Systemd-Protokollen"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs-de
image: "https://sergio.afanou.com/assets/images/image-midres-14.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Einige der überzeugendsten Vorteile von <code>systemd</code> sind diejenigen, die mit der Prozess- und Systemprotokollierung verbunden sind. Bei der Verwendung anderer Tools werden Protokolle normalerweise im gesamten System verteilt, von verschiedenen Daemons und Prozessen bearbeitet und können ziemlich schwer zu interpretieren, wenn sie sich über mehrere Anwendungen erstrecken. <code>Systemd</code> versucht, diese Probleme zu lösen, indem es eine zentralisierte Verwaltungslösung zur Protokollierung aller Kernel- und Userland-Prozesse bereitstellt. Das System, das diese Protokolle sammelt und verwaltet, wird als Journal bezeichnet.</p>

<p>Das Journal wird mit dem Daemon <code>journald</code> implementiert, der alle vom Kernel, initrd, services etc. erzeugten Nachrichten verarbeitet. In diesem Leitfaden besprechen wir die Verwendung des Dienstprogramms <code>journalctl</code>, mit dem Sie auf die in dem Journal gespeicherten Daten zugreifen und diese manipulieren können.</p>

<h2 id="allgemeiner-gedanke">Allgemeiner Gedanke</h2>

<p>Eine der Triebfedern hinter dem Journal <code>systemd</code> ist die Zentralisierung der Verwaltung von Protokollen, unabhängig davon, woher die Nachrichten stammen. Da ein Großteil des Boot-Prozesses und der Verwaltung von Diensten durch den Prozess <code>systemd</code> abgewickelt wird, ist es sinnvoll, die Art und Weise, wie Protokolle gesammelt und darauf zugegriffen wird, zu standardisieren. Der Daemon <code>journald</code> sammelt Daten aus allen verfügbaren Quellen und speichert sie in einem Binärformat für einfache und dynamische Manipulation.</p>

<p>Dies bringt eine Reihe von bedeutenden Vorteilen mit sich. Durch die Interaktion mit den Daten über ein einziges Dienstprogramm sind Administratoren in der Lage, Protokolldaten dynamisch nach ihren Bedürfnissen anzuzeigen. Dies kann so einfach sein, wie das Anzeigen der Boot-Daten von vor drei Boot-Vorgängen oder das Kombinieren der Protokolleinträge von zwei verwandten Diensten nacheinander, um ein Kommunikationsproblem zu beheben.</p>

<p>Das Speichern der Protokolldaten in einem Binärformat bedeutet auch, dass die Daten in beliebigen Ausgabeformaten angezeigt werden können, je nachdem, was Sie gerade benötigen. Für die tägliche Protokollverwaltung sind Sie eventuell daran gewöhnt, die Protokolle im Standard-Format <code>syslog</code> anzuzeigen, aber wenn Sie sich später entscheiden, Dienstunterbrechungen grafisch darzustellen, können Sie jeden Eintrag als JSON-Objekt ausgeben, um ihn für Ihren grafischen Dienst konsumierbar zu machen. Da die Daten nicht im Klartext auf die Festplatte geschrieben werden, ist keine Konvertierung erforderlich, wenn Sie ein anderes On-Demand-Format benötigen.</p>

<p>Das Journal <code>systemd</code> kann entweder mit einer vorhandenen <code>syslog</code>-Implementierung verwendet werden oder es kann die Funktionalität <code>syslog</code> ersetzen, je nach Ihren Bedürfnissen. Während das Journal <code>systemd</code> die meisten Protokollierungsanforderungen von Administratoren abdeckt, kann es auch bestehende Protokollierungsmechanismen ergänzen. Sie haben vielleicht einen zentralisierten <code>syslog</code>-Server, den Sie verwenden, um Daten von mehreren Servern zu kompilieren, aber Sie möchten die Protokolle auch von mehreren Diensten auf einem einzigen System mit dem Journal <code>systemd</code> zusammenführen. Sie können beides tun, indem Sie diese Technologien kombinieren.</p>

<h2 id="einstellen-der-systemzeit">Einstellen der Systemzeit</h2>

<p>Einer der Vorteile der Verwendung eines binären Journals für die Protokollierung ist die Fähigkeit, Protokolldatensätze in UTC oder lokaler Zeit anzuzeigen. Standardmäßig zeigt <code>systemd</code> Ergebnisse in der lokalen Zeit an.</p>

<p>Aus diesem Grund stellen wir, bevor wir mit dem Journal beginnen, sicher, dass die Zeitzone korrekt eingestellt ist. Die Suite <code>systemd</code> verfügt über ein Tool namens <code>timedatectl</code>, das dabei helfen kann.</p>

<p>Sehen Sie zuerst nach, welche Zeitzonen mit der Option <code>list-timezones</code> verfügbar sind:</p>
<pre class="code-pre "><code>timedatectl list-timezones
</code></pre>
<p>Dadurch werden die auf Ihrem System verfügbaren Zeitzonen aufgelistet. Wenn Sie diejenige gefunden haben, die dem Standort Ihres Servers entspricht, können Sie sie mit der Option <code>set-timezone</code> einstellen:</p>
<pre class="code-pre "><code>sudo timedatectl set-timezone <span class="highlight">zone</span>
</code></pre>
<p>Um sicherzustellen, dass Ihr Rechner jetzt die richtige Zeit verwendet, verwenden Sie den Befehl <code>timedatectl</code> allein oder mit der Option <code>status</code>. Die Anzeige wird die gleiche sein:</p>
<pre class="code-pre "><code>timedatectl status
</code></pre><pre class="code-pre "><code>      <span class="highlight">Local time: Thu 2015-02-05 14:08:06 EST</span>
  Universal time: Thu 2015-02-05 19:08:06 UTC
        RTC time: Thu 2015-02-05 19:08:06
       Time zone: America/New_York (EST, -0500)
     NTP enabled: no
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
</code></pre>
<p>In der ersten Zeile sollte die korrekte Zeit angezeigt werden.</p>

<h2 id="grundlegendes-anzeigen-von-protokollen">Grundlegendes Anzeigen von Protokollen</h2>

<p>Um die Protokolle zu sehen, die der Daemon <code>journald</code> gesammelt hat, verwenden Sie den Befehl <code>journalctl</code>.</p>

<p>Bei alleiniger Verwendung wird jeder im System vorhandene Journaleintrag innerhalb eines Pager (normalerweise <code>less</code>) angezeigt, damit Sie durchsuchen können. Die ältesten Einträge werden oben angezeigt:</p>
<pre class="code-pre "><code>journalctl
</code></pre><pre class="code-pre "><code>-- Logs begin at Tue 2015-02-03 21:48:52 UTC, end at Tue 2015-02-03 22:29:38 UTC. --
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journald[139]: Received SIGTERM from PID 1 (systemd).
Feb 03 21:48:52 localhost.localdomain kernel: audit: type=1404 audit(1423000132.274:2): enforcing=1 old_en
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: input: ImExPS/2 Generic Explorer Mouse as /devices/platform/
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  8 users, 102 roles, 4976 types, 294 bools, 1 sens,
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  83 classes, 104131 rules

. . .
</code></pre>
<p>Sie werden wahrscheinlich seitenweise Daten durchblättern müssen, die zehn- oder hunderttausende Zeilen lang sein können, wenn sich <code>systemd</code> schon lange auf Ihrem System befindet. Dies demonstriert, wie viele Daten in der Journal-Datenbank vorhanden sind.</p>

<p>Das Format wird denjenigen vertraut sein, die an die Standard-Protokollierung <code>syslog</code> gewöhnt sind. Allerdings werden hier tatsächlich Daten aus mehr Quellen gesammelt als traditionelle <code>syslog</code>-Implementierungen in der Lage sind. Es beinhaltet Protokolle des frühen Boot-Prozesses, des Kernel, der initrd und der Anwendungsstandardfehler und -ausgänge. Diese sind alle im Journal verfügbar.</p>

<p>Vielleicht fällt Ihnen auf, dass alle angezeigten Zeitstempel in lokaler Zeit sind. Dies ist für jeden Protokolleintrag verfügbar, da die lokale Zeit auf unserem System korrekt eingestellt ist. Alle Protokolle werden mit dieser neuen Information angezeigt.</p>

<p>Wenn Sie die Zeitstempel in UTC anzeigen möchten, können Sie das Flag <code>--utc</code> verwenden:</p>
<pre class="code-pre "><code>journalctl --utc
</code></pre>
<h2 id="journal-filterung-nach-zeit">Journal-Filterung nach Zeit</h2>

<p>Obwohl der Zugriff auf eine so große Datensammlung durchaus nützlich ist, kann es schwierig oder unmöglich sein, eine so große Menge mental zu untersuchen und zu verarbeiten. Aus diesem Grund ist eine der wichtigsten Funktionen von <code>journalctl</code> seine Filteroptionen.</p>

<h3 id="anzeigen-von-protokollen-aus-dem-aktuellen-boot">Anzeigen von Protokollen aus dem aktuellen Boot</h3>

<p>Die grundlegendste dieser Optionen, die Sie möglicherweise täglich verwenden, ist das Flag <code>-b</code>. Damit werden Ihnen alle Journaleinträge angezeigt, die seit dem neuesten Neustart gesammelt wurden.</p>
<pre class="code-pre "><code>journalctl -b
</code></pre>
<p>Dies hilft Ihnen, Informationen zu identifizieren und zu verwalten, die für Ihre aktuelle Umgebung relevant sind.</p>

<p>In Fällen, in denen Sie diese Funktion nicht verwenden und mehr als einen Tag von Bootvorgängen anzeigen, werden Sie sehen, dass <code>journalctl</code> eine Zeile eingefügt hat, die so aussieht, wann immer das System heruntergefahren wurde:</p>
<pre class="code-pre "><code>. . .

-- Reboot --

. . .
</code></pre>
<p>Dies kann verwendet werden, um Ihnen dabei zu helfen, die Informationen logisch in Boot-Sitzungen aufzuteilen.</p>

<h3 id="vergangene-bootvorgänge">Vergangene Bootvorgänge</h3>

<p>Während Sie in der Regel die Informationen aus dem aktuellen Bootvorgang anzeigen möchten, gibt es sicherlich Zeiten, in denen auch vergangene Bootvorgänge hilfreich sind. Das Journal kann Informationen von vielen vorherigen Bootvorgängen speichern, sodass <code>journalctl</code> eingestellt werden kann, um Informationen einfacher anzuzeigen.</p>

<p>Bei einigen Distributionen ist das Speichern von Informationen zu früheren Bootvorgängen standardmäßig aktiviert, bei anderen ist diese Funktion deaktiviert. Um persistente Boot-Informationen zu aktivieren, können Sie entweder das Verzeichnis zum Speichern des Journals erstellen, indem Sie Folgendes eingeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir -p /var/log/journal
</li></ul></code></pre>
<p>Oder Sie können die Journal-Konfigurationsdatei bearbeiten:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/systemd/journald.conf
</li></ul></code></pre>
<p>Setzen Sie unter dem Abschnitt <code>[Journal]</code> die Option <code>Storage=</code> auf „persistent“, um die persistente Protokollierung zu aktivieren:</p>
<div class="code-label " title="/etc/systemd/journald.conf">/etc/systemd/journald.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
[Journal]
Storage=<span class="highlight">persistent</span>
</code></pre>
<p>Wenn das Speichern von vorherigen Bootvorgängen auf Ihrem Server aktiviert ist, stellt <code>journalctl</code> einige Befehle bereit, um Ihnen die Arbeit mit Bootvorgängen als Einheit der Division zu erleichtern. Um die Bootvorgänge zu sehen, von denen <code>journald</code> weiß, verwenden Sie die Option <code>--list-boots</code> mit <code>journalctl</code>:</p>
<pre class="code-pre "><code>journalctl --list-boots
</code></pre><pre class="code-pre "><code>-2 caf0524a1d394ce0bdbcff75b94444fe Tue 2015-02-03 21:48:52 UTC—Tue 2015-02-03 22:17:00 UTC
-1 13883d180dc0420db0abcb5fa26d6198 Tue 2015-02-03 22:17:03 UTC—Tue 2015-02-03 22:19:08 UTC
 0 bed718b17a73415fade0e4e7f4bea609 Tue 2015-02-03 22:19:12 UTC—Tue 2015-02-03 23:01:01 UTC
</code></pre>
<p>Dies zeigt eine Zeile für jeden Bootvorgang an. Die erste Spalte ist der Offset für den Bootvorgang, der verwendet werden kann, um den Bootvorgang mit <code>journalctl</code> einfach zu referenzieren. Wenn Sie eine absolute Referenz benötigen, steht die Boot-ID in der zweiten Spalte. Die Zeit, auf die sich die Boot-Sitzung bezieht, erkennen Sie an den beiden Zeitangaben, die am Ende aufgeführt sind.</p>

<p>Um Informationen von diesen Bootvorgängen anzuzeigen, können Sie entweder Informationen aus der ersten oder zweiten Spalte verwenden.</p>

<p>Um beispielsweise das Journal von dem vorherigen Boot zu sehen, verwenden Sie den relativen Zeiger <code>-1</code> mit dem Flag <code>-b</code>:</p>
<pre class="code-pre "><code>journalctl -b -1
</code></pre>
<p>Zum Abrufen von Daten aus einem Boot können Sie auch die Boot-ID verwenden:</p>
<pre class="code-pre "><code>journalctl -b caf0524a1d394ce0bdbcff75b94444fe
</code></pre>
<h3 id="zeitfenster">Zeitfenster</h3>

<p>Während es unglaublich nützlich ist, Protokolleinträge nach Bootvorgang zu sehen, möchten Sie vielleicht oft Zeitfenster abfragen, die nicht gut mit Systemstarts übereinstimmen. Dies kann besonders bei lang laufenden Servern mit erheblicher Betriebszeit der Fall sein.</p>

<p>Sie können nach beliebigen Zeitgrenzen filtern, indem Sie die Optionen <code>--since</code> und <code>--until</code> verwenden, die die angezeigten Einträge auf diejenigen nach bzw. vor der angegebenen Zeit einschränken.</p>

<p>Die Zeitwerte können in einer Vielzahl von Formaten vorliegen. Für absolute Zeitwerte sollten Sie das folgende Format verwenden:</p>
<pre class="code-pre "><code>YYYY-MM-DD HH:MM:SS
</code></pre>
<p>Beispielsweise können wir alle Einträge seit dem 10. Januar 2015 um 17:15 Uhr sehen, indem wir eingeben:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10 17:15:00"
</code></pre>
<p>Wenn Komponenten des obigen Formats weggelassen werden, werden einige Standardwerte angewendet. Wenn beispielsweise das Datum weggelassen wird, wird das aktuelle Datum angenommen. Wenn die Zeitkomponente fehlt, wird „00:00:00“ (Mitternacht) ersetzt. Das Sekundenfeld kann auch weggelassen werden, sodass standardmäßig „00“ verwendet wir:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10" --until "2015-01-11 03:00"
</code></pre>
<p>Das Journal versteht auch einige relative Werte und benannte Abkürzungen. Beispielsweise können Sie die Wörter „gestern“, „heute“, „morgen“ oder „jetzt“ verwenden. Relative Zeiten geben Sie an, indem Sie einem nummerierten Wert ein „-“ oder „+“ voranstellen oder Wörter wie „vor“ in einem Satzbau verwenden.</p>

<p>Um die Daten von gestern zu erhalten, könnten Sie eingeben:</p>
<pre class="code-pre "><code>journalctl --since yesterday
</code></pre>
<p>Wenn Sie Berichte über eine Dienstunterbrechung erhalten haben, die um 9:00 Uhr begann und bis vor einer Stunde andauerte, könnten Sie eingeben:</p>
<pre class="code-pre "><code>journalctl --since 09:00 --until "1 hour ago"
</code></pre>
<p>Wie Sie sehen können, ist es relativ einfach, flexible Zeitfenster zu definieren, um die Einträge zu filtern, die Sie sehen möchten.</p>

<h2 id="filtern-nach-nachrichteninteresse">Filtern nach Nachrichteninteresse</h2>

<p>Wir haben vorstehend einige Möglichkeiten kennengelernt, wie Sie die Journaldaten anhand von Zeiteinschränkungen filtern können. In diesem Abschnitt werden wir besprechen, wie Sie basierend auf dem Dienst oder der Komponente filtern können, an der Sie interessiert sind. Das Journal <code>systemd</code> bietet eine Vielzahl von Möglichkeiten, dies zu tun.</p>

<h3 id="nach-einheit">Nach Einheit</h3>

<p>Die vielleicht nützlichste Art der Filterung ist nach der Einheit, an der Sie interessiert sind. Wir können die Option <code>-u</code> verwenden, um auf diese Weise zu filtern.</p>

<p>Um beispielsweise alle Protokolle einer Nginx-Einheit auf unserem System zu sehen, können wir eingeben:</p>
<pre class="code-pre "><code>journalctl -u nginx.service
</code></pre>
<p>Normalerweise würden Sie wahrscheinlich auch nach der Zeit filtern wollen, um die Zeilen anzuzeigen, an denen Sie interessiert sind. Um beispielsweise zu überprüfen, wie der Dienst heute ausgeführt wird, können Sie eingeben:</p>
<pre class="code-pre "><code>journalctl -u nginx.service --since today
</code></pre>
<p>Diese Art der Fokussierung wird extrem hilfreich, wenn Sie die Fähigkeit des Journals nutzen, Datensätze aus verschiedenen Einheiten zusammenzuführen. Wenn Ihr Nginx-Prozess beispielsweise mit einer PHP-FPM-Einheit verbunden ist, um dynamischen Inhalt zu verarbeiten, können Sie die Einträge von beiden in chronologischer Reihenfolge zusammenführen, indem Sie beide Einheiten angeben:</p>
<pre class="code-pre "><code>journalctl -u nginx.service -u php-fpm.service --since today
</code></pre>
<p>Dies kann es wesentlich einfacher machen, die Interaktionen zwischen verschiedenen zu erkennen und System zu debuggen, anstatt einzelne Prozesse.</p>

<h3 id="nach-prozess-benutzer-oder-gruppen-id">Nach Prozess, Benutzer oder Gruppen-ID</h3>

<p>Einige Dienste spawnen eine Vielzahl von untergeordneten Prozesse, um die Arbeit zu erledigen. Wenn Sie die genaue PID des Prozesses, an dem Sie interessiert sind, ausgekundschaftet haben, können Sie auch nach dieser filtern.</p>

<p>Dazu können wir durch Angabe des Feldes <code>_PID</code> filtern. Wenn die PID, an der wir interessiert sind, beispielsweise 8088 ist, könnten wir eingeben:</p>
<pre class="code-pre "><code>journalctl _PID=8088
</code></pre>
<p>Zu anderen Zeiten möchten Sie möglicherweise alle Einträge anzeigen, die von einem bestimmten Benutzer oder einer Gruppe protokolliert wurden. Dies kann mit den Filtern <code>_UID</code> oder <code>_GID</code> geschehen. Wenn Ihr Webserver beispielsweise unter dem Benutzer <code>www-data</code> läuft, können Sie die Benutzer-ID finden, indem Sie eingeben:</p>
<pre class="code-pre "><code>id -u www-data
</code></pre><pre class="code-pre "><code>33
</code></pre>
<p>Anschließend können Sie die zurückgegebene ID verwenden, um die Journalergebnisse zu filtern:</p>
<pre class="code-pre "><code>journalctl _UID=<span class="highlight">33</span> --since today
</code></pre>
<p>Das Journal <code>systemd</code> hat viele Felder, die für die Filterung verwendet werden können. Einige davon werden von dem zu protokollierenden Prozess übergeben und einige werden von <code>journald</code> anhand von Informationen angewendet, die es zum Zeitpunkt der Protokollierung aus dem System sammelt.</p>

<p>Der führende Unterstrich gibt an, dass das Feld <code>_PID</code> von dem letzteren Typ ist. Das Journal zeichnet automatisch die PID des protokollierenden Prozesses auf und indexiert sie für spätere Filterungen. Sie können sich über alle verfügbaren Journalfelder informieren, indem Sie eingeben:</p>
<pre class="code-pre "><code>man systemd.journal-fields
</code></pre>
<p>Wir werden einige davon in diesem Leitfaden besprechen. Für den Moment werden wir jedoch eine weitere nützliche Option bespreche, die mit der Filterung nach diesen Feldern zu tun hat. Die Option <code>-F</code> kann verwendet werden, um alle verfügbaren Werte für ein bestimmtes Journalfeld anzuzeigen.</p>

<p>Um beispielsweise zu sehen, für welche Gruppen-IDs das Journal <code>systemd</code> Einträge aufweist, können Sie eingeben:</p>
<pre class="code-pre "><code>journalctl -F _GID
</code></pre><pre class="code-pre "><code>32
99
102
133
81
84
100
0
124
87
</code></pre>
<p>Dies zeigt Ihnen alle Werte, die das Journal für das Feld Gruppen-ID gespeichert hat. Dies kann Ihnen beim Erstellen Ihrer Filter helfen.</p>

<h3 id="nach-komponentenpfad">Nach Komponentenpfad</h3>

<p>Wir können auch filtern, indem wir eine Pfadposition bereitstellen.</p>

<p>Wenn der Pfad zu einer ausführbaren Datei führt, zeigt <code>journalctl</code> alle Einträge an, die die betreffende ausführbare Datei betreffen. Um beispielsweise die Einträge zu finden, die die ausführbare Datei <code>bash</code> betreffen, können Sie eingeben:</p>
<pre class="code-pre "><code>journalctl /usr/bin/bash
</code></pre>
<p>Wenn eine Einheit für die ausführbare Datei verfügbar ist, ist diese Methode sauberer und bietet bessere Informationen (Einträge aus zugehörigen untergeordneten Prozessen usw.). Manchmal ist dies jedoch nicht möglich.</p>

<h3 id="anzeigen-von-kernel-meldungen">Anzeigen von Kernel-Meldungen</h3>

<p>Kernel-Meldungen, die normalerweise in der Ausgabe <code>dmesg</code> zu finden sind, können ebenfalls aus dem Journal abgerufen werden.</p>

<p>Um nur diese Meldungen anzuzeigen, können wir die Flags <code>-k</code> oder <code>--dmesg</code> zu unserem Befehl hinzufügen:</p>
<pre class="code-pre "><code>journalctl -k
</code></pre>
<p>Standardmäßig werden damit die Kernel-Meldungen des aktuellen Bootvorgangs angezeigt. Sie können einen alternativen Bootvorgang angeben, indem Sie die zuvor besprochenen Boot-Auswahl-Flags verwenden. Um beispielsweise die Meldungen von vor fünf Bootvorgängen zu erhalten, könnten Sie eingeben:</p>
<pre class="code-pre "><code>journalctl -k -b -5
</code></pre>
<h3 id="nach-priorität">Nach Priorität</h3>

<p>Ein Filter, an dem Systemadministratoren oft interessiert sind, ist die Meldungspriorität. Während es oft nützlich ist, Informationen auf einem sehr ausführlichen Niveau zu protokollieren, können Protokolle mit niedriger Priorität beim tatsächlichen Verarbeiten der verfügbaren Informationen ablenken und verwirrend sein.</p>

<p>Sie können <code>journalctl</code> verwenden, um nur Meldungen einer bestimmten Priorität oder höher anzuzeigen, indem Sie die Option <code>-p</code> verwenden. Dadurch können Sie Meldungen mit niedrigerer Priorität herausfiltern.</p>

<p>Um beispielsweise nur Einträge anzuzeigen, die auf der Fehlerebene oder höher protokolliert wurden, können Sie eingeben:</p>
<pre class="code-pre "><code>journalctl -p err -b
</code></pre>
<p>Dies zeigt Ihnen alle Meldungen an, die als Fehler, kritisch, Alarm oder Notfall gekennzeichnet sind. Das Journal implementiert die Standard-<code>syslog</code>-Meldungsebenen. Sie können entweder den Namen der Priorität oder den entsprechenden numerischen Wert verwenden. In der Reihenfolge von höchster bis niedrigster Priorität sind dies:</p>

<ul>
<li>0: emerg (Notfall)</li>
<li>1: alert (Alarm)</li>
<li>2: crit (kritisch)</li>
<li>3: err (Fehler)</li>
<li>4: warning (Warnung)</li>
<li>5: notice (Hinweis)</li>
<li>6: info (Information)</li>
<li>7: debug (Fehlersuche)</li>
</ul>

<p>Die vorstehenden Zahlen oder Namen können austauschbar mit der Option <code>-p</code> verwendet werden. Wenn Sie die Priorität auswählen, werden Meldungen angezeigt, die auf der angegebenen Ebene markiert sind, und solche, die darüber liegen.</p>

<h2 id="Ändern-der-journalanzeige">Ändern der Journalanzeige</h2>

<p>Vorstehend haben wir die Auswahl von Einträgen durch Filterung demonstriert. Es gibt jedoch noch andere Möglichkeiten, die Ausgabe zu ändern. Wir können die Anzeige <code>journalctl</code> an verschiedene Bedürfnisse anpassen.</p>

<h3 id="kürzen-oder-erweitern-der-ausgabe">Kürzen oder Erweitern der Ausgabe</h3>

<p>Wir können anpassen, wie <code>journalctl</code> Daten anzeigt, indem wir ihn anweisen, die Ausgabe zu verkürzen oder zu erweitern.</p>

<p>Standardmäßig zeigt <code>journalctl</code> den gesamten Eintrag in dem Pager an und lässt die Einträge rechts am Bildschirm auslaufen. Auf diese Informationen können Sie durch Drücken der rechten Pfeiltaste zugreifen.</p>

<p>Wenn Sie die Ausgabe lieber gekürzt haben möchten, wobei an den Stellen, an denen Informationen entfernt wurden, eine Ellipse eingefügt wird, können Sie die Option <code>--no-full</code> verwenden:</p>
<pre class="code-pre "><code>journalctl --no-full
</code></pre><pre class="code-pre "><code>. . .

Feb 04 20:54:13 journalme sshd[937]: Failed password for root from 83.234.207.60...h2
Feb 04 20:54:13 journalme sshd[937]: Connection closed by 83.234.207.60 [preauth]
Feb 04 20:54:13 journalme sshd[937]: PAM 2 more authentication failures; logname...ot
</code></pre>
<p>Sie können damit auch in die entgegengesetzte Richtung gehen und <code>journalctl</code> anweisen, alle Informationen anzuzeigen, unabhängig davon, ob sie nicht druckbare Zeichen enthält. Wir können dies mit dem Flag <code>-a</code> tun:</p>
<pre class="code-pre "><code>journalctl -a
</code></pre>
<h3 id="ausgabe-auf-standardausgabe">Ausgabe auf Standardausgabe</h3>

<p>Standardmäßig zeigt <code>journalctl</code> die Ausgabe in einem Pager an, um den Konsum zu erleichtern. Wenn Sie jedoch vorhaben, die Daten mit Textmanipulationswerkzeugen zu verarbeiten, möchten Sie wahrscheinlich in der Lage sein, auf die Standardausgabe auszugeben.</p>

<p>Sie können dies mit der Option <code>--no-pager</code> tun:</p>
<pre class="code-pre "><code>journalctl --no-pager
</code></pre>
<p>Dies kann direkt in ein Verarbeitungsprogramm geleitet oder in eine Datei auf der Festplatte umgeleitet werden, je nach Ihren Bedürfnissen.</p>

<h3 id="ausgabeformate">Ausgabeformate</h3>

<p>Wenn Sie, wie oben erwähnt, Journaleinträge verarbeiten, wird es Ihnen die Analyse der Daten wahrscheinlich leichter fallen, wenn sie in einem besser konsumierbaren Format vorliegen. Glücklicherweise kann das Journal in einer Vielzahl von Formaten angezeigt werden, wenn es erforderlich ist. Dazu können Sie die Option <code>-o</code> mit einem Formatbezeichner verwenden.</p>

<p>Sie können die Journaleinträge beispielsweise in JSON ausgeben, indem Sie eingeben:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json
</code></pre><pre class="code-pre "><code>{ "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635", "__REALTIME_TIMESTAMP" : "1422990364739502", "__MONOTONIC_TIMESTAMP" : "27200938", "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d", "PRIORITY" : "6", "_UID" : "0", "_GID" : "0", "_CAP_EFFECTIVE" : "3fffffffff", "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee", "_HOSTNAME" : "desktop", "SYSLOG_FACILITY" : "3", "CODE_FILE" : "src/core/unit.c", "CODE_LINE" : "1402", "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading", "SYSLOG_IDENTIFIER" : "systemd", "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5", "_TRANSPORT" : "journal", "_PID" : "1", "_COMM" : "systemd", "_EXE" : "/usr/lib/systemd/systemd", "_CMDLINE" : "/usr/lib/systemd/systemd", "_SYSTEMD_CGROUP" : "/", "UNIT" : "nginx.service", "MESSAGE" : "Starting A high performance web server and a reverse proxy server...", "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973" }

. . .
</code></pre>
<p>Dies ist nützlich für das Parsen mit Dienstprogrammen. Sie könnten das Format <code>json-pretty</code> verwenden, um die Datenstruktur besser in den Griff zu bekommen, bevor Sie sie an den JSON-Verbraucher übergeben:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json-pretty
</code></pre><pre class="code-pre "><code>{
    "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635",
    "__REALTIME_TIMESTAMP" : "1422990364739502",
    "__MONOTONIC_TIMESTAMP" : "27200938",
    "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d",
    "PRIORITY" : "6",
    "_UID" : "0",
    "_GID" : "0",
    "_CAP_EFFECTIVE" : "3fffffffff",
    "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee",
    "_HOSTNAME" : "desktop",
    "SYSLOG_FACILITY" : "3",
    "CODE_FILE" : "src/core/unit.c",
    "CODE_LINE" : "1402",
    "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading",
    "SYSLOG_IDENTIFIER" : "systemd",
    "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5",
    "_TRANSPORT" : "journal",
    "_PID" : "1",
    "_COMM" : "systemd",
    "_EXE" : "/usr/lib/systemd/systemd",
    "_CMDLINE" : "/usr/lib/systemd/systemd",
    "_SYSTEMD_CGROUP" : "/",
    "UNIT" : "nginx.service",
    "MESSAGE" : "Starting A high performance web server and a reverse proxy server...",
    "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973"
}

. . .
</code></pre>
<p>Die folgenden Formate können für die Anzeige verwendet werden:</p>

<ul>
<li><strong>cat</strong>: Zeigt nur das Meldungsfeld selbst an.</li>
<li><strong>export</strong>: Ein Binärformat, das sich zum Übertragen oder Sichern eignet.</li>
<li><strong>json</strong>: Standard-JSON mit einem Eintrag pro Zeile.</li>
<li><strong>json-pretty</strong>: JSON, das für eine bessere menschliche Lesbarkeit formatiert ist.</li>
<li><strong>json-sse</strong>: JSON-formatierte Ausgabe, die eingehüllt ist, um das Hinzufügen von vom Server gesendeten Ereignissen zu ermöglichen.</li>
<li><strong>short</strong>: Die Standardausgabe im <code>syslog</code>-Stil.</li>
<li><strong>short-iso</strong>: Das Standardformat, das zum Anzeigen von ISO-8601 Wallclock-Zeitstempeln erweitert wurde.</li>
<li><strong>short-monotonic</strong>: Das Standardformat mit monotonen Zeitstempeln.</li>
<li><strong>short-precise</strong>: Das Standardformat mit Mikrosekundengenauigkeit.</li>
<li><strong>verbose</strong>: Zeigt jedes für den Eintrag verfügbare Journalfeld an, einschließlich derjenigen, die normalerweise intern verborgen sind.</li>
</ul>

<p>Mit diesen Optionen können Sie die Journaleinträge in dem Format anzeigen, das Ihren aktuellen Bedürfnissen am besten entspricht.</p>

<h2 id="aktive-prozessüberwachung">Aktive Prozessüberwachung</h2>

<p>Der Befehl <code>journalctl</code> imitiert, wie viele Administratoren <code>tail</code> zur Überwachung der aktiven oder jüngsten Aktivität verwenden. Diese Funktionalität ist in <code>journalctl</code> eingebaut, sodass Sie auf diese Funktionen zugreifen können, ohne eine Pipe zu einem anderen Tool verwenden zu müssen.</p>

<h3 id="anzeigen-aktueller-protokolle">Anzeigen aktueller Protokolle</h3>

<p>Um eine bestimmte Anzahl von Datensätzen anzuzeigen, können Sie die Option <code>-n</code> verwenden, die genau wie <code>tail -n</code> funktioniert.</p>

<p>Sie zeigt standardmäßig die letzten 10 Einträge an:</p>
<pre class="code-pre "><code>journalctl -n
</code></pre>
<p>Sie können die Anzahl der Einträge, die Sie sehen möchten, mit einer Zahl nach dem <code>-n</code> angeben:</p>
<pre class="code-pre "><code>journalctl -n 20
</code></pre>
<h3 id="verfolgen-von-protokollen">Verfolgen von Protokollen</h3>

<p>Um die Protokolle aktiv zu verfolgen, während sie geschrieben werden, können Sie das Flag <code>-f</code> verwenden. Auch dies funktioniert so, wie Sie es vielleicht erwarten, wenn Sie Erfahrung mit der Verwendung von <code>tail -f</code> haben:</p>
<pre class="code-pre "><code>journalctl -f
</code></pre>
<h2 id="journalwartung">Journalwartung</h2>

<p>Vielleicht fragen Sie sich, was es kostet, all die Daten zu speichern, die wir bisher gesehen haben. Außerdem sind Sie vielleicht daran interessiert, einige ältere Protokolle aufzuräumen und Speicherplatz freizugeben.</p>

<h3 id="ermitteln-der-aktuellen-festplattenbelegung">Ermitteln der aktuellen Festplattenbelegung</h3>

<p>Sie können herausfiden, wie viel Platz das Journal derzeit auf der Festplatte belegt, indem Sie das Flag <code>--disk-usage</code> verwenden:</p>
<pre class="code-pre "><code>journalctl --disk-usage
</code></pre><pre class="code-pre "><code>Journals take up 8.0M on disk.
</code></pre>
<h3 id="löschen-alter-protokolle">Löschen alter Protokolle</h3>

<p>Wenn Sie Ihr Journal verkleinern möchten, können Sie dies auf zwei verschiedene Arten tun (verfügbar mit <code>systemd</code> Version 218 und höher).</p>

<p>Wenn Sie die Option <code>--vacuum-size</code> verwenden, können Sie Ihr Journal durch Angabe einer Größe verkleinern. Dadurch werden alte Einträge entfernt, bis der gesamte auf der Festplatte belegte Journal-Speicherplatz die angeforderte Größe erreicht hat:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-size=1G
</code></pre>
<p>Eine weitere Möglichkeit, das Journal zu verkleinern, ist die Angabe einer Sperrzeit mit der Option <code>--vacuum-time</code>. Alle Einträge, die über diese Zeit hinausgehen, werden gelöscht. Dadurch können Sie die Einträge behalten, die nach einer bestimmten Zeit erstellt wurden.</p>

<p>Um beispielsweise Einträge aus dem letzten Jahr zu behalten, können Sie eingeben:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-time=1years
</code></pre>
<h3 id="begrenzen-der-journal-erweiterung">Begrenzen der Journal-Erweiterung</h3>

<p>Sie können Ihren Server so konfigurieren, dass er den Speicherplatz, den das Journal einnehmen kann, begrenzt. Dies kann durch die Bearbeitung der Datei <code>/etc/systemd/journald.conf</code> erfolgen.</p>

<p>Die folgenden Einträge können verwendet werden, um das Wachstum des Journals zu begrenzen:</p>

<ul>
<li><strong><code>SystemMaxUse=</code></strong>: Gibt den maximalen Festplattenplatz an, den das Journal im permanenten Speicher belegen kann.</li>
<li><strong><code>SystemKeepFree=</code></strong>: Gibt den Speicherplatz an, den das Journal beim Hinzufügen von Journaleinträgen im permanenten Speicher frei lassen soll.</li>
<li><strong><code>SystemMaxFileSize=</code></strong>: Kontrolliert, wie große einzelne Journaldateien im persistenten Speicher werden können, bevor sie rotiert werden.</li>
<li><strong><code>RuntimeMaxUse=</code></strong>: Gibt den maximalen Festplattenspeicher an, der im flüchtigen Speicher (innerhalb des Dateisystems <code>/run</code>) verwendet werden kann.</li>
<li><strong><code>RuntimeKeepFree=</code></strong>: Gibt an, wie viel Speicherplatz beim Schreiben von Daten in den flüchtigen Speicher (innerhalb des Dateisystems <code>/run</code>) für andere Verwendungen freigehalten werden soll.</li>
<li><strong><code>RuntimeMaxFileSize=</code></strong>: Gibt an, wie viel Speicherplatz eine einzelne Journaldatei im flüchtigen Speicher (innerhalb des Dateisystems <code>/run</code>) einnehmen kann, bevor sie rotiert wird.</li>
</ul>

<p>Durch die Einstellung dieser Werte können Sie kontrollieren, wie <code>journald</code> den Speicherplatz auf Ihrem Server verbraucht und bewahrt. Denken Sie daran, dass <strong><code>SystemMaxFileSize</code></strong> und <strong><code>RuntimeMaxFileSize</code></strong> darauf abzielen, dass archivierte Dateien die angegebenen Grenzen erreichen. Dies ist wichtig zu bedenken, wenn Sie Dateizählungen nach einer Vakuum-Operation interpretieren.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Wie Sie sehen können, ist das Journal <code>systemd</code> unglaublich nützlich für das Erfassen und Verwalten Ihrer System- und Anwendungsdaten. Die meiste Flexibilität ergibt sich aus den umfangreichen Metadaten, die automatisch aufgezeichnet werden, und der zentralisierten Natur des Protokolls. Der Befehl <code>journalctl</code> macht es einfach, die erweiterten Funktionen des Journals zu nutzen und umfangreiche Analysen und relationales Debugging verschiedener Anwendungskomponenten durchzuführen.</p>
