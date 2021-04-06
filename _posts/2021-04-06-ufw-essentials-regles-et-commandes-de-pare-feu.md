---
title : "UFW Essentials : règles et commandes de pare-feu"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands-fr
image: "https://sergio.afanou.com/assets/images/image-midres-2.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>UFW est un outil de configuration de pare-feu pour les iptables qui est inclus dans Ubuntu par défaut. Sous forme d'aide-mémo, ce guide vous permettra de trouver rapidement les commandes UFW qui créeront des règles de pare-feu iptables qui sont utiles dans les scénarios classiques du quotidien. Ceci intègre des exemples UFW d'autorisation et de blocage de plusieurs services par port, interface de réseau ou adresse IP source.</p>

<h4 id="comment-utiliser-ce-guide">Comment utiliser ce guide</h4>

<ul>
<li>S'il s'agit de vos premiers pas avec UFW pour la configuration de pare-feu, consultez notre <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04">introduction à UFW</a></li>
<li>La plupart des règles décrites ici supposent que vous utilisez le règlement UFW par défaut. En effet, il est configuré pour autoriser le trafic sortant et refuser le trafic entrant, par défaut. Par conséquent, vous pouvez autoriser le trafic de manière sélective par le biais des politiques par défaut.</li>
<li>Utilisez les sections suivantes pour réaliser ce que vous tentez de faire. La plupart des sections ne sont prédites sur aucune autre. Vous pouvez donc utiliser les exemples ci-dessous indépendamment</li>
<li>Utilisez le menu Contents sur le côté droit de cette page (sur les largeurs de page large) ou la fonction de recherche de votre navigateur pour localiser les sections dont vous avez besoin</li>
<li>Copiez et collez les exemples de ligne de commande donnés, en substituant les valeurs en rouge par vos propres valeurs</li>
</ul>

<p>N'oubliez pas que vous pouvez vérifier votre règlement UFW actuel avec <code>sudo ufw status</code> ou <code>sudo ufw status verbose</code>.</p>

<h2 id="bloquer-une-adresse-ip">Bloquer une adresse IP</h2>

<p>Pour bloquer toutes les connexions réseau qui proviennent d'une adresse IP spécifique, <code>15.15.15.51</code> par exemple, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw deny from <span class="highlight">15.15.15.51</span>
</li></ul></code></pre>
<p>Dans cet exemple, <code>from 15.15.15.51</code> spécifie une adresse IP <strong>source</strong> de « 15.15.15.51 ». Si vous le souhaitez, vous pouvez spécifier ici un sous-réseau, tel que <code>15.15.15.0/24</code> à la place. L'adresse IP source peut être spécifiée dans toute règle de pare-feu, notamment une règle <strong>allow</strong>.</p>

<h3 id="bloquer-les-connexions-à-une-interface-réseau">Bloquer les connexions à une interface réseau</h3>

<p>Pour bloquer les connexions d'une adresse IP spécifique, par exemple <code>15.15.15.51</code> à une interface réseau spécifique, par exemple <code>eth0</code>, utilisez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw deny in on eth0 from <span class="highlight">15.15.15.51</span>
</li></ul></code></pre>
<p>C'est la même chose que dans l'exemple précédent, en ajoutant <code>in on eth0</code>. Vous pouvez spécifier l'interface réseau dans n'importe quelle règle de pare-feu. C'est un excellent moyen de limiter la règle à un réseau spécifique.</p>

<h2 id="service -ssh">Service : SSH</h2>

<p>Si vous utilisez un serveur cloud, vous voudrez probablement autoriser les connexions SSH entrantes (port 22) afin de pouvoir vous connecter à votre serveur et le gérer. Cette section vous montre de quelle manière configurer votre pare-feu avec plusieurs règles liées au SSH.</p>

<h3 id="autoriser-ssh">Autoriser SSH</h3>

<p>Pour autoriser toutes les connexions SSH entrantes, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow ssh
</li></ul></code></pre>
<p>Voici une autre syntaxe qui vous permettra de spécifier le numéro de port du service SSH :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 22
</li></ul></code></pre>
<h3 id="autoriser-un-ssh-entrant-à-partir-de-l-39-adresse-ip-ou-un-sous-réseau-spécifique">Autoriser un SSH entrant à partir de l'adresse IP ou un sous-réseau spécifique</h3>

