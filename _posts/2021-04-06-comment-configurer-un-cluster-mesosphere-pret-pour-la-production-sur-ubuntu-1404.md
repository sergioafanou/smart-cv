---
title : "Comment configurer un cluster Mesosphere prêt pour la production sur Ubuntu 14.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-a-production-ready-mesosphere-cluster-on-ubuntu-14-04-fr
image: "https://sergio.afanou.com/assets/images/image-midres-29.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Mesosphere est un système qui combine un certain nombre de composants afin de gérer efficacement la création de clusters des serveurs et des déploiements hautement disponibles au-dessus d'une couche de système d'exploitation existante. Contrairement aux systèmes comme CoreOS, Mesosphere n'est pas un système d'exploitation spécialisé mais plutôt un ensemble de paquets.</p>

<p>Dans ce guide, nous allons découvrir comment configurer un cluster hautement disponible dans Mesosphere. Cette configuration nous permettra de nous doter d'une fonctionnalité de basculement dans le cas où l'un de nos nœuds maîtres tomberait en panne, ainsi qu'un pool de serveurs esclaves qui gère les tâches planifiées.</p>

<p>Au cours de ce guide, nous utiliserons des serveurs Ubuntu 14.04.</p>

<h2 id="conditions-prélables-et-objectifs">Conditions prélables et objectifs</h2>

<p>Avant de suivre ce guide, il est fortement recommandé de revoir notre <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-mesosphere">introduction à Mesosphere</a>. Il s'agit d'un excellent moyen pour vous de vous familiariser avec les composants dont le système est composé et de vous aider à identifier de quoi chaque unité est responsable.</p>

<p>Au cours de ce tutoriel, nous utiliserons six serveurs Ubuntu. Cela satisfait à la recommandation Apache Mesos qui préconise l'utilisation d'au moins trois maîtres dans un environnement de production. Cela permet également d'avoir un pool de trois serveurs de travail ou esclaves, qui se verront attribués du travail lorsque les tâches seront envoyées au cluster.</p>

<p>Les six serveurs avec lesquels nous travaillerons utiliserons <code>zookeeper</code> pour suivre le serveur maître leader actuel. La couche Mesos, intégré au-dessus, offrira une synchronisation et une manipulation des ressources distribuées Elle est responsable de la gestion du cluster. Marathon, le système d'init distribué du cluster, est utilisé pour planifier des tâches et travailler manuellement sur les serveurs esclaves.</p>

<p>Dans le cas de ce guide, nous supposerons que nos machines ont la configuration suivante :</p>

<table><thead>
<tr>
<th>Nom d'hôte</th>
<th>Fonction</th>
<th>adresse IP</th>
</tr>
</thead><tbody>
<tr>
<td>maître1</td>
<td>Maître Mesos</td>
<td>192.0.2.1</td>
</tr>
<tr>
<td>maître2</td>
<td>Maître Mesos</td>
<td>192.0.2.2</td>
</tr>
<tr>
<td>maître3</td>
<td>Maître Mesos</td>
<td>192.0.2.3</td>
</tr>
<tr>
<td>esclave1</td>
<td>Esclave Mesos</td>
<td>192.0.2.51</td>
</tr>
<tr>
<td>esclave2</td>
<td>Esclave Mesos</td>
<td>192.0.2.52</td>
</tr>
<tr>
<td>esclave3</td>
<td>Esclave Mesos</td>
<td>192.0.2.53</td>
</tr>
</tbody></table>

<p>Ubuntu 14.04 devrait être installé sur chacune de ces machines. Vous devriez compléter les éléments de configuration de base répertoriés dans notre <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04">guide de configuration du serveur initial Ubuntu 14.04</a>.</p>

<p>Une fois que vous aurez terminé les deux étapes ci-dessus, poursuivez la lecture de ce guide.</p>

<h2 id="installer-mesosphere-sur-les-serveurs">Installer Mesosphere sur les serveurs</h2>

<p>La première étape pour faire fonctionner votre cluser consiste à installer le logiciel. Heureusement, le projet Mesosphere conserve un dépôt Ubuntu avec des packages à jour faciles à installer.</p>

