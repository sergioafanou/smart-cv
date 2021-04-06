---
title : "Como usar a Fetch API do JavaScript para buscar dados"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-fetch-api-to-get-data-pt
image: "https://sergio.afanou.com/assets/images/image-midres-29.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Houve um tempo em que o <code>XMLHttpRequest</code> era usado para fazer solicitações de API. Ele não incluía promessas e não gerava um código JavaScript organizado. Ao usar o jQuery, usava-se a sintaxe mais organizada com o <code>jQuery.ajax()</code>.</p>

<p>Agora, o JavaScript tem sua própria maneira integrada de fazer solicitações de API. Isso é feito pela Fetch API, um novo padrão para fazer solicitações de servidor com promessas, que inclui também muitas outras funcionalidades.</p>

<p>Neste tutorial, você criará solicitações GET e POST usando a Fetch API.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para concluir este tutorial, você precisará do seguinte:</p>

<ul>
<li>A versão mais recente do Node instalada em sua máquina. Para instalar o Node no macOS, siga os passos descritos neste tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">Como instalar o Node.js e criar um ambiente de desenvolvimento local no macOS</a>.</li>
<li>Uma compreensão básica de programação em JavaScript. Aprenda mais sobre isso com a série <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">Como programar em JavaScript</a>.</li>
<li>Uma certa compreensão sobre promessas em JavaScript. Leia a <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">seção de promessas</a> deste artigo sobre <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">o loop de eventos, callbacks, promessas e async/await</a> em JavaScript.</li>
</ul>

<h2 id="passo-1-—-começando-com-a-sintaxe-da-fetch-api">Passo 1 — Começando com a sintaxe da Fetch API</h2>

<p>Para usar a Fetch API, chame o método <code>fetch</code>, que aceita a URL da API como um parâmetro:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre>
<p>Após o método <code>fetch()</code>, inclua o método de promessa <code>then()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function() {

})
</code></pre>
<p>O método <code>fetch()</code> retorna uma promessa. Se a promessa retornada for <code>resolve</code>, a função dentro do método <code>then()</code> é executada. Essa função contém o código para lidar com os dados recebidos da API.</p>

<p>Abaixo do método <code>then()</code>, inclua o método <code>catch()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function() {

});
</code></pre>
<p>A API chamada usando <code>fetch()</code> pode estar inoperante ou outros erros podem ocorrer. Se isso acontecer, a promessa <code>reject</code> será retornada. O método <code>catch</code> é usado para lidar com <code>reject</code>. O código dentro de <code>catch()</code> será executado se um erro ocorrer ao chamar a API escolhida.</p>

<p>Resumindo, usar a Fetch API será parecido com isto:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then(function() {

})
.catch(function() {

});
</code></pre>
<p>Com uma compreensão da sintaxe para usar a Fetch API, agora siga em frente para usar <code>fetch()</code> em uma API real.</p>

<h2 id="passo-2-—-usando-fetch-para-buscar-dados-de-uma-api">Passo 2 — Usando Fetch para buscar dados de uma API</h2>

<p>As amostras de código a seguir baseiam-se na <a href="https://randomuser.me">Random User API</a>. Usando a API, você irá buscar dez usuários e os exibirá na página usando o JavaScript puro.</p>

<p>A ideia é obter todos os dados da Random User API e exibí-los em itens de lista dentro da lista de autores. Comece criando um arquivo HTML e adicionando um cabeçalho e uma lista não ordenada com o <code>id</code> de <code>authors</code>:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;h1&gt;Authors&lt;/h1&gt;
&lt;ul id="authors"&gt;&lt;/ul&gt;
</code></pre>
<p>Agora, adicione identificadores <code>script</code> no final do seu arquivo HTML e use um seletor DOM para pegar o <code>ul</code>. Utilize <code>getElementById</code> com <code>authors</code> como o argumento. Lembre-se, <code>authors</code> é o <code>id</code> para o <code>ul</code> criado anteriormente:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;

    const ul = document.getElementById('authors');

