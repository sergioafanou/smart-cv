---
title : "Comment importer et exporter une base de données MongoDB sur Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04-fr
image: "https://sergio.afanou.com/assets/images/image-midres-52.jpg"
---

<p><em>L'auteur a choisi le <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> pour recevoir un don dans le cadre du programme <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introduction">Introduction</h3>

<p>MongoDB est l'un des moteurs de base de données NoSQL les plus populaires. Il doit sa célébrité à son évolutivité, sa puissance, sa fiabilité et sa facilité d'utilisation. Dans cet article, nous allons vous montrer de quelle manière importer et exporter vos bases de données MongoDB.</p>

<p>Notez que par import et export, nous faisons référence aux opérations qui traitent des données sous un format lisible par l'homme, compatibles avec d'autres produits logiciels. En revanche, les opérations de sauvegarde et de restauration créent ou utilisent des données binaires spécifiques à MongoDB, qui préservent la cohérence et l'intégrité de vos données et également ses attributs MongoDB spécifiques. Par conséquent, lors de la migration, il est généralement recommandé d'utiliser la sauvegarde et la restauration dans la mesure où les systèmes source et cible sont compatibles.</p>

<p>La sauvegarde, la restauration et les tâches de migration ne sont pas traitées dans cet article. Pour plus d'informations à ce sujet, consultez <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Comment sauvegarder, restaurer et migrer une base de données MongoDB sur Ubuntu 20.04</a>.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour suivre ce tutoriel, vous aurez besoin des éléments suivants :</p>

<ul>
<li>Un serveur Ubuntu 20.04 configuré en suivant le <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">guide Configuration initiale du serveur Ubuntu 20.04</a>, comprenant un non-root user avec des privilèges sudo et un pare-feu.</li>
<li>MongoDB installé et configuré en utilisant l'article <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Comment installer MongoDB sur Ubuntu 20.04</a>.</li>
<li>Comprendre les différences entre les données JSON et BSON dans MongoDB. Pour une explication détaillée à ce sujet, reportez-vous à l&rsquo;<a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04#step-1-%E2%80%94-using-json-and-bson-in-mongodb"><strong>Étape 1 — Utiliser JSON et BSON dans MongoDB</strong> dans notre tutoriel, Comment sauvegarder, restaurer et migrer une base de données MongoDB sur Ubuntu 20.04</a>.</li>
</ul>

<h2 id="Étape-1-—-importer-des-informations-dans-mongodb">Étape 1 — Importer des informations dans MongoDB</h2>

<p>Pour apprendre à importer des informations dans MongoDB, nous allons utiliser un échantillon populaire de base de données MongoDB, les restaurants. Elle est au format .json et peut être téléchargée en utilisant <code>wget</code> de la manière suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json
</li></ul></code></pre>
<p>Une fois le téléchargement terminé, vous devriez obtenir un fichier nommé <code>primer-dataset.json</code> (de 12 Mo) dans le répertoire actuel. Importons les données de ce fichier dans une nouvelle base de données que nous appellerons <code>newdb</code> et dans une collection nommée <code>restaurants</code>.</p>

<p>Utilisez la commande <code>mongoimport</code> de la manière suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoimport --db newdb --collection restaurants --file primer-dataset.json
</li></ul></code></pre>
<p>Le résultat ressemblera à ceci :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:37:55.607+0000    connected to: mongodb://localhost/
2020-11-11T19:37:57.841+0000    25359 document(s) imported successfully. 0 document(s) failed to import
</code></pre>
<p>Comme la commande ci-dessus l'indique, nous avons importé 25 359 documents. Étant donné que nous ne disposions pas d'une base de données appelée <code>newdb</code>, MongoDB l'a créée automatiquement.</p>

<p>Vérifions l'importation.</p>

<p>Connectez-vous à la base de données <code>newdb</code> nouvellement créée :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Vous êtes maintenant connecté à l'instance de base de données <code>newdb</code>. Notez que votre invite a changé et indique désormais que vous êtes connecté à la base de données.</p>

