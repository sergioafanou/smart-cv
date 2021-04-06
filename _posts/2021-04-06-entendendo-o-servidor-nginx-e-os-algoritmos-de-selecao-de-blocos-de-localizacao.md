---
title : "Entendendo o servidor Nginx e os algoritmos de seleção de blocos de localização"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms-pt
image: "https://sergio.afanou.com/assets/images/image-midres-15.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>O Nginx é um dos servidores Web mais populares do mundo. Ele é capaz de lidar com grandes cargas com muitas conexões de cliente simultâneas, e pode funcionar facilmente como um servidor Web, um servidor de e-mail ou um servidor de proxy reverso.</p>

<p>Neste guia, vamos discutir alguns dos detalhes dos bastidores que determinam como o Nginx processa as solicitações de clientes.  A compreensão dessas ideias pode ajudar a eliminar a necessidade de suposições ao projetar servidores e blocos de localização e pode tornar o processamento de solicitações menos imprevisível.</p>

<h2 id="configurações-em-bloco-do-nginx">Configurações em bloco do Nginx</h2>

<p>O Nginx divide logicamente as configurações destinadas a atender diferentes conteúdo em blocos, que operam em uma estrutura hierárquica.  Cada vez que uma solicitação de cliente é feita, o Nginx começa um processo para determinar quais blocos de configuração devem ser usados para lidar com a solicitação.  Este processo de decisão é o que discutiremos neste guia.</p>

<p>Os principais blocos que discutiremos são o bloco de <strong>servidor</strong> e o bloco de <strong>localização</strong>.</p>

<p>Um bloco de servidor é um subconjunto da configuração do Nginx que define um servidor virtual usado para manusear solicitações de um determinado tipo.  Os administradores geralmente configuram vários blocos de servidor e decidem qual bloco deve lidar com qual conexão com base no nome de domínio, porta e endereço IP solicitados.</p>

<p>Um bloco de localização fica dentro de um bloco de servidor e é usado para definir como o Nginx deve manusear solicitações para diferentes recursos e URIs para o servidor pai.  O espaço URI pode ser subdividido da maneira que o administrador preferir usando esses blocos.  Ele é um modelo extremamente flexível.</p>

<h2 id="como-o-nginx-decida-qual-bloco-de-servidor-irá-manusear-uma-solicitação">Como o Nginx decida qual bloco de servidor irá manusear uma solicitação</h2>

<p>Como o Nginx permite que o administrador defina vários blocos de servidor que funcionam como instâncias de servidor Web separadas, ele precisa de um procedimento para determinar qual desses blocos de servidor será usado para satisfazer uma solicitação.</p>

<p>Ele faz isso através de um sistema definido de verificações que são usadas para encontrar a melhor combinação possível.  As principais diretivas do bloco de servidor com as quais o Nginx se preocupa durante esse processo são a diretiva <code>listen</code> (escuta) e a diretiva <code>server_name</code> (nome do servidor).</p>

<h3 id="analisando-a-diretiva-“listen”-para-encontrar-possíveis-correspondências">Analisando a diretiva “listen” para encontrar possíveis correspondências</h3>

<p>Primeiramente, o Nginx analisa o endereço IP e a porta da solicitação.  Ele compara esses valores com a diretiva <code>listen</code> de cada servidor para construir uma lista dos blocos de servidor que podem resolver a solicitação.</p>

<p>A diretiva <code>listen</code> tipicamente define quais endereços IP e portas que serão respondidos pelo bloco de servidor.  Por padrão, qualquer bloco de servidor que não inclua uma diretiva <code>listen</code> recebe os parâmetros de escuta de <code>0.0.0.0:80</code> (ou <code>0.0.0.0:8080</code> se o Nginx estiver sendo executado por um usuário normal, não root).  Isso permite que esses blocos respondam a solicitações em qualquer interface na porta 80, mas esse valor padrão não possui muito peso dentro do processo de seleção de servidor.</p>

<p>A diretiva <code>listen</code> pode ser definida como:</p>