<h3 id="ajouter-les-dépôts-mesosphere-à-vos-hôtes">Ajouter les dépôts Mesosphere à vos hôtes</h3>

<p>Sur <strong>tous</strong> les hôtes (maîtres et esclaves), suivez les étapes suivantes.</p>

<p>Tout d'abord, ajoutez le dépôt Mesosphere à votre liste de sources. Pour suivre ce processus, la clé du projet Mesosphere devra être téléchargée à partir du serveur de clés Ubuntu, puis l'URL applicable de notre version Ubuntu développée. Le projet vous offre un moyen pratique de le faire :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF
DISTRO=$(lsb_release -is | tr '[:upper:]' '[:lower:]')
CODENAME=$(lsb_release -cs)
echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" | sudo tee /etc/apt/sources.list.d/mesosphere.list
</code></pre>
<h3 id="installer-les-composants-nécessaires">Installer les composants nécessaires</h3>

<p>Une fois le dépôt Mesosphere ajouté à votre système, vous devez mettre à jour votre cache de package local pour avoir accès à votre nouveau composant :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo apt-get -y update
</code></pre>
<p>Ensuite, vous devez installer les packages nécessaires. Les composants dont vous avez besoin dépendront du rôle de l'hôte.</p>

<p>Pour vos hôtes <strong>maîtres</strong>, vous avez besoin du package de méta <code>mesosphere</code>. Cela inclut les applications <code>zookeeper</code>, <code>mesos</code>, <code>marathon</code> et <code>chronos</code> :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo apt-get install mesosphere
</code></pre>
<p>Pour vos hôtes <strong>slave</strong>, vous avez uniquement besoin du package <code>mesos</code>, qui extrait également <code>zookeeper</code> comme dépendance :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo apt-get install mesos
</code></pre>
<h2 id="configurer-les-informations-de-connexion-de-zookeeper-pour-mesos">Configurer les informations de connexion de Zookeeper pour Mesos</h2>

<p>Dans un premier temps, nous allons configurer nos informations de connexion à <code>zookeeper</code>. Il s'agit de la couche sous-jacente qui permet à tous vos hôtes de se connecter aux bons serveurs maîtres, c'est pourquoi il est logique de commencer ici.</p>

<p>Nos serveurs maîtres seront les seuls membres de notre cluster <code>zookeeper</code>, mais l'intégralité de nos serveurs devront être configurés pour pouvoir communiquer en utilisant le protocole. Le fichier qui définit cela est le suivant <code>/etc/mesos/zk</code>.</p>

<p>Sur <strong>tous</strong> vos hôtes, procédez à l'étape suivante. Ouvrez le fichier avec les privilèges root :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo nano /etc/mesos/zk
</code></pre>
<p>À l'intérieur, vous trouverez l'URL de connexion configurée par défaut pour accéder à une instance locale. Cela ressemble à ce qui suit :</p>
<pre class="code-pre "><code>zk://localhost:2181/mesos
</code></pre>
<p>Vous devez la modifier afin qu'elle pointe vers nos trois serveurs maîtres. Pour cela, vous devez remplacer <code>localhost</code> par l'adresse IP de notre premier maître Mesos. Nous pouvons ensuite ajouter une virgule après la spécification du port et reproduire le format pour ajouter nos deuxième et troisième maîtres à la liste.</p>

<p>Dans notre guide, les adresses IP de nos maîtres sont les suivantes : <code>192.0.2.1</code>, <code>192.168.2.2</code> et <code>192.168.2.3</code>. En utilisant ces valeurs, notre fichier ressemblera à ce qui suit :</p>
<pre class="code-pre "><code>zk://<span class="highlight">192.0.2.1</span>:2181,<span class="highlight">192.0.2.2</span>:2181,<span class="highlight">192.0.2.3</span>:2181/mesos
</code></pre>
<p>La ligne doit commencer par <code>zk://</code> et se terminer par <code>/mesos</code>. Entre deux sont spécifiées les adresses IP de vos serveurs maîtres et les ports <code>zookeeper</code> (<code>2181</code> par défaut).</p>

<p>Enregistrez et fermez le fichier lorsque vous avez terminé.</p>

