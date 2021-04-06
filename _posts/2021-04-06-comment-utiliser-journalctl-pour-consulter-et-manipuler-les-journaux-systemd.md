---
title : "Comment utiliser Journalctl pour consulter et manipuler les journaux Systemd"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs-fr
image: "https://sergio.afanou.com/assets/images/image-midres-21.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>L'un des avantages les plus éloquents de <code>systemd</code> est la journalisation des processus et des systèmes. Lorsque vous utilisez d'autres outils, les journaux se retrouvent généralement dispersés dans le système, traités par différents daemons et processus et leur interprétation peut s'avérer difficile lorsqu'ils s'étendent sur plusieurs applications. <code>Systemd</code> tente de résoudre ces problèmes en offrant une solution de gestion centralisée pour la journalisation de tous les processus du noyau et de l'espace utilisateur. Le système qui collecte et gère ces journaux est ce que l'on nomme le journal.</p>

<p>Le journal est implémenté avec le daemon <code>journald</code>, qui gère tous les messages produits par le noyau, initrd, services, etc. Dans ce guide, nous allons traiter de la façon d'utiliser l'utilitaire <code>journalctl</code> qui peut servir à accéder et manipuler les données contenues dans le journal.</p>

<h2 id="concept-général">Concept général</h2>

<p>L'une des principales motivations du journal <code>systemd</code> consiste à centraliser la gestion des journaux, quelle que soit l'origine des messages. Comme une grande partie du processus de démarrage et de gestion des services est traitée par le processus <code>systemd</code>, il est logique de normaliser la méthode de collecte et d'accès aux journaux. Le daemon <code>journald</code> collecte des données de toutes les sources disponibles et les stocke sous un format binaire qui permet une manipulation facile et dynamique.</p>

<p>Cela nous offre un certain nombre d'avantages importants. En interagissant avec les données à l'aide d'un seul utilitaire, les administrateurs peuvent afficher les données du journal de façon dynamique en fonction de leurs besoins. Vous pouvez tout simplement visualiser les données de démarrage sur les trois derniers démarrages ou combiner les entrées du journal de manière séquentielle à partir de deux services liés pour déboguer un problème de communication.</p>

<p>Le stockage des données du journal sous un format binaire signifie également que vous pouvez afficher les données sous des formats de sortie arbitraires en fonction de ce dont vous avez besoin à un moment donné. Par exemple, pour assurer une gestion quotidienne des journaux, vous avez peut-être l'habitude de visualiser les journaux sous un format <code>syslog</code> standard, mais si vous décidez ultérieurement de présenter les interruptions de service sous forme de graphique, vous pouvez générer chaque entrée en tant qu'objet JSON pour que votre service de graphique puisse le prendre en charge. Étant donné que les données ne sont pas écrites sur le disque en texte clair, vous n'aurez besoin d'aucune conversion si vous avez besoin d'un format à la demande différent.</p>

<p>Le journal <code>systemd</code> peut soit être utilisé avec une implémentation <code>syslog</code> existante, soit remplacer la fonctionnalité <code>syslog</code> en fonction de vos besoins. Bien que le journal <code>systemd</code> couvre la plupart des besoins de journalisation de l'administrateur, il peut également venir compléter les mécanismes de journalisation existants. Par exemple, il se peut que vous utilisiez un serveur <code>syslog</code> centralisé pour compiler des données à partir de plusieurs serveurs, mais vous souhaitez peut-être également pouvoir imbriquer les journaux de plusieurs services sur un seul système avec le journal <code>systemd</code>. Vous pouvez faire les deux en combinant ces technologies.</p>

<h2 id="configurer-l-39-heure-du-système">Configurer l'heure du système</h2>

<p>L'un des avantages de l'utilisation d'un journal binaire pour la journalisation est la possibilité d'afficher les enregistrements de journaux en UTC ou à l'heure locale selon les besoins. Par défaut, <code>systemd</code> affichera les résultats en utilisant l'heure locale.</p>

