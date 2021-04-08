---
title : "Como importar e exportar um banco de dados MongoDB no Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04-pt
image: "https://sergio.afanou.com/assets/images/image-midres-34.jpg"
---

<p><em>O autor selecionou a <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a>​​​​​ para receber uma doação como parte do programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introdução">Introdução</h3>

<p>O MongoDB é um dos mecanismos de banco de dados NoSQL mais populares. Ele é famoso por ser escalável, poderoso, confiável e fácil de usar. Neste artigo, vamos mostrar como importar e exportar seus bancos de dados MongoDB.</p>

<p>Devemos deixar claro que ao dizer importação e exportação, estamos nos referindo àquelas operações que lidam com dados em um formato legível para humanos e compatível com outros produtos de software. Em contrapartida, as operações de backup e restauração criam ou usam dados binários específicos do MongoDB, que preservam a consistência e integridade dos seus dados, além de seus atributos específicos do MongoDB. Assim, para a migração, geralmente é preferível usar o backup e restauração, desde que os sistemas de origem e de destino sejam compatíveis.</p>

<p>As tarefas de backup, reinicialização e migração estão além do escopo deste artigo. Para obter mais informações, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Como fazer backup, restaurar e migrar um banco de dados MongoDB no Ubuntu 20.04</a>.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para concluir este tutorial, você precisará do seguinte:</p>

<ul>
<li>Um Droplet Ubuntu 20.04 configurado conforme o <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Guia de configuração inicial de servidor do Ubuntu 20.04</a>, incluindo um usuário sudo não root e um firewall.</li>
<li>O MongoDB instalado e configurado usando o artigo <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Como instalar o MongoDB no Ubuntu 20.04</a>.</li>
<li>Saber as diferenças entre os dados JSON e BSON no MongoDB. Para uma discussão detalhada, leia <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04#step-1-%E2%80%94-using-json-and-bson-in-mongodb"><strong>Passo 1 — Usando o JSON e BSON no MongoDB</strong> em nosso tutorial, Como fazer backup, restaurar e migrar um banco de dados MongoDB no Ubuntu 20.04</a>.</li>
</ul>

<h2 id="passo-1-—-importando-informações-para-o-mongodb">Passo 1 — Importando informações para o MongoDB</h2>

<p>Para aprender como funciona a importação de informações para o MongoDB, vamos usar como exemplo um banco de dados MongoDB popular sobre restaurantes. Ele está no formato .json e pode ser baixado usando o <code>wget</code> desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json
</li></ul></code></pre>
<p>Assim que o download terminar, você terá um arquivo chamado <code>primer-dataset.json</code> (com tamanho de 12 MB) no diretório atual. Vamos importar os dados desse arquivo para um novo banco de dados chamado <code>newdb</code> e para uma coleção chamada <code>restaurants</code>.</p>

<p>Use o comando <code>mongoimport</code> desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoimport --db newdb --collection restaurants --file primer-dataset.json
</li></ul></code></pre>
<p>O resultado ficará parecido com este:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:37:55.607+0000    connected to: mongodb://localhost/
2020-11-11T19:37:57.841+0000    25359 document(s) imported successfully. 0 document(s) failed to import
</code></pre>
<p>Como o comando acima mostra, 25359 documentos foram importados. Como não tínhamos um banco de dados chamado <code>newdb</code>, o MongoDB o criou automaticamente.</p>

<p>Vamos verificar a importação.</p>

<p>Conecte-se ao banco de dados <code>newdb</code> recém-criado:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Agora, você está conectado à instância de banco de dados <code>newdb</code>. Note que seu prompt mudou, indicando que você está conectado ao banco de dados.</p>

<p>Conte os documentos na coleção de restaurantes com o comando:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.count()
</li></ul></code></pre>
<p>O resultado mostrará <code>25359</code>, que é o número de documentos importados. Para uma verificação ainda melhor, selecione o primeiro documento da coleção de restaurantes desta forma:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.findOne()
</li></ul></code></pre>
<p>O resultado ficará parecido com este:</p>
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
<p>Uma verificação detalhada como essa poderia revelar problemas com os documentos, como seu conteúdo, codificação, etc. O formato json usa a codificação <code>UTF-8</code> e suas exportações e importações devem estar naquela codificação. Tenha isso em mente se for editar manualmente algum arquivo json. Caso contrário, o MongoDB manuseará ele automaticamente para você.</p>

<p>Para sair do prompt do MongoDB, digite <code>exit</code> no prompt:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Você será enviado de volta ao prompt de linha de comando normal como seu usuário não raiz.</p>

<h2 id="passo-2-—-exportando-informações-do-mongodb">Passo 2 — Exportando informações do MongoDB</h2>

<p>Como mencionado anteriormente, ao exportar informações do MongoDB, é possível adquirir um arquivo de texto legível para humanos com seus dados. Por padrão, as informações são exportadas no formato json, mas também é possível exportar para csv (valores separados por vírgula).</p>

<p>Para exportar informações do MongoDB, use o comando <code>mongoexport</code>. Ele permite fazer uma exportação bastante refinada, sendo possível especificar um banco de dados, uma coleção, um campo e até mesmo usar uma consulta para a exportação.</p>