<p>Utilisez cette même saisie dans chacun de vos maîtres et esclaves. Cela aidera chaque serveur individuel à se connecter aux bons serveurs maîtres et pouvoir ainsi communiquer avec le cluster.</p>

<h2 id="paramétrer-la-configuration-de-zookeeper-des-serveurs-maîtres">Paramétrer la configuration de Zookeeper des serveurs maîtres</h2>

<p>Sur vos serveurs <strong>master</strong>, nous devrons procéder à une configuration supplémentaire de <code>zookeeper</code>.</p>

<p>La première étape consiste à configurer un numéro d'identification unique, de 1 à 255 pour chacun de vos serveurs maîtres. Il se trouve dans le fichier <code>/etc/zookeeper/conf/myid</code>. Maintenant, ouvrez-le :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo nano /etc/zookeeper/conf/myid
</code></pre>
<p>Supprimez toutes les informations qui se trouvent dans ce fichier et remplacez-les par un seul numéro, de 1 à 255. Le numéro attribué à chacun de vos serveurs maîtres doit être unique. Par souci de simplicité, il est plus facile de commencer par 1 et de progresser. Dans notre guide, nous utiliserons 1, 2 et 3.</p>

<p>Notre premier serveur contiendra uniquement ce qui suit dans le fichier :</p>
<pre class="code-pre "><code>1
</code></pre>
<p>Enregistrez et fermez le fichier lorsque vous avez terminé. Procédez ainsi sur chacun de vos serveurs maîtres.</p>

<p>Ensuite, nous devons modifier notre fichier de configuration <code>zookeeper</code> pour cartographier nos ID <code>zookeeper</code> sur les hôtes en tant que tel. Vous aurez ainsi la garantie que le service est bien en capacité de résoudre correctement chaque hôte à partir du système d'ID qu'il utilise.</p>

<p>Maintenant, ouvrez le fichier de configuration <code>zookeeper</code> :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo nano /etc/zookeeper/conf/zoo.cfg
</code></pre>
<p>Dans ce fichier, vous devrez cartographier chaque ID sur un hôte. La spécification de l'hôte inclura deux ports. Le premier servira à communiquer avec le leader. Le second se chargera des élections au moment où un nouveau leader sera nécessaire. Les serveurs <code>zookeeper</code> sont identifiés par le terme « server » suivi d'un point et de leur numéro d'ID.</p>

<p>Dans notre guide, nous utiliserons les ports par défaut pour chaque fonction et nos ID vont de 1 à 3. Notre fichier ressemblera à ce qui suit :</p>
<pre class="code-pre "><code>server.<span class="highlight">1</span>=<span class="highlight">192.168.2.1</span>:2888:3888
server.<span class="highlight">2</span>=<span class="highlight">192.168.2.2</span>:2888:3888
server.<span class="highlight">3</span>=<span class="highlight">192.168.2.3</span>:2888:3888
</code></pre>
<p>Ajoutez ces mêmes cartographies dans chacun des fichiers de configuration de vos serveurs maîtres. Enregistrez et fermez chaque fichier lorsque vous avez terminé.</p>

<p>Avec cela, notre configuration <code>zookeeper</code> est achevée. Nous pouvons commencer à nous concentrer sur Mesos et Marathon.</p>

<h2 id="configurer-mesos-sur-les-serveurs-maîtres">Configurer Mesos sur les serveurs maîtres</h2>

<p>Ensuite, nous allons configurer Mesos sur les trois serveurs maîtres. Suivez ces étapes sur chacun de vos serveurs maîtres.</p>

<h3 id="modifier-le-quorum-pour-refléter-la-taille-de-votre-cluster">Modifier le Quorum pour refléter la taille de votre cluster</h3>

<p>Tout d'abord, nous devons ajuster le quorum nécessaire à la prise de décision. Il déterminera le nombre d'hôtes nécessaires pour que le cluster soit en état de fonctionnement.</p>

<p>Le quorum doit être configuré de manière à ce que plus de 50 pour cent des membres maîtres soient présents pour prendre des décisions. Cependant, il nous faut également établir une certaine tolérance de défaillance dans le cas où, si tous nos maîtres ne sont pas présents, le cluster puisse toujours fonctionner.</p>