<p>Pour cette raison, avant de commencer à travailler sur le journal, nous veillerons à ce que le fuseau horaire soit correctement défini. En fait, la suite <code>systemd</code> est fournie avec un outil nommé <code>timedatectl</code> qui peut vous y aider.</p>

<p>Tout d'abord, déterminez quels sont les fuseaux horaires disponibles avec l'option <code>list-timezones</code> :</p>
<pre class="code-pre "><code>timedatectl list-timezones
</code></pre>
<p>Cela répertoriera les fuseaux horaires disponibles sur votre système. Une fois que vous trouverez celui qui correspond à l'emplacement de votre serveur, vous pouvez le configurer en utilisant l'option <code>set-timezone</code> :</p>
<pre class="code-pre "><code>sudo timedatectl set-timezone <span class="highlight">zone</span>
</code></pre>
<p>Pour vous assurer que votre machine utilise maintenant l'heure qui convient, utilisez la commande <code>timedatectl</code> seule ou avec l'option <code>status</code>. L'affichage sera le même :</p>
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
<p>La première ligne devrait afficher la bonne heure.</p>

<h2 id="visualisation-du-journal-de-base">Visualisation du journal de base</h2>

<p>Pour voir les journaux collectés par le daemon <code>journald</code>, utilisez la commande <code>journalctl</code>.</p>

<p>Lorsqu'utilisée seule, chaque entrée de journal qui se trouve dans le système s'affichera dans un pager (généralement <code>less</code>) pour que vous puissiez y naviguer. Les entrées les plus anciennes seront les premières :</p>
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
<p>Vous aurez probablement des pages et des pages de données à consulter, ce qui peut représenter des dizaines ou des centaines de milliers de lignes si <code>systemd</code> est installé sur votre système depuis longtemps. Cela montre combien de données sont disponibles dans la base de données du journal.</p>

<p>Le format sera familier pour ceux qui sont habitués à la journalisation standard des <code>syslog</code>. Cependant, les données collectées proviennent d'un nombre plus important de sources que ne le permettent les implémentations <code>syslog</code> traditionnelles. Cela inclut les journaux des premiers processus de démarrage, le noyau, l'initrd et l'erreur standard d'application. Tous ces éléments sont disponibles dans le journal.</p>

<p>Vous remarquerez peut-être que toutes les heures affichées sont à l'heure locale. Maintenant que nous notre heure locale est configurée sur notre système, elle est disponible pour chaque entrée du journal. Tous les journaux sont affichés en utilisant cette nouvelle information.</p>

<p>Si vous souhaitez afficher les heures en UTC, vous pouvez utiliser la balise <code>--utc</code>.</p>
<pre class="code-pre "><code>journalctl --utc
</code></pre>
<h2 id="filtrage-des-journaux-par-heure">Filtrage des journaux par heure</h2>

<p>Même s'il est très pratique d'avoir accès à une série aussi grande de données, il peut s'avérer difficile, voire impossible, d'inspecter et de traiter mentalement une telle quantité de données. Par conséquent, ses options de filtrage font partie des fonctionnalités les plus importantes de <code>journalctl</code>.</p>

<h3 id="affichage-des-journaux-du-démarrage-en-cours">Affichage des journaux du démarrage en cours</h3>

<p>L'option la plus basique que vous utiliserez éventuellement quotidiennement est la balise <code>-b</code>. Elle affichera toutes les entrées qui ont été recueillies depuis le dernier démarrage.</p>
<pre class="code-pre "><code>journalctl -b
</code></pre>
<p>Vous pourrez ainsi identifier et gérer plus facilement les informations qui correspondent à votre environnement actuel.</p>

<p>Dans les cas où vous n'utilisez pas cette fonctionnalité et affichez plusieurs jours de démarrages, vous verrez que <code>journalctl</code> a inséré une ligne qui ressemble à ce qui suit à chaque défaillance du système :</p>
<pre class="code-pre "><code>. . .

-- Reboot --

. . .
</code></pre>
<p>Vous pouvez utiliser cela pour vous aider à séparer logiquement les informations en sessions de démarrage.</p>

<h3 id="démarrages-précédents">Démarrages précédents</h3>

