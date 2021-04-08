---
title : "So bearbeiten Sie die Sudoers-Datei"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-de
image: "https://sergio.afanou.com/assets/images/image-midres-5.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>Die Trennung von Berechtigungen ist eines der grundlegenden Sicherheitsparadigmen, die in Linux- und Unix-ähnlichen Betriebssystemen implementiert sind. Normale Benutzer arbeiten mit eingeschränkten Berechtigungen, um den Umfang ihres Einflusses auf ihre eigene Umgebung und nicht auf das breitere Betriebssystem zu reduzieren.</p>

<p>Ein spezieller Benutzer namens <strong>root</strong> verfügt über <em>Super-Benutzer</em>-Berechtigungen. Dies ist ein Administratorkonto ohne die Einschränkungen, die für normale Benutzer gelten. Benutzer können Befehle mit Super-Benutzer- oder <strong>root</strong>-Berechtigungen auf verschiedene Arten ausführen.</p>

<p>In diesem Artikel wird erläutert, wie Sie <strong>root</strong>-Berechtigungen korrekt und sicher erhalten. Ein besonderer Schwerpunkt liegt dabei auf der Bearbeitung der Datei <code>/etc/sudoers</code>.</p>

<p>Wir werden diese Schritte auf einem Ubuntu 20.04-Server ausführen, aber die meisten modernen Linux-Distributionen wie Debian und CentOS sollten auf ähnliche Weise funktionieren.</p>

<p>In diesem Leitfaden wird davon ausgegangen, dass Sie die hier <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">beschriebene Ersteinrichtung des Servers</a> bereits abgeschlossen haben. Melden Sie sich als regulärer Benutzer ohne <strong>root</strong>-Berechtigung bei Ihrem Server an und fahren Sie unten fort.</p>

<p><span class='note'><strong>Hinweis:</strong> Dieses Tutorial befasst sich ausführlich mit der Eskalation von Berechtigungen und der <code>sudoers</code>-Datei. Wenn Sie einem Benutzer nur <code>sudo</code>-Berechtigungen hinzufügen möchten, lesen Sie unsere <em>Schnellstart-Tutorials zum Erstellen eines neuen Sudo-fähigen Benutzers</em> für <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> und <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a>.<br></span></p>

<h2 id="so-erhalten-sie-root-berechtigungen">So erhalten Sie root-Berechtigungen</h2>

<p>Es gibt drei grundlegende Möglichkeiten, um <strong>root</strong>-Berechtigungen zu erhalten, die sich in ihrem Entwicklungsstand unterscheiden.</p>

<h3 id="anmelden-als-root-benutzer">Anmelden als root-Benutzer</h3>

<p>Die einfachste und unkomplizierteste Methode zum Abrufen von <strong>root</strong>-Berechtigungen besteht darin, sich direkt als <strong>root</strong>-Benutzer bei Ihrem Server anzumelden.</p>

<p>Wenn Sie sich bei einem lokalen Computer anmelden (oder eine Out-of-Band-Konsolenfunktion auf einem virtuellen Server verwenden), geben Sie an der Anmeldeaufforderung <code>root</code> als Benutzernamen ein und geben Sie das <strong>root</strong>-Passwort ein, wenn Sie dazu aufgefordert werden.</p>

<p>Wenn Sie sich über SSH anmelden, geben Sie den <strong>root</strong>-Benutzer vor der IP-Adresse oder dem Domänennamen in Ihrer SSH-Verbindungszeichenfolge an:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ssh root@<span class="highlight">server_domain_or_ip</span>
</li></ul></code></pre>
<p>Wenn Sie keine SSH-Schlüssel für den <strong>root</strong>-Benutzer eingerichtet haben, geben Sie das <strong>root</strong>-Passwort ein, wenn Sie dazu aufgefordert werden.</p>

<h3 id="verwenden-sie-su-um-ein-root-benutzer-zu-werden">Verwenden Sie <code>su</code>, um ein root-Benutzer zu werden</h3>

<p>Die direkte Anmeldung als <strong>root</strong>-Benutzer wird normalerweise nicht empfohlen, da es einfach ist, das System für nicht administrative Aufgaben zu verwenden, was gefährlich ist.</p>

<p>Der nächste Weg, um Superuser-Berechtigungen zu erhalten, ermöglicht es Ihnen, jederzeit der <strong>root</strong>-Benutzer zu werden, wie Sie es benötigen.</p>