<p>Pour autoriser les connexions SSH entrantes à partir d'une adresse IP ou d'un sous-réseau spécifique, vous devez spécifier la source. Par exemple, si vous souhaitez autoriser l'intégralité du sous-réseau <code>15.15.15.0/24</code>, vous devez exécuter la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">15.15.15.0/24</span>  to any port 22
</li></ul></code></pre>
<h3 id="autoriser-un-rsync-entrant-à-partir-de-l-39-adresse-ip-ou-un-sous-réseau-spécifique">Autoriser un Rsync entrant à partir de l'adresse IP ou un sous-réseau spécifique</h3>

<p>Vous pouvez utiliser Rsync, qui fonctionne sur le port 873, pour transférer des fichiers d'un ordinateur à un autre.</p>

<p>Pour autoriser les connexions rsync entrantes à partir d'une adresse IP ou d'un sous-réseau spécifique, vous devez spécifier l'adresse IP source et le port de destination. Par exemple, si vous souhaitez autoriser que l'intégralité du sous-réseau <code>15.15.15.0/24</code> soit en mesure de rsync sur votre serveur, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">15.15.15.0/24</span> to any port 873
</li></ul></code></pre>
<h2 id="service -serveur-web">Service : serveur web</h2>

<p>Les serveurs Web, comme Apache et Nginx, écoutent généralement les requêtes des connexions HTTP et HTTPS sur le port 80 et 443, respectivement. Si votre politique pour le trafic entrant est configurée sur drop ou deny par défaut, il vous faudra créer des règles qui permettront à votre serveur de répondre à ces requêtes.</p>

<h3 id="autoriser-tous-les-http-entrants">Autoriser tous les HTTP entrants</h3>

<p>Pour autoriser toutes les connexions HTTP entrantes (port 80), exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow http
</li></ul></code></pre>
<p>Vous pouvez également utiliser une autre syntaxe en spécifiant le numéro de port du service HTTP :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 80
</li></ul></code></pre>
<h3 id="autoriser-tous-les-http-entrants">Autoriser tous les HTTP entrants</h3>

<p>Exécutez la commande suivante pour autoriser toutes les connexions HTTPS entrantes (port 443) :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow https
</li></ul></code></pre>
<p>Vous pouvez également utiliser une autre syntaxe en spécifiant le numéro de port du service HTTPS :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 443
</li></ul></code></pre>
<h3 id="autoriser-tout-le-trafic-http-et-https-entrant">Autoriser tout le trafic HTTP et HTTPS entrant</h3>

<p>Si vous souhaitez autoriser le trafic à la fois HTTP et HTTPS, vous pouvez créer une règle unique qui autorise les deux ports. Pour autoriser toutes les connexions HTTP et HTTPS entrantes (port 443), exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow proto tcp from any to any port 80,443
</li></ul></code></pre>
<p>Notez que vous devez indiquer le protocole en utilisant <code>proto tcp</code> lorsque vous spécifiez plusieurs ports.</p>

<h2 id="service -mysql">Service : MySQL</h2>

<p>MySQL écoute les connexions des clients sur le port 3306. Si un client utilise votre serveur de base de données MySQL sur un serveur distant, vous devez avoir la garantie de bien autoriser ce trafic.</p>

<h3 id="autoriser-mysql-à-partir-de-l-39-adresse-ip-ou-un-sous-réseau-spécifique">Autoriser MySQL à partir de l'adresse IP ou un sous-réseau spécifique</h3>

<p>Pour autoriser les connexions MySQL entrantes à partir d'une adresse IP ou d'un sous-réseau spécifique, vous devez spécifier la source. Par exemple, si vous souhaitez autoriser l'intégralité du sous-réseau <code>15.15.15.0/24</code>, vous devez exécuter la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">15.15.15.0/24</span> to any port 3306
</li></ul></code></pre>
<h3 id="autoriser-mysql-sur-l-39-interface-réseau-spécifique">Autoriser MySQL sur l'interface réseau spécifique</h3>

<p>Disons que vous disposez par exemple d'une interface réseau privée <code>eth1</code>, vous devez utiliser la commande suivante pour autoriser les connexions MySQL sur une interface réseau spécifique :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow in on <span class="highlight">eth1</span> to any port 3306
</li></ul></code></pre>
<h2 id="service -postgresql">Service : PostgreSQL</h2>

<p>PostgreSQL écoute les connexions des clients sur le port 5432. Si un client utilise votre serveur de base de données PostgreSQL sur un serveur distant, vous devez avoir la garantie de bien autoriser ce trafic.</p>