<p>Bien que, de manière générale, vous souhaitez afficher les informations du démarrage en cours, il arrivera parfois que les démarrages passés vous soient également utiles. Le journal peut enregistrer des informations de nombreux démarrages précédents, de sorte que <code>journalctl</code> puisse être créé pour afficher les informations facilement.</p>

<p>Certaines distributions permettent de sauvegarder les informations des démarrages précédents par défaut, d'autres désactivent cette fonctionnalité. Pour activer les informations de démarrage persistantes, vous pouvez soit créer le répertoire pour stocker le journal en saisissant ce qui suit :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir -p /var/log/journal
</li></ul></code></pre>
<p>Ou vous pouvez modifier le fichier de configuration du journal :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/systemd/journald.conf
</li></ul></code></pre>
<p>Sous la section <code>[Journal]</code>, configurez l'option <code>Storage=</code> sur « persistent » pour activer la journalisation persistante :</p>
<div class="code-label " title="/etc/systemd/journald.conf">/etc/systemd/journald.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
[Journal]
Storage=<span class="highlight">persistent</span>
</code></pre>
<p>Lorsque la sauvegarde des démarrages précédents est activée sur votre serveur, <code>journalctl</code> intègre quelques commandes pour vous aider à travailler avec des démarrages comme unité de division. Pour voir les démarrages dont <code>journald</code> a connaissance, utilisez l'option <code>--list-boots</code> avec <code>journalctl</code> :</p>
<pre class="code-pre "><code>journalctl --list-boots
</code></pre><pre class="code-pre "><code>-2 caf0524a1d394ce0bdbcff75b94444fe Tue 2015-02-03 21:48:52 UTC—Tue 2015-02-03 22:17:00 UTC
-1 13883d180dc0420db0abcb5fa26d6198 Tue 2015-02-03 22:17:03 UTC—Tue 2015-02-03 22:19:08 UTC
 0 bed718b17a73415fade0e4e7f4bea609 Tue 2015-02-03 22:19:12 UTC—Tue 2015-02-03 23:01:01 UTC
</code></pre>
<p>Vous verrez s'afficher une ligne pour chaque démarrage. La première colonne représente la valeur offset du démarrage que vous pouvez utiliser pour facilement référencer le démarrage avec <code>journalctl</code>. Si vous avez besoin d'une référence absolue, l'ID de démarrage se trouve dans la deuxième colonne. Vous pouvez déterminer l'heure à laquelle la session de démarrage renvoie en utilisant les deux spécifications chronologiques listées vers la fin.</p>

<p>Pour afficher les informations de ces démarrages, vous pouvez utiliser des informations provenant de la première ou de la deuxième colonne.</p>

<p>Par exemple, pour consulter le journal du démarrage précédent, utilisez le pointeur relatif <code>-1</code> avec la balise <code>-b</code> :</p>
<pre class="code-pre "><code>journalctl -b -1
</code></pre>
<p>Vous pouvez également utiliser l'ID de démarrage pour rappeler les données d'un démarrage :</p>
<pre class="code-pre "><code>journalctl -b caf0524a1d394ce0bdbcff75b94444fe
</code></pre>
<h3 id="fenêtres-d-39-heures">Fenêtres d'heures</h3>

<p>Bien que la consultation des entrées du journal par démarrage soit très utile, il se peut que vous souhaitiez demander des fenêtres d'heures qui ne s'alignent pas correctement sur les démarrages du système. Cela peut être particulièrement pratique lorsque vous travaillez avec des serveurs de longue durée avec un temps de disponibilité significatif.</p>

<p>Vous pouvez filtrer par des périodes de temps arbitraires en utilisant les options <code>--since</code> et <code>--until</code>, qui limitent respectivement les entrées affichées à celles qui suivent ou précèdent l'heure donnée.</p>

