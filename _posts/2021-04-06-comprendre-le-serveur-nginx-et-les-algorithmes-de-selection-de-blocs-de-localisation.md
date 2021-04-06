---
title : "Comprendre le serveur Nginx et les algorithmes de sélection de blocs de localisation"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms-fr
image: "https://sergio.afanou.com/assets/images/image-midres-19.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Nginx fait partie des serveurs Web les plus populaires au monde. C'est avec succès qu'il peut gérer de grandes charges avec plusieurs connexions clients concurrentes et être facilement utilisé comme un serveur web, un serveur de courrier ou un serveur proxy inverse.</p>

<p>Dans ce guide, nous allons traiter de quelques-uns des détails en coulisses qui déterminent la manière dont Nginx traite les requêtes clients. En ayant une compréhension de ces idées, vous n'aurez plus à faire autant de devinettes quant à la conception du serveur et des blocs de localisation et vous serez alors en mesure de rendre la manipulation des requêtes moins imprévisible.</p>

<h2 id="configurations-de-bloc-nginx">Configurations de bloc Nginx</h2>

<p>Nginx divise de manière logique les configurations destinées à servir différents contenus dans des blocs qui se trouvent dans une structure hiérarchique. Chaque fois qu'un client fait une requête, Nginx entame un processus pour sélectionner les blocs de configuration qui seront utilisés pour gérer la requête. C'est ce processus de décision que nous allons aborder dans ce guide.</p>

<p>Les principaux blocs que nous allons aborder sont les suivants : le bloc <strong>server</strong> et le bloc <strong>location</strong>.</p>

<p>Un bloc de serveur est un sous-ensemble de la configuration de Nginx qui définit le serveur virtuel avec lequel les requêtes d'un type donné seront gérées. Les administrateurs configurent souvent plusieurs blocs de serveur et décident ensuite du bloc qui sera chargé de la connexion en fonction du nom du domaine, du port et de l'adresse IP demandés.</p>

<p>Un bloc de localisation se trouve dans un bloc de serveur. Il permet de définir la façon dont Nginx doit gérer les requêtes pour différentes ressources et URI du serveur parent. L'administrateur peut subdiviser l'espace URI de toutes les manières qu'il le souhaite en utilisant ces blocs. Il s'agit d'un modèle extrêmement flexible.</p>

<h2 id="comment-nginx-décide-du-bloc-de-serveur-qui-gérera-la-requête">Comment Nginx décide du bloc de serveur qui gérera la requête</h2>

<p>Étant donné que Nginx permet à l'administrateur de définir plusieurs blocs de serveur qui fonctionnent comme des instances de serveur virtuel distinctes, il lui faut une procédure qui lui permette de déterminer lesquels de ces blocs utiliser pour satisfaire une requête.</p>

<p>Pour cela, il passe par un système de vérifications utilisées pour trouver la meilleure correspondance possible. Les directives de bloc de serveur principal que Nginx prend en considération pendant ce processus sont les directives <code>listen</code> et <code>server_name</code>.</p>

<h3 id="analyser-la-directive-« listen »-pour-trouver-des-correspondances-possibles">Analyser la directive « listen » pour trouver des correspondances possibles</h3>

<p>Tout d'abord, Nginx examine l'adresse IP et le port de la requête. Il les fait ensuite correspondre avec la directive <code>listen</code> de chaque serveur pour créer une liste des blocs du serveur qui peuvent éventuellement résoudre la requête.</p>

<p>La directive <code>listen</code> définit généralement l'adresse IP et le port auxquels le bloc de serveur répondra. Par défaut, tout bloc de serveur qui n'inclut pas une directive <code>listen</code> se voit attribuer les paramètres d'écoute de <code>0.0.0.0:80</code> (ou <code>0.0.0.0:8080</code> si Nginx est exécuté par un non-root user normal). Ces blocs peuvent ainsi répondre aux requêtes sur toute interface sur le port 80. Mais cette valeur par défaut n'a pas beaucoup de poids dans le processus de sélection de serveur.</p>

<p>La directive <code>listen</code> peut être configurée sur :</p>

<ul>
<li>Un combo adresse IP/port.</li>
<li>Une adresse IP unique qui écoutera ensuite le port 80 par défaut.</li>
<li>Un port unique qui écoutera chaque interface sur ce port.</li>
<li>Le chemin vers une socket Unix.</li>
</ul>

