---
title : "Comment modifier le fichier Sudoers"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-fr
image: "https://sergio.afanou.com/assets/images/image-midres-36.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>La séparation de privilèges est l'un des paradigmes fondamentaux de la sécurité intégrée dans les systèmes d'exploitation de type Linux et Unix. Les utilisateurs réguliers utilisent des privilèges limités afin de réduire la portée de leur influence à leur propre environnement et non pas au système d'exploitation plus large.</p>

<p>Un utilisateur spécial, appelé <strong>root</strong>, dispose de privilèges de <em>super-utilisateur</em>. Il s'agit d'un compte administratif qui n'intègre pas les restrictions auxquelles sont soumis les utilisateurs normaux. Les utilisateurs peuvent exécuter des commandes avec des privilèges de super-utilisateur ou <strong>root</strong> de plusieurs manières différentes.</p>

<p>Tout au long de cet article, nous allons voir comment obtenir des privilèges <strong>root</strong> correctement et en toute sécurité, en mettant tout particulièrement l'accent sur la modification du fichier <code>/etc/sudoers</code>.</p>

<p>Nous procéderons à ces étapes sur un serveur Ubuntu 20.04. Cependant, notez que la plupart des distributions Linux modernes comme Debian et CentOS devraient fonctionner de manière similaire.</p>

<p>Ce guide suppose que vous avez déjà procédé à la <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">configuration initiale du serveur</a> mentionnée ici. Connectez-vous à votre serveur en tant que non-<strong>root</strong> user et continuez de la manière indiquée ci-dessous.</p>

<p><span class='note'><strong>Remarque :</strong> ce tutoriel traite de l'escalade des privilèges et du fichier <code>sudoer</code> plus en détails. Pour ajouter des privilèges <code>sudo</code> à un utilisateur, consultez nos tutoriels de démarrage rapide <em>Comment créer un nouvel utilisateur actif Sudo</em> pour <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> et <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a>.<br></span></p>

<h2 id="comment-obtenir-des-privilèges-root">Comment obtenir des privilèges root</h2>

<p>Vous disposez de trois méthodes de base pour obtenir des privilèges <strong>root</strong>, qui diffèrent en termes de sophistication.</p>

<h3 id="se-connecter-en-tant-que-root">Se connecter en tant que root</h3>

<p>Pour obtenir des privilèges <strong>root</strong>, la méthode la plus directe et la plus simple consiste à se connecter directement à votre serveur en tant que le <strong>root</strong> user.</p>

<p>Si vous vous connectez à une machine locale (ou en utilisant une fonction de console hors-bande sur un serveur virtuel), lorsque vous serez invité à vous connecter, saisissez <code>root</code> pour votre nom d'utilisateur et le mot de passe <strong>root</strong>.</p>

<p>Si vous vous connectez via SSH, spécifiez le <strong>root</strong> user avant l'adresse IP ou le nom de domaine dans votre chaîne de connexion SSH :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ssh root@<span class="highlight">server_domain_or_ip</span>
</li></ul></code></pre>
<p>Si vous n'avez pas configuré de clés SSH pour l'utilisateur <strong>root</strong>, saisissez le mot de passe <strong>root</strong> lorsque vous y serez invité.</p>

<h3 id="utiliser-su-pour-devenir-root">Utiliser <code>su</code> pour devenir root</h3>

<p>En règle générale, il n'est pas recommandé de vous connecter directement en <strong>root</strong>. En effet, même si cela vous permet de commencer à utiliser le système facilement pour effectuer des tâches non administratives, cette pratique reste dangereuse.</p>

<p>La prochaine méthode que nous allons vous présenter pour obtenir des privilèges de super-utilisateur vous permet de devenir l'utilisateur <strong>root</strong> à tout moment, à chaque fois que vous en aurez besoin.</p>