<ul>
<li>Uma combinação de endereço IP/porta.</li>
<li>Um endereço IP solto que então escutará na porta padrão 80.</li>
<li>Uma porta solta que escutará todas as interfaces nessa porta.</li>
<li>O caminho para um soquete do Unix.</li>
</ul>

<p>Geralmente, a última opção só terá implicações ao passar solicitações entre servidores diferentes.</p>

<p>Ao tentar determinar para qual bloco de servidor enviar uma solicitação, o Nginx primeiro tentará decidir com base na especificidade da diretiva <code>listen</code> usando as seguintes regras:</p>

<ul>
<li>O Nginx traduz todas as diretivas <code>listen</code> “incompletas” substituindo valores que estão faltando pelos seus valores padrão para que cada bloco possa ser avaliado por seu endereço IP e porta.  Alguns exemplos dessas traduções são:

<ul>
<li>Um bloco sem a diretiva <code>listen</code> usa o valor <code>0.0.0.0:80</code>.</li>
<li>Um bloco definido para um endereço IP <code>111.111.111.111</code> sem porta se torna <code>111.111.111.111:80</code></li>
<li>Um bloco definido para a porta <code>8888</code> sem endereço IP torna-se <code>0.0.0.0:8888</code></li>
</ul></li>
<li>Em seguida, o Nginx tenta coletar uma lista dos blocos de servidor que correspondem à solicitação, mais especificamente com base no endereço IP e porta. Isso significa que qualquer bloco que esteja funcionalmente usando <code>0.0.0 0</code> como endereço IP (para corresponder a qualquer interface), não será selecionado se houver blocos correspondentes que listam um endereço IP específico. Em todo caso, a porta deve ser exatamente a mesma.</li>
<li>Se houver apenas uma correspondência mais específica, o bloco de servidor será usado para atender a solicitação.  Se houver vários blocos de servidor com o mesmo nível de especificidade correspondente, o Nginx então começa a avaliar a diretiva <code>server_name</code> de cada bloco de servidor.</li>
</ul>

<p>É importante entender que o Nginx avaliará apenas a diretiva <code>server_name</code> quando precisar distinguir entre blocos de servidor que correspondem ao mesmo nível de especificidade na diretiva <code>listen</code>.  Por exemplo, se o <code>example.com</code> for hospedado na porta <code>80</code> de <code>192.168.1.10</code>, uma solicitação para <code>example.com</code> será sempre atendida pelo primeiro bloco neste exemplo, apesar da diretiva <code>server_name</code> no segundo bloco.</p>
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
<p>Caso mais de um bloco de servidor corresponda com o mesmo nível de especificidade, o próximo passo é verificar a diretiva <code>server_name</code>.</p>

<h3 id="analisando-a-diretiva-“server_name”-para-escolher-uma-correspondência">Analisando a diretiva “server_name” para escolher uma correspondência</h3>

<p>Em seguida, para avaliar ainda mais as solicitações que possuem diretivas <code>listen</code> igualmente específicas, o Nginx verifica o cabeçalho “Host” da solicitação.  Esse valor possui o domínio ou endereço IP que o cliente estava tentando realmente alcançar.</p>

<p>O Nginx tenta achar a melhor correspondência para o valor que ele encontra olhando a diretiva <code>server_name</code> dentro de cada um dos blocos de servidor que ainda são candidatos a seleção.  O Nginx avalia eles usando a seguinte fórmula:</p>