<p>La dernière option aura généralement des implications lorsque les requêtes passeront entre les différents serveurs.</p>

<p>Lorsque Nginx tentera de déterminer le bloc de serveur qui enverra une requête, il tentera d'abord de décider en fonction de la spécificité de la directive <code>listen</code> en utilisant les règles suivantes :</p>

<ul>
<li>Nginx traduit toutes les directives <code>listen</code> « incomplètes » en substituant les valeurs manquantes par leurs valeurs par défaut afin que chaque bloc puisse être évalué par son adresse IP et son port. Voici quelques exemples de ces traductions :

<ul>
<li>Un bloc sans directive <code>listen</code> utilise la valeur <code>0.0.0.0:80</code>.</li>
<li>Un bloc configuré sur une adresse IP <code>111.111.111.111</code> sans aucun port devient <code>111.111.111.111:80</code></li>
<li>Un bloc configuré sur le port <code>8888</code> sans adresse IP devient <code>0.0.0.0:8888</code></li>
</ul></li>
<li>Nginx tente ensuite de recueillir une liste des blocs du serveur qui correspondent à la requête de manière plus spécifique en fonction de l'adresse IP et du port. Cela signifie que tout bloc qui utilise fonctionnellement son adresse IP <code>0.0.0.0</code> (pour correspondre à toute interface) ne sera pas sélectionné s'il existe des blocs correspondants qui répertorient une adresse IP spécifique. Dans tous les cas, la correspondance avec le port doit être exacte.</li>
<li>S'il n'existe qu'une correspondance plus spécifique, la requête sera servie par ce bloc de serveur. En présence de plusieurs blocs de serveur avec le même niveau de spécificité, Nginx commence ensuite à évaluer la directive <code>server_name</code> de chaque bloc de serveur.</li>
</ul>

<p>Il est important de comprendre que Nginx évaluera la directive <code>server_name</code> uniquement au moment où il devra faire la distinction entre les blocs de serveur qui correspondent au même niveau de spécificité dans la directive <code>listen</code>. Par exemple, si <code>example.com</code> est hébergé sur le port <code>80</code> de <code>192.168.1.10</code>, le premier bloc servira toujours la requête <code>example.com</code> de cet exemple, malgré la présence de la directive <code>server_name</code> dans le second bloc.</p>
<pre class="code-pre "><code>server {
    listen 192.168.1.10;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}
</code></pre>
<p>Dans le cas où plusieurs blocs de serveur correspondent à une spécificité égale, l'étape suivante consistera à vérifier la directive <code>server_name</code>.</p>

<h3 id="analyser-la-directive-« server_name »-pour-choisir-une-correspondance">Analyser la directive « server_name » pour choisir une correspondance</h3>

<p>Ensuite, pour évaluer les requêtes qui disposent de directives <code>listen</code> également spécifiques, Nginx vérifie l'en-tête « Host » de la requête. Cette valeur contient le domaine ou l'adresse IP que le client a réellement tenté d'atteindre.</p>

<p>Nginx tente de trouver la meilleure correspondance pour la valeur qu'il trouve en examinant la directive <code>server_name</code> dans chacun des blocs de serveur toujours sélectionnés. Nginx les évalue en utilisant la formule suivante :</p>

<ul>
<li>Nginx tentera d'abord de trouver un bloc de serveur avec un <code>server_name</code> qui correspond à la valeur dans l'en-tête « Host » de la requête <em>exactly</em>. S'il la trouve, la requête sera servie par le bloc associé. S'il trouve plusieurs correspondances exactes, la <strong>première</strong> est utilisée.</li>
<li>S'il ne trouve aucune correspondance exacte, Nginx tentera ensuite de trouver un bloc de serveur avec un <code>server_name</code> correspondant à l'aide d'un métacaractère principal (indiqué par un <code>*</code> au début du nom dans la config). S'il en trouve un, la requête sera servie par ce bloc. S'il trouve plusieurs correspondances, la requête sera servie par la correspondance <strong>longest</strong>.</li>
<li>S'il ne trouve aucune correspondance en utilisant un métacaractère principal, Nginx recherchera ensuite un bloc de serveur avec un <code>server_name</code> correspondant à l'aide d'un métacaractère secondaire (indiqué par un nom de serveur qui se termine par un <code>*</code> dans la config). S'il en trouve un, la requête sera servie par ce bloc. S'il trouve plusieurs correspondances, la requête sera servie par la correspondance <strong>longest</strong>.</li>
<li>S'il ne trouve aucune correspondance en utilisant un métacaractère secondaire, Nginx évalue ensuite les blocs de serveur qui définissent le <code>server_name</code> en utilisant des expressions régulières (indiquées par un <code>~</code> avant le nom). Le <strong>first</strong> <code>server_name</code> avec une expression régulière qui correspond à l'en-tête « Host » sera utilisé pour servir la requête.</li>
<li>S'il ne trouve aucune correspondance d'expression régulière, Nginx sélectionne alors le bloc de serveur par défaut pour l'adresse IP et le port en question.</li>
</ul>