<p>Pour cela, vous pouvez invoquer la commande <code>su</code>, qui signifie « utilisateur de remplacement ». Pour obtenir des privilèges <strong>root</strong>, tapez :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">su
</li></ul></code></pre>
<p>Vous serez invité à saisir le mot de passe du <strong>root</strong> user, quite à quoi vous serez dirigé vers une session de shell <strong>root</strong>.</p>

<p>Une fois que vous en aurez terminé avec les tâches qui nécessitent des privilèges <strong>root</strong>, retournez à votre shell normal en saisissant :</p>
<pre class="code-pre super_user prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="#">exit
</li></ul></code></pre>
<h3 id="utiliser-sudo-pour-exécuter-des-commandes-en-tant-que-root">Utiliser <code>sudo</code> pour exécuter des commandes en tant que root</h3>

<p>La dernière méthode qui permet d'obtenir des privilèges <strong>root</strong> que nous allons aborder est celle qui utilise la commande <code>sudo</code>.</p>

<p>La commande <code>sudo</code> vous permet d'exécuter des commandes uniques avec des privilèges <strong>root</strong>, sans avoir à générer un nouveau shell. Elle s'exécute de la manière suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo <span class="highlight">command_to_execute</span>
</li></ul></code></pre>
<p>Contrairement à <code>su</code>, la commande <code>sudo</code> vous demandera de saisir le mot de passe de l'utilisateur <em>actuel</em>, pas le mot de passe <strong>root</strong>.</p>

<p>En raison de ses implications en termes de sécurité, les utilisateurs ne se voient pas attribuer l'accès <code>sudo</code> par défaut qui doit être préalablement configuré avant de pouvoir fonctionner correctement. Consultez nos tutoriels de démarrage rapide <em>Comment créer un nouvel utilisateur Sudo</em> dans <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> et <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a> pour en apprendre davantage sur la configuration d'un utilisateur <code>sudo</code>.</p>

<p>Dans la section suivante, nous allons voir comment modifier la configuration <code>sudo</code> plus en détail.</p>

<h2 id="qu-39-est-ce-que-visudo ">Qu'est-ce que Visudo ?</h2>

<p>La configuration de la commande <code>sudo</code> se fait par le biais d'un fichier situé dans <code>/etc/sudoers</code>.</p>

<p><span class='warning'><strong>Avertissement :</strong> ne modifiez jamais ce fichier en utilisant un éditeur de texte normal ! À la place, utilisez toujours la commande <code>visudo</code> !<br></span></p>

<p>Étant donné que l'utilisation d'une mauvaise syntaxe dans le fichier <code>/etc/sudoers</code> vous laissera créer un système défaillant dans lequel il sera impossible d'obtenir des privilèges élevés, il est important d'utiliser la commande <code>visudo</code> pour modifier le fichier.</p>

<p>La commande <code>visudo</code> ouvre un éditeur de texte normalement, mais valide la syntaxe du fichier au moment de la sauvegarde. Cela empêche les erreurs de configuration de venir bloquer les opérations <code>sudo</code>, qui est votre seule façon d'obtenir des privilèges <strong>root</strong>.</p>

<p>Traditionnellement, <code>visudo</code> ouvre le fichier <code>/etc/sudoers</code> avec l'éditeur de texte <code>vi</code>. Cependant, en Ubuntu, <code>visudo</code> est configuré pour utiliser l'éditeur de texte <code>nano</code> à la place.</p>

<p>Si vous souhaitez le reconfigurer sur <code>vi</code>, vous devez lancer la commande suivante :</p>
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
<p>Sélectionnez le numéro qui correspond au choix que vous souhaitez appliquer.</p>

<p>Sur CentOS, vous pouvez modifier cette valeur en ajoutant la ligne suivante à votre <code>~/.bashrc</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">export EDITOR=`which <span class="highlight">name_of_editor</span>`
</li></ul></code></pre>
<p>Créez le fichier pour implémenter les changements :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">. ~/.bashrc
</li></ul></code></pre>
<p>Une fois que vous aurez configuré <code>visudo</code>, exécutez la commande pour accéder au fichier <code>/etc/sudoers</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre>
<h2 id="comment-modifier-le-fichier-sudoers">Comment modifier le fichier Sudoers</h2>