<ul>
<li>O Nginx primeiro tentará encontrar um bloco de servidor com um <code>server_name</code> que corresponda <em>exatamente</em> ao valor no cabeçalho “Host” da solicitação.  Se for encontrado, o bloco associado será usado para atender à solicitação.  Se mais de uma correspondência exata for encontrada, a <strong>primeira</strong> é usada.</li>
<li>Se nenhuma correspondência exata for encontrada, o Nginx então tentará encontrar um bloco de servidor com um <code>server_name</code> correspondente usando um curinga inicial (indicado por um <code>*</code> no início do nome na configuração).  Se for encontrado, o bloco será usado para atender à solicitação.  Se várias correspondências forem encontradas, a correspondência <strong>mais longa</strong> será usada para atender à solicitação.</li>
<li>Se nenhuma correspondência for encontrada usando um curinga inicial, o Nginx então procura um bloco de servidor com um <code>server_name</code> correspondente usando um curinga à direita (indicado por um nome de servidor que termina com um <code>*</code> na configuração).  Se for encontrado, o bloco será usado para atender à solicitação.  Se várias correspondências forem encontradas, a correspondência <strong>mais longa</strong> será usada para atender à solicitação.</li>
<li>Se nenhuma correspondência for encontrada usando um curinga à direita, o Nginx então avalia blocos de servidor que definem o <code>server_name</code> usando expressões regulares (indicado por um <code>~</code> antes do nome).  O <strong>primeiro</strong> <code>server_name</code> com uma expressão regular que corresponda ao cabeçalho “Host” será usado para atender à solicitação.</li>
<li>Se nenhuma expressão regular correspondente for encontrada, o Nginx seleciona então o bloco de servidor padrão para aquele endereço IP e porta.</li>
</ul>

<p>Cada combinação de endereço IP/porta possui um bloco de servidor padrão que será usado quando um plano de ação não puder ser determinado com os métodos acima.  Para uma combinação de endereço IP/porta, ele será o primeiro bloco na configuração ou o bloco que contém a opção <code>default_server</code> como parte da diretiva <code>listen</code> (que iria se sobrepor ao algoritmo encontrado por primeiro).  Pode haver apenas uma declaração <code>default_server</code> para cada combinação de endereço IP/porta.</p>

<h3 id="exemplos">Exemplos</h3>

<p>Se houver um <code>server_name</code> definido que corresponda exatamente ao valor de cabeçalho “Host”, o bloco de servidor é selecionado para processar a solicitação.</p>

<p>Neste exemplo, se o cabeçalho “Host” da solicitação fosse definido como ”host1.example.com”, o segundo servidor seria selecionado:</p>
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
<p>Se nenhuma correspondência exata for encontrada, o Nginx então verifica se há um <code>server_name</code> com um curinga inicial que se encaixa.  A correspondência mais longa que começa com um curinga será selecionada para atender à solicitação.</p>

<p>Neste exemplo, se a solicitação tivesse um cabeçalho “Host” de ”www.example.org”, o segundo bloco de servidor seria selecionado:</p>
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
<p>Se nenhuma correspondência for encontrada com um curinga inicial, o Nginx então verá se uma correspondência existe usando um curinga no final da expressão.  Neste ponto, a correspondência mais longa que termina com um curinga será selecionada para atender à solicitação.</p>

<p>Por exemplo, se a solicitação tiver um cabeçalho “Host” definido como ”www.example.com”, o terceiro bloco de servidor será selecionado:</p>
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
<p>Se nenhuma correspondência com curingas puder ser encontrada, o Nginx então seguirá adiante para tentar corresponder o termo com as diretivas do <code>server_name</code> que usam expressões regulares.  A <em>primeira</em> expressão regular correspondente será selecionada para responder à solicitação.</p>

<p>Por exemplo, se o cabeçalho “Host” da solicitação for definido como ”www.example.com”, então o segundo bloco de servidor será selecionado para satisfazer a solicitação:</p>
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
<p>Se nenhum dos passos acima for capaz de satisfazer a solicitação, então a solicitação será passada ao servidor <em>padrão</em> para o endereço IP e porta correspondentes.</p>

<h2 id="fazendo-correspondência-de-blocos-de-localização">Fazendo correspondência de blocos de localização</h2>

<p>De maneira similar ao processo que o Nginx usa para selecionar o bloco de servidor que processará uma solicitação, o Nginx também possui um algoritmo estabelecido para decidir qual bloco de localização dentro do servidor usar para manusear solicitações.</p>

<h3 id="sintaxe-do-bloco-de-localização">Sintaxe do bloco de localização</h3>