<p>Wir können dies tun, indem wir den Befehl <code>su</code> aufrufen, der für „Ersatzbenutzer“ steht. Geben Sie Folgendes ein, um <strong>root</strong>-Berechtigungen zu erhalten:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">su
</li></ul></code></pre>
<p>Sie werden aufgefordert, das Kennwort des <strong>root</strong>-Benutzers einzugeben. Anschließend werden Sie in eine <strong>root</strong>-Shell-Sitzung versetzt.</p>

<p>Wenn Sie die Aufgaben abgeschlossen haben, für die <strong>root</strong>-Berechtigungen erforderlich sind, kehren Sie zu Ihrer normalen Shell zurück, indem Sie Folgendes eingeben:</p>
<pre class="code-pre super_user prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="#">exit
</li></ul></code></pre>
<h3 id="verwenden-sie-sudo-um-befehle-als-root-benutzer-auszuführen">Verwenden Sie <code>sudo</code>, um Befehle als root-Benutzer auszuführen</h3>

<p>Der letzte Weg, um <strong>root</strong>-Berechtigungen zu erhalten, den wir diskutieren werden, ist mit dem Befehl <code>sudo</code>.</p>

<p>Mit dem Befehl <code>sudo</code> können Sie einmalige Befehle mit <strong>root</strong>-Berechtigungen ausführen, ohne eine neue Shell erstellen zu müssen. Es wird wie folgt ausgeführt:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo <span class="highlight">command_to_execute</span>
</li></ul></code></pre>
<p>Im Gegensatz zu <code>su</code> fordert der Befehl <code>sudo</code> das Passwort des <em>aktuellen</em> Benutzers an, nicht das <strong>root</strong>-Passwort.</p>

<p>Aufgrund seiner Auswirkungen auf die Sicherheit wird <code>Sudo</code>-Zugriff Benutzern standardmäßig nicht gewährt und muss eingerichtet werden, bevor er ordnungsgemäß funktioniert. In unseren Schnellstart-Tutorials zum <em>Erstellen eines neuen Sudo-fähigen Benutzers</em> für <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> und <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a> erfahren Sie, wie Sie einen <code>Sudo</code>-fähigen Benutzer einrichten.</p>

<p>Im folgenden Abschnitt werden wir ausführlicher erläutern, wie Sie die <code>Sudo</code>-Konfiguration ändern können.</p>

<h2 id="was-ist-visudo">Was ist Visudo?</h2>

<p>Der Befehl <code>sudo</code> wird über eine Datei unter <code>/etc/sudoers</code> konfiguriert.</p>

<p><span class='warning'><strong>Warnung:</strong> Bearbeiten Sie diese Datei nie mit einem normalen Texteditor! Verwenden Sie stattdessen immer den Befehl <code>visudo</code>!<br></span></p>

<p>Da eine falsche Syntax in der Datei <code>/etc/sudoers</code> zu einem Systembruch führen kann, bei dem es nicht möglich ist, erhöhte Berechtigungen zu erhalten, ist es wichtig, den Befehl <code>visudo</code> zum Bearbeiten der Datei zu verwenden.</p>

<p>Der Befehl <code>visudo</code> öffnet wie gewohnt einen Texteditor, überprüft jedoch beim Speichern die Syntax der Datei. Dies verhindert, dass Konfigurationsfehler <code>sudo</code>-Vorgänge blockieren. Dies ist möglicherweise Ihre einzige Möglichkeit, <strong>root</strong>-Berechtigungen zu erhalten.</p>

<p>Traditionell öffnet <code>visudo</code> die Datei <code>/etc/sudoers</code> mit dem <code>vi</code>-Texteditor. Ubuntu hat <code>visudo</code> jedoch so konfiguriert, dass stattdessen der <code>nano</code>-Texteditor verwendet wird.</p>

<p>Wenn Sie ihn wieder in <code>vi</code> ändern möchten, geben Sie den folgenden Befehl ein:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo update-alternatives --config editor
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    10        manual mode

Press &lt;enter&gt; to keep the current choice[*], or type selection number:
</code></pre>
<p>Wählen Sie die Zaus, die der Auswahl entspricht, die Sie treffen möchten.</p>

