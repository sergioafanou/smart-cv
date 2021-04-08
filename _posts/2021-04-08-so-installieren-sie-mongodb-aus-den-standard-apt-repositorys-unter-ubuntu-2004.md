---
title : "So installieren Sie MongoDB aus den Standard-APT-Repositorys unter Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-from-the-default-apt-repositories-on-ubuntu-20-04-de
image: "https://sergio.afanou.com/assets/images/image-midres-50.jpg"
---

<h3 id="einführung">Einführung</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a> ist eine kostenlose und Open-Source-NoSQL-Dokumentendatenbank, die häufig in modernen Webanwendungen verwendet wird.</p>

<p>In diesem Tutorial installieren Sie MongoDB, verwalten den Dienst und aktivieren optional den Fernzugriff.</p>

<p><span class='note'><strong>Hinweis</strong>: Zum jetzigen Zeitpunkt wird in diesem Tutorial die Version <span class="highlight">3.6</span> von MongoDB installiert. Dies ist die Version, die in den Standard-Ubuntu-Repositorys verfügbar ist. Wir empfehlen jedoch generell, stattdessen die neueste Version von MongoDB zu installieren - Version <span class="highlight">4.4</span> zum jetzigen Zeitpunkt. Wenn Sie die neueste Version von MongoDB installieren möchten, empfehlen wir Ihnen, diese Anleitung zur <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Installation von MongoDB unter Ubuntu 20.04 aus dem Quellcode zu befolgen</a>.<br></span></p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Um dieser Anleitung zu folgen, benötigen Sie:</p>

<ul>
<li>Ein Ubuntu 20.04-Server, der gemäß des <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Leitfadens zur Ersteinrichtung des Servers</a> für Ubuntu 20.04 eingerichtet wurde, einschließlich eines non-root users, der über sudo-Berechtigungen verfügt, und einer mit UFW konfigurierten Firewall.</li>
</ul>

<h2 id="schritt-1-—-installieren-von-mongodb">Schritt 1 — Installieren von MongoDB</h2>

<p>Zu den offiziellen Paket-Repositorys von Ubuntu gehört MongoDB, was bedeutet, dass wir die erforderlichen Pakete mit <code>apt</code> installieren können. Wie in der Einführung erwähnt, ist die aus den Standard-Repositorys verfügbare Version nicht die neueste. Befolgen Sie stattdessen <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">dieses Tutorial</a>, um die neueste Version von Mongo zu installieren.</p>

<p>Aktualisieren Sie zunächst die Paketliste, um die neueste Version der Repository-Listen zu erhalten:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li></ul></code></pre>
<p>Installieren Sie nun die MongoDB selbst:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install mongodb
</li></ul></code></pre>
<p>Dieser Befehl fordert Sie auf, zu bestätigen, dass Sie das Paket <code>mongodb</code> und seine Abhängigkeiten installieren möchten. Drücken Sie dazu <code>Y</code> und dann <code>ENTER</code>.</p>

<p>Der Datenbankserver wird nach der Installation automatisch gestartet.</p>

<p>Überprüfen wir als Nächstes, ob der Server ausgeführt wird und korrekt funktioniert.</p>

<h2 id="schritt-2-—-Überprüfen-des-dienstes-und-der-datenbank">Schritt 2 — Überprüfen des Dienstes und der Datenbank</h2>

<p>Der Installationsprozess hat MongoDB automatisch gestartet. Überprüfen Sie jedoch, ob der Dienst gestartet wurde und die Datenbank funktioniert.</p>

<p>Überprüfen Sie als erstes den Status des Dienstes:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Sie sehen diese Ausgabe:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>● mongodb.service - An object/document-oriented database
     Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset: enabled)
     Active: <span class="highlight">active (running)</span> since Thu 2020-10-08 14:23:22 UTC; 49s ago
       Docs: man:mongod(1)
   Main PID: 2790 (mongod)
      Tasks: 23 (limit: 2344)
     Memory: 42.2M
     CGroup: /system.slice/mongodb.service
             └─2790 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /etc/mongodb.conf