<p>Nous disposons de trois maîtres. Par conséquent, le seul réglage qui satisfait à ces deux exigences est un quorum de deux. Étant donné que la configuration initiale suppose un réglage de serveur unique, le quorum est actuellement défini à un.</p>

<p>Ouvrez le fichier de configuration du quorum :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo nano /etc/mesos-master/quorum
</code></pre>
<p>Remplacez la valeur par « 2 » :</p>
<pre class="code-pre "><code>2
</code></pre>
<p>Enregistrez et fermez le fichier. Procédez ainsi sur chacun de vos serveurs maîtres.</p>

<h3 id="configurer-le-nom-d-39-hôte-et-l-39-adresse-ip">Configurer le nom d'hôte et l'adresse IP</h3>

<p>Nous allons ensuite spécifier le nom de l'hôte et l'adresse IP de chacun de nos serveurs maîtres. Pour le nom de l'hôte, nous utiliserons l'adresse IP afin que nos instances n'aient pas de difficultés à se résoudre correctement.</p>

<p>Pour nos serveurs maîtres, l'adresse IP doit être placée dans ces fichiers :</p>

<ul>
<li>/etc/mesos-master/ip</li>
<li>/etc/mesos-master/hostname</li>
</ul>

<p>Tout d'abord, ajoutez l'adresse IP individuelle de chaque nœud maître dans le fichier <code>/etc/mesos-master/ip</code>. N'oubliez pas de la modifier sur chaque serveur afin qu'elle corresponde à la valeur applicable :</p>
<pre class="code-pre "><code>echo <span class="highlight">192.168.2.1</span> | sudo tee /etc/mesos-master/ip
</code></pre>
<p>Maintenant, nous pouvons copier cette valeur sur le fichier du nom de l'hôte :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo cp /etc/mesos-master/ip /etc/mesos-master/hostname
</code></pre>
<p>Procédez ainsi sur chacun de vos serveurs maîtres.</p>

<h2 id="configurer-marathon-sur-les-serveurs-maîtres">Configurer Marathon sur les serveurs maîtres</h2>

<p>Maintenant que Mesos est configuré, nous pouvons procéder à la configuration de l'implémentation du système init en cluster de Mesosphere.</p>

<p>Marathon s'exécutera sur chacun de nos hôtes maîtres. Cependant, seul le serveur maître principal pourra planifier des tâches. Les autres instances de Marathon transmettront les requêtes de manière transparente sur le serveur maîtres.</p>

<p>Tout d'abord, nous devons à nouveau configurer le nom de l'hôte pour l'instance de Marathon de chaque serveur. Encore une fois, nous utiliserons l'adresse IP dont nous disposons déjà dans un fichier. Nous pouvons la copier dans l'emplacement du fichier dont nous avons besoin.</p>

<p>Cependant, la structure du répertoire de configuration de Marathon dont nous avons besoin n'est pas automatiquement créée. Nous allons devoir créer le répertoire pour pouvoir ensuite y copier le fichier :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo mkdir -p /etc/marathon/conf
sudo cp /etc/mesos-master/hostname /etc/marathon/conf
</code></pre>
<p>Ensuite, nous devons définir la liste des maîtres <code>zookeeper</code> auxquels Marathon se connectera pour obtenir des informations et une planification. Il s'agit de la même chaîne de connexion <code>zookeeper</code> que nous avons utilisée pour Mesos afin que nous puissions tout simplement y copier le fichier. Nous devons le placer dans un fichier appelé <code>master</code> :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo cp /etc/mesos/zk /etc/marathon/conf/master
</code></pre>
<p>Notre service Marathon pourra ainsi se connecter au cluster Mesos. Cependant, nous souhaitons également que Marathon stocke ses propres informations dans <code>zookeeper</code>. Pour cela, nous utiliserons l'autre fichier de connexion <code>zookeeper</code> comme base et modifierons tout simplement le point final.</p>

