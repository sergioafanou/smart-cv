---
title : "Comment utiliser ps, kill et nice pour gérer des processus sous Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux-fr
image: "https://sergio.afanou.com/assets/images/image-midres-37.jpg"
---

<h3 id="introduction">Introduction</h3>

<hr>

<p>Un serveur Linux, tout comme tous les autres ordinateurs que vous connaissez, exécute des applications. L'ordinateur considère cela comme des « processus ».</p>

<p>Bien que Linux se chargera de la gestion de bas niveau et en coulisses du cycle de vie d'un processus, vous avez besoin de pouvoir interagir avec le système d'exploitation et à le gérer ainsi à un niveau supérieur.</p>

<p>Dans ce guide, nous allons voir certains des aspects simples de la gestion de processus. Linux vous propose un vaste ensemble d'outils.</p>

<p>Nous allons voir ces idées sur un Ubuntu 12.04 VPS. Cependant, toute distribution Linux moderne fonctionnera de manière similaire.</p>

<h2 id="comment-visualiser-des-processus-en-cours-d-39-exécution-sous-linux">Comment visualiser des processus en cours d'exécution sous Linux</h2>

<hr>

<h3 id="top">top</h3>

<hr>

<p>Afin de déterminer quels sont les processus en cours d'exécution sur votre serveur, la façon la plus simple consiste à exécuter la commande <code>top</code> :</p>
<pre class="code-pre "><code>top***

top - 15:14:40 up 46 min, 1 user, load average: 0.00, 0.01, 0.05 Tasks:  56 total,   1 running, 55 sleeping, 0 stopped, 0 zombie Cpu(s): 0.0%us, 0.0%sy,  0.0%ni,100.0%id, 0.0%wa,  0.0%hi,  0.0%si, 0.0%st Mem:   1019600k total,   316576k used,   703024k free, 7652k buffers Swap:        0k total,        0k used,        0k free,   258976k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            1 root      20 0 24188 2120 1300 0.0 0.2   0:00.56 init     2 root 20   0 0 0 0 S 0 0.0 0:00.00 kthreadd     3 root      20 0 0    0 0 S 0.0 0.0 0:00.07 ksoftirqd/0     6 root RT 0 0    0 S  0.0  0.0   0:00.00 migration/0            7 root      RT   0     0 0    0 S  0.0  0.0   0:00.03 watchdog/0             8 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 cpuset                 9 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 khelper               10 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kdevtmpfs
</code></pre>
<p>Le premier morceau d'information vous donne les statistiques sur les systèmes, comme la charge du système et le nombre total de tâches.</p>

