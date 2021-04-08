---
title : "So verwenden Sie ps, kill und schön zum Verwalten von Prozessen unter Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux-de
image: "https://sergio.afanou.com/assets/images/image-midres-15.jpg"
---

<h3 id="einführung">Einführung</h3>

<hr>

<p>Auf einem Linux-Server werden wie auf jedem anderen Computer, mit dem Sie möglicherweise vertraut sind, Anwendungen ausgeführt. Auf dem Computer werden diese als „Prozesse“ bezeichnet.</p>

<p>Während Linux die Verwaltung auf niedriger Ebene hinter den Kulissen im Lebenszyklus eines Prozesses übernimmt, benötigen Sie eine Möglichkeit zur Interaktion mit dem Betriebssystem, um es von einer höheren Ebene aus zu verwalten.</p>

<p>In diesem Leitfaden werden wir einige einfache Aspekte der Prozessverwaltung erörtern. Linux bietet eine reichliche Sammlung von Tools für diesen Zweck.</p>

<p>Wir werden diese Ideen auf einem Ubuntu 12.04 VPS untersuchen, aber jede moderne Linux-Distribution funktioniert auf ähnliche Weise.</p>

<h2 id="so-zeigen-sie-laufende-prozesse-unter-linux-an">So zeigen Sie laufende Prozesse unter Linux an</h2>

<hr>

<h3 id="top">top</h3>

<hr>

<p>Der einfachste Weg, um herauszufinden, welche Prozesse auf Ihrem Server ausgeführt werden, besteht darin, den Befehl <code>top</code> auszuführen:</p>
<pre class="code-pre "><code>top***

top - 15:14:40 bis 46 min, 1 Benutzer, Lastdurchschnitt: 0,00, 0,01, 0,05 Aufgaben: 56 insgesamt, 1 laufend, 55 inaktiv, 0 gestoppt, 0 Zombie Cpu(s):  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem: 1019600k gesamt, 316576k gebraucht, 703024k frei, 7652k Puffer Swap: 0k insgesamt, 0k verwendet, 0k frei, 258976k zwischengespeichert   PID USER PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND               1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.07 ksoftirqd/0            6 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0            7 root      RT   0     0    0    0 S  0.0  0.0   0:00.03 watchdog/0             8 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 cpuset                 9 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 khelper               10 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kdevtmpfs
</code></pre>
<p>Der oberste Informationsblock enthält Systemstatistiken wie die Systemlast und die Gesamtzahl der Aufgaben.</p>

<p>Sie können leicht erkennen, dass 1 Prozess ausgeführt wird und 55 Prozesse inaktiv sind (auch bekannt als inaktiv/ohne CPU-Ressourcen).</p>

<p>Der untere Teil enthält die laufenden Prozesse und ihre Nutzungsstatistiken.</p>

<h3 id="htop">htop</h3>

<hr>

<p>Eine verbesserte Version von <code>top</code> namens <code>htop</code> ist in den Repositorys verfügbar. Installieren Sie sie mit diesem Befehl:</p>
<pre class="code-pre "><code>sudo apt-get install htop
</code></pre>
<p>Wenn wir den Befehl <code>htop</code> ausführen, sehen wir, dass es eine benutzerfreundlichere Anzeige gibt:</p>
<pre class="code-pre "><code>htop***

  Mem[|||||||||||           49/995MB]     Durchschnittslast: 0.00 0.03 0.05   CPU[                          0.0%]     Aufgaben: 21, 3 thr; 1 laufend   Swp[                         0/0MB]     Betriebszeit: 00:58:11   PID USER PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command  1259 root       20   0 25660  1880  1368 R  0.0  0.2  0:00.06 htop     1 root       20   0 24188  2120  1300 S  0.0  0.2  0:00.56 /sbin/init   311 root       20   0 17224   636   440 S  0.0  0.1  0:00.07 upstart-udev-brid   314 root       20   0 21592  1280   760 S  0.0  0.1  0:00.06 /sbin/udevd --dae   389 messagebu  20   0 23808   688   444 S  0.0  0.1  0:00.01 dbus-daemon --sys   407 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.02 rsyslogd -c5   408 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   409 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   406 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.04 rsyslogd -c5   553 root       20   0 15180   400   204 S  0.0  0.0  0:00.01 upstart-socket-br
</code></pre>
<p>Sie können hier <a href="https://www.digitalocean.com/community/articles/how-to-use-top-netstat-du-other-tools-to-monitor-server-resources#process">mehr über die Verwendung von top und htop erfahren</a>.</p>