<p>Nous allons vous présenter le fichier <code>/etc/sudoers</code> dans l'éditeur de texte que vous avez sélectionné.</p>

<p>J'ai copié et collé le fichier à partir d'Ubuntu 18.04 en supprimant les commentaires. Le fichier <code>/etc/sudoers</code> de CentOS dispose de bien plus de lignes, dont certaines ne seront pas abordées dans ce guide.</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

root    ALL=(ALL:ALL) ALL

%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

#includedir /etc/sudoers.d
</code></pre>
<p>Voyons ce que font ces lignes.</p>

<h3 id="lignes-par-défaut">Lignes par défaut</h3>

<p>La première ligne, « Defaults env_reset », réinitialise l'environnement du terminal afin de supprimer toute variable d'utilisateur. Il s'agit d'une mesure de sécurité utilisée pour effacer les variables d'environnement potentiellement néfastes de la session <code>sudo</code>.</p>

<p>La deuxième ligne, <code>Defaults mail_badpass</code>, indique au système d'envoyer des notifications par mail des tentatives de saisie erronée de mot de passe <code>sudo</code> à l'utilisateur <code>mailto</code> configuré. Il s'agit du compte <strong>root</strong> par défaut.</p>

<p>La troisième ligne, qui commence par « Defaults secure_path=&hellip; », spécifie le <code>PATH</code> (les endroits du système de fichiers dans lesquels le système d'exploitation recherchera des applications) qui sera utilisé pour les opérations <code>sudo</code>. Cela empêche toute utilisation de chemins utilisateurs susceptibles d'être préjudiciables.</p>

<h3 id="lignes-de-privilèges-des-utilisateurs">Lignes de privilèges des utilisateurs</h3>

<p>La quatrième ligne, qui dicte les privilèges <strong>sudo</strong> du <code>root</code> user, est différente des lignes précédentes. Examinons à quoi correspondent les différents champs :</p>

<ul>
<li><p><code><span class="highlight">root</span> ALL=(ALL:ALL) ALL</code> Le premier champ indique le nom d'utilisateur que la règle appliquera à (<strong>root</strong>).</p></li>
<li><p><code>root <span class="highlight">ALL</span>=(ALL:ALL) ALL</code> Le premier « ALL » indique que cette règle s'applique à tous les hôtes.</p></li>
<li><p><code>root ALL=(<span class="highlight">ALL</span>:ALL) ALL</code> Ce « ALL » indique que le <strong>root</strong> user peut exécuter des commandes en tant que tous les utilisateurs.</p></li>
<li><p><code>root ALL=(ALL:<span class="highlight">ALL</span>) ALL</code> Ce « ALL » indique que <strong>root</strong> user peut exécuter des commandes en tant que tous les groupes.</p></li>
<li><p><code>root ALL=(ALL:ALL) <span class="highlight">ALL</span></code> Le dernier « ALL » indique que ces règles s'appliquent à toutes les commandes.</p></li>
</ul>

<p>Cela signifie que notre <strong>root</strong> user peut exécuter toute commande en utilisant <code>sudo</code>, à condition qu'il fournisse son mot de passe.</p>

<h3 id="lignes-de-privilèges-de-groupes">Lignes de privilèges de groupes</h3>

<p>Les deux lignes suivantes sont similaires celles des privilèges de l'utilisateur, mais les règles <code>sudo</code> spécifiées s'appliquent aux groupes.</p>

<p>Les noms commençant par un <code>%</code> indiquent des noms de groupes.</p>

<p>Ici, nous voyons que le groupe <strong>admin</strong> peut exécuter toute commande en tant que n'importe quel utilisateur, quel que soit l'hôte. De la même façon, le groupe <strong>sudo</strong> dispose des mêmes privilèges, mais peut également s'exécuter en tant que tout groupe également.</p>

<h3 id="ligne-etc-sudoers-d-incluse">Ligne /etc/sudoers.d incluse</h3>

<p>Au premier regard, la dernière ligne peut ressembler à un commentaire :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .

#includedir /etc/sudoers.d
</code></pre>
<p>Elle <em>commence</em> par un <code>#</code>, ce qui indique généralement un commentaire. Cependant, en réalité, cette ligne indique que les fichiers dans le répertoire <code>/etc/sudoers.d</code> seront également sourcés et appliqués.</p>

<p>Les fichiers qui se trouvent dans ce répertoire suivent les mêmes règles que le fichier <code>/etc/sudoers</code> en lui-même. Tout fichier qui ne finit pas par <code>~</code> et qui ne contient pas un <code>.</code> sera lu et annexé à la configuration de <code>sudo</code>.</p>

<p>Cela est principalement destiné aux applications qui modifient les privilèges <code>sudo</code> lors de l'installation. En mettant toutes les règles associées dans un seul fichier du répertoire <code>/etc/sudoers.d</code>, vous pouvez facilement voir quels privilèges sont associés à quels comptes et inverser tout aussi aisément les identifiants sans avoir à tenter de manipuler le fichier <code>/etc/sudoers.d</code> directement.</p>

<p>Comme pour le fichier <code>/etc/sudoers</code> en lui-même, vous devriez toujours modifier les fichiers du répertoire <code>/etc/sudoers.d</code> avec <code>visudo</code>. La syntaxe à utiliser pour modifier ces fichiers serait la suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo -f /etc/sudoers.d/<span class="highlight">file_to_edit</span>
</li></ul></code></pre>
<h2 id="comment-donner-des-privilèges-sudo-à-un-utilisateur">Comment donner des privilèges sudo à un utilisateur</h2>

<p>Le plus souvent, dans le cadre de la gestion des autorisations <code>sudo</code>, les utilisateurs souhaitent accorder un accès <code>sudo</code> général à un nouvel utilisateur. Cette pratique est pratique pour octroyer un accès administratif complet au système.</p>

<p>Sur un système configuré avec un groupe d'administration générale comme le système Ubuntu utilisé dans ce guide, le plus simple consiste à ajouter l'utilisateur en question à ce groupe.</p>

<p>Par exemple, sur Ubuntu 20.04, le groupe <code>sudo</code> dispose de privilèges administratifs complets. Nous pouvons accorder ces mêmes privilèges à un utilisateur en les ajoutant au groupe de la manière suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG sudo <span class="highlight">username</span>
</li></ul></code></pre>
<p>Vous pouvez également utiliser la commande <code>gpasswd</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> sudo
</li></ul></code></pre>
<p>Les deux méthodes vous donneront les mêmes résultats.</p>

<p>En CentOS, on utilise généralement le groupe <code>wheel</code> au lieu du groupe <code>sudo</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG wheel <span class="highlight">username</span>
</li></ul></code></pre>
<p>Ou, on utilise <code>gpasswd</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> wheel
</li></ul></code></pre>
<p>Sur CentOS, si vous n'arrivez pas à ajouter un utilisateur au groupe immédiatement, vous aurez éventuellement à modifier le fichier <code>/etc/sudoers</code> pour retirer les commentaires du nom du groupe :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
%wheel ALL=(ALL) ALL
. . .
</code></pre>
<h2 id="comment-configurer-des-règles-personnalisées">Comment configurer des règles personnalisées</h2>

<p>Maintenant que nous nous sommes familiarisés avec la syntaxe générale du fichier, créons quelques nouvelles règles.</p>

<h3 id="comment-créer-des-alias">Comment créer des alias</h3>

<p>Le fichier <code>sudoers</code> peut être organisé plus efficacement en regroupant des éléments avec différents types de « alias ».</p>

<p>Par exemple, nous pouvons créer trois groupes différents d'utilisateurs, en chevauchant l'appartenance :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
User_Alias      GROUPONE = abby, brent, carl
User_Alias      GROUPTWO = brent, doris, eric,
User_Alias      GROUPTHREE = doris, felicia, grant
. . .
</code></pre>
<p>Les noms de groupes doivent commencer par une lettre majuscule. Nous pouvons ensuite autoriser les membres de <code>GROUPTWO</code> à mettre à jour la base de données <code>apt</code> en créant une règle similaire à celle-ci :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = /usr/bin/apt-get update
. . .
</code></pre>
<p>Si nous ne spécifions pas l'utilisateur/groupe par lequel la commande est exécutée, <code>sudo</code> désigne le <strong>root</strong> user par défaut.</p>

<p>Nous pouvons autoriser les membres de <code>GROUPTHREE</code> à arrêter et redémarrer la machine en créant un « alias de commande » et en l'utilisant dans une règle pour <code>GROUPTHREE</code> :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Cmnd_Alias      POWER = /sbin/shutdown, /sbin/halt, /sbin/reboot, /sbin/restart
GROUPTHREE  ALL = POWER
. . .
</code></pre>
<p>Nous créons un alias de commandes appelé <code>POWER</code>, qui intègre des commandes qui permettent d'arrêter et de redémarrer la machine. Nous allons ensuite autoriser les membres de <code>GROUPTHREE</code> à exécuter ces commandes.</p>

<p>Nous pouvons également créer des alias « Run as », qui peuvent remplacer la partie de la règle qui spécifie l'utilisateur par lequel la commande est exécutée :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Runas_Alias     WEB = www-data, apache
GROUPONE    ALL = (WEB) ALL
. . .
</code></pre>
<p>Cela permettra à toute personne membre de <code>GROUPONE</code> d'exécuter des commandes en tant que l'utilisateur <code>www-data</code> ou <code>apache</code> user.</p>

<p>Gardez juste à l'esprit, qu'en cas de conflit, les règles les plus récentes remplaceront les anciennes.</p>

<h3 id="comment-verrouiller-des-règles">Comment verrouiller des règles</h3>

<p>Il existe plusieurs manières d'avoir un meilleur contrôle sur la façon dont <code>sudo</code> réagit à un appel.</p>

<p>La commande <code>updatedb</code> associée au package <code>mlocate</code> est relativement sans danger sur un système à utilisateur unique. Si nous souhaitons autoriser les utilisateurs à l'exécuter avec des privilèges <strong>root</strong> <em>sans</em> avoir à saisir de mot de passe, nous pouvons créer une règle comme suit:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPONE    ALL = NOPASSWD: /usr/bin/updatedb
. . .
</code></pre>
<p><code>NOPASSWD</code> est une « balise » qui signifie qu'aucun mot de passe ne sera requis. Elle est accompagnée d'une commande appelée <code>PASSWD</code> qui indique le comportement par défaut. Une balise est pertinente pour le reste de la règle à moins qu'elle ne soit écrasée par une balise « jumelle » plus loin dans la ligne.</p>

<p>Par exemple, nous pouvons avoir une ligne comme celle-ci :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = NOPASSWD: /usr/bin/updatedb, PASSWD: /bin/kill
. . .
</code></pre>
<p>La balise <code>NOEXEC</code> est également une balise utile qui vous permettra d'empêcher certains comportements dangereux dans certains programmes.</p>

<p>Par exemple, certains programmes, comme <code>less</code>, peuvent générer d'autres commandes si vous saisissez ce qui suit à partir de leur interface :</p>
<pre class="code-pre "><code>!<span class="highlight">command_to_run</span>
</code></pre>
<p>Pour résumer, cela permet d'exécuter toute commande instruite par l'utilisateur avec les mêmes autorisations sous lesquelles <code>less</code> s'exécute, ce qui peut être assez dangereux.</p>

<p>Pour restreindre ce phénomène, nous pouvons utiliser une ligne comme la suivante :</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
<span class="highlight">username</span>  ALL = NOEXEC: /usr/bin/less
. . .
</code></pre>
<h2 id="informations-diverses">Informations diverses</h2>

<p>Il existe quelques autres éléments d'information qui pourront vous être d'une grande utilité lorsque vous utilisez <code>sudo</code>.</p>

<p>Si, dans le fichier de configuration, vous avez configuré un utilisateur ou un groupe sur « run as », vous pouvez exécuter les commandes en tant que ces utilisateurs en utilisant respectivement les balises <code>-u</code> et <code>-g</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -u <span class="highlight">run_as_user command</span>
</li><li class="line" data-prefix="$">sudo -g <span class="highlight">run_as_group command</span>
</li></ul></code></pre>
<p>Pour plus de commodité, <code>sudo</code> enregistrera par défaut vos détails d'authentification pendant un certain temps sur un terminal. Cela signifie que vous n'aurez plus à saisir votre mot de passe à chaque fois jusqu'à expiration du minuteur.</p>

<p>Pour des raisons de sécurité, si, une fois que vous aurez terminé d'exécuter des commandes administratives, vous souhaitez supprimer ce minuteur, vous pouvez exécuter la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li></ul></code></pre>
<p>Si, en revanche, vous souhaitez « primer » la commande <code>sudo</code> afin de ne plus recevoir d'invitation par la suite ou de renouveler votre location <code>sudo</code>, vous pouvez toujours saisir la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -v
</li></ul></code></pre>
<p>Vous serez invité à saisir votre mot de passe, invite qui sera mise en cache pour vos prochaines utilisations <code>sudo</code> jusqu'à expiration du délai de <code>sudo</code>.</p>

<p>Si vous vous demandez tout simplement quel type de privilèges ont été définis pour votre nom d'utilisateur, vous pouvez saisir :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -l
</li></ul></code></pre>
<p>Vous obtiendrez une liste de toutes les règles dans le fichier <code>/etc/sudoers</code> qui s'appliquent à votre utilisateur. Vous aurez ainsi une bonne idée de ce que vous serez autorisé à faire ou pas avec <code>sudo</code> en tant que tout utilisateur.</p>

<p>Il vous arrivera souvent d'exécuter une commande qui ne fonctionnera pas, car vous aurez oublié de la préfacer avec <code>sudo</code>. Pour ne pas avoir à re-saisir la commande, vous pouvez tirer profit de la fonctionnalité de bash qui signifie « répéter la dernière commande » :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo !!
</li></ul></code></pre>
<p>Le double point d'exclamation répétera la dernière commande. Nous l'avons précédé de <code>sudo</code> afin de rapidement changer la commande sans privilège en commande avec privilège.</p>

<p>Pour le plaisir, vous pouvez ajouter la ligne suivante à votre fichier <code>/etc/sudoers</code> avec <code>visudo</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Defaults    insults
. . .
</code></pre>
<p><code>sudo</code> renverra alors une insulte stupide à l'utilisateur si le mot de passe <code>sudo</code> qu'il a saisi est erroné. Nous pouvons utiliser <code>sudo -k</code> pour effacer l'ancien mot de passe en cache <code>sudo</code> pour l'essayer :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li><li class="line" data-prefix="$">sudo ls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[sudo] password for demo:    # <span class="highlight">enter an incorrect password here to see the results</span>
Your mind just hasn't been the same since the electro-shock, has it?
[sudo] password for demo:
My mind is going. I can feel it.
</code></pre>
<h2 id="conclusion">Conclusion</h2>

<p>Vous devriez maintenant avoir une compréhension de base sur la façon de lire et de modifier le fichier <code>sudoers</code> et des différentes méthodes disponibles pour obtenir des privilèges <strong>root</strong>.</p>

<p>N'oubliez pas qu'il existe une raison pour laquelle les privilèges de super-utilisateurs ne sont pas octroyés aux utilisateurs réguliers. Il est essentiel que vous ayez une bonne compréhension de ce que fait chacune des actions que vous exécutez avec des privilèges <strong>root</strong>. Ne prenez pas cette responsabilité à la légère. Apprenez à utiliser ces outils pour votre cas d'utilisation et à verrouiller toute fonctionnalité inutile.</p>
