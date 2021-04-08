---
title : "Comment sauvegarder, restaurer et migrer une base de données MongoDB sur Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04-fr
image: "https://sergio.afanou.com/assets/images/image-midres-45.jpg"
---

<p><em>L'auteur a choisi le <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> pour recevoir un don dans le cadre du programme <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h2 id="introduction">Introduction</h2>

<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">MongoDB</a> est l'un des moteurs de base de données NoSQL les plus populaires. Il doit sa célébrité à son évolutivité, sa robustesse, sa fiabilité et sa facilité d'utilisation. À l'aide de cet article, vous allez sauvegarder, restaurer et migrer un échantillon de base de données MongoDB.</p>

<p>Lorsque l'on parle d'importation et d'exportation d'une base de données, nous faisons référence à la gestion de données sous un format lisible par l'homme et qui sont compatibles avec d'autres produits logiciels. En revanche, les opérations de sauvegarde et de restauration de MongoDB créent ou utilisent des données binaires spécifiques à MongoDB. Elles préservent non seulement la cohérence et l'intégrité de vos données mais également leurs attributs MongoDB spécifiques. Par conséquent, lors de la migration, il est généralement recommandé d'utiliser la sauvegarde et la restauration dans la mesure où les systèmes source et cible sont compatibles.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Avant de suivre ce tutoriel, vérifiez si vous répondez bien aux conditions préalables suivantes :</p>

<ul>
<li>Un <a href="https://www.digitalocean.com/products/droplets/">serveur Ubuntu 20.04</a> configuré en suivant le guide <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Configuration initiale de serveur Ubuntu 20.04</a>, comprenant un non-root user avec privilèges sudo et un pare-feu.</li>
<li>MongoDB installé et configuré en utilisant l'article <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Comment installer MongoDB sur Ubuntu 20.04</a>.</li>
<li>Un exemple de la base de données MongoDB importée en utilisant les instructions données dans <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04">Comment importer et exporter une base de données MongoDB</a>.</li>
</ul>

<p>Sauf indication contraire, toutes les commandes qui nécessitent des privilèges root dans ce tutoriel doivent être exécutées en tant que non-root user avec des privilèges sudo.</p>

<h2 id="Étape-1-mdash-utiliser-json-et-bson-dans-mongodb">Étape 1 — Utiliser JSON et BSON dans MongoDB</h2>

<p>Avant de poursuivre avec cet article, vous devez avoir quelques connaissances de base sur le sujet. Si vous avez de l'expérience avec d'autres systèmes de base de données NoSQL comme Redis, il se peut que vous trouviez quelques similitudes avec MongoDB.</p>

<p>MongoDB utilise les formats <a href="http://json.org/">JSON</a> et BSON (JSON binaire) pour stocker ses informations. JSON est le format lisible par l'homme, idéal pour exporter et éventuellement importer vos données. Vous pouvez également gérer vos données exportées avec tout outil qui prend en charge JSON et notamment un éditeur de texte simple.</p>

<p>Voici à quoi ressemble un exemple de document JSON :</p>
<div class="code-label " title="Example of JSON Format">Example of JSON Format</div><pre class="code-pre "><code class="code-highlight language-bash">{"address":[
    {"building":"1007", "street":"Park Ave"},
    {"building":"1008", "street":"New Ave"},
]}
</code></pre>
<p>Bien que le format JSON soit pratique pour travailler, il ne prend cependant pas en charge tous les types de données disponibles dans BSON. Cela signifie que, si vous utilisez JSON, vous ferez l'expérience de ce que l'on nomme une « perte de fidélité » des informations. Que ce soit pour la sauvegarde ou la restauration, il est donc préférable d'utiliser le format binaire BSON.</p>

<p>Deuxièmement, vous n'aurez plus à vous soucier de créer explicitement une base de données MongoDB. Si la base de données spécifiée que vous souhaitez importer n'existe pas encore, elle sera automatiquement créée. Cette fonctionnalité est encore plus pratique avec la structure des collections (tables de la base de données). Contrairement à d'autres moteurs de base de données, dans MongoDB, la structure est également créée automatiquement à l'insertion du premier document (ligne de la base de données).</p>