<h2 id="verwendung-von-ps-zum-auflisten-von-prozessen">Verwendung von ps zum Auflisten von Prozessen</h2>

<hr>

<p>Sowohl <code>top</code> als auch <code>htop</code> bieten eine schöne Benutzeroberfläche, um laufende Prozesse zu sehen, die einem grafischen Aufgabenmanager ähneln.</p>

<p>Diese Tools sind jedoch nicht immer flexibel genug, um alle Szenarien angemessen zu behandeln. Ein leistungsfähiger Befehl namens <code>ps</code> ist oft die Antwort auf diese Probleme.</p>

<p>Wenn er ohne Argumente aufgerufen wird, kann die Ausgabe etwas fehlerhafter sein:</p>
<pre class="code-pre "><code>ps***

  PID TTY          TIME CMD  1017 pts/0    00:00:00 bash  1262 pts/0    00:00:00 ps
</code></pre>
<p>Diese Ausgabe zeigt alle mit dem aktuellen Benutzer und der Terminalsitzung verknüpften Prozesse an. Dies ist sinnvoll, da wir derzeit nur <code>bash</code> und <code>ps</code> mit diesem Terminal ausführen.</p>

<p>Um ein vollständigeres Bild der Prozesse auf diesem System zu erhalten, können wir Folgendes ausführen:</p>
<pre class="code-pre "><code>ps aux***

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND root         1  0.0  0.2  24188  2120 ?        Ss   14:28   0:00 /sbin/initroot         2  0.0  0.0      0     0 ?        S    14:28   0:00 [kthreadd] root         3  0.0  0.0      0     0 ?        S    14:28   0:00 [ksoftirqd/0] root         6  0.0  0.0      0     0 ?        S    14:28   0:00 [migration/0] root         7  0.0  0.0      0     0 ?        S    14:28   0:00 [watchdog/0] root         8  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [cpuset] root         9  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [khelper] . . .
</code></pre>
<p>Diese Optionen weisen <code>ps</code> an, Prozesse, die allen Benutzern gehören (unabhängig von ihrer Terminalzuordnung), in einem benutzerfreundlichen Format anzuzeigen.</p>

<p>Um eine <em>Baumansicht</em> zu sehen, in der hierarchische Beziehungen illustriert werden, können wir den Befehl mit diesen Optionen ausführen:</p>
<pre class="code-pre "><code>ps axjf***

 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND     0     2     0     0 ?           -1 S        0   0:00 [kthreadd]     2     3     0     0 ?           -1 S        0   0:00  \_ [ksoftirqd/0]     2     6     0     0 ?           -1 S        0   0:00  \_ [migration/0]     2     7     0     0 ?           -1 S        0   0:00  \_ [watchdog/0]     2     8     0     0 ?           -1 S&lt;       0   0:00  \_ [cpuset]     2     9     0     0 ?           -1 S&lt;       0   0:00  \_ [khelper]     2    10     0     0 ?           -1 S        0   0:00  \_ [kdevtmpfs]     2    11     0     0 ?           -1 S&lt;       0   0:00  \_ [netns] . . .
</code></pre>
<p>Wie Sie sehen können, wird der Prozess <code>kthreadd</code> als übergeordnetes Element des Prozesses <code>ksoftirqd/0</code> und der anderen Prozesse angezeigt.</p>

<h3 id="eine-anmerkung-zu-prozess-ids">Eine Anmerkung zu Prozess-IDs</h3>

<hr>

<p>In Linux- und Unix-ähnlichen Systemen wird jedem Prozess einer <strong>Prozess-ID</strong> oder <strong>PID</strong> zugewiesen. So identifiziert und verfolgt das Betriebssystem Prozesse.</p>

<p>Eine schnelle Möglichkeit zum Abrufen der PID eines Prozesses ist mit dem Befehl <code>pgrep</code>:</p>
<pre class="code-pre "><code>pgrep bash***

1017
</code></pre>
<p>Dadurch wird die Prozess-ID einfach abfragt und zurückgegeben.</p>

<p>Der erste beim Booten erzeugte Prozess namens <em>init</em> erhält die PID „1“.</p>
<pre class="code-pre "><code>pgrep init***

1
</code></pre>
<p>Dieser Prozess ist dann dafür verantwortlich, jeden anderen Prozess auf dem System zu erzeugen. Die späteren Prozesse erhalten größere PID-Nummern.</p>