<p>Comptez les documents dans la collection de restaurants en utilisant la commande suivante :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.count()
</li></ul></code></pre>
<p>Le résultat affichera <code>25359</code>, qui correspond au nombre de documents importés. Pour procéder à une vérification plus approfondie, vous pouvez sélectionner le premier document dans la collection des restaurants de la manière suivante :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.findOne()
</li></ul></code></pre>
<p>Le résultat ressemblera à ceci :</p>
<pre class="code-pre "><code>[secondary label Output]
{
    "_id" : ObjectId("5fac3d937f12c471b3f26733"),
    "address" : {
        "building" : "1007",
        "coord" : [
            -73.856077,
            40.848447
        ],
        "street" : "Morris Park Ave",
        "zipcode" : "10462"
    },
    "borough" : "Bronx",
    "cuisine" : "Bakery",
    "grades" : [
        {
            "date" : ISODate("2014-03-03T00:00:00Z"),
            "grade" : "A",
            "score" : 2
        },
...
    ],
    "name" : "Morris Park Bake Shop",
    "restaurant_id" : "30075445"
}
</code></pre>
<p>Une telle vérification détaillée pourrait révéler la présence de problèmes avec les documents comme avec leur contenu, leur encodage, etc. Le format json utilise l'encodage <code>UTF-8</code> et vos exportations et importations devraient utiliser ce même encodage. Gardez cela à l'esprit lorsque vous modifiez des fichiers json manuellement. Dans le cas contraire, MongoDB gérera cela automatiquement à votre place.</p>

<p>Pour quitter l'invite MongoDB, tapez <code>exit</code> à l'apparition de l'invite :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Vous serez renvoyé à l'invite de ligne de commande normale en tant que non-root user.</p>

<h2 id="Étape-2-—-exporter-des-informations-à-partir-de-mongodb">Étape 2 — Exporter des informations à partir de MongoDB</h2>

<p>Comme nous l'avons précédemment mentionné, en exportant des informations MongoDB, vous pouvez obtenir un fichier de texte lisible par l'humain. Les informations sont exportées par défaut sous format json, mais vous pouvez les également exporter sous csv (valeur séparée par des virgules).</p>

<p>Pour exporter des informations de MongoDB, utilisez la commande <code>mongoexport</code>. Elle vous permet de procéder à une exportation très minutieuse afin que vous puissiez spécifier une base de données, une collection, un champ et même utiliser une requête pour l'exportation.</p>

<p>Par exemple, un <code>mongoexport</code> simple consisterait à exporter la collection de restaurants à partir de la base de données <code>newdb</code> que nous avons précédemment importée. Vous pouvez procéder à cette opération de la manière suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants --out newdbexport.json
</li></ul></code></pre>
<p>Dans la commande ci-dessus, nous utilisons -<code>--db</code> pour spécifier la base de données, <code>-c</code> pour la collection et <code>--out</code> pour le fichier dans lequel les données seront enregistrées.</p>