<p>Antes de abordarmos como o Nginx decide qual bloco de localização usar para manusear solicitações, vamos analisar algumas das sintaxes que podem ser vistas nas definições de blocos de localização.  Os blocos de localização localizam-se dentro de blocos de servidor (ou outros blocos de localização) e são usados para decidir como processar o URI de solicitação (a parte da solicitação que vem após o nome de domínio ou endereço IP/porta).</p>

<p>Os blocos de localização geralmente possuem a seguinte forma:</p>
<pre class="code-pre "><code>location <span class="highlight">optional_modifier</span> <span class="highlight">location_match</span> {

    . . .

}
</code></pre>
<p>O <code><span class="highlight">location_match</span></code> no exemplo acima define com o que o Nginx deve comparar o URI de solicitação.  A existência ou não existência do modificador no exemplo acima afeta a maneira como o Nginx tenta corresponder o bloco de localização.  Os modificadores abaixo farão com que o bloco de localização associado seja interpretado da seguinte forma:</p>

<ul>
<li><strong>(none)</strong>: se nenhum modificador estiver presente, o local é interpretado como uma correspondência <em>por prefixo</em>.  Isso significa que o local dado será comparado ao início do URI de solicitação para determinar se há correspondência.</li>
<li><strong><code>=</code></strong>: se um sinal de igual for usado, o bloco será considerado uma correspondência se o URI de solicitação corresponder exatamente ao local dado.</li>
<li><strong><code>~</code></strong>: se um modificador til estiver presente, o local será interpretado como uma correspondência de expressão regular sensível a maiúsculas e minúsculas.</li>
<li><strong><code>~*</code></strong>: se um modificador de til e asterisco for usado, o bloco de localização será interpretado como uma correspondência de expressão regular que não diferencia maiúsculas de minúsculas.</li>
<li><strong><code>^~</code></strong>: se um modificador de acento circunflexo e til estiver presente, e se este bloco for selecionado como a melhor correspondência de expressão não regular, a correspondência de expressão regular não será realizada.</li>
</ul>

<h3 id="exemplos-demonstrando-a-sintaxe-do-bloco-de-localização">Exemplos demonstrando a sintaxe do bloco de localização</h3>

<p>Como um exemplo de correspondência de prefixos, o bloco de localização a seguir pode ser selecionado para responder aos URIs de solicitação que se pareçam com <code>/site</code>, <code>/site/page1/index.html</code> ou <code>/site/index.html</code>:</p>
<pre class="code-pre "><code>location /site {

    . . .

}
</code></pre>
<p>Para demonstrar a correspondência exata de URIs de solicitação, esse bloco será sempre usado para responder a um URI de solicitação que se pareça com <code>/page1</code>.  Ele <strong>não</strong> será usado para responder a um URI de solicitação <code>/page1/index.html</code>.  Lembre-se de que se esse bloco for selecionado e a solicitação for realizada usando uma página de índice, um redirecionamento interno será feito para outro local. Ele será o manuseador real da solicitação:</p>
<pre class="code-pre "><code>location = /page1 {

    . . .

}
</code></pre>
<p>Como um exemplo de um local que deve ser interpretado como uma expressão regular sensível a maiúsculas e minúsculas, este bloco poderia ser usado para manusear solicitações para <code>/tortoise.jpg</code>, mas <strong>não</strong> para <code>/FLOWER.PNG</code>:</p>
<pre class="code-pre "><code>location ~ \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Um bloco que permitiria a correspondência sem diferenciar maiúsculas de minúsculas parecido com o exemplo acima é mostrado abaixo.  Aqui, tanto <code>/tortoise.jpg</code> <em>quanto</em> <code>/FLOWER.PNG</code> poderiam ser manuseados por este bloco:</p>
<pre class="code-pre "><code>location ~* \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Por fim, este bloco impediria a ocorrência de correspondências de expressão regular se ele fosse escolhido como a melhor correspondência de expressão não regular.  Ele poderia manusear solicitações para <code>/costumes/ninja.html</code>:</p>
<pre class="code-pre "><code>location ^~ /costumes {

    . . .

}
</code></pre>
<p>Como se vê, os modificadores indicam como o bloco de localização deve ser interpretado.  No entanto, isso <em>não</em> nos informa qual algoritmo o Nginx usa para decidir para qual bloco de localização enviar a solicitação.  Vamos analisar isso a seguir.</p>