<p>Unter CentOS können Sie diesen Wert ändern, indem Sie Ihrem <code>~/.bashrc</code> die folgende Zeile hinzufügen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">export EDITOR=`which <span class="highlight">name_of_editor</span>`
</li></ul></code></pre>
<p>Geben Sie die Datei ein, um die Änderungen zu implementieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">. ~/.bashrc
</li></ul></code></pre>
<p>Nachdem Sie <code>visudo</code> konfiguriert haben, führen Sie den Befehl aus, um auf die Datei <code>/etc/sudoers</code> zuzugreifen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre>
<h2 id="so-bearbeiten-sie-die-sudoers-datei">So bearbeiten Sie die Sudoers-Datei</h2>

<p>In Ihrem ausgewählten Texteditor wird die Datei <code>/etc/sudoers</code> angezeigt.</p>

<p>Ich habe die Datei von Ubuntu 18.04 kopiert und eingefügt, wobei Kommentare entfernt wurden. Die Datei CentOS <code>/etc/sudoers</code> enthält viele weitere Zeilen, von denen einige in diesem Leitfaden nicht behandelt werden.</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

root    ALL=(ALL:ALL) ALL

%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

#includedir /etc/sudoers.d
</code></pre>
<p>Werfen wir einen Blick darauf, was diese Zeilen bewirken.</p>

<h3 id="standardzeilen">Standardzeilen</h3>

<p>In der ersten Zeile „Defaults env_reset“ wird die Terminalumgebung zurückgesetzt, um alle Benutzervariablen zu entfernen. Dies ist eine Sicherheitsmaßnahme, mit der potenziell schädliche Umgebungsvariablen aus der <code>sudo</code>-Sitzung entfernt werden.</p>

<p>In der zweiten Zeile, wird mit dem Befehl <code>Defaults mail_badpass</code> das System angewiesen, Benachrichtigungen über fehlerhafte <code>sudo</code>-Passwortversuche an den konfigurierten <code>mailto</code>-Benutzer zu senden. Standardmäßig ist dies das <strong>root</strong>-Konto.</p>

<p>Die dritte Zeile, die mit „Defaults Secure_path=&hellip;“ beginnt, gibt den <code>Pfad</code> an (die Stellen im Dateisystem, nach denen das Betriebssystem nach Anwendungen sucht), die für <code>sudo</code>-Operationen verwendet werden. Dies verhindert, dass Benutzerpfade verwendet werden, was schädlich sein kann.</p>

<h3 id="benutzerberechtigungszeilen">Benutzerberechtigungszeilen</h3>

<p>Die vierte Zeile, die die <strong>sudo</strong>-Berechtigungen des <code>root</code>-Benutzers vorschreibt, unterscheidet sich von den vorhergehenden Zeilen. Schauen wir uns an, was die verschiedenen Felder bedeuten:</p>

<ul>
<li><p><code><span class="highlight">root</span> ALL=(ALL:ALL) ALL</code> Das erste Feld gibt den Benutzernamen an, für den die Regel gilt (<strong>root</strong>).</p></li>
<li><p><code>root <span class="highlight">ALL</span>=(ALL:ALL) ALL</code> Das erste „ALL“ gibt an, dass diese Regel für alle Hosts gilt.</p></li>
<li><p><code>root ALL=(<span class="highlight">ALL</span>:ALL) ALL</code> Dieses „ALL“ gibt an, dass der <strong>root</strong>-Benutzer Befehle wie alle Benutzer ausführen kann.</p></li>
<li><p><code>root ALL=(ALL:<span class="highlight">ALL</span>) ALL</code> Dieses „ALL“ gibt an, dass der <strong>root</strong>-Benutzer Befehle wie alle Gruppen ausführen kann.</p></li>
<li><p><code>root ALL=(ALL:ALL) <span class="highlight">ALL</span></code> Das letzte „ALL“ gibt an, dass diese Regeln für alle Befehle gelten.</p></li>
</ul>

<p>Dies bedeutet, dass unser <strong>root</strong>-Benutzer jeden Befehl mit <code>sudo</code> ausführen kann, solange er sein Passwort angibt.</p>

<h3 id="gruppenberechtigungszeilen">Gruppenberechtigungszeilen</h3>

<p>Die nächsten beiden Zeilen ähneln den Benutzerberechtigungszeilen, geben jedoch <code>sudo</code>-Regeln für Gruppen an.</p>

<p>Namen, die mit <code>%</code> beginnen, geben Gruppennamen an.</p>

<p>Hier sehen wir, dass die <strong>Administratorgruppe</strong> jeden Befehl als jeder Benutzer auf jedem Host ausführen kann. Ebenso hat die <strong>sudo</strong>-Gruppe die gleichen Berechtigungen, kann aber auch wie jede andere Gruppe ausgeführt werden.</p>

