---
title : "Comment configurer une authentification par clé SSH sur un serveur Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server-fr
image: "https://sergio.afanou.com/assets/images/image-midres-20.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>SSH, ou secure shell, est un protocole crypté utilisé pour administrer et communiquer avec des serveurs. Si vous travaillez avec un serveur Linux, il est fort probable que vous passiez la majeure partie de votre temps dans une session terminal connectée à votre serveur par SSH.</p>

<p>Bien qu'il existe plusieurs façons de se connecter à un serveur SSH, dans ce guide, nous allons essentiellement nous concentrer sur la configuration des clés SSH. Les clés SSH vous donnent un moyen facile et extrêmement sûr de vous connecter à votre serveur. Il s'agit donc de la méthode que nous recommandons à tous les utilisateurs.</p>

<h2 id="comment-fonctionnent-les-clés-ssh ">Comment fonctionnent les clés SSH ?</h2>

<p>Un serveur SSH utilise diverses méthodes pour authentifier des clients. La plus simple est l'authentification par mot de passe, qui, malgré sa simplicité d'utilisation, est loin d'être la plus sécurisée.</p>

<p>Bien que l'envoi des mots de passe au serveur se fasse de manière sécurisée, ces derniers ne sont généralement pas suffisamment complexes ou longs pour arrêter les attaquants assidus et insistants. La puissance de traitement moderne combinée aux scripts automatisés rendent tout à fait possible toute attaque par force brute d'un compte protégé par mot de passe. Bien qu'il existe d'autres méthodes d'ajouter davantage de sécurité (<code>fail2ban</code>, etc.), les clés SSH ont fait leur preuve en termes de fiabilité comme de sécurité.</p>

<p>Les paires de clés SSH sont deux clés chiffrées qui peuvent être utilisées pour authentifier un client sur un serveur SSH. Chaque paire de clés est composée d'une clé publique et d'une clé privée.</p>

<p>Le client doit conserver la clé privée qui doit rester absolument secrète. Si la clé privée venait à être compromise, tout attaquant pourrait alors se connecter aux serveurs configurés avec la clé publique associée sans authentification supplémentaire. Par mesure de précaution supplémentaire, la clé peut être cryptée sur le disque avec une phrase de passe.</p>

<p>La clé publique associée pourra être librement partagée sans aucun impact négatif. La clé publique servira à crypter les messages que <strong>seule</strong> la clé privée pourra déchiffrer. Cette propriété est utilisée comme un moyen de s'authentifier avec la paire de clés.</p>

<p>La clé publique est chargée sur un serveur distant auquel vous devez pouvoir vous connecter avec SSH. La clé est ajoutée à un fichier spécifique dans le compte utilisateur auquel vous allez vous connecter qui se nomme <code>~/.ssh/authorized_keys</code>.</p>

<p>Si un client tente de s'authentifier à l'aide de clés SSH, le serveur pourra demander au client s'il a bien la clé privée en sa possession. Une fois que le client pourra prouver qu'il possède bien la clé privée, une session shell est lancée ou la commande demandée est exécutée.</p>

<h2 id="comment-créer-des-clés-ssh">Comment créer des clés SSH</h2>

<p>Afin de configurer l'authentification avec des clés SSH sur votre serveur, la première étape consiste à générer une paire de clés SSH sur votre ordinateur local.</p>

<p>Pour ce faire, nous pouvons utiliser un utilitaire spécial appelé <code>ssh-keygen</code>, inclus dans la suite standard d'outils OpenSSH. Par défaut, cela créera une paire de clés RSA de 2048 bits, parfaite pour la plupart des utilisations.</p>

<p>Sur votre ordinateur local, générez une paire de clés SSH en saisissant ce qui suit : </p>
<pre class="code-pre "><code>ssh-keygen
</code></pre><pre class="code-pre "><code>Generating public/private rsa key pair.
Enter file in which to save the key (/home/<span class="highlight">username</span>/.ssh/id_rsa):
</code></pre>
<p>L'utilitaire vous demandera de sélectionner un emplacement pour les clés qui seront générées. Le système stockera les clés par défaut dans le répertoire <code>~/.ssh</code> du répertoire d'accueil de votre utilisateur. La clé privée se nommera <code>id_rsa</code> et la clé publique associée, <code>id_rsa.pub</code>.</p>