<h3 id="como-o-nginx-escolhe-qual-localização-usar-para-processar-solicitações">Como o Nginx escolhe qual localização usar para processar solicitações</h3>

<p>O Nginx escolhe o local que será usado para atender a uma solicitação de forma semelhante a como seleciona um bloco de servidor.  Ele executa um processo que determina o melhor bloco de localização para qualquer solicitação.  Entender esse processo é um requisito crucial para ser capaz de configurar o Nginx de maneira confiável e precisa.</p>

<p>Tendo em mente os tipos de declaração de localização que descrevemos acima, o Nginx avalia os possíveis contextos de localização comparando o URI de solicitação com cada um dos locais.  Ele faz isso usando o seguinte algoritmo:</p>

<ul>
<li>O Nginx começa verificando todas as correspondências de localização baseadas em prefixos (todos os tipos de localização que não envolvem expressões regulares). Ele compara cada localização com o URI de solicitação completo.</li>
<li>Primeiramente, o Nginx procura por uma correspondência exata.  Se um bloco de localização usando o modificador <code>=</code> for encontrado para corresponder exatamente ao URI de solicitação, este bloco de localização é selecionado imediatamente para atender à solicitação.</li>
<li>Se nenhuma correspondência exata de bloco de localização (com o modificador <code>=</code>) for encontrada, o Nginx então passa a avaliar prefixos não exatos.  Ele descobre o prefixo de correspondência mais longo para um determinado URI de solicitação, que é então avaliado da seguinte forma:

<ul>
<li>Se o prefixo correspondente mais longo possuir o modificador <code>^~</code>, então o Nginx imediatamente terminará sua pesquisa e selecionará esse local para atender à solicitação.</li>
<li>Se o prefixo correspondente mais longo <em>não</em> usar o modificador <code>^~</code>, a correspondência é armazenada pelo Nginx por enquanto, para que o foco da pesquisa possa mudar.</li>
</ul></li>
<li>Após o prefixo correspondente mais longo ser determinado e armazenado, o Nginx passa a avaliar as localizações de expressões regulares (tanto as que diferenciam, quanto as que não diferenciam letras maiúsculas de minúsculas).  Se houver alguma localização de expressão regular <em>dentro</em> da localização do prefixo correspondente mais longo, o Nginx as moverá para o topo de sua lista de localizações de regex para verificação.  Em seguida, o Nginx tenta fazer a correspondência com as localizações das expressões regulares sequencialmente.  A <strong>primeira</strong> localização de expressão regular que corresponder ao URI de solicitação é selecionada imediatamente para atender à solicitação.</li>
<li>Se nenhuma localização de expressão regular que corresponda ao URI de solicitação for encontrada, a localização de prefixo armazenada anteriormente será selecionada para atender à solicitação.</li>
</ul>

<p>É importante entender que, por padrão, o Nginx atenderá correspondências por expressão regular preferencialmente em relação a correspondências por prefixo.  No entanto, ele <em>avalia</em> primeiro as localizações de prefixos, permitindo que o administrador altere essa tendência especificando localizações usando os modificadores <code>=</code> e <code>^~</code>.</p>

<p>Também é importante notar que, embora as localizações de prefixo geralmente selecionarem com base na correspondência mais longa e específica, a avaliação de expressões regulares é interrompida quando a primeira localização correspondente é encontrada.  Isso significa que o posicionamento dentro da configuração tem vastas implicações para as localizações de expressões regulares.</p>