<p>Das <em>übergeordnete Element</em> eines Prozesses ist der Prozess, der für das Ablegen verantwortlich war. Übergeordnete Prozesse verfügen über eine <strong>PPID</strong>, die Sie in den Spaltenüberschriften vieler Prozessverwaltungsanwendungen sehen können, einschließlich <code>top</code>, <code>htop</code> und <code>ps</code>.</p>

<p>Jede Kommunikation zwischen dem Benutzer und dem Betriebssystem über Prozesse umfasst die Übersetzung zwischen Prozessnamen und PIDs zu einem bestimmten Zeitpunkt während des Vorgangs. Aus diesem Grund teilen Dienstprogramme Ihnen die PID mit.</p>

<h3 id="Übergeordnete-untergeordnete-beziehungen">Übergeordnete-untergeordnete Beziehungen</h3>

<hr>

<p>Das Erstellen eines untergeordneten Prozesses erfolgt in zwei Schritten: fork(), das einen neuen Adressraum erstellt und die Ressourcen des übergeordneten Elements per Copy-on-Write kopiert, um dem untergeordneten Prozess zur Verfügung zu stehen; und exec(), das eine ausführbare Datei in den Adressraum lädt und ausführt.</p>

<p>Für den Fall, dass ein untergeordneter Prozess vor seinem übergeordneten Prozess beendet wird, wird der untergeordnete Prozess zu einem Zombie, bis der übergeordnete Prozess Informationen darüber gesammelt oder dem Kernel angezeigt hat, dass er diese Informationen nicht benötigt. Die Ressourcen aus dem untergeordneten Prozess werden dann freigegeben. Wenn der übergeordnete Prozess jedoch vor dem untergeordneten Prozess beendet wird, wird der untergeordnete Prozess von init übernommen, obwohl er auch einem anderen Prozess neu zugewiesen werden kann.</p>

<h2 id="so-senden-sie-prozesssignale-in-linux">So senden Sie Prozesssignale in Linux</h2>

<hr>

<p>Alle Prozesse in Linux reagieren auf <em>Signale</em>. Signale sind eine Methode auf Betriebssystemebene, mit der Programme angewiesen werden, ihr Verhalten zu beenden oder zu ändern.</p>

<h3 id="so-senden-sie-prozesssignale-nach-pid">So senden Sie Prozesssignale nach PID</h3>

<hr>

<p>Die häufigste Art, Signale an ein Programm weiterzuleiten, ist mit dem Befehl <code>kill</code>.</p>

<p>Wie Sie möglicherweise erwarten, besteht die Standardfunktion dieses Dienstprogramms darin, zu versuchen, einen Prozess zu beenden:</p>

<pre>kill <span class="highlight">PID_of_target_process</span></pre>

<p>Dadurch wird das <strong>TERM</strong>-Signal an den Prozess gesendet. Das TERM-Signal weist den Prozess an, zu beenden. Dadurch kann das Programm Reinigungsvorgänge durchführen und reibungslos beenden.</p>

<p>Wenn sich das Programm schlecht verhält und bei Erhalt des TERM-Signals nicht beendet wird, können wir das Signal durch Weiterleiten des <code>KILL</code>-Signals eskalieren:</p>

<pre>kill -KILL <span class="highlight">PID_of_target_process</span></pre>

<p>Dies ist ein spezielles Signal, das nicht an das Programm gesendet wird.</p>

<p>Stattdessen wird es dem Betriebssystem-Kernel übergeben, der den Prozess herunterschaltet. Dies wird verwendet, um Programme zu umgehen, die die an sie gesendeten Signale ignorieren.</p>

<p>Jedem Signal ist eine Nummer zugeordnet, die anstelle des Namens übergeben werden kann. Beispielsweise können Sie „-15“ anstelle von „-TERM“ und „-9“ anstelle von „-KILL“ übergeben.</p>

<h3 id="so-verwenden-sie-signale-für-andere-zwecke">So verwenden Sie Signale für andere Zwecke</h3>

<hr>

<p>Signale werden nicht nur zum Herunterfahren von Programmen verwendet. Sie können auch verwendet werden, um andere Aktionen auszuführen.</p>

<p>Beispielsweise werden viele Daemons neu gestartet, wenn sie das <code>HUP</code>- oder Auflegesignal erhalten. Apache ist ein Programm, das so funktioniert.</p>

<pre>sudo kill -HUP <span class="highlight">pid_of_apache</span></pre>

<p>Der obige Befehl führt dazu, dass Apache seine Konfigurationsdatei neu lädt und Inhalte wiederbelebt.</p>

<p>Sie können alle Signale auflisten, die mit kill gesendet werden können, indem Sie Folgendes eingeben:</p>
<pre class="code-pre "><code>kill -l***