<p>Les valeurs de l'heure peuvent se présenter sous différents formats. Pour les valeurs de temps absolus, vous devez utiliser le format suivant :</p>
<pre class="code-pre "><code>YYYY-MM-DD HH:MM:SS
</code></pre>
<p>Par exemple, nous pouvons voir toutes les entrées depuis le 10 janvier 2015 à 17 h 15 en saisissant ce qui suit :</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10 17:15:00"
</code></pre>
<p>Si les composants du format ci-dessus sont supprimés, certaines valeurs par défaut seront appliquées. Par exemple, si la date est omise, le système supposera qu'il s'agit de la date du jour. Si le composant de l'heure est manquant, « 00:00 » (minuit) sera remplacé. Le champ des secondes peut également être laissé à la valeur par défaut de « 00 » :</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10" --until "2015-01-11 03:00"
</code></pre>
<p>Le journal comprend également certaines valeurs relatives, nommées raccourcis. Par exemple, vous pouvez utiliser les mots « yesterday », « today », « tomorrow » ou « now ». Vous pouvez indiquer des heures relatives en préfixant la valeur numérotée de « - » ou « + » ou en utilisant des mots comme « ago » dans une construction de phrases.</p>

<p>Pour obtenir les données de la veille, vous pourriez taper :</p>
<pre class="code-pre "><code>journalctl --since yesterday
</code></pre>
<p>Si vous recevez des rapports indiquant une interruption de service qui commence à 9 h 00 et s'est achevée il y a une heure, vous pourriez taper :</p>
<pre class="code-pre "><code>journalctl --since 09:00 --until "1 hour ago"
</code></pre>
<p>Comme vous pouvez le voir, il est relativement facile de définir des fenêtres flexibles de temps pour filtrer les entrées que vous souhaitez voir.</p>

<h2 id="filtrer-par-intérêt-des-messages">Filtrer par intérêt des messages</h2>

<p>Nous avons précédemment appris certaines des façons qui vous permettent de filtrer les données du journal en utilisant des contraintes de temps. Au cours de cette section, nous allons discuter de la façon de filtrer vos données en fonction du service ou du composant qui vous intéresse. Le journal <code>systemd</code> intègre différents moyens de le faire.</p>

<h3 id="par-unité">Par unité</h3>

<p>La façon probablement la plus pratique de filtrer vos données est de le faire par l'unité qui vous intéresse. Nous pouvons utiliser l'option <code>-u</code> pour filtrer nos données de cette façon.</p>

<p>Par exemple, pour consulter tous les journaux d'une unité Nginx sur notre système, nous pouvons taper :</p>
<pre class="code-pre "><code>journalctl -u nginx.service
</code></pre>
<p>Généralement, vous voudrez probablement filtrer vos données par heure afin d'afficher les lignes qui vous intéressent. Par exemple, pour vérifier comment le service fonctionne aujourd'hui, vous pouvez taper :</p>
<pre class="code-pre "><code>journalctl -u nginx.service --since today
</code></pre>
<p>Ce type de concentration peut s'avérer extrêmement utile si vous souhaiter profiter de la capacité du journal à imbriquer les enregistrements de différentes unités. Par exemple, si votre processus Nginx est connecté à une unité PHP-FPM pour traiter le contenu dynamique, vous pouvez fusionner les entrées des deux par ordre chronologique en spécifiant les deux unités :</p>
<pre class="code-pre "><code>journalctl -u nginx.service -u php-fpm.service --since today
</code></pre>
<p>Cela peut grandement faciliter la détection des interactions entre les différents programmes et les systèmes de débogages au lieu de processus individuels.</p>

<h3 id="par-processus-utilisateur-ou-id-de-groupe">Par processus, utilisateur ou ID de groupe</h3>

<p>Afin de fonctionner correctement, certains services génèrent tout un panel de processus enfants. Si vous avez mis en évidence le PID exact du processus qui vous intéresse, vous pouvez également filtrer vos données par cette valeur.</p>