<p>Um exemplo de <code>mongoexport</code> simples seria exportar a coleção de restaurantes do banco de dados <code>newdb</code> que importamos anteriormente. Isso pode ser feito desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants --out newdbexport.json
</li></ul></code></pre>
<p>No comando acima, usamos o <code>--db</code> para especificar o banco de dados, <code>-c</code> para a coleção e <code>--out</code> para o arquivo no qual os dados serão salvos.</p>

<p>O resultado de um <code>mongoexport</code> executado com sucesso deve ser parecido com este:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:39:57.595+0000    connected to: mongodb://localhost/
2020-11-11T19:39:58.619+0000    [###############.........]  newdb.restaurants  16000/25359  (63.1%)
2020-11-11T19:39:58.871+0000    [########################]  newdb.restaurants  25359/25359  (100.0%)
2020-11-11T19:39:58.871+0000    exported 25359 records
</code></pre>
<p>O resultado acima mostra que 25359 documentos foram importados — o mesmo número dos importados.</p>

<p>Em alguns casos, pode ser necessário exportar apenas uma parte da sua coleção. Considerando a estrutura e o conteúdo do arquivo json de restaurantes, vamos exportar todos os restaurantes que satisfaçam os critérios de estar localizado no bairro do Bronx e ser de cozinha chinesa. Se quisermos obter essas informações diretamente enquanto conectados ao MongoDB, conecte-se novamente ao banco de dados:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongo newdb
</li></ul></code></pre>
<p>Então, use esta consulta:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.restaurants.find( { "borough": "Bronx", "cuisine": "Chinese" } )
</li></ul></code></pre>
<p>Os resultados são exibidos no terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">2020-12-03T01:35:25.366+0000    connected to: mongodb://localhost/
</li><li class="line" data-prefix="$">2020-12-03T01:35:25.410+0000    exported 323 records
</li></ul></code></pre>
<p>Para sair do prompt do MongoDB, digite <code>exit</code>:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">exit
</li></ul></code></pre>
<p>Se quiser exportar os dados de uma linha de comando sudo sem estar conectado ao banco de dados, incorpore a consulta anterior no comando <code>mongoexport</code> especificando-a no argumento <code>-q</code> desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongoexport --db newdb -c restaurants -q "{\"borough\": \"Bronx\", \"cuisine\": \"Chinese\"}" --out Bronx_Chinese_retaurants.json
</li></ul></code></pre>
<p>Note que estamos adicionando o caractere de escape de barra invertida (<code>\</code>) nas aspas duplas da consulta. De maneira similar, é necessário adicionar o caractere de escape a qualquer outro caractere especial na consulta.</p>

<p>Se a exportação tiver sido bem-sucedida, o resultado se parecerá com este:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-11-11T19:49:21.727+0000    connected to: mongodb://localhost/
2020-11-11T19:49:21.765+0000    exported 323 records
</code></pre>
<p>O exemplo acima mostra que 323 registros foram exportados, e é possível encontrá-los no arquivo <code>Bronx_Chinese_retaurants.json</code> que especificamos.</p>

<p>Use o <code>cat</code> e <code>less</code> para analisar os dados:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat Bronx_Chinese_retaurants.json | less
</li></ul></code></pre>
<p>Use o <code>SPACE</code> para paginar os dados:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><div class="secondary-code-label " title="Output">Output</div><ul class="prefixed"><li class="line" data-prefix="$">date":{"$date":"2015-01-14T00:00:00Z"},"grade":"Z","score":36}],"na{"_id":{"$oid":"5fc8402d141f5e54f9054f8d"},"address":{"building":"1236","coord":[-73.8893654,40.81376179999999],"street":"238 Spofford Ave","zipcode":"10474"},"borough":"Bronx","cuisine":"Chinese","grades":[{"date":{"$date":"2013-12-30T00:00:00Z"},"grade":"A","score":8},{"date":{"$date":"2013-01-08T00:00:00Z"},"grade":"A","score":10},{"date":{"$date":"2012-06-12T00:00:00Z"},"grade":"B","score":15}],
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">. . .
</li></ul></code></pre>
<p>Pressione <code>q</code> para sair. Agora, é possível importar e exportar um banco de dados MongoDB.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Este artigo introduziu as informações essenciais sobre a importação e exportação de e para um banco de dados MongoDB. Continue a leitura com <a href="https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04">Como fazer backup, restaurar e migrar um banco de dados MongoDB no Ubuntu 20.04</a>.</p>

<p>Considere também o uso da replicação. A replicação permite continuar executando seu serviço MongoDB ininterruptamente a partir de um servidor MongoDB subordinado enquanto estiver restaurando o mestre depois de uma falha. Parte da replicação é o <a href="https://docs.mongodb.org/manual/core/replica-set-oplog/">registro de operações (oplog)</a>, que registra todas as operações que modificam seus dados. É possível usar esse registro, assim como usaria o registro binário no MySQL, para restaurar seus dados depois que o último backup tiver sido realizado. Lembre-se que os backups geralmente são realizados durante a noite, e se quiser restaurar um backup durante a tarde, você estará perdendo todas as atualizações desde o último backup.</p>