</code></pre>
<p>Laut dieser Ausgabe wird der MongoDB-Server ausgeführt.</p>

<p>Wir können dies weiter überprüfen, indem wir tatsächlich eine Verbindung zum Datenbankserver herstellen und den folgenden Diagnosebefehl ausführen. Dadurch werden die aktuelle Datenbankversion, die Serveradresse und der Port sowie die Ausgabe des Statusbefehls ausgegeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo --eval 'db.runCommand({ connectionStatus: 1 })'
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>MongoDB shell version v<span class="highlight">3.6.8</span>
connecting to: <span class="highlight">mongodb://127.0.0.1:27017</span>
Implicit session: session { "id" : UUID("e3c1f2a1-a426-4366-b5f8-c8b8e7813135") }
MongoDB server version: <span class="highlight">3.6.8</span>
{
    "authInfo" : {
        "authenticatedUsers" : [ ],
        "authenticatedUserRoles" : [ ]
    },
    "ok" : 1
}
</code></pre>
<p>Ein Wert von <code>1</code> für das <code>ok</code>-Feld in der Antwort gibt an, dass der Server ordnungsgemäß funktioniert:</p>

<p>Als Nächstes werden wir uns ansehen, wie Sie die Serverinstanz verwalten.</p>

<h2 id="schritt-3-—-verwalten-des-mongodb-dienstes">Schritt 3 — Verwalten des MongoDB-Dienstes</h2>

<p>Der in Schritt 1 beschriebene Installationsprozess konfiguriert MongoDB als <code>systemd</code>-Dienst. Dies bedeutet, dass Sie ihn zusammen mit allen anderen Systemdiensten in Ubuntu mit Standard-<code>systemctl</code>-Befehlen verwalten können.</p>

<p>Um den Status des Dienstes zu überprüfen, geben Sie Folgendes ein:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Sie können den Server jederzeit stoppen, indem Sie Folgendes eingeben:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop mongodb
</li></ul></code></pre>
<p>Um den Server zu starten, wenn er angehalten wurde, geben Sie Folgendes ein:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start mongodb
</li></ul></code></pre>
<p>Sie können den Server auch mit dem folgenden Befehl neu starten:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>Standardmäßig ist MongoDB so konfiguriert, dass es automatisch mit dem Server gestartet wird. Falls Sie den automatischen Start deaktivieren möchten, geben Sie Folgendes ein:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable mongodb
</li></ul></code></pre>
<p>Sie können den automatischen Start jederzeit mit dem folgenden Befehl wieder aktivieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable mongodb
</li></ul></code></pre>
<p>Als nächstes passen wir die Firewall-Einstellungen für unsere MongoDB-Installation an.</p>

<h2 id="schritt-4-—-anpassen-der-firewall-optional">Schritt 4 — Anpassen der Firewall (optional)</h2>

<p>Angenommen, Sie haben die Anweisungen zum <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">ersten Server-Setup-Tutorial</a> befolgt, um die Firewall auf Ihrem Server zu aktivieren, ist der Zugriff auf den MongoDB-Server über das Internet nicht möglich.</p>

<p>Wenn Sie MongoDB nur lokal mit Anwendungen verwenden möchten, die auf dem gleichen Server ausgeführt werden, ist dies die empfohlene und sichere Einstellung. Wenn Sie jedoch über das Internet eine Verbindung mit Ihrem MongoDB-Server herstellen möchten, müssen Sie die eingehenden Verbindungen durch Hinzufügen einer UFW-Regel zulassen.</p>

<p>Um von überall aus auf MongoDB über den Standardport <code>27017</code> zuzugreifen, können Sie <code>sudo ufw allow <span class="highlight">27017</span></code> ausführen. Wenn Sie jedoch bei einer Standardinstallation den Internetzugang zum MongoDB-Server aktivieren, kann jeder uneingeschränkt auf den Datenbankserver und seine Daten zugreifen.</p>