<p>Troisièmement, dans MongoDB, il se peut que la lecture et l'insertion de grandes quantités de données, (comme les tâches de cet article par exemple) exigent un volume intensif de ressources et consomment une grande partie de votre CPU, de votre mémoire et de votre espace disque. Ce point est crucial, car on utilise fréquemment MongoDB avec de grandes bases de données et les Big Data. Pour palier ce problème, la solution la plus simple consiste à exécuter les exportations et les sauvegardes pendant la nuit ou en-dehors des heures de pointe.</p>

<p>Quatrièmement, il se peut que la cohérence des informations pose des problèmes si le serveur MongoDB est occupé et si les informations qui s'y trouvent changent pendant le processus d'exportation ou de sauvegarde de la base de données. Il existe une solution possible à ce problème qui est la <a href="https://docs.mongodb.com/manual/faq/replica-sets/">réplication</a>. Vous pourrez envisager de l'utiliser une fois que vous en saurez davantage sur MongoDB.</p>

<p>Bien que vous puissiez utiliser les <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-14-04">fonctions import et export</a> pour sauvegarder et restaurer vos données, il existe de meilleures façons d'assurer la complète intégrité de vos bases de données MongoDB. Pour sauvegarder vos données, nous vous recommandons d'utiliser la commande <code>mongodump</code>. Pour la restauration, utilisez <code>mongorestore</code>. Voyons comment ces deux commandes fonctionnent.</p>

<h2 id="Étape-2-mdash-utiliser-mongodump-pour-sauvegarder-une-base-de-données-mongodb">Étape 2 — Utiliser <code>mongodump</code> pour sauvegarder une base de données MongoDB</h2>

<p>Examinons tout d'abord de quelle manière sauvegarder votre base de données MongoDB.</p>

<p>Un des arguments essentiels de <code>mongodump</code> est <code>--db</code>. Il spécifie le nom de la base de données que vous souhaitez sauvegarder. Si vous omettez de spécifier le nom de la base de données, <code>mongodump</code> procédera à la sauvegarde de toutes vos bases de données. Le second argument important est <code>--out</code>. Il permet de définir le répertoire dans lequel les données seront déchargées. Par exemple, sauvegardons la base de données <code>newdb</code> et sauvegardons-la dans le répertoire <code>/var/backups/mongobackups</code>. Idéalement, chacune de nos sauvegardes devraient se trouver dans un répertoire affichant la date du jour comme suit : <code>/var/backups/mongobacackup/10-29-20</code>.</p>

<p>Créez tout d'abord ce répertoire <code>/var/backups/mongobackups</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir /var/backups/mongobackups
</li></ul></code></pre>
<p>Ensuite, exécutez <code>mongodump</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</li></ul></code></pre>
<p>Vous verrez un résultat similaire à ce qui suit :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:22:36.886+0000    writing newdb.restaurants to
2020-10-29T19:22:36.969+0000    done dumping newdb.restaurants (25359 documents)
</code></pre>
<p>Notez que, dans le chemin du répertoire ci-dessus, nous avons utilisé <code>date +"%m-%d-%y</code> qui permet d'obtenir la date du jour automatiquement. Nos sauvegardes se trouveront ainsi dans le répertoire nommé de la manière suivante : <code>/var/backups/<span class="highlight">10-29-20</span>/</code>. Ceci est particulièrement pratique pour automatiser les sauvegardes.</p>

<p>À ce stade, vous disposez d'une sauvegarde complète de la base de données <code>newdb</code> dans le répertoire <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>. Cette sauvegarde intègre tout ce dont vous avez besoin pour restaurer la <code>newdb</code> correctement et préserver sa dénommée « fidélité ».</p>

<p>En règle générale, vous devriez régulièrement procéder à des sauvegardes, de préférence au moment où le serveur est le moins chargé. Par conséquent, vous pouvez configurer la commande <code>mongodump</code> comme un travail cron afin qu'elle s'exécute régulièrement, par exemple, chaque jour à 03:03 du matin.</p>

<p>Pour cela, ouvrez crontab, l'éditeur de cron :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Notez que, lorsque vous exécutez <code>sudo crontab</code>, vous allez modifier les tâches cron pour le root user. Cette commande est recommandée. En effet, si vous configurez les crons pour votre utilisateur, ils ne s'exécuteront pas correctement, surtout si votre profil sudo nécessite une vérification du mot de passe.</p>