&lt;/script&gt;
</code></pre>
<p>Crie uma variável constante chamada <code>url</code> que armazenará o URL da API que irá retornar dez usuários aleatórios:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api/?results=10';
</code></pre>
<p>Com <code>ul</code> e <code>url</code> instalados, é hora de criar as funções que serão usadas para criar os itens de lista. Crie uma função chamada <code>createNode</code> que recebe um parâmetro chamado <code>element</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {

}
</code></pre>
<p>Mais tarde, quando o <code>createNode</code> for chamado, será necessário passar o nome de um elemento HTML real a ser criado.</p>

<p>Dentro da função, adicione uma instrução <code>return</code> que retorna o <code>element</code> usando <code>document.createElement()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}
</code></pre>
<p>Também será necessário criar uma função chamada <code>append</code> que recebe dois parâmetros: <code>parent</code> e <code>el</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {

}
</code></pre>
<p>Essa função irá acrescentar <code>el</code> ao <code>parent</code> usando o <code>document.createElement</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {
    return parent.appendChild(el);
}
</code></pre>
<p>Tanto o <code>createNode</code> quanto o <code>append</code> estão prontos para o uso. Agora, com a Fetch API, chame a Random User API usando <code>fetch()</code> com <code>url</code> como o argumento:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre><pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
  .then(function(data) {

    })
  })
  .catch(function(error) {

  });
</code></pre>
<p>No código acima, você está chamando a Fetch API e passando o URL para a Random User API. Então, uma resposta é recebida. No entanto, a resposta recebida não é JSON, mas um objeto com uma série de métodos que podem ser usados dependendo do que você quer fazer com as informações. Para converter o objeto retornado em JSON, use o método <code>json()</code>.</p>

<p>Adicione o método <code>then()</code>, que irá conter uma função com um parâmetro chamado <code>resp</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; )
</code></pre>
<p>O parâmetro <code>resp</code> recebe o valor do objeto retornado de <code>fetch(url)</code>. Use o método <code>json()</code> para converter <code>resp</code> em dados JSON:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; resp.json())
</code></pre>
<p>Os dados JSON ainda precisam ser processados. Adicione outra instrução <code>then()</code> com uma função que tem um argumento chamado <code>data</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {

    })
})
</code></pre>
<p>Dentro dessa função, crie uma variável chamada <code>authors</code> que seja definida igual à <code>data.results</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {
    let authors = data.results;
</code></pre>
<p>Para cada autor em <code>authors</code>, será criado um item de lista que exibe uma figura o nome deles. O método <code>map()</code> é ótimo para isso:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let authors = data.results;
return authors.map(function(author) {

})
</code></pre>
<p>Dentro de sua função <code>map</code>, crie uma variável chamada <code>li</code> que será definida igual a <code>createNode</code> com <code>li</code> (o elemento HTML) como o argumento:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">return authors.map(function(author) {
    let li = createNode('li');
})
</code></pre>
<p>Repita isso para criar um elemento <code>span</code> e um elemento <code>img</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let li = createNode('li');
let img = createNode('img');
let span = createNode('span');
</code></pre>
<p>A API oferece um nome para o author e uma imagem que acompanha o nome. Defina <code>img.src</code> para a imagem do autor:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let img = createNode('img');
let span = createNode('span');

img.src = author.picture.medium;
</code></pre>
<p>O elemento <code>span</code> deve conter o primeiro e último nome de <code>author</code>. A propriedade <code>innerHTML</code> e a interpolação de strings permitirão fazer isso:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">img.src = author.picture.medium;
span.innerHTML = `${author.name.first} ${author.name.last}`;
</code></pre>
<p>Com a imagem e o elemento de lista criados juntamente com o elemento <code>span</code>, use a função <code>append</code> criada anteriormente para exibir esses elementos na página:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">append(li, img);
append(li, span);
append(ul, li);
</code></pre>
<p>Com ambas as funções <code>then()</code> concluídas, adicione agora a função <code>catch()</code>. Essa função irá registrar o erro em potencial no console:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Este é o código completo da solicitação que você criou:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}

function append(parent, el) {
  return parent.appendChild(el);
}

