---
title : "Cómo importar y exportar una base de datos de MongoDB en Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04-es
image: "https://sergio.afanou.com/assets/images/image-midres-32.jpg"
---

<p><em>El autor seleccionó <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> para que reciba una donación como parte del programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introducción">Introducción</h3>

<p>MongoDB es uno de los motores de base de datos más populares de NoSQL. Es famoso por ser escalable, potente, confiable y fácil de usar. En este artículo, le mostraremos cómo importar y exportar sus bases de datos de MongoDB.</p>

<p>Debemos aclarar que importar y exportar se refiere a aquellas operaciones que manejan los datos en un formato legible por el ser humano, compatible con otros productos de software. Por el contrario, las operaciones de copia de seguridad y restauración crean o utilizan datos binarios específicos de MongoDB, que preservan la uniformidad y la integridad de sus datos, y también sus atributos específicos de MongoDB. Por lo tanto, para migrar, generalmente es preferible usar la copia de seguridad y la restauración siempre que los sistemas de origen y destino sean compatibles.</p>

<p>Las tareas de copia de seguridad, restauración y migración están fuera del alcance de este artículo. Para obtener más información, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Cómo respaldar, restaurar y migrar una base de datos de MongoDB en Ubuntu 20.04</a>.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para completar este tutorial, necesitará lo siguiente:</p>

<ul>
<li>Un droplet de Ubuntu 20.04 configurado mediante la <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">guía de configuración inicial para servidores de Ubuntu 20.04</a>, incluyendo un usuario sudo no root y un firewall.</li>
<li>MongoDB instalado y configurado usando el artículo <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Cómo instalar MongoDB en Ubuntu 20.04</a>.</li>
<li>Un entendimiento de las diferencias entre los datos JSON y BSON en MongoDB. Para obtener un análisis más detallado, consulte el <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04#step-1-%E2%80%94-using-json-and-bson-in-mongodb"><strong>Paso 1: Usar JSON y BSON en MongoDB</strong> en nuestro tutorial, Cómo hacer una copia de seguridad, restaurar y migrar una base de datos de MongoDB en Ubuntu 20.04</a>.</li>
</ul>

<h2 id="paso-1-importar-la-información-a-mongodb">Paso 1: Importar la información a MongoDB</h2>

<p>Para aprender cómo funciona la importación de información a MongoDB vamos a utilizar una base de datos popular de MongoDB de muestra sobre restaurantes. Está en formato .json y se puede descargar con <code>wget</code> de esta manera:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json
</li></ul></code></pre>
<p>Una vez completada la descarga, debería tener un archivo llamado <code>primer-dataset.json</code> (12 MB de tamaño) en el directorio actual. Importaremos los datos de este archivo a una nueva base de datos llamada <code>newdb</code> y a una colección llamada <code>restaurants</code>.</p>

<p>Utilice el comando <code>mongoimport</code> de esta manera:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoimport --db newdb --collection restaurants --file primer-dataset.json
</li></ul></code></pre>
<p>El resultado tendrá el siguiente aspecto:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:37:55.607+0000    connected to: mongodb://localhost/
2020-11-11T19:37:57.841+0000    25359 document(s) imported successfully. 0 document(s) failed to import
</code></pre>
<p>Como muestra el comando anterior, se han importado 25359 documentos. Dado que no teníamos una base de datos llamada <code>newdb</code>, MongoDB la creó de forma automática.</p>

<p>Vamos a verificar la importación.</p>

<p>Conéctese a la base de datos <code>newdb</code> recientemente creada:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Ahora está conectado a la instancia de la base de datos <code>newdb</code>. Observe que la línea de comandos ha cambiado, indicando que está conectado a la base de datos.</p>

<p>Cuente los documentos de la colección de restaurantes con el comando:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.count()
</li></ul></code></pre>
<p>El resultado mostrará <code>25359</code>, que es el número de documentos importados. Para obtener una comprobación aún mejor, puede seleccionar el primer documento de la colección de restaurantes de esta manera:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.findOne()
</li></ul></code></pre>
<p>El resultado tendrá el siguiente aspecto:</p>
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
<p>Una comprobación tan detallada podría revelar problemas con los documentos, tales como el contenido, la codificación, etc. El formato json utiliza la codificación <code>UTF-8</code> sus exportaciones e importaciones deberían estar en dicha codificación. Tenga esto en cuenta si edita los archivos json de forma manual. De lo contrario, MongoDB se encargará de ello automáticamente.</p>

<p>Para salir de la línea de comandos de MongoDB, escriba <code>exit</code> en la línea de comandos:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Volverá a la línea de comandos normal como usuario no root.</p>

<h2 id="paso-2-exportar-información-desde-mongodb">Paso 2: Exportar información desde MongoDB</h2>

<p>Como mencionamos antes, cuando exporta la información de MongoDB, puede obtener un archivo de texto legible por el ser humano con sus datos. De manera predeterminada, la información se exportará en formato json, pero también puede exportar a csv (valor separado por comas).</p>

<p>Para exportar información desde MongoDB, utilice el comando <code>mongoexport</code>. Le permite exportar de forma muy detallada, de modo que puede especificar una base de datos, una colección, un campo e incluso utilizar una consulta para la exportación.</p>