<p>Pour ce faire, nous pouvons appliquer notre filtre en renseignant le champ <code>_PID</code>. Par exemple, si le PID qui nous intéresse est 8088, nous pourrions taper :</p>
<pre class="code-pre "><code>journalctl _PID=8088
</code></pre>
<p>À d'autres moments, vous voudrez afficher toutes les entrées enregistrées par un utilisateur ou un groupe spécifique. Vous pouvez le faire avec les filtres <code>_UID</code> ou <code>_GID</code>. Par exemple, si votre serveur web fonctionne sous l'utilisateur <code>www-data</code>, vous pouvez trouver l'ID de l'utilisateur en tapant ce qui suit :</p>
<pre class="code-pre "><code>id -u www-data
</code></pre><pre class="code-pre "><code>33
</code></pre>
<p>Ensuite, vous pouvez utiliser l'ID qui a été renvoyé pour filtrer les résultats du journal :</p>
<pre class="code-pre "><code>journalctl _UID=<span class="highlight">33</span> --since today
</code></pre>
<p>Le journal <code>systemd</code> intègre de nombreux champs qui peuvent être utilisés pour le filtrage. Certaines d'entre eux sont passés à partir du processus en cours d'enregistrement et d'autres appliqués par <code>journald</code> en utilisant les informations qu'il recueille à partir du système au moment de la journalisation.</p>

<p>Le résultat principal indique que le champ <code>_PID</code> est de ce dernier type. Le journal enregistre et indexe automatiquement le PID du processus en cours de journalisation qui permet un filtrage ultérieur. Pour en savoir plus sur tous les champs disponibles des journaux, saisissez :</p>
<pre class="code-pre "><code>man systemd.journal-fields
</code></pre>
<p>Nous allons traiter de certains de ces éléments dans ce guide. Cependant, pour le moment, nous allons étudier une option plus utile liée au filtrage de ces champs. Vous pouvez utiliser l'option <code>-F</code> pour afficher toutes les valeurs disponibles pour un champ donné du journal.</p>

<p>Par exemple, pour consulter pour quels groupes d'ID le journal <code>systemd</code> dispose d'entrées, vous pouvez saisir :</p>
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
<p>Cela affichera toutes les valeurs que le journal a stockées pour le champ de l'ID du groupe. Cela peut vous aider à construire vos filtres.</p>

<h3 id="par-chemin-de-composants">Par chemin de composants</h3>

<p>Nous pouvons également filtrer nos données en renseignant un emplacement de chemin.</p>

<p>Si le chemin mène à un exécutable, <code>journalctl</code> affichera toutes les entrées qui impliquent l'exécutable en question. Par exemple, pour trouver les entrées qui impliquent l'exécutable <code>bash</code>, vous pouvez taper :</p>
<pre class="code-pre "><code>journalctl /usr/bin/bash
</code></pre>
<p>Généralement, si une unité est disponible pour l'exécutable, cette méthode est plus propre et donne de meilleures informations (entrées des processus enfants associés, etc). Cependant, il arrivera parfois que cela ne soit pas possible.</p>

<h3 id="afficher-les-messages-du-noyau">Afficher les messages du noyau</h3>

<p>Les messages du noyau, ceux qui se trouvent généralement dans la sortie <code>dmesg</code> peuvent également être récupérés dans le journal.</p>

<p>Pour afficher uniquement ces messages, nous pouvons ajouter les balises <code>-k</code> ou <code>--dmesg</code> à notre commande :</p>
<pre class="code-pre "><code>journalctl -k
</code></pre>
<p>Par défaut, cela affichera les messages du noyau du démarrage actuel. Vous pouvez spécifier un démarrage alternatif en utilisant les balises usuelles de sélection de démarrage dont nous avons précédemment parlé. Par exemple, pour obtenir les messages des cinq derniers démarrages, vous pourriez taper :</p>
<pre class="code-pre "><code>journalctl -k -b -5
</code></pre>
<h3 id="par-priorité">Par priorité</h3>

<p>L'un des filtres auxquels s'intéressent souvent les administrateurs de système est la priorité des messages. Bien qu'il soit souvent utile de consigner les informations à un niveau très verbeux, pour digérer les informations disponibles, les journaux de faible priorité peuvent être déroutants et confus.</p>

<p>Vous pouvez utiliser <code>journalctl</code> pour afficher uniquement les messages d'une priorité spécifiée ou au-dessus en utilisant l'option <code>-p</code>. Cela vous permet de filtrer les messages de priorité inférieure.</p>