<p>La sortie d'un <code>mongoexport</code> réussie devrait ressembler à ceci :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:39:57.595+0000    connected to: mongodb://localhost/
2020-11-11T19:39:58.619+0000    [###############.........]  newdb.restaurants  16000/25359  (63.1%)
2020-11-11T19:39:58.871+0000    [########################]  newdb.restaurants  25359/25359  (100.0%)
2020-11-11T19:39:58.871+0000    exported 25359 records
</code></pre>
<p>La sortie ci-dessus indique que 25 359 documents ont été importés, le même nombre que celui des documents importés.</p>

<p>Dans certains cas, vous aurez éventuellement besoin d'exporter uniquement une partie de votre collection. En considérant la structure et le contenu du fichier json des restaurants, exportons tous les restaurants qui satisfont aux critères suivants : localisés dans le quartier du Bronx et proposant de la cuisine chinoise. Pour obtenir ces informations directement, tout en étant connecté à MongoDB, connectez-vous à nouveau à la base de données :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Ensuite, utilisez la requête suivante :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.find( { "borough": "Bronx", "cuisine": "Chinese" } )
</li></ul></code></pre>
<p>Les résultats s'affichent sur le terminal :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">2020-12-03T01:35:25.366+0000    connected to: mongodb://localhost/
</li><li class="line" data-prefix="$">2020-12-03T01:35:25.410+0000    exported 323 records
</li></ul></code></pre>
<p>Pour quitter l'invite MongoDB, tapez <code>exit</code> :</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Si vous souhaitez exporter les données à partir d'une ligne de commande sudo, plutôt que pendant que vous êtes connecté à la base de données, intégrez la requête précédente dans la commande <code>mongoexport</code>, en la spécifiant pour l'argument <code>-q</code> comme ceci :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants -q "{\"borough\": \"Bronx\", \"cuisine\": \"Chinese\"}" --out Bronx_Chinese_retaurants.json
</li></ul></code></pre>
<p>Notez que nous omettons les guillemets en utilisant backslash (<code>\</code>) dans la requête. De la même manière, vous devez omettre tout autre caractère spécial dans la requête.</p>

<p>Si l'exportation est probante, le résultat devrait ressembler à ce qui suit :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:49:21.727+0000    connected to: mongodb://localhost/
2020-11-11T19:49:21.765+0000    exported 323 records
</code></pre>
<p>Ce qui précède indique que 323 enregistrements ont été exportés. Vous pouvez les trouver dans le fichier <code>Bronx_Chinese_retaurants.json</code> que nous avons spécifié.</p>

<p>Utilisez <code>cat</code> et <code>less</code> pour analyser les données :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat Bronx_Chinese_retaurants.json | less
</li></ul></code></pre>
<p>Utilisez <code>SPACE</code> pour paginer à travers les données :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">date":{"$date":"2015-01-14T00:00:00Z"},"grade":"Z","score":36}],"na{"_id":{"$oid":"5fc8402d141f5e54f9054f8d"},"address":{"building":"1236","coord":[-73.8893654,40.81376179999999],"street":"238 Spofford Ave","zipcode":"10474"},"borough":"Bronx","cuisine":"Chinese","grades":[{"date":{"$date":"2013-12-30T00:00:00Z"},"grade":"A","score":8},{"date":{"$date":"2013-01-08T00:00:00Z"},"grade":"A","score":10},{"date":{"$date":"2012-06-12T00:00:00Z"},"grade":"B","score":15}],
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">. . .
</li></ul></code></pre>
<p>Appuyez sur <code>q</code> pour fermer. Vous pouvez maintenant importer et exporter une base de données MongoDB.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Cet article vous a présenté les éléments essentiels de l'importation et l'exportation d'informations vers et depuis une base de données MongoDB. Vous pouvez approfondir vos connaissances en consultant <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Comment sauvegarder, restaurer et migrer une base de données MongoDB sur Ubuntu 20.04</a>.</p>

<p>Vous pouvez également envisager d'utiliser une réplication. La réplication vous permet de continuer à exécuter votre service MongoDB sans interruption à partir d'un serveur MongoDB esclave alors que vous restaurez le maître à partir d'une défaillance. Une partie de la réplication est le <a href="https://docs.mongodb.org/manual/core/replica-set-oplog/">operations log (oplog)</a>. Il enregistre toutes les opérations qui modifient vos données. Vous pouvez utiliser ce journal, tout comme vous utiliseriez le journal binaire dans MySQL, pour restaurer vos données une fois la dernière sauvegarde effectuée. N'oubliez pas que, étant donné que les sauvegardes se font généralement pendant la nuit, si vous décidez de restaurer une sauvegarde le soir, vous manquerez toutes les mises à jour qui auront lieu depuis la dernière sauvegarde.</p>