<p>In den meisten Fällen sollte nur von bestimmten vertrauenswürdigen Orten auf MongoDB zugegriffen werden, wie z. B. von einem anderen Server, der eine Anwendung hostet. Um nur einem anderen vertrauenswürdigen Server den Zugriff auf den Standardport von MongoDB zu ermöglichen, können Sie die IP-Adresse des Remote-Servers im Befehl <code>ufw</code> angeben. Auf diese Weise darf nur dieser Computer explizit eine Verbindung herstellen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">trusted_server_ip</span>/32 to any port <span class="highlight">27017</span>
</li></ul></code></pre>
<p>Sie können die Änderung der Firewall-Einstellungen mit <code>ufw</code> überprüfen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>
<p>Sie sollten in der Ausgabe den Datenverkehr zu Port <code>27017</code> erlauben. Beachten Sie, dass, wenn Sie beschlossen haben, nur eine bestimmte IP-Adresse für die Verbindung zum MongoDB-Server zuzulassen, die IP-Adresse des zulässigen Speicherorts anstelle von <code>Anywhere</code> in der Ausgabe dieses Befehls aufgeführt wird:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
<span class="highlight">27017                      ALLOW       Anywhere</span>
OpenSSH (v6)               ALLOW       Anywhere (v6)
<span class="highlight">27017 (v6)                 ALLOW       Anywhere (v6)</span>
</code></pre>
<p>Weitere erweiterte Firewall-Einstellungen zum Einschränken des Zugriffs auf Dienste finden Sie in <a href="https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands">UFW-Grundlagen: gebräuchliche Firewall-Regeln und -Befehle</a>.</p>

<p>Obwohl der Port geöffnet ist, überwacht MongoDB immer noch nur die lokale Adresse <code>127.0.0.1</code>. Fügen Sie der Datei <code>mongodb.conf</code> die öffentlich routbare IP-Adresse Ihres Servers hinzu, um Remoteverbindungen zuzulassen.</p>

<p>Öffnen Sie die MongoDB-Konfigurationsdatei in Ihrem bevorzugten Editor. Dieser Beispielbefehl verwendet <code>nano</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongodb.conf
</li></ul></code></pre>
<p>Fügen Sie die IP-Adresse Ihres MongoDB-Servers zum <code>bindIP</code>-Wert hinzu. Stellen Sie sicher, dass Sie ein Komma zwischen der vorhandenen IP-Adresse und der hinzugefügten Adresse einrichten:</p>
<div class="code-label " title="/etc/mongodb.conf">/etc/mongodb.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
logappend=true

bind_ip = 127.0.0.1<span class="highlight">,your_server_ip</span>
#port = 27017

...
</code></pre>
<p>Speichern Sie die Datei und beenden Sie den Editor. Wenn Sie <code>nano</code> zum Bearbeiten der Datei verwendet haben, drücken Sie dazu <code>STRG + X</code>, <code>Y</code> und dann <code>ENTER</code>.</p>

<p>Starten Sie dann den MongoDB-Dienst neu:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>MongoDB wartet jetzt auf Remoteverbindungen, aber jeder kann darauf zugreifen. Befolgen Sie <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-mongodb-on-ubuntu-20-04">die Anweisungen zum Sichern von MongoDB unter Ubuntu 20.04</a>, um einen Administrator hinzuzufügen und die Dinge weiter zu sperren.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Weitere Anleitungen zum Konfigurieren und Verwenden von MongoDB finden Sie in <a href="https://www.digitalocean.com/community/search?q=mongodb">diesen Community-Artikeln von DigitalOcean</a>. Die offizielle <a href="https://docs.mongodb.com/v3.6/">MongoDB-Dokumentation</a> ist auch eine großartige Ressource für die Möglichkeiten, die MongoDB bietet.</p>