<p>En règle générale, à ce stade, il est préférable de conserver l'emplacement par défaut. Cela permettra à votre client SSH de trouver automatiquement vos clés SSH lorsqu'il voudra s'authentifier. Si vous souhaitez choisir un autre chemin, vous devez le saisir maintenant. Sinon, appuyez sur ENTER pour accepter l'emplacement par défaut.</p>

<p>Si vous avez précédemment généré une paire de clés SSH, vous verrez apparaître une invite similaire à la suivante :</p>
<pre class="code-pre "><code>/home/<span class="highlight">username</span>/.ssh/id_rsa already exists.
Overwrite (y/n)?
</code></pre>
<p>Si vous choisissez d'écraser la clé sur le disque, vous ne pourrez <strong>plus</strong> vous authentifier à l'aide de la clé précédente. Soyez très prudent lorsque vous sélectionnez « yes », car il s'agit d'un processus de suppression irréversible.</p>
<pre class="code-pre "><code>Created directory '/home/<span class="highlight">username</span>/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
</code></pre>
<p>Vous serez ensuite invité à saisir une phrase de passe pour la clé. Il s'agit d'une phrase de passe facultative qui peut servir à crypter le fichier de la clé privée sur le disque.</p>

<p>Il serait légitime de vous demander quels avantages pourrait avoir une clé SSH si vous devez tout de même saisir une phrase de passe. Voici quelques-uns des avantages :</p>

<ul>
<li>La clé SSH privée (la partie qui peut être protégée par une phrase de passe) n'est jamais exposée sur le réseau. La phrase de passe sert uniquement à décrypter la clé sur la machine locale. Cela signifie que toute attaque par force brute du réseau sera impossible avec une phrase de passe.</li>
<li>La clé privée est conservée dans un répertoire à accès restreint. Le client SSH ne pourra pas reconnaître des clés privées qui ne sont pas conservées dans des répertoires à accès restreint. La clé elle-même doit également être configurée avec des autorisations restreintes (lecture et écriture uniquement disponibles pour le propriétaire). Cela signifie que les autres utilisateurs du système ne peuvent pas vous espionner.</li>
<li>Tout attaquant souhaitant craquer la phrase de passe de la clé SSH privée devra préalablement avoir accès au système. Ce qui signifie qu'il aura déjà accès à votre compte utilisateur ou le compte root. Dans ce genre de situation, la phrase de passe peut empêcher l'attaquant de se connecter immédiatement à vos autres serveurs, en espérant que cela vous donne du temps pour créer et implémenter une nouvelle paire de clés SSH et supprimer l'accès de la clé compromise.</li>
</ul>

<p>Étant donné que la clé privée n'est jamais exposée au réseau et est protégée par des autorisations d'accès au fichier, ce fichier ne doit jamais être accessible à toute autre personne que vous (et le root user). La phrase de passe offre une couche de protection supplémentaire dans le cas où ces conditions seraient compromises.</p>