<p>Chaque combo adresse IP/port est doté d'un bloc de serveur par défaut qui sera utilisé lorsqu'aucun cours d'action ne pourra pas être déterminé avec les méthodes ci-dessus. Pour un combo adresse IP/port, il s'agira soit du premier bloc de la configuration ou du bloc qui contient l'option <code>default_server</code> dans la directive <code>listen</code> (qui remplacera le premier algorithme trouvé). Il ne peut y avoir qu'une seule déclaration <code>default_server</code> pour chaque combinaison adresse IP/port.</p>

<h3 id="exemples">Exemples</h3>

<p>S'il existe un <code>server_name</code> défini qui correspond exactement à la valeur de l'en-tête « Host », ce bloc de serveur sera sélectionné pour traiter la requête.</p>

<p>Dans cet exemple, si l'en-tête « Host » de la requête est configuré sur « host1.example.com », c'est le second serveur qui sera sélectionné :</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name *.example.com;

    . . .

}

server {
    listen 80;
    server_name host1.example.com;

    . . .

}
</code></pre>
<p>S'il ne trouve aucune correspondance exacte, Nginx vérifie alors s'il existe un <code>server_name</code> qui commence avec un métacaractère qui convient. Ce sera la correspondance la plus longue et qui commence par un métacaractère qui sera sélectionnée pour répondre à la requête.</p>

<p>Dans cet exemple, si l'en-tête de la requête indique « Host » pour « www.example.org », c'est le second bloc de serveur qui sera sélectionné :</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name www.example.*;

    . . .

}

server {
    listen 80;
    server_name *.example.org;

    . . .

}

server {
    listen 80;
    server_name *.org;

    . . .

}
</code></pre>
<p>S'il ne trouve aucune correspondance qui commence par un métacaractère, Nginx verra alors s'il existe une correspondance en utilisant un métacaractère à la fin de l'expression. À ce stade, la requête sera servie par la correspondance la plus longue et qui contient un métacaractère.</p>

<p>Par exemple, si l'en-tête de la requête est configuré sur « www.example.com », ce sera le troisième bloc de serveur qui sera sélectionné :</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name host1.example.com;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name www.example.*;

    . . .

}
</code></pre>
<p>S'il ne trouve aucune correspondance avec un métacaractère, Nginx tentera alors de faire correspondre les directives <code>server_name</code> qui utilisent des expressions régulières. C'est la <em>first</em> expression régulière correspondante qui sera sélectionnée pour répondre à la requête.</p>

<p>Par exemple, si l'en-tête « Host » de la requête est configuré sur « www.example.com », c'est alors le second serveur qui sera sélectionné pour satisfaire la requête :</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name ~^(www|host1).*\.example\.com$;

    . . .

}

server {
    listen 80;
    server_name ~^(subdomain|set|www|host1).*\.example\.com$;

    . . .

}
</code></pre>
<p>Si aucune des étapes ci-dessus ne permet de satisfaire la requête, la requête sera alors transmise au serveur par <em>default</em> de l'adresse IP et le port correspondants.</p>

<h2 id="mise-en-correspondance-des-blocs-de-localisation">Mise en correspondance des blocs de localisation</h2>

<p>Tout comme le processus que Nginx utilise pour sélectionner le bloc de serveur qui traitera une requête, Nginx dispose également d'un algorithme établi pour décider du bloc de localisation du serveur qui servira au traitement des requêtes.</p>