<p>Tout d'abord, copiez le fichier dans l'emplacement zookeeper de Marathon :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo cp /etc/marathon/conf/master /etc/marathon/conf/zk
</code></pre>
<p>Ensuite, ouvrez le fichier dans votre éditeur :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo nano /etc/marathon/conf/zk
</code></pre>
<p>Le point final est la seule partie que nous ayons besoin de modifier. Nous allons remplacer <code>/mesos</code> par <code>/marathon</code> :</p>
<pre class="code-pre "><code>zk://192.0.2.1:2181,192.0.2.2:2181,192.0.2.3:2181/<span class="highlight">marathon</span>
</code></pre>
<p>C'est tout ce qu'il faut faire pour configurer Marathon.</p>

<h2 id="configurer-les-règles-d-39-init-des-services-et-redémarrer-les-services">Configurer les règles d'init des services et redémarrer les services</h2>

<p>Ensuite, nous allons redémarrer les services de serveurs maîtres pour utiliser les paramètres qui nous avons configurés.</p>

<p>Tout d'abord, nous allons nous assurer que nos serveurs maîtres exécutent uniquement le processus maître Mesos et non pas le processus esclave. Nous pouvons arrêter tout processus esclave actuellement en cours d'exécution (cela peut échouer mais ce n'est pas un problème car il s'agit uniquement de vérifier que le processus est arrêté). Nous pouvons également que le serveur ne commence pas avec le processus esclave au démarrage en créant un fichier de remplacement :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo stop mesos-slave
echo manual | sudo tee /etc/init/mesos-slave.override
</code></pre>
<p>Maintenant, il ne reste plus qu'à redémarrer <code>zookeeper</code>, qui configurera nos élections maîtresses. Nous pouvons ensuite démarrer nos processus Mesos maître et Marathon :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo restart zookeeper
sudo start mesos-master
sudo start marathon
</code></pre>
<p>Pour obtenir un pic dans ce que vous venez juste de configurer, consultez l'un de nos serveurs maîtres notre navigateur web au port <code>5050</code> :</p>
<pre class="code-pre "><code>http://<span class="highlight">192.168.2.1</span>:5050
</code></pre>
<p>Vous devriez voir l'interface principale de Mesos. Vous serez éventuellement informé que vous êtes redirigé vers le master actif en fonction de si vous vous êtes connecté au leader ou pas. Dans tous les cas, l'écran ressemblera à ce qui suit :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/mesos_main.png" alt="Principale interface de Mesos"></p>

<p>Il s'agit d'une vue de votre cluster actuel. Vous n'y voyez pas grand-chose car il n'y a aucun nœud esclave disponible et aucune tâche a été démarrée.</p>

<p>Nous avons également configuré Marathon, le contrôleur de tâches longue durée de Mesosphere. Il sera disponible sur le port <code>8080</code> sur l'un de vos maîtres :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/marathon_main.png" alt="Interface principale de Marathon"></p>

<p>Nous allons brièvement voir comment utiliser ces interfaces une fois que nous avons configuré nos esclaves.</p>

<h2 id="configurer-les-serveurs-esclaves">Configurer les serveurs esclaves</h2>

<p>Maintenant que nous avons configuré nos serveurs maîtres, nous pouvons commencer à configurer nos serveurs esclaves.</p>

<p>Nous avons déjà configuré nos esclaves avec les informations de connexion <code>zookeeper</code> de nos serveurs maîtres. Les esclaves eux-mêmes n'exécutent pas leurs propres instances <code>zookeeper</code>.</p>