<p>Par exemple, pour afficher uniquement les entrées enregistrées au niveau des erreurs ou au-dessus, vous pouvez taper :</p>
<pre class="code-pre "><code>journalctl -p err -b
</code></pre>
<p>Cela vous montrera tous les messages indiquant une erreur, un état critique, un avertissement ou une urgence. Le journal implémente les niveaux de messages <code>syslog</code> standard. Vous pouvez utiliser soit le nom de la priorité, soit la valeur numérique correspondante. En partant de la plus haute à la plus basse, les priorités sont les suivantes :</p>

<ul>
<li>0 : emerg</li>
<li>1 : alert</li>
<li>2 : crit</li>
<li>3 : err</li>
<li>4 : warning</li>
<li>5 : notice</li>
<li>6 : info</li>
<li>7 : debug</li>
</ul>

<p>Vous pouvez utiliser les numéros ou noms ci-dessus de manière interchangeable avec l'option <code>-p</code>. En sélectionnant une priorité, vous afficherez les messages marqués au niveau indiqué et aux niveaux supérieurs.</p>

<h2 id="modification-de-l-39-affichage-du-journal">Modification de l'affichage du journal</h2>

<p>Précédemment, nous vous avons montré comment utiliser le filtrage pour sélectionner des données. Il existe cependant d'autres façons de modifier la sortie. Nous pouvons ajuster l'affichage <code>journalctl</code> en fonction de divers besoins.</p>

<h3 id="tronquer-ou-étendre-le-résultat">Tronquer ou étendre le résultat</h3>

<p>Nous pouvons ajuster la façon dont <code>journalctl</code> affiche les données en l'instruisant de réduire ou d'étendre la sortie.</p>

<p>Par défaut, <code>journalctl</code> affichera l'intégralité du résultat dans un pager, ce qui permet aux entrées de se diriger vers la droite de l'écran. Pour accéder à cette information, appuyez sur la touche de flèche droite.</p>

<p>Si vous souhaitez tronquer le résultat, en insérant une ellipse à l'endroit où les informations ont été supprimées, vous pouvez utiliser l'option <code>--no-full</code> :</p>
<pre class="code-pre "><code>journalctl --no-full
</code></pre><pre class="code-pre "><code>. . .

Feb 04 20:54:13 journalme sshd[937]: Failed password for root from 83.234.207.60...h2
Feb 04 20:54:13 journalme sshd[937]: Connection closed by 83.234.207.60 [preauth]
Feb 04 20:54:13 journalme sshd[937]: PAM 2 more authentication failures; logname...ot
</code></pre>
<p>Vous pouvez également aller dans la direction inverse et demander à <code>journalctl</code> d'afficher toutes les informations dont il dispose, qu'il inclut des caractères non imprimables ou pas. Nous pouvons le faire avec la balise <code>-a</code> :</p>
<pre class="code-pre "><code>journalctl -a
</code></pre>
<h3 id="sortie-sur-standard">Sortie sur Standard</h3>

<p>Par défaut, <code>journalctl</code> affiche la sortie dans un pager pour faciliter la consommation des données. Cependant, si vous prévoyez de traiter les données avec des outils de manipulation de texte, vous voudrez probablement pouvoir produire une sortie standard sur la sortie.</p>

<p>Vous pouvez le faire avec l'option <code>--no-pager</code> :</p>
<pre class="code-pre "><code>journalctl --no-pager
</code></pre>
<p>Vous pouvez immédiatement acheminer votre résultat dans un utilitaire de traitement ou le rediriger vers un fichier sur le disque, en fonction de vos besoins.</p>

<h3 id="formats-de-sortie">Formats de sortie</h3>

<p>Si vous traitez des entrées de journaux, comme mentionné ci-dessus, il vous sera probablement plus facile d'analyser les données si elles sont dans format plus digeste. Heureusement, le journal peut s'afficher sous divers formats selon les besoins. Vous pouvez le faire en utilisant l'option <code>-o</code> et un spécificateur de format.</p>