<h3 id="postgresql-à-partir-d-39-une-adresse-ip-ou-un-sous-réseau-spécifique">PostgreSQL à partir d'une adresse IP ou un sous-réseau spécifique</h3>

<p>Pour autoriser les connexions PostgreSQL entrantes à partir d'une adresse IP ou d'un sous-réseau spécifique, vous devez spécifier la source. Par exemple, si vous souhaitez autoriser l'intégralité du sous-réseau <code>15.15.15.0/24</code>, vous devez exécuter la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">15.15.15.0/24</span> to any port 5432
</li></ul></code></pre>
<p>La deuxième commande qui autorise le trafic sortant des connexions PostgreSQL <strong>established</strong>, n'est nécessaire que si la politique <code>OUTPUT</code> n'est pas défini sur <code>ACCEPT</code>.</p>

<h3 id="autoriser-postgresql-sur-une-interface-réseau-spécifique">Autoriser PostgreSQL sur une interface réseau spécifique</h3>

<p>Disons que vous disposez par exemple d'une interface réseau privée <code>eth1</code>, vous devez utiliser la commande suivante pour autoriser les connexions PostgreSQL sur une interface réseau spécifique :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow in on <span class="highlight">eth1</span> to any port 5432
</li></ul></code></pre>
<p>La deuxième commande qui autorise le trafic sortant des connexions PostgreSQL <strong>established</strong>, n'est nécessaire que si la politique <code>OUTPUT</code> n'est pas défini sur <code>ACCEPT</code>.</p>

<h2 id="service -mail">Service : Mail</h2>

<p>Les serveurs de courrier électronique, comme Sendmail et Postfix, écoutent sur une variété de ports en fonction des protocoles utilisés pour livrer les courriels. Si vous utilisez un serveur de messagerie, vous devez déterminer les protocoles que vous utilisez et autoriser les types de trafic applicables. Nous allons également vous montrer de quelle manière créer une règle pour bloquer les courriels SMTP sortants.</p>

<h3 id="bloquer-un-courriel-smtp-sortant">Bloquer un courriel SMTP sortant</h3>

<p>Si votre serveur ne doit pas envoyer de courriel sortant, il vous faudra bloquer ce type de trafic. Pour bloquer les courriels SMTP sortants, qui utilise le port 25, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw deny out 25
</li></ul></code></pre>
<p>Vous configurez votre pare-feu de sorte qu'il <strong>drop</strong> tout le trafic sortant sur le port 25. Si vous devez rejeter un autre service en fonction de son numéro de port, vous pouvez l'utiliser à la place du port 25, vous devez simplement le remplacer.</p>

<h3 id="autoriser-tous-les-smtp-entrants">Autoriser tous les SMTP entrants</h3>

<p>Pour permettre à votre serveur de répondre aux connexions SMTP, port 25, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 25
</li></ul></code></pre>
<p><span class='remarque'><strong>Remarque :</strong> il est courant que les serveurs SMTP utilisent le port 587 pour le courrier sortant.<br></span></p>

<h3 id="autoriser-tous-les-imap-entrants">Autoriser tous les IMAP entrants</h3>

<p>Pour permettre à votre serveur de répondre aux connexions IMAP, port 143, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 143
</li></ul></code></pre>
<h3 id="autoriser-tous-les-imaps-entrants">Autoriser tous les IMAPS entrants</h3>

<p>Pour permettre à votre serveur de répondre aux connexions IMAPS, port 993, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 993
</li></ul></code></pre>
<h3 id="autoriser-tous-les-pop3-entrants">Autoriser tous les POP3 entrants</h3>

<p>Pour permettre à votre serveur de répondre aux connexions POP3, port 110, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 110
</li></ul></code></pre>
<h3 id="autoriser-tous-les-pop3s-entrants">Autoriser tous les POP3S entrants</h3>

<p>Pour permettre à votre serveur de répondre aux connexions POP3S, port 995, exécutez la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow 995
</li></ul></code></pre>
<h2 id="conclusion">Conclusion</h2>

<p>Ainsi, vous devriez couvrir plusieurs des commandes que l'on utilise couramment pour configurer un pare-feu en utilisant UFW. Bien entendu, UFW est un outil très flexible. N'hésitez pas à combiner les commandes avec différentes options selon vos besoins spécifiques si elles ne sont pas couvertes ici.</p>

<p>Bonne chance !</p>
