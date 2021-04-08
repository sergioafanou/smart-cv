---
title : "Como fazer backup, restaurar e migrar um banco de dados MongoDB no Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04-pt
image: "https://sergio.afanou.com/assets/images/image-midres-2.jpg"
---

<p><em>O autor selecionou a <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a>​​​​​ para receber uma doação como parte do programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h2 id="introdução">Introdução</h2>

<p>O <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">MongoDB</a> é um dos mecanismos de banco de dados NoSQL mais populares. Ele é famoso por ser escalonável, robusto, confiável e fácil de usar. Neste artigo, você vai fazer backup, reiniciar e migrar um banco de dados MongoDB de exemplo.</p>

<p>Importar e exportar um banco de dados significa lidar com dados em um formato legível para humanos que seja compatível com outros produtos de software. Em contrapartida, o backup e as operações de restauração do MongoDB criam ou usam dados binários específicos do MongoDB, que preservam não apenas a consistência e integridade dos seus dados, mas também seus atributos específicos do MongoDB. Assim, para a migração, geralmente é preferível usar o backup e restauração, desde que o sistema de origem e de destino sejam compatíveis.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Antes de seguir este tutorial, certifique-se de completar os seguintes pré-requisitos:</p>

<ul>
<li><a href="https://www.digitalocean.com/products/droplets/">Um Droplet Ubuntu 20.04</a> configurado conforme o <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Guia de configuração inicial de servidor do Ubuntu 20.04</a>, incluindo um usuário sudo não root e um firewall.</li>
<li>O MongoDB instalado e configurado usando o artigo <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Como instalar o MongoDB no Ubuntu 20.04</a>.</li>
<li>Um banco de dados MongoDB de exemplo importado usando as instruções em <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04">Como importar e exportar um banco de dados MongoDB</a>.</li>
</ul>

<p>Exceto se explicitamente dito, todos os comandos que exigem privilégios root neste tutorial devem ser executados como um usuário não root com privilégios sudo.</p>

<h2 id="passo-1-—-usando-o-json-e-bson-no-mongodb">Passo 1 — Usando o JSON e BSON no MongoDB</h2>

<p>Antes de ir adiante com este artigo, é necessária uma compreensão básica sobre o assunto. Se você tiver experiência com outros sistemas de banco de dados NoSQL como o Redis, pode encontrar algumas similaridades ao trabalhar com o MongoDB.</p>

<p>O MongoDB usa os formatos <a href="http://json.org/">JSON</a> e BSON (JSON binário) para armazenar suas informações. O JSON é o formato legível para humanos que é perfeito para exportar e, eventualmente, importar seus dados. Você pode gerenciar ainda mais seus dados exportados com qualquer ferramenta compatível com JSON, incluindo um editor de texto simples.</p>

<p>Um documento json de exemplo se parece com este:</p>
<div class="code-label " title="Example of JSON Format">Example of JSON Format</div><pre class="code-pre "><code class="code-highlight language-bash">{"address":[
    {"building":"1007", "street":"Park Ave"},
    {"building":"1008", "street":"New Ave"},
]}
</code></pre>
<p>É conveniente se trabalhar com o JSON, mas ele não suporta todos os tipos de dados disponíveis em BSON. Isso significa que haverá a chamada “perda de fidelidade” das informações se usar o JSON. Para fazer backup e restaurar, é melhor usar o binário BSON.</p>

<p>Em segundo lugar, você não precisa se preocupar com a criação explícita de um banco de dados MongoDB. Se o banco de dados especificado para importar ainda não existir, ele é criado automaticamente. O caso com a estrutura das coleções (tabelas de bancos de dados) é ainda melhor. Diferentemente de outros mecanismos de bancos de dados, no MongoDB, a estrutura é também criada automaticamente no momento da inserção do primeiro documento (linha do banco de dados).</p>

<p>Em terceiro lugar, no MongoDB, ler ou inserir grandes quantidades de dados, como as tarefas deste artigo, pode gerar um uso intensivo de recursos e consumir grande parte do seu CPU, memória e espaço em disco. Isso deve ser considerado, já que o MongoDB é frequentemente usado para grandes bancos de dados e Big Data. A solução mais simples para esse problema é executar as exportações e backups durante a noite ou horários fora do pico.</p>

<p>Em quarto lugar, a consistência das informações pode ser problemática se você tiver um servidor MongoDB ocupado, no qual as informações mudam durante a exportação do banco de dados ou o processo de backup. Uma solução possível para esse problema é <a href="https://docs.mongodb.com/manual/faq/replica-sets/">a replicação</a>, que você pode considerar quando avançar no tópico do MongoDB.</p>

<p>Embora você possa usar as <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-14-04">funções de importação e exportação</a> para fazer backup e restaurar seus dados, há melhores maneiras de garantir a integridade total dos seus bancos de dados MongoDB. Para fazer backup dos seus dados, você deve usar o comando <code>mongodump</code>. Para restaurar, use o <code>mongorestore</code>. Vamos ver como eles funcionam.</p>

<h2 id="passo-2-—-usando-o-mongodump-para-fazer-backup-de-um-banco-de-dados-mongodb">Passo 2 — Usando o <code>mongodump</code> para fazer backup de um banco de dados MongoDB</h2>

<p>Vamos mostrar primeiro como fazer backup do seu banco de dados MongoDB.</p>

<p>Um argumento essencial para o <code>mongodump</code> é o <code>--db</code>, que especifica o nome do banco de dados do qual você deseja fazer backup. Se você não especificar um nome de banco de dados, o <code>mongodump</code> faz backup de todos os seus bancos de dados. O segundo argumento importante é o <code>--out</code>, que define o diretório no qual os dados serão despejados. Por exemplo, vamos fazer backup do banco de dados <code>newdb</code> e armazená-lo no diretório <code>/var/backups/mongobackups</code>. Idealmente, teremos cada um dos nossos backups em um diretório com a data atual como <code>/var/backups/mongobackups/10-29-20</code>.</p>