<p>Por fim, é importante entender que as correspondências por expressões regulares <em>dentro</em> da correspondência de prefixo mais longa “passam à frente” quando o Nginx avalia as localizações de regex.  Elas serão avaliadas, por ordem, antes de qualquer uma das outras correspondências por expressão regular serem consideradas.  Maxim Dounin, um desenvolvedor do Nginx incrivelmente prestativo, explica <a href="https://www.ruby-forum.com/topic/4422812#1136698">nesta postagem</a> essa parte do algoritmo de seleção.</p>

<h3 id="quando-a-avaliação-de-blocos-de-localização-salta-para-outras-localizações">Quando a avaliação de blocos de localização salta para outras localizações?</h3>

<p>De maneira geral, quando um bloco de localização é selecionado para atender a uma solicitação, a solicitação é manuseada inteiramente dentro desse contexto a partir daquele ponto.  Apenas a localização selecionada e as diretivas herdadas determinam como a solicitação é processada, sem a interferência de blocos de localização irmãos.</p>

<p>Embora essa seja uma regra geral que permite projetar seus blocos de localização de maneira previsível, é importante perceber que há momentos em que uma nova procura de localização é acionada por determinadas diretivas dentro da localização selecionada.  As exceções à regra de “apenas um bloco de localização” podem ter implicações sobre como a solicitação é realmente atendida e podem não se alinhar com as expectativas que você tinha quando projetou seus blocos de localização.</p>

<p>Algumas diretivas que podem levar a esse tipo de redirecionamento interno são:</p>

<ul>
<li><strong>index</strong></li>
<li><strong>try_files</strong></li>
<li><strong>rewrite</strong></li>
<li><strong>error_page</strong></li>
</ul>

<p>Vamos analisa-los brevemente.</p>

<p>A diretiva <code>index</code> sempre leva a um redirecionamento interno se for usada para processar a solicitação.  As correspondências exatas de localização são frequentemente usadas para acelerar o processo de seleção por finalizarem imediatamente a execução do algoritmo.  No entanto, se você fizer uma correspondência exata de localização que é um <em>diretório</em>, existe uma boa chance de a solicitação ser redirecionada para uma localização diferente para ser de fato processada.</p>

<p>Neste exemplo, a primeira localização corresponde a um URI de solicitação <code>/exact</code>, mas, para manusear a solicitação, a diretiva <code>index</code> herdada pelo bloco inicia um redirecionamento interno para o segundo bloco:</p>
<pre class="code-pre "><code>index index.html;

location = /exact {

    . . .

}

location / {

    . . .

}
</code></pre>
<p>No caso acima, se fosse realmente necessário que a execução permanecesse no primeiro bloco, você precisaria escolher um método diferente para satisfazer a solicitação para o diretório.  Por exemplo, você poderia definir um <code>index</code> inválido para esse bloco e ativar o <code>autoindex</code>:</p>
<pre class="code-pre "><code>location = /exact {
    index nothing_will_match;
    autoindex on;
}

location  / {

    . . .

}
</code></pre>
<p>Essa é uma maneira de impedir que o <code>index</code> mude os contextos, mas provavelmente não é útil para a maioria das configurações.  Geralmente, uma correspondência exata nos diretórios pode ser útil para coisas como reescrever a solicitação (que também resulta em uma nova pesquisa de localização).</p>

<p>Outra situação onde a localização de processamento pode ser reavaliada é com a diretiva <code>try_files</code>.  Essa diretiva diz ao Nginx para verificar a existência de um conjunto de arquivos ou diretórios nomeados.  O último parâmetro pode ser um URI para o qual o Nginx fará um redirecionamento interno.</p>

<p>Considere a configuração a seguir:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>No exemplo acima, se uma solicitação for feita para <code>/blahblah</code>, a primeira localização inicialmente receberá a solicitação.  Ele tentará encontrar um arquivo chamado <code>blahblah</code> no diretório <code>/var/www/main</code>.  Se ele não puder encontrar um, ele seguirá procurando por um arquivo chamado <code>blahblah.html</code>.  Em seguida, ele tentará ver se há um diretório chamado <code>blahblah/</code> dentro do diretório <code>/var/www/main</code>.  Se todas essas tentativas falharem, ele redirecionará para <code>/fallback/index.html</code>.  Isso desencadeará outra pesquisa de localização que será capturada pelo segundo bloco de localização.  Isso atenderá o arquivo <code>/var/www/another/fallback/index.html</code>.</p>