<h3 id="inklusive-zeile-etc-sudoers-d">Inklusive Zeile /etc/sudoers.d</h3>

<p>Die letzte Zeile könnte auf den ersten Blick wie ein Kommentar aussehen:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .

#includedir /etc/sudoers.d
</code></pre>
<p>Sie **beginnt mit einem <code>#</code>, das normalerweise einen Kommentar anzeigt. Diese Zeile zeigt jedoch tatsächlich an, dass Dateien im Verzeichnis <code>/etc/sudoers.d</code> ebenfalls bezogen und angewendet werden.</p>

<p>Dateien in diesem Verzeichnis folgen denselben Regeln wie die Datei <code>/etc/sudoers</code>. Jede Datei, die nicht mit <code>~</code> endet und keinen <code>.</code> enthält, wird gelesen und an die <code>sudo</code>-Konfiguration angehängt.</p>

<p>Dies ist hauptsächlich für Anwendungen gedacht, die die <code>sudo</code>-Berechtigungen bei der Installation ändern möchten. Wenn Sie alle zugehörigen Regeln in einer einzigen Datei im Verzeichnis <code>/etc/sudoers.d</code> ablegen, können Sie leicht erkennen, welche Berechtigungen welchen Konten zugeordnet sind, und Anmeldeinformationen einfach umkehren, ohne die Datei <code>/etc/sudoers</code> direkt bearbeiten zu müssen.</p>

<p>Wie bei der Datei <code>/etc/sudoers</code> selbst sollten Sie Dateien im Verzeichnis <code>/etc/sudoers.d</code> immer mit <code>visudo</code> bearbeiten. Die Syntax zum Bearbeiten dieser Dateien lautet:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo -f /etc/sudoers.d/<span class="highlight">file_to_edit</span>
</li></ul></code></pre>
<h2 id="so-geben-sie-einem-benutzer-sudo-berechtigungen">So geben Sie einem Benutzer Sudo-Berechtigungen</h2>

<p>Die häufigste Operation, die Benutzer beim Verwalten von <code>sudo</code>-Berechtigungen ausführen möchten, besteht darin, einem neuen Benutzer allgemeinen <code>sudo</code>-Zugriff zu gewähren. Dies ist nützlich, wenn Sie einem Konto vollen Administratorzugriff auf das System gewähren möchten.</p>

<p>Der einfachste Weg, dies auf einem System zu tun, das mit einer Allzweck-Verwaltungsgruppe wie dem Ubuntu-System in diesem Leitfaden eingerichtet ist, besteht darin, den betreffenden Benutzer zu dieser Gruppe hinzuzufügen.</p>

<p>Unter Ubuntu 20.04 verfügt die <code>sudo</code>-Gruppe beispielsweise über vollständige Administratorrechte. Wir können einem Benutzer dieselben Berechtigungen gewähren, indem wir ihn der Gruppe wie folgt hinzufügen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG sudo <span class="highlight">username</span>
</li></ul></code></pre>
<p>Der Befehl <code>gpasswd</code> kann auch verwendet werden:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> sudo
</li></ul></code></pre>
<p>Beides wird dasselbe bewirken.</p>

<p>Unter CentOS ist dies normalerweise die <code>wheel</code>-Gruppe anstelle der <code>sudo</code>-Gruppe:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG wheel <span class="highlight">username</span>
</li></ul></code></pre>
<p>Oder unter Verwendung von <code>gpasswd</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> wheel
</li></ul></code></pre>
<p>Wenn das Hinzufügen des Benutzers zur Gruppe unter CentOS nicht sofort funktioniert, müssen Sie möglicherweise die Datei <code>/etc/sudoers</code> bearbeiten, um den Gruppennamen zu kommentieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
%wheel ALL=(ALL) ALL
. . .
</code></pre>
<h2 id="so-richten-sie-benutzerdefinierte-regeln-ein">So richten Sie benutzerdefinierte Regeln ein</h2>

<p>Nachdem wir uns mit der allgemeinen Syntax der Datei vertraut gemacht haben, erstellen wir einige neue Regeln.</p>

<h3 id="so-erstellen-sie-alias-dateien">So erstellen Sie Alias-Dateien</h3>

<p>Die <code>sudoers</code>-Datei kann einfacher organisiert werden, indem Dinge mit verschiedenen Arten von „Aliases“ gruppiert werden.</p>

