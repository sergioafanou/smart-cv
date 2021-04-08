---
title : "Comment installer MongoDB à partir des référentiels APT par défaut sur Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-from-the-default-apt-repositories-on-ubuntu-20-04-fr
image: "https://sergio.afanou.com/assets/images/image-midres-29.jpg"
---

<h3 id="introduction">Introduction</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a> est une base de données de documents NoSQL gratuite et open source couramment utilisée dans les applications web modernes.</p>

<p>Dans ce tutoriel, vous allez apprendre à installer MongoDB, gérer son service et activer l'option d'accès à distance.</p>

<p><span class='note'><strong>Remarque</strong> : au moment de sa rédaction, ce tutoriel installe la version <span class="highlight">3.6</span> de MongoDB. Il s'agit de la version mise à disposition à partir des référentiels Ubuntu par défaut. Cependant, nous recommandons généralement d'installer plutôt la dernière version de MongoDB (la version <span class="highlight">4.4</span> au moment de la rédaction). Si vous souhaitez installer la dernière version de MongoDB, nous vous encourageons à suivre le guide suivant sur <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Comment installer MongoDB sur Ubuntu 20.04 à partir de la source</a>.<br></span></p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour suivre ce tutoriel, vous aurez besoin de :</p>

<ul>
<li>Un serveur Ubuntu 20.04 configuré en suivant ce <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Tutoriel de configuration initiale du serveur</a>, comprenant un non-root user avec des privilèges d'administration et un pare-feu configuré avec UFW.</li>
</ul>

<h2 id="Étape-1-—-installation-de-mongodb">Étape 1 — Installation de MongoDB</h2>

<p>Les référentiels des paquets officiels d'Ubuntu incluent MongoDB. Nous pouvons donc installer les paquets nécessaires en utilisant <code>apt</code>. Comme nous l'avons mentionné dans l'introduction, la version disponible à partir des référentiels par défaut n'est pas la plus récente. Pour installer la dernière version de Mongo, veuillez plutôt suivre <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">ce tutoriel</a>.</p>

<p>Tout d'abord, mettez à jour la liste des paquets pour obtenir la version la plus récente des listes du référentiel :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li></ul></code></pre>
<p>Maintenant, installez le paquet MongoDB en lui-même :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install mongodb
</li></ul></code></pre>
<p>Cette commande vous invite à confirmer si vous souhaitez bien installer le paquet <code>mongodb</code> et ses dépendances. Pour ce faire, appuyez sur <code>Y</code>, puis sur <code>ENTER</code>.</p>

<p>Cette commande installe plusieurs paquets qui contiennent une version stable de MongoDB, ainsi que des outils de gestion utiles pour le serveur MongoDB. Le serveur de la base de données démarre automatiquement après l'installation.</p>

<p>Ensuite, vérifions si le serveur fonctionne correctement.</p>

<h2 id="Étape-2-—-vérification-du-service-et-de-la-base-de-données">Étape 2 — Vérification du service et de la base de données</h2>

<p>Le processus d'installation a démarré MongoDB automatiquement. Cependant, vérifions tout de même si le service a bien été lancé et si la base de données fonctionne.</p>

<p>Tout d'abord, vérifiez l'état du service :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
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
<p>Selon ce résultat, le serveur MongoDB est opérationnel.</p>

<p>Pour vérifier cela de manière plus approfondie, en fait, nous allons nous connecter au serveur de base de données et exécuter la commande de diagnostic suivante. Cela générera la version actuelle de la base de données, l'adresse et le port du serveur et la sortie de la commande de l'état :</p>
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
<p>Dans la réponse, une valeur de <code>1</code> dans le champ <code>ok</code> indique que le serveur fonctionne correctement.</p>

<p>Ensuite, nous allons apprendre à gérer l'instance du serveur.</p>

<h2 id="Étape-3-—-gestion-du-service-mongodb">Étape 3 — Gestion du service MongoDB</h2>

<p>Le processus d'installation décrit à l'étape 1 configure MongoDB en tant que service <code>systemd</code>. Cela signifie que vous pouvez le gérer en utilisant des commandes <code>systemctl</code> standard avec tous les autres services du système dans Ubuntu.</p>

<p>Pour vérifier l'état du service, tapez :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Vous pouvez arrêter le serveur à tout moment en saisissant ce qui suit :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop mongodb
</li></ul></code></pre>
<p>Pour démarrer le serveur lorsqu'il est arrêté, tapez :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start mongodb
</li></ul></code></pre>
<p>Vous pouvez également redémarrer le serveur en utilisant la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>Par défaut, MongoDB est configuré pour démarrer automatiquement avec le serveur. Si jamais vous souhaitez désactiver ce démarrage automatique, saisissez ce qui suit :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable mongodb
</li></ul></code></pre>
<p>Vous pouvez réactiver le démarrage automatique à tout moment en utilisant la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable mongodb
</li></ul></code></pre>
<p>Maintenant, réglons les paramètres du pare-feu de notre installation MongoDB.</p>