<h3 id="syntaxe-de-bloc-de-localisation">Syntaxe de bloc de localisation</h3>

<p>Avant de voir de quelle manière Nginx procède pour décider du bloc de localisation qui sera utilisé pour traiter les requêtes, étudions un peu la syntaxe que vous êtes susceptible de rencontrer dans les définitions de bloc de localisation. Les blocs de localisation se trouvent dans les blocs serveur (ou autresblocs de localisation) et servent à décider de quelle manière traité l'URl de la requête (la partie de la demande qui se trouve après le nom du domaine ou l'adresse IP/port).</p>

<p>Les blocs de localisation ont généralement cette forme :</p>
<pre class="code-pre "><code>location <span class="highlight">optional_modifier</span> <span class="highlight">location_match</span> {

    . . .

}
</code></pre>
<p>Le <code><span class="highlight">location_match</span></code> ci-dessus définit avec quel élément Nginx devrait comparer l'URl de la requête. Dans l'exemple ci-dessus, l'existence ou l'absence du modificateur affecte la façon dont Nginx tente de faire correspondre le bloc de localisation. Les modificateurs donnés ci-dessous entraîneront l'interprétation suivante du bloc de localisation associé :</p>

<ul>
<li><strong>(none)</strong> : si aucun modificateur n'est présent, l'emplacement est interprété comme une correspondance de <em>prefix</em>. Cela signifie que la localisation donnée sera comparée avec le début de l'URI de la requête pour voir s'il y a une correspondance.</li>
<li><strong><code>=</code></strong> : si le signe égal est utilisé, ce bloc sera considéré comme une correspondance si l'URI de la requête correspond exactement à la localisation indiquée.</li>
<li><strong><code>~</code></strong> : la présence d'un modificateur tilde indique que cet emplacement sera interprété comme une correspondance d'expression régulière sensible à la casse.</li>
<li><strong><code>~*</code></strong> : si un modificateur tilde et astérisque est utilisé, le bloc de localisation sera interprété comme une correspondance d'expression régulière insensible à la casse.</li>
<li><strong><code>^~</code></strong> : si un modificateur de carat et tilde est présent et que ce bloc est sélectionné comme la meilleure correspondance d'expression non régulière, la mise en correspondance des expressions régulières n'aura pas lieu.</li>
</ul>

<h3 id="exemples-de-la-syntaxe-d-39-un-bloc-de-localisation">Exemples de la syntaxe d'un bloc de localisation</h3>

<p>Pour vous donner un exemple de correspondance de préfixe, vous pouvez sélectionner le bloc de localisation suivant pour répondre aux URl de requête qui ressemblent à <code>/site</code>, <code>/site/page1/index.html</code> ou <code>/site/index.html</code>:</p>
<pre class="code-pre "><code>location /site {

    . . .

}
</code></pre>
<p>Pour démontrer une correspondance exacte de l'URI, ce bloc sera toujours utilisé pour répondre à une URI de la requête qui ressemble à <code>/page1</code>. Il ne sera <strong>pas</strong> utilisé pour répondre à une URI de requête <code>/page1/index.html</code>. N'oubliez pas que, si ce bloc est sélectionné et que la requête est renseignée à l'aide d'une page d'index, le système procédera à une redirection interne vers un autre emplacement qui correspondra au gestionnaire réel de la requête :</p>
<pre class="code-pre "><code>location = /page1 {

    . . .

}
</code></pre>
<p>Pour vous donner un exemple du type de localisation qui doit être interprétée comme une expression régulière sensible à la casse, vous pouvez utiliser ce bloc pour gérer les requêtes de <code>/tortoise.jpg</code>, mais <strong>pas</strong> pour <code>/FLOWER.PNG</code> :</p>
<pre class="code-pre "><code>location ~ \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Voici un bloc qui permettrait de faire une mise en correspondance insensible à la casse similaire à ce qui précède : Ici, ce bloc peut gérer <code>/tortoise.jpg</code> <em>et</em> <code>/FLOWER.PNG</code> à la fois :</p>
<pre class="code-pre "><code>location ~* \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Enfin, ce bloc empêchera l'affichage de la correspondance avec l'expression régulière, s'il est configuré de manière à être mis en correspondance avec la meilleure expression non régulière. Il peut gérer les requêtes de <code>/costumes/ninja.html</code> :</p>
<pre class="code-pre "><code>location ^~ /costumes {

    . . .

}
</code></pre>
<p>Comme vous le voyez, les modificateurs indiquent de quelle manière le bloc de localisation doit être interprété. Cependant, cela <em>ne nous indique pas</em> l'algorithme que Nginx utilise pour décider du bloc de localisation qui enverra la requête. C'est ce que nous allons voir maintenant.</p>