<p>Vous pouvez facilement voir qu'il y a 1 processus en cours d'exécution et que 55 processus sont dormants (aka idle/n'utilisant pas les ressources du processeur).</p>

<p>La partie inférieure comporte les processus en cours d'exécution et leurs statistiques d'utilisation.</p>

<h3 id="htop">htop</h3>

<hr>

<p>Vous disposez d'une version améliorée de <code>top</code>, appelée <code>htop</code>, dans les référentiels. Installez-la avec cette commande :</p>
<pre class="code-pre "><code>sudo apt-get install htop
</code></pre>
<p>Si nous exécutons la commande <code>htop</code>, l'affichage qui apparaîtra sera plus convivial :</p>
<pre class="code-pre "><code>htop***

  Mem[|||||||||||           49/995MB]     Load average: 0.00 0.03 0.05   CPU[                          0.0%]     Tasks: 21, 3 thr; 1 running   Swp[                         0/0 Mo]     Uptime: 00:58:11   PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command  1259 root       20   0 25660  1880  1368 R  0.0  0.2  0:00.06 htop     1 root       20   0 24188  2120  1300 S  0.0  0.2  0:00.56 /sbin/init   311 root       20   0 17224   636   440 S  0.0  0.1  0:00.07 upstart-udev-brid   314 root       20   0 21592  1280   760 S  0.0  0.1  0:00.06 /sbin/udevd --dae   389 messagebu  20   0 23808   688   444 S  0.0  0.1  0:00.01 dbus-daemon --sys   407 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.02 rsyslogd -c5   408 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   409 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   406 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.04 rsyslogd -c5   553 root       20   0 15180   400   204 S  0.0  0.0  0:00.01 upstart-socket-br
</code></pre>
<p>Vous pouvez <a href="https://www.digitalocean.com/community/articles/how-to-use-top-netstat-du-other-tools-to-monitor-server-resources#process">en savoir plus sur la manière d'utiliser top et htop</a> ici.</p>

<h2 id="comment-utiliser-ps-pour-répertorier-des-processus">Comment utiliser ps pour répertorier des processus</h2>

<hr>

<p><code>top</code> et <code>htop</code> vous offrent tous les deux une belle interface pour visualiser les processus en cours d'exécution, similaire à un gestionnaire de tâches graphique.</p>

<p>Cependant, ces outils ne sont pas toujours suffisamment flexibles pour couvrir tous les scénarios correctement. Une commande puissante appelée <code>ps</code> est souvent utilisée pour répondre à ces problèmes.</p>

<p>Lorsque vous appelez une commande sans arguments, la sortie peut manquer d'éclat :</p>
<pre class="code-pre "><code>ps***

  PID TTY          TIME CMD  1017 pts/0    00:00:00 bash  1262 pts/0    00:00:00 ps
</code></pre>
<p>Cette sortie affiche tous les processus associés à l'utilisateur et la session du terminal actuels. Cela est logique, car nous exécutons actuellement <code>bash</code> et <code>ps</code> avec ce terminal.</p>

<p>Pour obtenir une image plus complète des processus sur ce système, nous pouvons exécuter ce qui suit :</p>
<pre class="code-pre "><code>ps aux***

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND root         1  0.0  0.2  24188  2120 ?        Ss   14:28   0:00 /sbin/init root         2  0.0  0.0      0     0 ?        S    14:28   0:00 [kthreadd] root         3  0.0  0.0      0     0 ?        S    14:28   0:00 [ksoftirqd/0] root         6  0.0  0.0      0     0 ?        S    14:28   0:00 [migration/0] root         7  0.0  0.0      0     0 ?        S    14:28   0:00 [watchdog/0] root         8  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [cpuset] root         9  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [khelper] . . .
</code></pre>
<p>Ces options indiquent à <code>ps</code> d'afficher les processus détenus par tous les utilisateurs (quel que soit le terminal auquel ils sont associés) sous un format convivial pour les utilisateurs.</p>

<p>Pour avoir une vue en <em>arborescencee</em>, dans laquelle les relations hiérarchiques sont illustrées, nous pouvons exécuter la commande avec les options suivantes :</p>
<pre class="code-pre "><code>ps axjf***

 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND     0     2     0     0 ?           -1 S        0   0:00 [kthreadd]     2     3     0     0 ?           -1 S        0   0:00  \_ [ksoftirqd/0]     2     6     0     0 ?           -1 S        0   0:00  \_ [migration/0]     2     7     0     0 ?           -1 S        0   0:00  \_ [watchdog/0]     2     8     0     0 ?           -1 S&lt;       0   0:00  \_ [cpuset]     2     9     0     0 ?           -1 S&lt;       0   0:00  \_ [khelper]     2    10     0     0 ?           -1 S        0   0:00  \_ [kdevtmpfs]     2    11     0     0 ?           -1 S&lt;       0   0:00  \_ [netns] . . .
</code></pre>
<p>Comme vous pouvez le voir, le processus <code>kthreadd</code> est montré comme un parent du processus <code>ksoftirqd/0</code> et les autres.</p>

<h3 id="une-remarque-sur-les-identifiants-de-processus">Une remarque sur les identifiants de processus</h3>

<hr>

<p>Dans les systèmes de type Linux et Unix, chaque processus se voit attribuer un <strong>process ID</strong> ou** PID**. Voici de quelle manière le système d'exploitation identifie et assure le suivi des processus.</p>

<p>Vous pouvez rapidement obtenir le PID d'un processus en utilisant la commande <code>pgrep</code> :</p>
<pre class="code-pre "><code>pgrep bash***

1017
</code></pre>
<p>Cette commande interrogera simplement l'ID du processus et la renverra.</p>

<p>Le premier processus généré au démarrage, appelé <em>init</em>, se voit attribuer le PID de « 1 ».</p>
<pre class="code-pre "><code>pgrep init***

1
</code></pre>
<p>Ce processus est ensuite chargé de générer tout autre processus sur le système. Les processus suivants se voient attribuer des numéros PID plus grands.</p>

<p>Le <em>parent</em> d'un processus est le processus qui était chargé de le générer. Les processus parent disposent d'un <strong>PPID</strong>, que vous pouvez voir dans l'en-tête des colonnes dans de nombreuses applications de gestion de processus, dont <code>top</code>, <code>htop</code> et <code>ps</code>.</p>

<p>Toute communication sur les processus entre l'utilisateur et le système d'exploitation implique, à un moment donné de l'opération, la traduction entre les noms de processus et le PID. C'est pour cette raison que les utilitaires vous indiquent le PID.</p>

<h3 id="relations-parent-enfant">Relations parent-enfant</h3>

<hr>

<p>La création d'un processus enfant se fait en deux étapes : fork(), qui crée un nouvel espace d'adresse et copie les ressources que le parent possède via une copie-sur-écriture pour les rendre disponibles sur le processus enfant ; et exec(), qui charge un exécutable dans l'espace de l'adresse et l'exécute.</p>

<p>Dans le cas où un processus enfant meurt avant son parent, l'enfant devient un zombie jusqu'à que le parent collecte les informations le concernant ou indique au noyau qu'il n'a pas besoin de ces informations Les ressources du processus enfant seront ensuite libérées. Cependant, si le processus parent meurt avant l'enfant, l'enfant sera adopté par l'init, bien qu'il puisse également être réattribué à un autre processus.</p>

<h2 id="comment-envoyer-des-signaux-de-processus-sous-linux">Comment envoyer des signaux de processus sous Linux</h2>

<hr>

<p>Tous les processus de Linux répondent à des <em>signals</em>. Les signaux permettent d'indiquer aux programmes, au niveau du système d'exploitation, de s'arrêter ou de modifier leur comportement.</p>

<h3 id="comment-envoyer-des-signaux-de-processus-par-pid">Comment envoyer des signaux de processus par PID</h3>

<hr>

<p>La façon la plus courante de transmettre des signaux à un programme consiste à utiliser la commande <code>kill</code>.</p>

<p>Comme vous pouvez vous y attendre, la fonctionnalité par défaut de cet utilitaire consiste à tenter de tuer un processus :</p>

<p>&lt;pre&gt;kill &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>of</em>target_process&lt;/span&gt;&lt;/pre&gt;</p>

<p>Cela envoie le signal <strong>TERM</strong> au processus. Le signal TERM indique au processus de bien vouloir se terminer. Cela permet au programme d'effectuer des opérations de nettoyage et de s'arrêter en douceur.</p>

<p>Si le programme se comporte mal et ne se ferme pas lorsque le signal TERM est actionné, nous pouvons escalader le signal en passant le signal <code>KILL</code> :</p>

<p>&lt;pre&gt;kill -KILL &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>of</em>target_process&lt;/span&gt;&lt;/pre&gt;</p>

<p>Il s'agit d'un signal spécial que n'est pas envoyé au programme.</p>

<p>Au lieu de cela, il est envoyé au noyau du système d'exploitation qui interrompt le processus. Vous pouvez l'utiliser pour contourner les programmes qui ignorent les signaux qui leur sont envoyés.</p>

<p>Chaque signal est associé à un numéro que vous pouvez passer à la place du nom. Par exemple, vous pouvez passer « -15 » au lieu de « -TERM » et « -9 » au lieu de « -KILL ».</p>

<h3 id="comment-utiliser-des-signaux-à-d-39-autres-fins">Comment utiliser des signaux à d'autres fins</h3>

<hr>

<p>Les signaux ne servent pas uniquement à fermer des programmes. Vous pouvez également les utiliser pour effectuer d'autres actions.</p>

<p>Par exemple, de nombreux démons redémarrent lorsqu'un signal <code>HUP</code> ou de suspension leur est envoyé Apache est un programme qui fonctionne ainsi.</p>

<p>&lt;pre&gt;sudo kill -HUP &lt;span class=&ldquo;highlight&rdquo;&gt;pid<em>of</em>apache&lt;/span&gt;&lt;/pre&gt;</p>

<p>La commande ci-dessus poussera Apache à recharger son fichier de configuration et à reprendre le contenu d’utilisation.</p>

<p>Vous pouvez répertorier tous les signaux que vous pouvez envoyer avec kill en saisissant :</p>
<pre class="code-pre "><code>kill -l***

1) SIGHUP    2) SIGINT   3) SIGQUIT  4) SIGILL   5) SIGTRAP  6) SIGABRT  7) SIGBUS   8) SIGFPE   9) SIGKILL 10) SIGUSR1 11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM . . .
</code></pre>
<h3 id="comment-envoyer-des-signaux-de-processus-par-nom">Comment envoyer des signaux de processus par nom</h3>