<p>Primeiramente, crie o diretório <code>/var/backups/mongobackups</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir /var/backups/mongobackups
</li></ul></code></pre>
<p>Em seguida, execute o <code>mongodump</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</li></ul></code></pre>
<p>Você verá uma saída como esta:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:22:36.886+0000    writing newdb.restaurants to
2020-10-29T19:22:36.969+0000    done dumping newdb.restaurants (25359 documents)
</code></pre>
<p>Note que no caminho de diretório acima, usamos <code>date +"%m-%d-%y"</code> que obtém automaticamente a data atual. Isso permitirá que tenhamos backups dentro de um diretório como <code>/var/backups/<span class="highlight">10-29-20</span>/</code>. Isso é ainda mais conveniente quando automatizamos os backups.</p>

<p>Neste ponto, você possui um backup completo do banco de dados <code>newdb</code> no diretório <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>. Esse backup possui todo o necessário para restaurar o <code>newdb</code> corretamente e preservar sua chamada “fidelidade”.</p>

<p>Como uma regra geral, você deve fazer backups regulares e, de preferência, quando o servidor estiver menos carregado. Assim, você pode definir o comando <code>mongodump</code> como uma tarefa cron para que ele seja executado regularmente, por exemplo, todos os dias às 03:03.</p>

<p>Para fazer isso, abra o crontab, o editor do cron:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Note que quando executa <code>sudo crontab</code>, você estará editando as tarefas cron para o usuário root. Isso é recomendado, porque se você definir os crons para seu usuário, eles podem não ser executados corretamente, especialmente se seu perfil sudo exigir uma verificação por senha.</p>

<p>Dentro do prompt do crontab, insira o seguinte comando <code>mongodump</code>:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">3 3 * * * mongodump --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</code></pre>
<p>No comando acima, omitimos o argumento <code>--db</code> de propósito, porque geralmente é interessante fazer backup de todos os nossos bancos de dados.</p>

<p>Dependendo dos tamanhos dos seus bancos de dados MongoDB, é possível esgotar rapidamente o espaço em disco com muitos backups. É por isso que é recomendável limpar os backups antigos regularmente ou compactá-los.</p>

<p>Por exemplo, para excluir todos os backups com idade maior que sete dias, use o seguinte comando bash:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</li></ul></code></pre>
<p>De maneira similar ao comando <code>mongodump</code> anterior, também é possível adicionar isso como uma tarefa cron. Ele deve ser executado pouco antes de iniciar o próximo backup, por exemplo, às 03:01. Para esse fim, abra novamente o crontab:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Em seguida, insira a linha a seguir:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</code></pre>
<p>Salve e feche o arquivo.</p>

<p>A realização de todas as tarefas neste passo garantirá uma solução de backup adequada para seus bancos de dados MongoDB.</p>

<h2 id="passo-3-—-usando-o-mongorestore-para-restaurar-e-migrar-um-banco-de-dados-mongodb">Passo 3 — Usando o <code>mongorestore</code> para restaurar e migrar um banco de dados MongoDB</h2>

<p>Ao restaurar seu banco de dados MongoDB de um backup anterior, você obtém uma cópia exata das suas informações do MongoDB obtidas em um momento específico, incluindo todos os índices e tipos de dados. Isso é especialmente útil quando quiser migrar seus bancos de dados MongoDB. Para restaurar o MongoDB, usaremos o comando <code>mongorestore</code>, que funciona com os backups binários que o <code>mongodump</code> produz.</p>

<p>Vamos continuar nossos exemplos com o banco de dados <code>newdb</code> e ver como podemos restaurá-lo a partir do backup obtido anteriormente. Primeiramente, vamos especificar o nome do banco de dados com o argumento <code>--nsInclude</code>. Vamos usar o <code>newdb.*</code> para restaurar todas as coleções. Para restaurar uma única coleção como <code>restaurants</code>, use o <code>newdb.restaurants</code> ao invés disso.</p>

<p>Em seguida, usando o <code>--drop</code>, vamos garantir que o banco de dados de destino seja primeiro descartado, para que depois o backup seja restaurado em um banco de dados limpo. Como um argumento final, vamos especificar o diretório do último backup, que será parecido com isto: <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<p>Assim que tiver um backup com carimbo de data/hora, restaure-o usando este comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongorestore --db newdb --drop /var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/
</li></ul></code></pre>
<p>Você verá uma saída como esta:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:25:45.825+0000    the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --nsInclude instead
2020-10-29T19:25:45.826+0000    building a list of collections to restore from /var/backups/mongobackups/10-29-20/newdb dir
2020-10-29T19:25:45.829+0000    reading metadata for newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.metadata.json
2020-10-29T19:25:45.834+0000    restoring newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.bson
2020-10-29T19:25:46.130+0000    no indexes to restore
2020-10-29T19:25:46.130+0000    finished restoring newdb.restaurants (25359 documents)
2020-10-29T19:25:46.130+0000    done
</code></pre>
<p>No caso acima, estamos restaurando os dados no mesmo servidor onde criamos o backup. Se quiser migrar os dados para outro servidor e usar a mesma técnica, você deve copiar o diretório de backup — em nosso caso, <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code> — para o outro servidor.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Agora, você terminou de realizar algumas tarefas essenciais relacionadas ao backup, restauração e migração dos seus bancos de dados MongoDB. Nenhum servidor MongoDB de produção deve ser executado sem uma estratégia de backup confiável, como aquela descrita aqui.</p>