<p>Par exemple, vous pouvez générer les entrées du journal dans JSON en tapant :</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json
</code></pre><pre class="code-pre "><code>{ "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635", "__REALTIME_TIMESTAMP" : "1422990364739502", "__MONOTONIC_TIMESTAMP" : "27200938", "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d", "PRIORITY" : "6", "_UID" : "0", "_GID" : "0", "_CAP_EFFECTIVE" : "3fffffffff", "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee", "_HOSTNAME" : "desktop", "SYSLOG_FACILITY" : "3", "CODE_FILE" : "src/core/unit.c", "CODE_LINE" : "1402", "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading", "SYSLOG_IDENTIFIER" : "systemd", "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5", "_TRANSPORT" : "journal", "_PID" : "1", "_COMM" : "systemd", "_EXE" : "/usr/lib/systemd/systemd", "_CMDLINE" : "/usr/lib/systemd/systemd", "_SYSTEMD_CGROUP" : "/", "UNIT" : "nginx.service", "MESSAGE" : "Starting A high performance web server and a reverse proxy server...", "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973" }

. . .
</code></pre>
<p>Très utile pour l'analyse avec les utilitaires. Vous pourriez utiliser le format <code>json-pretty</code> pour obtenir une meilleure maîtrise sur la structure des données avant de les passer au consommateur JSON :</p>
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
<p>Les formats d'affichage disponibles sont les suivants :</p>

<ul>
<li><strong>cat</strong> : affiche uniquement le champ du message en lui-même.</li>
<li><strong>export</strong> : un format binaire adapté au transfert ou à la sauvegarde de données.</li>
<li><strong>json</strong> : JSON standard avec une entrée par ligne.</li>
<li><strong>json-pretty</strong> : JSON formaté pour une meilleure lisibilité par l'homme</li>
<li><strong>json-sse</strong> : résultat formaté sous JSON enveloppé pour que l'événement add server-sen soit compatible</li>
<li><strong>short</strong> : la sortie de style <code>syslog</code> par défaut</li>
<li><strong>short-iso</strong> :le format par défaut a été augmenté pour afficher les horodatages d'horloge murale ISO 8601</li>
<li><strong>short-monotonic</strong> : le format par défaut avec des horodatages monotones.</li>
<li><strong>short-precise</strong> : le format par défaut avec précision à la microseconde près</li>
<li><strong>verbose</strong> : affiche chaque champ de journal disponible pour l'entrée, y compris ceux généralement cachés en interne.</li>
</ul>

<p>Ces options vous permettent d'afficher les entrées du journal dans le format le plus adapté à vos besoins actuels.</p>

<h2 id="surveillance-active-des-processus">Surveillance active des processus</h2>

<p>La commande <code>journalctl</code> imite le nombre d'administrateurs qui utilisent <code>tail</code> pour la surveillance d'une activité active ou récente. Cette fonctionnalité est intégrée dans <code>journalctl</code> et vous permet d'accéder à ces fonctionnalités sans avoir à vous diriger vers un autre outil.</p>

<h3 id="affichage-des-journaux-récents">Affichage des journaux récents</h3>

<p>Pour afficher une quantité d'enregistrements définie, vous pouvez utiliser l'option <code>-n</code>, qui fonctionne exactement comme <code>tail -n</code>.</p>

<p>Par défaut, les 10 entrées les plus récentes s'afficheront :</p>
<pre class="code-pre "><code>journalctl -n
</code></pre>
<p>Vous pouvez spécifier le nombre d'entrées que vous souhaitez consulter en ajoutant un numéro après le <code>-n</code> :</p>
<pre class="code-pre "><code>journalctl -n 20
</code></pre>
<h3 id="suivi-des-journaux">Suivi des journaux</h3>

<p>Pour suivre activement les journaux à mesure de leur écriture, vous pouvez utiliser la balise <code>-f</code>. Encore une fois, cela fonctionne comme vous pouvez vous y attendre si vous déjà utilisé <code>tail -f</code>:</p>
<pre class="code-pre "><code>journalctl -f
</code></pre>
<h2 id="maintenance-des-journaux">Maintenance des journaux</h2>

<p>Vous devez éventuellement vous demander combien coûte le stockage de toutes ces données que nous avons vues jusqu'à présent. En outre, vous pourriez vouloir nettoyer certains journaux anciens et libérer de l'espace.</p>