<p>Outra diretiva que pode levar a uma mudança de bloco de localização é a <code>rewrite</code>.  Quando se usa o <code>último</code> parâmetro com a diretiva <code>rewrite</code>, ou quando não se usa nenhum parâmetro, o Nginx procurará uma nova localização correspondente com base nos resultados de rewrite.</p>

<p>Por exemplo, se modificarmos o último exemplo para incluir uma rewrite, é possível ver que a solicitação às vezes é passada diretamente para a segunda localização sem depender da diretiva <code>try_files</code>:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    rewrite ^/rewriteme/(.*)$ /$1 last;
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>No exemplo acima, uma solicitação para <code>/rewriteme/hello</code> será manuseada inicialmente por um primeiro bloco de localização.  Ela será reescrita para <code>/hello</code> e uma localização será pesquisada.  Neste caso, ela corresponderá novamente à primeira localização e será processada pelo <code>try_files</code> como de costume, talvez retornando para <code>/fallback/index.html</code> se nada for encontrado (usando o redirecionamento interno do <code>try_files</code> que discutimos acima).</p>

<p>No entanto, se uma solicitação for feita para <code>/rewriteme/fallback/hello</code>, o primeiro bloco novamente corresponderá.  A diretiva rewrite é aplicada novamente, dessa vez resultando em <code>/fallback/hello</code>.  A solicitação será então atendida a partir do segundo bloco de localização.</p>

<p>Uma situação relacionada acontece com a diretiva <code>return</code> ao enviar os códigos de status <code>301</code> ou <code>302</code>.  Neste caso, a diferença é que isso resulta em uma solicitação inteiramente nova na forma de um redirecionamento visível externamente.  Essa mesma situação pode ocorrer com a diretiva <code>rewrite</code> ao usar os sinalizadores <code>redirect</code> ou <code>permanent</code>.  No entanto, essas pesquisas de localização não devem ser inesperadas, uma vez que os redirecionamentos visíveis externamente sempre resultam em uma nova solicitação.</p>

<p>A diretiva <code>error_page</code> pode levar a um redirecionamento interno parecido com aquele criado por <code>try_files</code>.  Essa diretiva é usada para definir o que deve acontecer quando certos códigos de status são encontrados.  Isso provavelmente nunca será executado se o <code>try_files</code> estiver definido, já que essa diretiva manuseia todo o ciclo de vida de uma solicitação.</p>

<p>Considere este exemplo:</p>
<pre class="code-pre "><code>root /var/www/main;

location / {
    error_page 404 /another/whoops.html;
}

location /another {
    root /var/www;
}
</code></pre>
<p>Todas as solicitações (que não sejam aquelas que começam com <code>/another</code>) serão manuseadas pelo primeiro bloco, que atenderá arquivos em <code>/var/www/main</code>.  No entanto, se um arquivo não for encontrado (um status 404), um redirecionamento interno para <code>/another/whoops.html</code> ocorrerá, levando a uma nova pesquisa de localização que eventualmente chegará no segundo bloco.  Este arquivo será atendido por <code>/var/www/another/whoops.html</code>.</p>

<p>Como se vê, entender as circunstâncias nas quais o Nginx aciona uma nova pesquisa de localização pode ajudar a prever o comportamento que você verá ao fazer solicitações.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Compreender as maneiras como o Nginx processa as solicitações de clientes pode tornar seu trabalho como um administrador muito mais fácil.  Você será capaz de saber qual bloco de servidor o Nginx selecionará com base em cada solicitação de cliente.  Você também será capaz de dizer como o bloco de localização será selecionado com base no URI de solicitação.  De maneira geral, saber como o Nginx seleciona diferentes blocos lhe dará a capacidade de rastrear os contextos que o Nginx aplicará para atender a cada solicitação.</p>