const ul = document.getElementById('authors');
const url = 'https://randomuser.me/api/?results=10';

fetch(url)
.then((resp) =&gt; resp.json())
.then(function(data) {
  let authors = data.results;
  return authors.map(function(author) {
    let li = createNode('li');
    let img = createNode('img');
    let span = createNode('span');
    img.src = author.picture.medium;
    span.innerHTML = `${author.name.first} ${author.name.last}`;
    append(li, img);
    append(li, span);
    append(ul, li);
  })
})
.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Você acabou de realizar uma solicitação GET com sucesso usando a Random User API e a Fetch API. No próximo passo, irá aprender como realizar solicitações POST.</p>

<h2 id="passo-3-—-lidando-com-solicitações-post">Passo 3 — Lidando com solicitações POST</h2>

<p>A Fetch usa por padrão solicitações GET, mas é possível usar qualquer outro tipo de solicitação, alterar os cabeçalhos e enviar os dados. Para fazer isso, é necessário definir seu objeto e passá-lo como o segundo argumento da função fetch.</p>

<p>Antes de criar uma solicitação POST, crie os dados que gostaria de enviar à API. Este será um objeto chamado <code>data</code> com a chave <code>name</code> e o valor <code>Sammy</code> (ou seu nome):</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sammy'
}
</code></pre>
<p>Certifique-se de incluir uma variável constante que contém o link da Random User API.</p>

<p>Como esta é uma solicitação POST, será necessário declarar isso explicitamente. Crie um objeto chamado <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {

}
</code></pre>
<p>Esse objeto precisa incluir três chaves: <code>method</code>, <code>body</code> e <code>headers</code>. A chave <code>method</code> deve conter o valor <code>'POST'</code>. <code>body</code> deve ser definido igual ao objeto <code>data</code> que acabou de ser criado. <code>headers</code> deve conter o valor de <code>new Headers()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {
  method: 'POST',
  body: data,
  headers: new Headers()
}
</code></pre>
<p>A interface <code>Headers</code>, que é uma propriedade da Fetch API, permite realizar várias ações em cabeçalhos de solicitação HTTP e de resposta. Se quiser aprender mais sobre isso, este artigo chamado <a href="https://www.digitalocean.com/community/tutorials/nodejs-express-routing">Como definir rotas e métodos de solicitação HTTP no Express</a> pode oferecer-lhe mais informações.</p>

<p>Com esse código no lugar, a solicitação POST pode ser feita usando a Fetch API. Você incluirá <code>url</code> e <code>fetchData</code> como argumentos para sua solicitação POST <code>fetch</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
</code></pre>
<p>A função <code>then()</code> irá incluir o código que lida com a resposta recebida do servidor da Random User API:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
.then(function() {
    // Handle response you get from the server
});
</code></pre>
<p>Para criar um objeto e usar a função <code>fetch()</code>, há também outra opção. Ao invés de criar um objeto como o <code>fetchData</code>, é possível usar o construtor de solicitações para criar seu objeto de solicitação. Para fazer isso, crie uma variável chamada <code>request</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request =
</code></pre>
<p>A variável <code>request</code> deve ser definida igual a <code>new Request</code>. O constructo <code>new Request</code> recebe dois argumentos: a url da API (<code>url</code>) e um objeto. O objeto também deve incluir as chaves <code>method</code>, <code>body</code> e <code>headers</code> assim como o <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});
</code></pre>
<p>Agora, <code>request</code> pode ser usado como o argumento único para o <code>fetch()</code>, uma vez que ele também inclui a url da API:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>No conjunto, seu código ficará semelhante a este:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});

fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>Agora, você conhece dois métodos para criar e executar solicitações POST com a Fetch API.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Embora a Fetch API ainda não seja suportada por todos os navegadores, é uma ótima alternativa ao XMLHttpRequest. Se quiser aprender como chamar APIs da Web usando o React, <a href="https://www.digitalocean.com/community/tutorials/how-to-call-web-apis-with-the-useeffect-hook-in-react">confira este artigo</a> sobre este tópico.</p>