<h2 id="Étape-4-—-réglage-du-pare-feu-facultatif">Étape 4 — Réglage du pare-feu (facultatif)</h2>

<p>En supposant que vous ayez suivi les instructions du <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">tutoriel de configuration initiale de serveur</a> pour activer le pare-feu sur votre serveur, le serveur MongoDB sera inaccessible à partir d'Internet.</p>

<p>Si vous prévoyez d'utiliser MongoDB uniquement en local, avec des applications exécutées sur le même serveur, il s'agit de la configuration recommandée et sécurisée à utiliser. Cependant, si vous souhaitez vous connecter à votre serveur MongoDB à partir d'Internet, vous devez autoriser les connexions entrantes en ajoutant une règle UFW.</p>

<p>Pour autoriser l'accès à MongoDB sur son port par défaut <code>27017</code> à partir de tout endroit, vous pouvez exécuter <code>sudo ufw allow <span class="highlight">27017</span></code>. Cependant, en activant l'accès Internet au serveur MongoDB sur une installation par défaut, toute personne aura un accès sans restriction au serveur de la base de données et à ses données.</p>

<p>Dans la plupart des cas, MongoDB ne doit être accessible qu'à partir de certains lieux de confiance (un autre serveur hébergeant une application, par exemple). Pour autoriser un autre serveur de confiance à accéder uniquement au port par défaut de MongoDB, vous pouvez spécifier l'adresse IP du serveur distant dans la commande <code>ufw</code>. Ainsi, seule la machine sera explicitement autorisée à se connecter :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">trusted_server_ip</span>/32 to any port <span class="highlight">27017</span>
</li></ul></code></pre>
<p>Vous pouvez vérifier le changement dans les paramètres de pare-feu avec <code>ufw</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>
<p>Vous devriez voir le trafic vers le port <code>27017</code> autorisé dans la sortie. Notez que si vous avez décidé d'autoriser une certaine adresse IP à se connecter au serveur MongoDB, l'adresse IP de l'emplacement autorisé sera référencée à la place de <code>Anywhere</code> dans la sortie de cette commande :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
<span class="highlight">27017                      ALLOW       Anywhere</span>
OpenSSH (v6)               ALLOW       Anywhere (v6)
<span class="highlight">27017 (v6)                 ALLOW       Anywhere (v6)</span>
</code></pre>
<p>Vous pouvez trouver des paramètres de pare-feu plus avancés pour restreindre l'accès aux services dans <a href="https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands">les Essentiels d'UFW : Règles et commandes communes du pare-feu</a>.</p>

<p>Même si le port est ouvert, MongoDB continuera toujours d'écouter uniquement l'adresse locale <code>127.0.0.1</code>. Pour autoriser des connexions à distance, ajoutez l'adresse IP publique de votre serveur au fichier <code>mongodb.conf</code>.</p>

<p>Ouvrez le fichier de configuration de MongoDB dans votre éditeur de texte préféré. Cet exemple de commande utilise <code>nano</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongodb.conf
</li></ul></code></pre>
<p>Ajoutez l'adresse IP de votre serveur MongoDB à la valeur <code>bindIP</code>. Veillez à placer une virgule entre l'adresse IP existante et celle que vous avez ajoutée :</p>
<div class="code-label " title="/etc/mongodb.conf">/etc/mongodb.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
logappend=true

bind_ip = 127.0.0.1<span class="highlight">,your_server_ip</span>
#port = 27017

...
</code></pre>
<p>Enregistrez le fichier et quittez l'éditeur. Si vous avez modifié le fichier avec <code>nano</code>, faites-le en appuyant sur <code>CTRL + X</code>, <code>Y</code>, puis sur <code>ENTER</code>.</p>

<p>Ensuite, redémarrez le service MongoDB :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>MongoDB écoute maintenant les connexions à distance, mais toute personne peut y accéder. Suivez le tutoriel <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-mongodb-on-ubuntu-20-04">Comment sécuriser MongoDB sur Ubuntu 20.04</a> pour ajouter un utilisateur administratif et verrouiller un peu plus l'accès.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Vous pouvez trouver d'autres tutoriels plus approfondis sur la façon de configurer et d'utiliser MongoDB dans <a href="https://www.digitalocean.com/community/search?q=mongodb">ces articles publiés par la communauté DigitalOcean</a>. La <a href="https://docs.mongodb.com/v3.6/">documentation officielle de MongoDB</a> est également une excellente ressource pour découvrir les possibilités que MongoDB a à offrir.</p>