<hr>

<p>Bien que la façon classique d'envoyer des signaux consiste à utiliser des PIDS, il existe également des méthodes de le faire avec des noms de processus réguliers.</p>

<p>La commande <code>pkill</code> fonctionne de manière pratiquement de la même manière que <code>kill</code>, mais elle fonctionne plutôt avec le nom d'un processus :</p>
<pre class="code-pre "><code>pkill -9 ping
</code></pre>
<p>La commande ci-dessus est l'équivalent de :</p>
<pre class="code-pre "><code>kill -9 `pgrep ping`
</code></pre>
<p>Vous pouvez utiliser la commande <code>killall</code> pour envoyer un signal à chaque instance d'un processus donné :</p>
<pre class="code-pre "><code>killall firefox
</code></pre>
<p>La commande ci-dessus enverra le signal TERM à chaque instance de firefox en cours d'exécution sur l'ordinateur.</p>

<h2 id="comment-ajuster-les-priorités-de-processus">Comment ajuster les priorités de processus</h2>

<hr>

<p>Il vous arrivera souvent de vouloir ajuster la priorité donnée aux processus dans un environnement de serveur.</p>

<p>Certains processus peuvent être considérés comme critiques à la mission pour votre situation, tandis que d'autres peuvent être exécutés chaque fois qu'il y aura des ressources restantes.</p>