<p>Nous pouvons arrêter tout processus <code>zookeeper</code> qui fonctionne actuellement sur nos nœuds esclaves et créer un fichier de remplacement afin qu'il ne démarre pas automatiquement lorsque le serveur redémarre :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo stop zookeeper
echo manual | sudo tee /etc/init/zookeeper.override
</code></pre>
<p>Ensuite, nous voulons créer un autre fichier de remplacement pour s'assurer que le processus maître Mesos ne commence pas sur nos serveurs esclaves. Nous allons également nous assurer qu'il est actuellement arrêté (il se peut que cette commande échoue si le processus est déjà arrêté. Ceci n'est pas un problème) :</p>
<pre class="code-pre "><code class="code-highlight language-bash">echo manual | sudo tee /etc/init/mesos-master.override
sudo stop mesos-master
</code></pre>
<p>Ensuite, nous devons configurer l'adresse IP et le nom de l'hôte, tout comme nous l'avons fait pour nos serveurs maîtres. Cela implique la mise en place de l'adresse IP de chaque nœud dans un fichier, cette fois sous le répertoire <code>/etc/mesos-slave</code>. Nous utiliserons également le nom de l'hôte pour accéder facilement aux services via l'interface web :</p>
<pre class="code-pre "><code>echo <span class="highlight">192.168.2.51</span> | sudo tee /etc/mesos-slave/ip
sudo cp /etc/mesos-slave/ip /etc/mesos-slave/hostname
</code></pre>
<p>Encore une fois, utilisez l'adresse IP de chaque serveur esclave pour la première commande. Cela garantira qu'elle est bien liée à la bonne interface.</p>

<p>Maintenant, nous avons tous les éléments pour démarrer nos esclaves Mesos. Il nous suffit juste d'activer le service :</p>
<pre class="code-pre "><code class="code-highlight language-bash">sudo start mesos-slave
</code></pre>
<p>Procédez ainsi sur chacune de vos machines esclaves.</p>

<p>Pour voir si vos esclaves s'enregistrent avec succès dans votre cluster, revenez à l'un de vos serveurs maîtres au port <code>5050</code> :</p>
<pre class="code-pre "><code>http://<span class="highlight">192.168.2.1</span>:5050
</code></pre>
<p>Maintenant, vous devriez voir le nombre d'esclaves actifs afficher « 3 » dans l'interface :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/three_slaves.png" alt="Trois esclaves Mesos"></p>

<p>Vous pouvez également voir que les ressources disponibles dans l'interface ont été mises à jour pour refléter les ressources pooled dans vos machines esclaves :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/resources.png" alt="Ressources Mesos"></p>

<p>Pour obtenir de plus amples informations sur chacune de vos machines, vous pouvez cliquer sur le lien « Slaves » en haut de l'interface. Ceci nous donnera un aperçu de la contribution en ressources de chaque machine, ainsi que les liens vers une page de chaque esclave :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/slaves_page.png" alt="Page des esclaves Mesos"></p>

<h2 id="démarrer-des-services-sur-mesos-et-marathon">Démarrer des services sur Mesos et Marathon</h2>

<p>Marathon est l'utilitaire Mesosphere permettant de planifier les tâches de longue durée. Il est facile de penser que Marathon est le système init pour un cluster Mesosphere car il prend en charge les services de démarrage et d'arrêt, tout en planifiant les tâches et en s'assurant que les applications remontent si elles descendent.</p>

<p>Vous pouvez également ajouter des services et des tâches à Marathon de quelques manières différentes. Nous allons uniquement couvrir les services de base. Les conteneurs Docker seront traités dans un prochain guide.</p>

<h3 id="démarrer-un-service-via-l-39-interface-web">Démarrer un service via l'interface Web</h3>

<p>La manière la plus directe de lancer un service rapidement sur le cluster consiste à ajouter une application par le biais de l'interface Web Marathon.</p>

<p>Tout d'abord, consultez l'interface Web de Marathon sur l'un de vos serveurs maîtres. N'oubliez pas que l'interface de Marathon se trouve sur le port <code>8080</code> :</p>
<pre class="code-pre "><code>http://<span class="highlight">192.168.2.1</span>:8080
</code></pre>
<p>À partir de là, vous pouvez cliquer sur le bouton « New App » dans le coin supérieur droit. Cela fera apparaître une superposition dans laquelle vous pouvez ajouter des informaions sur votre nouvelle application :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/marathon_new_app.png" alt="Nouvelle application Marathon"></p>

<p>Complétez les champs avec les exigences de votre application. Seules les champs suivants sont obligatoires</p>

<ul>
<li><strong>ID</strong> : un ID unique que l'utilisateur choisi pour identifier un processus. Vous pouvez utiliser ce que vous voulez, mais il doit être unique.</li>
<li><strong>Command</strong> : il s'agit de la commande réelle que Marathon exécutera réellement. Il s'agit du processus qui sera surveillé et redémarré en cas d'échec.</li>
</ul>

<p>En utilisant ces informations, vous pouvez configurer un service simple qui imprime simplement « hello » et se met en veille pendant 10 secondes. Nous allons appeler ce « hello » :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/simple_app.png" alt="Application simple de Marathon"></p>

<p>Une fois que vous reviendrez à l'interface, le service passera de « Deploying » à « Running » :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/running.png" alt="Application Marathon en cours d'exécution"></p>

<p>Environ toutes les 10 secondes, l'élément « Tasks/Instances » passera de « 1/1 » à « 0/1 » au fur et à mesure que la quantité de mise en veille passe et le service s'arrête. Ensuite, Marathon redémarre automatiquement la tâche. Nous pouvons voir ce processus plus clairement dans l'interface Web de Mesos sur le port <code>5050</code> :</p>
<pre class="code-pre "><code>http://<span class="highlight">192.168.2.1</span>:5050
</code></pre>
<p>Ici, vous pouvez voir le processus se terminer et être redémarré :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/restart_task.png" alt="Tâche de redémarrage de Mesos"></p>

<p>En cliquant sur « Sandbox » et que « stdout » d'une des tâches, vous pouvez voir la sortie « hello » en cours de production :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/output.png" alt="Sortie Mesos"></p>

<h3 id="démarrage-d-39-un-service-via-l-39-api">Démarrage d'un service via l'API</h3>

<p>Nous pouvons également soumettre des services via l'API Marathon. Pour cela, il vous faudra y transmettre un objet JSON contenant tous les champs que la superposition contenait.</p>

<p>Il s'agit d'un processus relativement simple. Encore une fois, les seuls champs requis sont <code>id</code> pour l'identificateur de processus et <code>cmd</code> qui contient la commande réelle à exécuter.</p>

<p>Donc, nous devons créer un fichier JSON appelé <code>hello.json</code> avec ces informations :</p>
<pre class="code-pre "><code class="code-highlight language-bash">nano hello.json
</code></pre>
<p>À l'intérieur, la spécification minimum devrait ressembler à ce qui suit :</p>
<pre class="code-pre "><code class="code-highlight language-json">{
  "id": "hello2",
  "cmd": "echo hello; sleep 10"
}
</code></pre>
<p>Ce service fonctionnera correctement. Cependant, si nous souhaitons réellement émuler le service que nous avons créé dans l'IU web, nous devons ajouter quelques champs supplémentaires. Ces valeurs seront configurées par défaut dans l'IU web et nous pouvons les répliquer ici :</p>
<pre class="code-pre "><code class="code-highlight language-json">{
  "id": "hello2",
  "cmd": "echo hello; sleep 10",
  "mem": 16,
  "cpus": 0.1,
  "instances": 1,
  "disk": 0.0,
  "ports": [0]
}
</code></pre>
<p>Sauvegardez et fermez le fichier JSON une fois que vous aurez fini.</p>

<p>Ensuite, nous pouvons le soumettre en utilisant l'API Marathon. La cible est l'un de nos services maître de Marathon sur le port <code>8080</code> et le point de terminaison est <code>/v2/apps</code>. La charge de données de notre fichier, que nous nous pouvons y lire <code>curl</code> en utilisant la balise <code>-d</code> avec la balsise <code>@</code> pour indiquer un fichier.</p>

<p>La commande ressemblera à ceci :</p>
<pre class="code-pre "><code>curl -i -H 'Content-Type: application/json' -d@hello2.json <span class="highlight">192.168.2.1</span>:8080/v2/apps
</code></pre>
<p>Si nous regardons l'interface Marathon, nous pouvons voir que l'ajout à bien été effectué. Il semble avoir exactement les mêmes propriétés que notre premier service :</p>

<p><img src="https://assets.digitalocean.com/articles/mesos_cluster/two_services.png" alt="Deux services Marathon"></p>

<p>Vous pouvez surveiller et accéder au nouveau service exactement de la même manière que le premier service.</p>

<h2 id="conclusion">Conclusion</h2>

<p>À ce stade, vous devriez avoir un cluster Mesosphere qui fonctionne. Pour le moment, nous avons uniquement abordé la configuration de base. Vous devriez cependant être en mesure de voir les possibilités qu'offre le système Mesosphere.</p>

<p>Dans les futurs guides, nous allons voir comment déployer des conteneurs Docker sur votre cluster et utiliser certains outils plus en profondeur.</p>