<h3 id="comment-nginx-choisit-la-localisation-qui-gérera-les-requêtes">Comment Nginx choisit la localisation qui gérera les requêtes</h3>

<p>Nginx choisit la localisation qui sera utilisée pour servir une requête de manière similaire à la façon dont il sélectionne un bloc de serveur. Il passe par un processus qui détermine le meilleur bloc de localisation pour toute requête donnée. Il est vraiment crucial d'avoir une bonne compréhension de ce processus afin de pouvoir configurer Nginx de manière fiable et précise.</p>

<p>Tout en gardant à l'esprit les types de déclarations de localisation que nous avons décrites ci-dessus, Nginx évalue les contextes de localisation possibles en comparant l'URI de la requête avec chacun des emplacements. Pour cela, il utilise l'algorithme suivant :</p>

<ul>
<li>Nginx commence par vérifier tous les correspondances de localisation sur la base des préfixes (tous les types de localisation qui n'impliquent pas une expression régulière). Il vérifie chaque localisation en fonction de l'URI complet de la requête.</li>
<li>Nginx recherche tout d'abord une correspondance exacte. S'il trouve qu'un bloc de localisation qui utilise le modificateur <code>=</code> correspond exactement à l'URI de la requête, ce bloc de localisation est immédiatement sélectionné pour servir la requête.</li>
<li>S'il ne trouve aucun bloc de localisation (avec le modificateur <code>=</code>) qui corresponde exactement, Nginx passe alors à l'évaluation des préfixes inexacts. Il trouve la localisation préfixée la plus longue qui correspond à l'URI de la requête donnée, qu'il évalue ensuite de la manière suivante :

<ul>
<li>Si la localisation préfixée la plus longue correspondante contient le modificateur <code>^~</code>, Nginx arrête immédiatement sa recherche et sélectionnera cette localisation pour servir la requête.</li>
<li>Si la localisation préfixée la plus longue correspondante <em>n'utilise pas</em> le modificateur <code>^~</code>, Nginx enregistre la correspondance pour le moment afin que le focus de la recherche puisse être déplacé.</li>
</ul></li>
<li>Après avoir déterminé et stocké la localisation préfixée la plus longue correspondante, Nginx passe à l'évaluation des localisations d'expressions régulières (à la fois sensible et insensible à la casse). S'il existe des localisations d'expression régulière <em>à l'intérieur</em> de la localisation préfixée la plus longue correspondante, Nginx les déplacera en haut de la liste des localisations de regex pour procéder à une vérification. Nginx tente ensuite de faire une correspondance avec les localisations d'expression régulières de manière séquentielle. La <strong>première</strong> localisation d'expressions régulières qui correspond à l'URI de la requête est immédiatement sélectionnée pour servir la requête.</li>
<li>S'il ne trouve aucune localisation d'expressions régulières qui corresponde à l'URI de la requête, la localisation préfixée précédemment stockée sera alors sélectionnée pour servir la requête.</li>
</ul>

<p>Il est important de comprendre que, par défaut, Nginx préférera servir des correspondances d'expression régulière que des correspondances de préfixe. Cependant, il <em>évalue</em> d'abord les localisations préfixées pour que l'administration puisse écraser cette tendance en spécifiant les localisations avec les modificateurs <code>=</code> et <code>^~</code>.</p>

<p>Il est également important de noter que bien que les localisations préfixées sont généralement sélectionnées en fonction de la correspondance la plus spécifique et la plus longue, l'évaluation des expressions régulières s'arrête une fois que la première localisation correspondante est trouvée. Cela signifie que, dans la configuration, le positionnement a un grand impact sur les localisations d'expressions régulières.</p>

<p>Pour finir, il est important de comprendre que les correspondances d'expressions régulières <em>dans</em> la correspondance de préfixe la plus longue « sauteront la ligne » lorsque Nginx procédera à l'évaluation des localisations de regex. Il procédera à l'évaluation de ces éléments, dans l'ordre, avant même qu'une des autres correspondances d'expression régulière ne soit prise en considération. Dans <a href="https://www.ruby-forum.com/topic/4422812#1136698">cet article</a>, Maxim Dounin, un développeur Nginx incroyablement enrichissant, nous explique cette partie de l'algorithme de sélection.</p>

<h3 id="À-quel-moment-l-39-évaluation-des-blocs-de-localisation-passe-t-elle-à-d-39-autres-localisations ">À quel moment l'évaluation des blocs de localisation passe-t-elle à d'autres localisations ?</h3>

<p>En règle générale, lorsqu'un bloc de localisation est sélectionné pour servir une requête, l'intégralité de la requête est traitée dans ce même contexte à partir de ce moment-là. Seules la localisation sélectionnée et les directives héritées déterminent de quelle manière la requête est traitée, sans que les blocs de localisation apparentés ne viennent interférer.</p>

<p>Bien qu'il s'agisse d'une règle générale qui vous permettra de concevoir vos blocs de localisation de manière prévisible, il est important que vous sachiez que, parfois, certaines directives dans la localisation sélectionnée déclenchent une nouvelle recherche de localisation. Les exceptions à la règle « seulement un bloc de localisation » peuvent avoir un impact sur la façon dont la requête est effectivement servie et ne seront pas en accord avec les attentes que vous aviez lors de la conception de vos blocs de localisation.</p>

<p>Voici quelques-unes des directives qui peuvent conduire à ce type de redirection interne :</p>

<ul>
<li><strong>index</strong></li>
<li><strong>try_files</strong></li>
<li><strong>rewrite</strong></li>
<li><strong>error_page</strong></li>
</ul>

<p>Examinons-les brièvement.</p>

<p>La directive <code>index</code> entraîne toujours une redirection interne si elle est utilisée pour gérer la requête. Les correspondances de localisation exactes permettent souvent d’accélérer le processus de sélection en mettant immédiatement fin à l'exécution de l'algorithme. Cependant, si vous effectuez une correspondance de localisation exacte qui est un <em>répertoire</em>, il est possible que la requête soit redirigée vers un autre emplacement pour le traitement en tant que tel.</p>

<p>Dans cet exemple, la mise en correspondance de la première localisation se fait par un URI de requête <code>/exact</code>, mais pour gérer la requête, la directive <code>index</code> héritée du bloc initialise une redirection interne vers le second bloc :</p>
<pre class="code-pre "><code>index index.html;

location = /exact {

    . . .

}

location / {

    . . .

}
</code></pre>
<p>Dans le cas précédent, si vous souhaitez vraiment que l'exécution reste dans le premier bloc, il vous faudra trouver un moyen différent de satisfaire la requête au répertoire. Par exemple, vous pouvez configurer un <code>index</code> non valide pour ce bloc et activer <code>autoindex</code> :</p>
<pre class="code-pre "><code>location = /exact {
    index nothing_will_match;
    autoindex on;
}

location  / {

    . . .

}
</code></pre>
<p>Il s'agit d'un moyen d'empêcher un <code>index</code> de changer de contexte, mais il n'est probablement pas si pratique pour la plupart des configurations. Une correspondance exacte sur les répertoires vous permet la plupart du temps de réécrire la requête par exemple (ce qui peut également générer une nouvelle recherche de localisation).</p>

<p>Il existe également une autre instance dans laquelle la localisation de traitement peut être réévaluée, avec la directive <code>try_files</code> Cette directive indique à Nginx de vérifier s'il existe un ensemble de fichiers ou de répertoires nommés. Le dernier paramètre peut être un URI vers lequel Nginx procédera à une redirection interne.</p>

<p>Prenons le cas de la configuration suivante :</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>Dans l'exemple ci-dessus, si une requête est exécutée pour <code>/blahblah</code>, la première localisation obtiendra initialement la requête. Il tentera de trouver un fichier nommé <code>blahblah</code> dans le répertoire <code>/var/www/main</code>. S'il ne peut en trouver, il redirigera sa recherche sur un fichier nommé <code>blahblah.html</code>. Il tentera ensuite de voir s'il existe un répertoire appelé <code>blahblah/</code> dans le répertoire <code>/var/www/main</code>. Si toutes ces tentatives son un échec, il se redirigera vers <code>/fallback/index.html</code>. Cela déclenchera une autre recherche de localisation que le deuxième bloc de localisation détectera. Cela servira le fichier <code>/var/www/another/fallback/index.html</code>.</p>

<p>La directive <code>rewrite</code> est également une des autres directives qui permettent de passer un bloc de localisation. Lorsque Nginx utilise le paramètre <code>last</code> avec la directive <code>rewrite</code>, ou n'utilise aucun paramètre du tout, il recherchera une nouvelle localisation correspondante en fonction des résultats de la réécriture.</p>

<p>Par exemple, si nous modifions le dernier exemple pour y inclure une réécriture, nous pouvons voir que la requête est parfois transmise directement à la seconde localisation sans s'appuyer sur la directive <code>try_files</code> :</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    rewrite ^/rewriteme/(.*)$ /$1 last;
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>Dans l'exemple ci-dessus, une requête <code>/rewriteme/hello</code> sera initialement gérée par le premier bloc de localisation. Elle sera réécrite <code>/hello</code>, puis la recherche de la localisation sera déclenchée. Dans ce cas, elle correspondra à nouveau à la première localisation et sera traité par le <code>try_files</code> comme d'habitude, peut même redirigé sur <code>/fallback/index.html</code> si la recherche est infructueuse (en utilisant la redirection interne <code>try_files</code> discutée plus haut).</p>

<p>Cependant, si une requête <code>/rewriteme/fallback/hello</code> est effectuée, le premier bloc sera à nouveau une correspondance. La réécriture est à nouveau appliquée, ce qui génère cette fois-ci <code>/fallback/hello</code>. La requête sera ensuite servie par le second bloc de localisation.</p>

<p>La situation est connexe avec la directive <code>return</code> lorsque vous envoyez les codes de statut <code>301</code> ou <code>302</code>. La différence ici est que la requête obtenue et une requête entièrement nouvelle qui prend la forme d'une redirection visible à l'extérieur. Cette même situation peut se produire avec la directive <code>rewrite</code> si vous utilisez les balises <code>redirect</code> ou <code>permanent</code>. Cependant, ces recherches de localisation ne devraient pas être fortuites, car les redirections visibles en externe entraînent toujours une nouvelle requête.</p>

<p>La directive <code>error_page</code> peut générer une redirection interne similaire à celle créée par <code>try_files</code>. Cette directive permet de définir ce qui devrait se passer si le système rencontre certains codes de statut. Cela ne sera probablement jamais exécuté si <code>try_files</code> est configuré. En effet, cette directive gère l'intégralité du cycle de vie d'une requête.</p>

<p>Prenons l'exemple suivant :</p>
<pre class="code-pre "><code>root /var/www/main;

location / {
    error_page 404 /another/whoops.html;
}

location /another {
    root /var/www;
}
</code></pre>
<p>Chaque requête (autres que celles qui commencent par <code>/another</code>) sera traitée par le premier bloc, qui servira les fichiers <code>/var/www/main</code>. Cependant, si un fichier est introuvable (un statut 404), vous verrez se produire une redirection interne vers <code>/another/whoops.html</code>, générant une nouvelle recherche de localisation qui attérira éventuellement sur le second bloc. Ce fichier sera servi à partir de <code>/var/www/another/whoops.html</code>.</p>

<p>Comme vous pouvez le voir, en comprenant les circonstances dans lesquelles Nginx déclenche une recherche de nouvelle localisation, vous serez plus à même de prévoir le comportement que vous verrez lorsque vous ferez des requêtes.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Le fait de comprendre de quelle manière Nginx traite les requêtes clients peut vraiment vous faciliter la tâche en tant qu'administrateur. Vous pourrez savoir quel bloc de serveur Nginx sélectionnera en fonction de chaque requête clients. Vous pourrez également deviner de quelle manière le bloc de localisation sera sélectionné en fonction de l'URI de la requête. Dans l'ensemble, en sachant de quelle manière Nginx sélectionne les différents blocs, vous serez en capacité de reconnaître les contextes que Nginx appliquera pour servir chaque requête.</p>