1) SIGHUP    2) SIGINT   3) SIGQUIT  4) SIGILL   5) SIGTRAP  6) SIGABRT  7) SIGBUS   8) SIGFPE   9) SIGKILL 10) SIGUSR1 11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM . . .
</code></pre>
<h3 id="so-senden-sie-prozesssignale-nach-name">So senden Sie Prozesssignale nach Name</h3>

<hr>

<p>Obwohl die konventionelle Art des Sendens von Signalen durch die Verwendung von PIDs ist, gibt es auch Methoden, dies mit regulären Prozessnamen zu tun.</p>

<p>Der Befehl <code>pkill</code> funktioniert fast genau so wie <code>kill</code>, operiert jedoch stattdessen auf einem Prozessnamen:</p>
<pre class="code-pre "><code>pkill -9 ping
</code></pre>
<p>Der obige Befehl ist das Äquivalent von:</p>
<pre class="code-pre "><code>kill -9 `pgrep ping`
</code></pre>
<p>Wenn Sie ein Signal an jede Instanz eines bestimmten Prozesses senden möchten, können Sie den Befehl <code>killall</code> verwenden:</p>
<pre class="code-pre "><code>killall firefox
</code></pre>
<p>Der obige Befehl sendet das TERM-Signal an jede Instanz von Firefox, das auf dem Computer ausgeführt wird.</p>

<h2 id="so-passen-sie-prozessprioritäten-an">So passen Sie Prozessprioritäten an</h2>

<hr>

<p>Oft möchten Sie anpassen, welchen Prozessen in einer Serverumgebung Priorität eingeräumt wird.</p>

<p>Einige Prozesse können als geschäftskritisch für Ihre Situation angesehen werden, während andere ausgeführt werden können, wenn Ressourcen übrig bleiben.</p>

<p>Linux kontrolliert die Priorität durch einen Wert namens <strong>niceness</strong>.</p>

<p>Hohe Prioritätsaufgaben werden als weniger <em>nett</em> angesehen, da sie auch keine Ressourcen teilen. Prozesse mit niedriger Priorität sind dagegen <em>nett</em>, weil sie darauf bestehen, nur minimale Ressourcen zu verbrauchen.</p>

<p>Als wir am Anfang des Artikels <code>top</code> ausgeführt haben, gab es eine Spalte mit der Bezeichnung „NI“. Dies ist der <em>nette</em> Wert des Prozesses:</p>
<pre class="code-pre "><code>top***

Aufgaben: 56 insgesamt, 1 laufend, 55 inaktiv, 0 gestoppt, 0 Zombie Cpu(s):  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k insgesamt,   324496k verwendet,   695104k frei,     8512k Puffer Swap:   0k insgesamt,   0k verwendet,   0k frei,    264812k zwischengespeichert   PID-BENUTZER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            1635 root      20   0 17300 1200  920 R  0.3  0.1   0:00.01 top                    1 root      20   0 24188 2120 1300 S  0,0  0,2   0:00,56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.11 ksoftirqd/0
</code></pre>
<p>Nette Werte können je nach System zwischen „-19/-20“ (höchste Priorität) und „19/20“ (niedrigste Priorität) liegen.</p>

<p>Um ein Programm mit einem bestimmten netten Wert auszuführen, können wir den Befehl <code>nice</code> verwenden:</p>

<pre>nice -n 15 <span class="highlight">command_to_execute</span></pre>

<p>Dies funktioniert nur, wenn ein neues Programm gestartet wird.</p>

<p>Um den netten Wert eines Programms zu ändern, das bereits ausgeführt wird, verwenden wir ein Tool namens <code>renice</code>:</p>

<pre>renice 0 <span class="highlight">PID_to_prioritize</span></pre>

<p><strong>Hinweis: Während nice zwangsläufig mit einem Befehlsnamen funktioniert, ruft renice die Prozess-PID auf</strong></p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<hr>

<p>Die Prozessverwaltung ist ein Thema, das für neue Benutzer manchmal schwer zu verstehen ist, da sich die verwendeten Tools von ihren grafischen Gegenstücken unterscheiden.</p>

<p>Die Ideen sind jedoch vertraut und intuitiv und werden mit ein wenig Übung zur Gewohnheit. Da Prozesse an allem beteiligt sind, was Sie mit einem Computersystem tun, ist es eine wesentliche Fähigkeit, zu lernen, wie man sie effektiv steuert.</p>

<div class="author">Von Justin Ellingwood</div>