<p>L'ajout d'une phrase de passe est facultatif. Si vous en entrez une, vous devrez la saisir à chaque fois que vous utiliserez cette clé (à moins que vous utilisiez un logiciel d'agent SSH qui stocke la clé décryptée). Nous vous recommandons d'utiliser une phrase de passe. Cependant, si vous ne souhaitez pas définir une phrase de passe, il vous suffit d'appuyer sur ENTER pour contourner cette invite.</p>
<pre class="code-pre "><code>Your identification has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.
Your public key has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.pub.
The key fingerprint is:
a9:49:2e:2a:5e:33:3e:a9:de:4e:77:11:58:b6:90:26 <span class="highlight">username</span>@remote_host
The key's randomart image is:
+--[ RSA 2048]----+
|     ..o         |
|   E o= .        |
|    o. o         |
|        ..       |
|      ..S        |
|     o o.        |
|   =o.+.         |
|. =++..          |
|o=++.            |
+-----------------+
</code></pre>
<p>Vous disposez désormais d'une clé publique et privée que vous pouvez utiliser pour vous authentifier. L'étape suivante consiste à placer la clé publique sur votre serveur afin que vous puissiez utiliser l'authentification par clé SSH pour vous connecter.</p>

<h2 id="comment-intégrer-votre-clé-publique-lors-de-la-création-de-votre-serveur">Comment intégrer votre clé publique lors de la création de votre serveur</h2>

<p>Si vous démarrez un nouveau serveur DigitalOcean, vous pouvez automatiquement intégrer votre clé publique SSH dans le compte root de votre nouveau serveur.</p>

<p>En bas de la page de création de Droplet, une option vous permet d'ajouter des clés SSH à votre serveur :</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_options.png" alt="SSH key embed"></p>

<p>Si vous avez déjà ajouté un fichier de clé publique à votre compte DigitalOcean, vous la verrez apparaître comme une option sélectionnable (il y a deux clés existantes dans l'exemple ci-dessus : « Work key » et « Home key »). Pour intégrer une clé existante, cliquez dessus pour la mettre en surbrillance. Vous pouvez intégrer plusieurs clés sur un seul serveur :</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_selection.png" alt="SSH key selection"></p>

<p>Si vous n'avez pas encore chargé de clé SSH publique sur votre compte, ou si vous souhaitez ajouter une nouvelle clé à votre compte, cliquez sur le bouton « + Add SSH Key ». Cela créera une invite :</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_prompt.png" alt="SSH key prompt"></p>

<p>Dans la case « SSH Key content », collez le contenu de votre clé publique SSH. En supposant que vous ayez généré vos clés en utilisant la méthode ci-dessus, vous pouvez obtenir le contenu de votre clé publique sur votre ordinateur local en tapant :</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre><pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDNqqi1mHLnryb1FdbePrSZQdmXRZxGZbo0gTfglysq6KMNUNY2VhzmYN9JYW39yNtjhVxqfW6ewc+eHiL+IRRM1P5ecDAaL3V0ou6ecSurU+t9DR4114mzNJ5SqNxMgiJzbXdhR+j55GjfXdk0FyzxM3a5qpVcGZEXiAzGzhHytUV51+YGnuLGaZ37nebh3UlYC+KJev4MYIVww0tWmY+9GniRSQlgLLUQZ+FcBUjaqhwqVqsHe4F/woW1IHe7mfm63GXyBavVc+llrEzRbMO111MogZUcoWDI9w7UIm8ZOTnhJsk7jhJzG2GpSXZHmly/a/buFaaFnmfZ4MYPkgJD username@example.com
</code></pre>
<p>Collez cette valeur, dans son intégralité, dans la boîte plus grande. Dans la case « Comment (optional) », vous pouvez choisir une étiquette pour la clé. Elle apparaîtra sous le nom de clé dans l'interface DigitalOcean :</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/new_key.png" alt="SSH new key"></p>

<p>Lorsque vous créez votre Droplet, les clés SSH publiques que vous avez sélectionnées seront placées dans le fichier <code>~/.ssh/authorized_keys</code> du compte de l'utilisateur root. Cela vous permettra de vous connecter au serveur à partir de l'ordinateur qui intègre votre clé privée.</p>

<h2 id="comment-copier-une-clé-publique-sur-votre-serveur">Comment copier une clé publique sur votre serveur</h2>

<p>Si vous disposez déjà d'un serveur et que vous n'avez pas intégré de clés lors de la création, vous pouvez toujours charger votre clé publique et l'utiliser pour vous authentifier sur votre serveur.</p>

<p>La méthode que vous allez utiliser dépendra principalement des outils dont vous disposez et des détails de votre configuration actuelle. Vous obtiendrez le même résultat final avec toutes les méthodes suivantes. La première méthode est la plus simple et la plus automatisée. Celles qui suivent nécessitent chacune des manipulations supplémentaires si vous ne pouvez pas utiliser les méthodes précédentes.</p>

<h3 id="copier-votre-clé-publique-à-l-39-aide-de-ssh-copy-id">Copier votre clé publique à l'aide de SSH-Copy-ID</h3>

<p>La façon la plus simple de copier votre clé publique sur un serveur existant consiste à utiliser un utilitaire appelé <code>ssh-copy-id</code>. En raison de sa simplicité, nous vous recommandons d'utiliser cette méthode, si elle est disponible.</p>

<p>L'outil <code>ssh-copy-id</code> est inclus dans les paquets OpenSSH de nombreuses distributions. Vous pouvez donc en disposer sur votre système local. Pour que cette méthode fonctionne, vous devez déjà disposer d'un accès SSH à votre serveur, basé sur un mot de passe.</p>

<p>Pour utiliser l'utilitaire, il vous suffit de spécifier l'hôte distant auquel vous souhaitez vous connecter et le compte utilisateur auquel vous avez accès SSH par mot de passe. Il s'agit du compte sur lequel votre clé publique SSH sera copiée.</p>

<p>La syntaxe est la suivante :</p>
<pre class="code-pre "><code>ssh-copy-id <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Il se peut que vous voyez apparaître un message similaire à celui-ci :</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Cela signifie simplement que votre ordinateur local ne reconnaît pas l'hôte distant. Cela se produira la première fois que vous vous connecterez à un nouvel hôte. Tapez « yes » et appuyez sur ENTER (ENTRÉE) pour continuer.</p>

<p>Ensuite, l'utilitaire recherchera sur votre compte local la clé <code>id_rsa.pub</code> que nous avons créée précédemment. Lorsqu'il trouvera la clé, il vous demandera le mot de passe du compte de l'utilisateur distant :</p>
<pre class="code-pre "><code>/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
username@111.111.11.111's password:
</code></pre>
<p>Saisissez le mot de passe (votre saisie ne s'affichera pas pour des raisons de sécurité) et appuyez sur ENTER. L'utilitaire se connectera au compte sur l'hôte distant en utilisant le mot de passe que vous avez fourni. Il copiera ensuite le contenu de votre clé <code>~/.ssh/id_rsa.pub</code> dans un fichier situé dans le répertoire de base <code>~/.ssh</code> du compte distant appelé <code>authorized_keys</code>.</p>

<p>Vous obtiendrez un résultat similaire à ce qui suit :</p>
<pre class="code-pre "><code>Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'username@111.111.11.111'"
and check to make sure that only the key(s) you wanted were added.
</code></pre>
<p>À ce stade, votre clé <code>id_rsa.pub</code> a été téléchargée sur le compte distant. Vous pouvez passer à la section suivante.</p>

<h3 id="copier-votre-clé-publique-à-l-39-aide-de-ssh">Copier votre clé publique à l'aide de SSH</h3>

<p>Si vous ne disposez pas de <code>ssh-copy-id</code>, mais que vous avez un accès SSH par mot de passe à un compte sur votre serveur, vous pouvez télécharger vos clés en utilisant une méthode SSH classique.</p>

<p>Nous pouvons le faire en extrayant le contenu de notre clé SSH publique sur notre ordinateur local et en l'acheminant par une connexion SSH vers le serveur distant. D'autre part, nous pouvons nous assurer que le répertoire <code>~/.ssh</code> existe bien sous le compte que nous utilisons pour ensuite générer le contenu que nous avons transmis dans un fichier appelé <code>authorized_keys</code> dans ce répertoire.</p>

<p>Nous allons utiliser le symbole de redirection <code>&gt;&gt;</code> pour ajouter le contenu au lieu d'écraser le contenu précédent. Cela nous permettra d'ajouter des clés sans détruire les clés précédemment ajoutées.</p>

<p>La commande ressemblera à ceci :</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub | ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span> "mkdir -p ~/.ssh &amp;&amp; cat &gt;&gt; ~/.ssh/authorized_keys"
</code></pre>
<p>Il se peut que vous voyez apparaître un message similaire à celui-ci :</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Cela signifie simplement que votre ordinateur local ne reconnaît pas l'hôte distant. Cela se produira la première fois que vous vous connecterez à un nouvel hôte. Tapez « yes » et appuyez sur ENTER (ENTRÉE) pour continuer.</p>

<p>Ensuite, vous serez invité à saisir le mot de passe du compte auquel vous tentez de vous connecter :</p>
<pre class="code-pre "><code>username@111.111.11.111's password:
</code></pre>
<p>Après avoir saisi votre mot de passe, le contenu de votre clé <code>id_rsa.pub</code> sera copié à la fin du fichier <code>authorized_keys</code> du compte de l'utilisateur distant. Si cela fonctionne, passez à la section suivante.</p>

<h3 id="copier-manuellement-votre-clé-publique">Copier manuellement votre clé publique</h3>

<p>Si vous ne disposez pas d'un accès SSH à votre serveur protégé par un mot de passe, vous devrez suivre le processus décrit ci-dessus manuellement.</p>

<p>Le contenu de votre fichier <code>id_rsa.pub</code> devra être ajouté à un fichier qui se trouvera dans <code>~/.ssh/authorized_keys</code> sur votre machine distante.</p>

<p>Pour afficher le contenu de votre clé <code>id_rsa.pub</code>, tapez ceci dans votre ordinateur local :</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre>
<p>Vous verrez le contenu de la clé de manière similaire à ceci :</p>
<pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCqql6MzstZYh1TmWWv11q5O3pISj2ZFl9HgH1JLknLLx44+tXfJ7mIrKNxOOwxIxvcBF8PXSYvobFYEZjGIVCEAjrUzLiIxbyCoxVyle7Q+bqgZ8SeeM8wzytsY+dVGcBxF6N4JS+zVk5eMcV385gG3Y6ON3EG112n6d+SMXY0OEBIcO6x+PnUSGHrSgpBgX7Ks1r7xqFa7heJLLt2wWwkARptX7udSq05paBhcpB0pHtA1Rfz3K2B+ZVIpSDfki9UVKzT8JUmwW6NNzSgxUfQHGwnW7kj4jp4AT0VZk3ADw497M2G/12N0PPB5CnhHf7ovgy6nL1ikrygTKRFmNZISvAcywB9GVqNAVE+ZHDSCuURNsAInVzgYo9xgJDW8wUw2o8U77+xiFxgI5QSZX3Iq7YLMgeksaO4rBJEa54k8m5wEiEE1nUhLuJ0X/vh2xPff6SQ1BL/zkOhvJCACK6Vb15mDOeCSq54Cr7kvS46itMosi/uS66+PujOO+xt/2FWYepz6ZlN70bRly57Q06J+ZJoc9FfBCbCyYH7U/ASsmY095ywPsBo1XQ9PqhnN1/YOorJ068foQDNVpm146mUpILVxmq41Cj55YKHEazXGsdBIbXWhcrRf4G2fJLRcGUr9q8/lERo9oxRm5JFX6TCmj6kmiFqv+Ow9gI0x8GvaQ== demo@test
</code></pre>
<p>Accédez à votre hôte distant en utilisant la méthode dont vous disposez. Par exemple, si votre serveur est un Droplet DigitalOcean, vous pouvez vous connecter à l'aide de la console Web disponible dans le panneau de configuration :</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/console_access.png" alt="Accès à la console DigitalOcean"></p>

<p>Une fois que vous avez accès à votre compte sur le serveur distant, vous devez vous assurer que le répertoire <code>~/.ssh</code> est créé. Cette commande va créer le répertoire si nécessaire, ou ne rien faire s'il existe déjà :</p>
<pre class="code-pre "><code>mkdir -p ~/.ssh
</code></pre>
<p>Maintenant, vous pouvez créer ou modifier le fichier <code>authorized_keys</code> dans ce répertoire. Vous pouvez ajouter le contenu de votre fichier <code>id_rsa.pub</code> à la fin du fichier <code>authorized_keys</code>, en le créant si nécessaire, à l'aide de la commande suivante :</p>
<pre class="code-pre "><code>echo <span class="highlight">public_key_string</span> &gt;&gt; ~/.ssh/authorized_keys
</code></pre>
<p>Dans la commande ci-dessus, remplacez la chaîne <code><span class="highlight">public_key_string</span></code> par la sortie de la commande <code>cat ~/.ssh/id_rsa.pub</code> que vous avez exécutée sur votre système local. Elle devrait commencer par <code>ssh-rsa AAAA...</code>.</p>

<p>Si cela fonctionne, vous pouvez passer à l'authentification sans mot de passe.</p>

<h2 id="authentification-sur-votre-serveur-à-l-39-aide-des-clés-ssh">Authentification sur votre serveur à l'aide des clés SSH</h2>

<p>Si vous avez effectué avec succès l'une des procédures ci-dessus, vous devriez pouvoir vous connecter à l'hôte distant <em>sans</em> le mot de passe du compte distant.</p>

<p>Le processus de base est le même :</p>
<pre class="code-pre "><code>ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Si c'est la première fois que vous vous connectez à cet hôte (si vous avez utilisé la dernière méthode ci-dessus), vous verrez peut-être quelque chose comme ceci :</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Cela signifie simplement que votre ordinateur local ne reconnaît pas l'hôte distant. Tapez « yes » et appuyez sur ENTER pour continuer.</p>

<p>Si vous n'avez pas fourni de phrase de passe pour votre clé privée, vous serez immédiatement connecté. Si vous avez configuré une phrase de passe pour la clé privée au moment de la création de la clé, vous devrez la saisir maintenant. Ensuite, une nouvelle session shell devrait être générée pour vous avec le compte sur le système distant.</p>

<p>Si cela fonctionne, continuer pour savoir comment verrouiller le serveur.</p>

<h2 id="désactiver-l-39-authentification-par-mot-de-passe-sur-votre-serveur">Désactiver l'authentification par mot de passe sur votre serveur</h2>

<p>Si vous avez pu vous connecter à votre compte en utilisant SSH sans mot de passe, vous avez réussi à configurer une authentification basée sur des clés SSH pour votre compte. Cependant, votre mécanisme d'authentification par mot de passe est toujours actif, ce qui signifie que votre serveur est toujours exposé aux attaques par force brute.</p>

<p>Avant de procéder aux étapes décrites dans cette section, vérifiez que vous avez bien configuré une authentification par clé SSH pour le compte root sur ce serveur, ou de préférence, que vous avez bien configuré une authentification par clé SSH pour un compte sur ce serveur avec un accès <code>sudo</code>. Cette étape permettra de verrouiller les connexions par mot de passe. Il est donc essentiel de s'assurer que vous pourrez toujours obtenir un accès administratif.</p>

<p>Une fois les conditions ci-dessus satisfaites, connectez-vous à votre serveur distant avec les clés SSH, soit en tant que root, soit avec un compte avec des privilèges <code>sudo</code>. Ouvrez le fichier de configuration du démon SSH :</p>
<pre class="code-pre "><code>sudo nano /etc/ssh/sshd_config
</code></pre>
<p>Dans le fichier, recherchez une directive appelée <code>PasswordAuthentication</code>. Elle est peut-être commentée. Décommentez la ligne et réglez la valeur sur « no ». Cela désactivera votre capacité à vous connecter avec SSH en utilisant des mots de passe de compte :</p>
<pre class="code-pre "><code>PasswordAuthentication no
</code></pre>
<p>Enregistrez et fermez le fichier lorsque vous avez terminé. Pour implémenter effectivement les modifications que vous venez d'apporter, vous devez redémarrer le service.</p>

<p>Sur les machines Ubuntu ou Debian, vous pouvez lancer la commande suivante :</p>
<pre class="code-pre "><code>sudo service ssh restart
</code></pre>
<p>Sur les machines CentOS/Fedora, le démon s'appelle <code>sshd</code> :</p>
<pre class="code-pre "><code>sudo service sshd restart
</code></pre>
<p>Une fois cette étape terminée, vous avez réussi à transiter votre démon SSH de manière à ce qu'il réponde uniquement aux clés SSH.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Vous devriez maintenant avoir une authentification basée sur une clé SSH configurée et active sur votre serveur, vous permettant de vous connecter sans fournir de mot de passe de compte. À partir de là, vos options sont multiples. Si vous souhaitez en savoir plus sur SSH, consultez notre <a href="https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys">Guide des fondamentaux SSH</a>.</p>