<p>Linux contrôle la priorité par le biais d'une valeur appelée <strong>niceness</strong>.</p>

<p>Les tâches hautement prioritaires sont considérées comme moins <em>nice</em>, car elles ne partagent pas également les ressources. Les processus faiblement prioritaires sont en revanche <em>nice</em> car ils insistent à prendre seulement des ressources minimales.</p>

<p>Lorsque nous avons exécuté <code>top</code> au début de l'article, il y avait une colonne nommée « NI ». Il s'agit de la valeur <em>nice</em> du processus :</p>
<pre class="code-pre "><code>top***

 Tasks:  56 total,   1 running,  55 sleeping,   0 stopped,   0 zombie Cpu(s):  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k total,   324496k used,   695104k free,     8512k buffers Swap:        0k total,        0k used,        0k free,   264812k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            1635 root      20   0 17300 1200  920 R  0.3  0.1   0:00.01 top                    1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.11 ksoftirqd/0
</code></pre>
<p>Les valeurs nice peuvent aller de « -19/-20  » (priorité la plus grande) à «19/20» (priorité la plus faible) en fonction du système.</p>

<p>Pour exécuter un programme avec une certaine nice valeur, nous pouvons utiliser la commande <code>nice</code> :</p>

<p>&lt;pre&gt;nice -n 15 &lt;span class=&ldquo;highlight&rdquo;&gt;command<em>to</em>execute&lt;/span&gt;&lt;/pre&gt;</p>

<p>Elle fonctionne uniquement au démarrage d'un nouveau programme.</p>

<p>Pour modifier la valeur nice d'un programme déjà en cours d'exécution, nous utilisons un <code>outil</code> appelé renice :</p>

<p>&lt;pre&gt;renice 0 &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>to</em>prioritize&lt;/span&gt;&lt;/pre&gt;</p>

<p><strong>Remarque : bien que nice fonctionne avec un nom de commande par nécessité, renice fonctionne en appelant le PID de processus</strong></p>

<h2 id="conclusion">Conclusion</h2>

<hr>

<p>La gestion de processus est un sujet parfois difficile à comprendre pour les nouveaux utilisateurs car les outils utilisés sont différents de leurs homologues graphiques.</p>

<p>Cependant, les idées sont familières et intuitives et deviennent naturelles avec un peu de pratique. Étant donné que les processus sont impliqués dans toutes les tâches que vous effectuez avec un système informatique, il est essentiel que vous appreniez à les contrôler efficacement.</p>

<p>&lt;div class=&ldquo;author&rdquo;&gt;Par Justin Ellingwood&lt;/div&gt;</p>