<p>Zum Beispiel können wir drei verschiedene Benutzergruppen mit überlappender Mitgliedschaft erstellen:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
User_Alias      GROUPONE = abby, brent, carl
User_Alias      GROUPTWO = brent, doris, eric,
User_Alias      GROUPTHREE = doris, felicia, grant
. . .
</code></pre>
<p>Gruppennamen müssen mit einem Großbuchstaben beginnen. Wir können dann Mitgliedern von <code>GROUPTWO</code> erlauben, die <code>apt</code>-Datenbank zu aktualisieren, indem wir eine Regel wie die folgende erstellen:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = /usr/bin/apt-get update
. . .
</code></pre>
<p>Wenn Sie keinen Benutzer/keine Gruppe angeben, die wie oben ausgeführt werden soll, ist <code>sudo</code> standardmäßig der <strong>root</strong>-Benutzer.</p>

<p>Wir können Mitgliedern von <code>GROUPTHREE</code> erlauben, den Computer herunterzufahren und neu zu starten, indem wir einen „Befehls-Alias“ erstellen und diesen in einer Regel für <code>GROUPTHREE</code> verwenden:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Cmnd_Alias      POWER = /sbin/shutdown, /sbin/halt, /sbin/reboot, /sbin/restart
GROUPTHREE  ALL = POWER
. . .
</code></pre>
<p>Wir erstellen einen Befehls-Alias namens <code>POWER</code>, der Befehle zum Ausschalten und Neustarten des Computers enthält. Wir erlauben dann den Mitgliedern von <code>GROUPTHREE</code>, diese Befehle auszuführen.</p>

<p>Wir können auch Aliase „Ausführen als“ erstellen, die den Teil der Regel ersetzen können, der den Benutzer angibt, der den Befehl ausführen soll:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Runas_Alias     WEB = www-data, apache
GROUPONE    ALL = (WEB) ALL
. . .
</code></pre>
<p>Auf diese Weise kann jeder, der Mitglied von <code>GROUPONE</code> ist, Befehle als <code>www-Datenbenutzer</code> oder <code>Apache</code>-Benutzer ausführen.</p>

<p>Denken Sie daran, dass spätere Regeln frühere Regeln überschreiben, wenn ein Konflikt zwischen beiden besteht.</p>

<h3 id="so-sperren-sie-regeln">So sperren Sie Regeln</h3>

<p>Es gibt eine Reihe von Möglichkeiten, wie Sie mehr Kontrolle darüber erlangen können, wie <code>sudo</code> auf einen Anruf reagiert.</p>

<p>Der dem <code>mlocate</code>-Paket zugeordnete Befehl <code>updateb</code> ist auf einem Einzelbenutzersystem relativ harmlos. Wenn wir Benutzern erlauben möchten, es mit <strong>root</strong>-Berechtigungen auszuführen, <em>ohne</em> ein Passwort eingeben zu müssen, können wir eine Regel wie die folgende festlegen:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPONE    ALL = NOPASSWD: /usr/bin/updatedb
. . .
</code></pre>
<p><code>NOPASSWD</code> ist ein „Tag“, das bedeutet, dass kein Passwort angefordert wird. Es hat einen Begleitbefehl namens <code>PASSWD</code>, der das Standardverhalten ist. Ein Tag ist für den Rest der Regel relevant, es sei denn, es wird später von seinem „Twin“ -Tag außer Kraft gesetzt.</p>

<p>Zum Beispiel können wir eine Zeile wie diese haben:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = NOPASSWD: /usr/bin/updatedb, PASSWD: /bin/kill
. . .
</code></pre>
<p>Ein weiteres hilfreiches Tag ist <code>NOEXEC</code>, mit dem in bestimmten Programmen gefährliches Verhalten verhindert werden kann.</p>

<p>Beispielsweise können einige Programme, wie z. B. <code>less</code>, andere Befehle erzeugen, indem man dies über seine Benutzeroberfläche eingibt:</p>
<pre class="code-pre "><code>!<span class="highlight">command_to_run</span>
</code></pre>
<p>Dies führt grundsätzlich jeden Befehl aus, den der Benutzer mit denselben Berechtigungen erteilt, unter denen <code>less</code> ausgeführt wird, was sehr gefährlich sein kann.</p>

<p>Um dies einzuschränken, könnten wir eine Zeile wie die folgende verwenden:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
<span class="highlight">username</span>  ALL = NOEXEC: /usr/bin/less
. . .
</code></pre>
<h2 id="verschiedene-informationen">Verschiedene Informationen</h2>