<h3 id="trouver-l-39-utilisation-actuelle-du-disque">Trouver l'utilisation actuelle du disque</h3>

<p>Vous pouvez trouver la quantité d'espace que le journal occupe actuellement sur le disque en utilisant la balise <code>--disk-usage</code> :</p>
<pre class="code-pre "><code>journalctl --disk-usage
</code></pre><pre class="code-pre "><code>Journals take up 8.0M on disk.
</code></pre>
<h3 id="suppression-des-anciens-journaux">Suppression des anciens journaux</h3>

<p>Si vous souhaitez réduire votre journal, vous disposez de deux façons différentes de le faire (disponibles avec les versions 218 et ultérieures de <code>systemd</code>).</p>

<p>Si vous utilisez l'option <code>--vacuum-size</code>, vous pouvez réduire votre journal en indiquant une taille. Les anciennes entrées seront supprimées jusqu'à que l'espace de journal total occupé sur le disque atteigne la taille demandée :</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-size=1G
</code></pre>
<p>Il existe une autre façon de réduire le journal, qui consiste à fournir une heure de calcul avec l'option <code>--vacuum-time</code>. Le système supprime toutes les entrées au-delà de cette heure. Vous pourrez ainsi conserver les entrées qui ont été créées après une heure spécifique.</p>

<p>Par exemple, pour conserver les entrées de l'année dernière, vous pouvez taper :</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-time=1years
</code></pre>
<h3 id="limitation-de-l-39-expansion-du-journal">Limitation de l'expansion du journal</h3>

<p>Vous pouvez configurer votre serveur de manière à ce qu'il place des limites sur la quantité d'espace que le journal peut prendre. Pour cela, vous devez éditer le fichier <code>/etc/systemd/journald.conf</code>.</p>

<p>Vous pouvez utiliser les éléments suivants pour limiter l'expansion du journal :</p>

<ul>
<li><strong><code>SystemMaxUse=</code></strong> : Spécifie l'espace disque maximal qui peut être utilisé par le journal dans un stockage persistant.</li>
<li><strong><code>SystemKeepFree=</code></strong> : Spécifie la quantité d'espace libre que le journal doit laisser lors de l'ajout d'entrées du journal au stockage persistant.</li>
<li><strong><code>SystemMaxFileSize=</code></strong> : contrôle la taille que les fichiers journaux individuels peuvent atteindre dans le stockage persistant avant la rotation.</li>
<li><strong><code>RuntimeMaxUse=</code></strong> : Spécifie l'espace disque maximal qui peut être utilisé dans le stockage volatile (dans le système de fichiers <code>/run</code>).</li>
<li><strong><code>RuntimeKeepFree=</code></strong> : Spécifie la quantité d'espace à mettre de côté pour d'autres utilisations lors de l'écriture des données sur un stockage volatile (dans le système de fichiers <code>/run</code>).</li>
<li><strong><code>RuntimeMaxFileSize=</code></strong> : Spécifie la quantité d'espace qu'un fichier de journal individuel peut prendre en charge dans un stockage volatile (dans le système de fichiers <code>/run</code>) avant la rotation.</li>
</ul>

<p>En configurant ces valeurs, vous pouvez contrôler la façon dont <code>journald</code> consomme et conserve l'espace sur votre serveur. Gardez à l'esprit que <strong><code>SystemMaxFileSize</code></strong> et <strong><code>RuntimeMaxFileSize</code></strong> cibleront les fichiers archivés pour atteindre les limites indiquées. Ceci est important à retenir lors de l'interprétation du nombre de fichiers après une opération de nettoyage.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Comme vous pouvez le voir, le journal <code>systemd</code> est très utile pour collecter et gérer les données de votre système et de l'application. Une grande partie de la flexibilité provient des métadonnées complètes automatiquement enregistrées et de la nature centralisée du journal. La commande <code>journalctl</code> permet de profiter des fonctionnalités avancées du journal et de faire une analyse et un débogage relationnel des différents composants de l'application.</p>