<p>Dans l'invite crontab, insérez la commande <code>mongodump</code> suivante :</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">3 3 * * * mongodump --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</code></pre>
<p>Dans la commande ci-dessus, nous avons omis l'argument <code>--db</code> sur l'objet délibérément, car vous souhaiterez généralement sauvegarder toutes vos bases de données.</p>

<p>En fonction de la taille de votre base de données MongoDB, vous devriez rapidement manquer d'espace disque à cause du nombre élevé de sauvegardes. C'est pourquoi il est également recommandé de nettoyer ou de compresser régulièrement les anciennes sauvegardes.</p>

<p>Par exemple, si vous souhaitez supprimer toutes les sauvegardes de plus de sept jours, vous pouvez utiliser la commande bash suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</li></ul></code></pre>
<p>De la même manière que la commande <code>mongodump</code> précédente, vous pouvez également ajouter cette commande en tant que cron. Elle devra être exécutée juste avant de commencer la prochaine sauvegarde, par exemple, à 03:01 du matin. Pour cela, ouvrez à nouveau crontab :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Ensuite, insérez la ligne suivante :</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</code></pre>
<p>Enregistrez et fermez le fichier.</p>

<p>En exécutant toutes les tâches de cette étape, vous disposerez d'une solution de sauvegarde adaptée à vos bases de données MongoDB.</p>

<h2 id="Étape-3-mdash-utiliser-mongorestore-pour-restaurer-et-migrer-une-base-de-données-mongodb">Étape 3 — Utiliser <code>mongorestore</code> pour restaurer et migrer une base de données MongoDB</h2>

<p>Lorsque vous restaurez votre base de données MongoDB à l'aide d'une sauvegarde précédente, vous obtenez la copie exacte de vos informations MongoDB à un moment particulier qui inclut tous les index et les types de données. Ceci est particulièrement utile pour migrer vos bases de données MongoDB. Pour restaurer MongoDB, nous allons utiliser la commande <code>mongorestore</code> qui fonctionne avec les sauvegardes binaires générées par <code>mongodump</code>.</p>

<p>Continuons à travailler sur nos exemples avec la base de données <code>newdb</code> pour voir de quelle manière nous pouvons la restaurer à partir de la sauvegarde précédemment réalisée. Nous allons tout d'abord spécifier le nom de la base de données en utilisant l'argument <code>--nsInclude</code>. Nous allons ensuite utiliser <code>newdb.*</code> pour restaurer toutes les collections. Pour restaurer une collection unique, comme <code>restaurants</code>, utilisez plutôt <code>newdb.restaurants</code>.</p>

<p>Puis, en utilisant <code>--drop</code>, nous allons veiller à ce que la base de données cible soit tout d'abord supprimée afin que la sauvegarde puisse être restaurée dans une base de données propre. Pour finir, nous allons spécifier le répertoire de la dernière sauvegarde, dont l'appellation devrait ressembler à ce qui suit : <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<p>Une fois que vous disposez d'une sauvegarde horodatée, vous pouvez la restaurer en utilisant la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongorestore --db newdb --drop /var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/
</li></ul></code></pre>
<p>Vous verrez un résultat similaire à ce qui suit :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:25:45.825+0000    the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --nsInclude instead
2020-10-29T19:25:45.826+0000    building a list of collections to restore from /var/backups/mongobackups/10-29-20/newdb dir
2020-10-29T19:25:45.829+0000    reading metadata for newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.metadata.json
2020-10-29T19:25:45.834+0000    restoring newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.bson
2020-10-29T19:25:46.130+0000    no indexes to restore
2020-10-29T19:25:46.130+0000    finished restoring newdb.restaurants (25359 documents)
2020-10-29T19:25:46.130+0000    done
</code></pre>
<p>Dans le cas ci-dessus, nous avons restauré les données sur le serveur dans lequel nous avons créé la sauvegarde. Si vous souhaitez migrer les données sur un autre serveur et utiliser la même technique, vous devrez alors copier le répertoire de sauvegarde, c'est-à-dire <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code> dans notre cas, sur l'autre serveur.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Vous avez désormais exécuté quelques-unes des tâches essentielles liées à la sauvegarde, la restauration et la migration de vos bases de données MongoDB. Aucun serveur MongoDB de production ne devrait jamais être exécuté sans une stratégie de sauvegarde fiable, comme celle décrite ici.</p>