<p>Es gibt einige weitere Informationen, die beim Umgang mit <code>sudo</code> nützlich sein können.</p>

<p>Wenn Sie in der Konfigurationsdatei einen Benutzer oder eine Gruppe als „Ausführen als“ angegeben haben, können Sie Befehle als diese Benutzer mithilfe der Flags <code>-u</code> bzw. <code>-g</code> ausführen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -u <span class="highlight">run_as_user command</span>
</li><li class="line" data-prefix="$">sudo -g <span class="highlight">run_as_group command</span>
</li></ul></code></pre>
<p>Praktischerweise speichert <code>sudo</code> Ihre Authentifizierungsdaten standardmäßig für einen bestimmten Zeitraum in einem Terminal. Dies bedeutet, dass Sie Ihr Passwort erst erneut eingeben müssen, wenn dieser Timer abgelaufen ist.</p>

<p>Wenn Sie diesen Timer aus Sicherheitsgründen löschen möchten, wenn Sie mit dem Ausführen von Verwaltungsbefehlen fertig sind, können Sie Folgendes ausführen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li></ul></code></pre>
<p>Wenn Sie andererseits den Befehl <code>sudo</code> „vorbereiten“ möchten, damit Sie später nicht dazu aufgefordert werden, oder Ihren <code>sudo</code>-Mietvertrag erneuern möchten, können Sie jederzeit Folgendes eingeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -v
</li></ul></code></pre>
<p>Sie werden aufgefordert, Ihr Passwort einzugeben, das für spätere <code>sudo</code>-Verwendungen zwischengespeichert wird, bis der <code>sudo</code>-Zeitrahmen abläuft.</p>

<p>Wenn Sie sich nur fragen, welche Berechtigungen für Ihren Benutzernamen definiert sind, können Sie Folgendes eingeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -l
</li></ul></code></pre>
<p>Dadurch werden alle Regeln in der Datei <code>/etc/sudoers</code> aufgelistet, die für Ihren Benutzer gelten. Dies gibt Ihnen eine gute Vorstellung davon, was Sie mit <code>sudo</code> als Benutzer tun dürfen oder nicht.</p>

<p>Es gibt viele Fälle, in denen Sie einen Befehl ausführen und dieser fehlschlägt, weil Sie vergessen haben, ihm <code>sudo</code> voranzustellen. Um zu vermeiden, dass Sie den Befehl erneut eingeben müssen, können Sie eine Bash-Funktion nutzen, die „letzten Befehl wiederholen“ bedeutet:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo !!
</li></ul></code></pre>
<p>Das doppelte Ausrufezeichen wiederholt den letzten Befehl. Wir haben <code>sudo</code> vorangestellt, um den nicht privilegierten Befehl schnell in einen privilegierten Befehl umzuwandeln.</p>

<p>Für ein bisschen Spaß können Sie Ihrer <code>/etc/sudoers</code>-Datei mit <code>visudo</code> die folgende Zeile hinzufügen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Defaults    insults
. . .
</code></pre>
<p>Dies führt dazu, dass <code>sudo</code> eine dumme Beleidigung zurückgibt, wenn ein Benutzer ein falsches Passwort für <code>sudo</code> eingibt. Wir können <code>sudo -k</code> verwenden, um das vorherige zwischengespeicherte <code>sudo</code>-Passwort zu löschen und es auszuprobieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li><li class="line" data-prefix="$">sudo ls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[sudo] password for demo:    # <span class="highlight">enter an incorrect password here to see the results</span>
Your mind just hasn't been the same since the electro-shock, has it?
[sudo] password for demo:
My mind is going. I can feel it.
</code></pre>
<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Sie sollten nun ein grundlegendes Verständnis für das Lesen und Ändern der <code>sudoers</code>-Datei und einen Überblick über die verschiedenen Methoden haben, mit denen Sie <strong>root</strong>-Berechtigungen erhalten können.</p>

<p>Denken Sie daran, dass Superuser-Berechtigungen regulären Benutzern aus einem bestimmten Grund nicht gewährt werden. Es ist wichtig, dass Sie verstehen, was jeder Befehl tut, den Sie mit <strong>root</strong>-Berechtigungen ausführen. Übernehmen Sie die Verantwortung nicht leichtfertig. Erfahren Sie, wie Sie diese Tools am besten für Ihren Anwendungsfall verwenden und nicht benötigte Funktionen sperren können.</p>