<p>Un ejemplo simple de <code>mongoexport</code> sería exportar la colección de restaurantes desde la base de datos <code>newdb</code> que hemos importado anteriormente. Puede hacerse de esta manera:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants --out newdbexport.json
</li></ul></code></pre>
<p>En el comando anterior, se utilizó <code>--db</code> para especificar la base de datos, <code>-c</code> para la colección y <code>--out</code>, para el archivo en el que se guardarán los datos.</p>

<p>El resultado de una exitosa exportación de <code>mongoexport</code> debe tener el siguiente aspecto:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:39:57.595+0000    connected to: mongodb://localhost/
2020-11-11T19:39:58.619+0000    [###############.........]  newdb.restaurants  16000/25359  (63.1%)
2020-11-11T19:39:58.871+0000    [########################]  newdb.restaurants  25359/25359  (100.0%)
2020-11-11T19:39:58.871+0000    exported 25359 records
</code></pre>
<p>El resultado anterior muestra que se han importado 25359 documentos, el mismo número que de los documentos importados.</p>

<p>En algunos casos, es posible que necesite exportar solo una parte de su colección. Teniendo en cuenta la estructura y el contenido del archivo json de los restaurantes, vamos a exportar todos los restaurantes que cumplen los criterios de estar situados en el barrio del Bronx y de tener cocina china. Si queremos obtener esta información directamente mientras estamos conectados a MongoDB, vuelva a conectarse a la base de datos:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Luego, utilice esta consulta:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.find( { "borough": "Bronx", "cuisine": "Chinese" } )
</li></ul></code></pre>
<p>Los resultados se muestran en el terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">2020-12-03T01:35:25.366+0000    connected to: mongodb://localhost/
</li><li class="line" data-prefix="$">2020-12-03T01:35:25.410+0000    exported 323 records
</li></ul></code></pre>
<p>Para salir de la línea de comandos de MongoDB, escriba <code>exit</code>:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Si desea exportar los datos de una línea de comandos sudo en lugar de mientras está conectado a la base de datos, haga que la consulta anterior forme parte del comando <code>mongoexport</code> especificándolo para el argumento <code>-q</code> de esta manera:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants -q "{\"borough\": \"Bronx\", \"cuisine\": \"Chinese\"}" --out Bronx_Chinese_retaurants.json
</li></ul></code></pre>
<p>Tenga en cuenta que estamos evitando las comillas dobles con una barra oblicua inversa (<code>\</code>) en la consulta. De manera similar, tiene que evitar cualquier otro carácter especial en la consulta.</p>

<p>Si la exportación se realizó de forma correcta, el resultado debería tener el siguiente aspecto:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:49:21.727+0000    connected to: mongodb://localhost/
2020-11-11T19:49:21.765+0000    exported 323 records
</code></pre>
<p>Lo anterior muestra que se han exportado 323 registros y puede encontrarlos en el archivo <code>Bronx_Chinese_retaurants.json</code> que especificamos.</p>

<p>Utilice <code>cat</code> y <code>less</code> para escanear los datos:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat Bronx_Chinese_retaurants.json | less
</li></ul></code></pre>
<p>Utilice <code>SPACE</code> para navegar por los datos:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">date":{"$date":"2015-01-14T00:00:00Z"},"grade":"Z","score":36}],"na{"_id":{"$oid":"5fc8402d141f5e54f9054f8d"},"address":{"building":"1236","coord":[-73.8893654,40.81376179999999],"street":"238 Spofford Ave","zipcode":"10474"},"borough":"Bronx","cuisine":"Chinese","grades":[{"date":{"$date":"2013-12-30T00:00:00Z"},"grade":"A","score":8},{"date":{"$date":"2013-01-08T00:00:00Z"},"grade":"A","score":10},{"date":{"$date":"2012-06-12T00:00:00Z"},"grade":"B","score":15}],
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">. . .
</li></ul></code></pre>
<p>Presione <code>q</code> para salir. Ahora puede importar y exportar una base de datos de MongoDB.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Este artículo le ha presentado los aspectos esenciales de importación y exportación de información desde y hacia una base de datos de MongoDB. Puede continuar leyendo sobre <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Cómo hacer una copia de seguridad, restaurar y migrar una base de datos de Mong</a>oDB en Ubuntu 20.04.</p>

<p>También puede considerar usar la replicación. La replicación le permite continuar ejecutando su servicio de MongoDB sin interrupción desde un servidor de MongoDB esclavo mientras restaura el maestro tras un fallo. Parte de la replicación es el <a href="https://docs.mongodb.org/manual/core/replica-set-oplog/">registro de operaciones (oplog)</a>, que registra todas las operaciones que modifican sus datos. Puede usar este registro, igual que usaría el registro binario en MySQL, para restaurar sus datos después de haber realizado la última copia de seguridad. Recuerde que las copias de seguridad suelen realizarse durante la noche, y si decide restaurar una copia de seguridad en la noche, se perderá todas las actualizaciones desde la última copia de seguridad.</p>
