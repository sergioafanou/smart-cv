---
title : "Comment utiliser l'API Fetch de JavaScript pour récupérer des données"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-fetch-api-to-get-data-fr
image: "https://sergio.afanou.com/assets/images/image-midres-14.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Il fut un temps où l'on utilisait <code>XMLHttpRequest</code> pour créer des requêtes API. Il n'intégrait pas les promesses et n'était pas conçu pour générer des codes JavaScript propres. En utilisant jQuery, vous avez utilisé la syntaxe de nettoyage avec <code>jQuery.ajax()</code>.</p>

<p>Maintenant, JavaScript intègre un moyen qui lui est propre de créer des requêtes API. Il s'agit de l'API Fetch, une nouvelle norme qui permet de réaliser des requêtes sur un serveur en utilisant des promesses. Elle intègre cependant bien d'autres fonctionnalités.</p>

<p>Au cours de ce tutoriel, vous allez apprendre à utiliser API Fetch pour réaliser des requêtes GET et POST.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour terminer ce tutoriel, vous aurez besoin des éléments suivants :</p>

<ul>
<li>La dernière version de Node installée sur votre machine. Pour installer Node sur macOS, suivez les étapes décrites dans le tutoriel suivant : <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">Comment installer Node.js et créer un environnement de développement local sur macOS</a>.</li>
<li>Des notions de base en codage JavaScript, que vous pouvez acquérir en étudiant la série nommée : <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">Comment coder en JavaScript</a>.</li>
<li>Des notions sur les promesses en JavaScript. Lisez la <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">section Promesses</a> de cet article qui traite de <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">la boucle des événements, des rappels, les promesses et async/await</a> en JavaScript.</li>
</ul>

<h2 id="Étape-1-—-premiers-pas-avec-la-syntaxe-de-l-39-api-fetch">Étape 1 — Premiers pas avec la syntaxe de l'API Fetch</h2>

<p>Pour utiliser l'API Fetch, appelez la méthode <code>fetch</code>, qui accepte l'URL de l'API comme paramètre :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre>
<p>Après la méthode <code>fetch()</code>, ajoutez la méthode de promesse <code>then()</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function() {

})
</code></pre>
<p>La méthode <code>fetch()</code> retourne une promesse. Si la promesse renvoyée est <code>resolve</code>, cela signifie que la fonction dans la méthode <code>then()</code> est bien exécutée. Cette fonction contient le code qui permet de traiter les données reçues à partir de l'API.</p>

<p>Sous la méthode <code>then()</code>, ajoutez la méthode <code>catch()</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function() {

});
</code></pre>
<p>Il se peut que l'API que vous appelez en utilisant <code>fetch()</code> soit défaillante ou que d'autres erreurs se produisent. Le cas échéant, le système renverra la promesse <code>reject</code>. Vous pouvez utiliser la méthode <code>cath</code> pour gérer <code>reject</code>. Le code dans <code>catch()</code> sera exécuté dans le cas où une erreur se produit lors de l'appel de l'API de votre choix.</p>

<p>Pour résumer, l'utilisation de l'API Fetch ressemblera à l'exemple suivant :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then(function() {

})
.catch(function() {

});
</code></pre>
<p>En ayant une compréhension de la syntaxe qui permet d'utiliser l'API Fetch, vous pouvez désormais passer à l'utilisation de <code>fetch()</code> sur une API réelle.</p>

<h2 id="Étape-2-—-utilisation-de-fetch-pour-obtenir-des-données-à-partir-d-39-une-api">Étape 2 — Utilisation de Fetch pour obtenir des données à partir d'une API</h2>

<p>Les échantillons de code suivants seront basés sur l&rsquo;<a href="https://randomuser.me">API Random User</a>. En utilisant l'API, vous obtiendrez dix utilisateurs que vous pourrez afficher sur la page en utilisant Vanilla JavaScript.</p>

<p>L'idée est de récupérer toutes les données de l'API Random User et de les afficher dans les éléments de la liste de l'auteur. Commencez par créer un fichier HTML, ajoutez un titre et une liste non ordonnée avec l&rsquo;<code>id</code> des <code>authors</code> :</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;h1&gt;Authors&lt;/h1&gt;
&lt;ul id="authors"&gt;&lt;/ul&gt;
</code></pre>
<p>Ajoutez maintenant les balises <code>script</code> au bas de votre fichier HTML et utilisez un sélecteur DOM pour récupérer l&rsquo;<code>ul</code>. Utilisez l'argument <code>getElementById</code> avec <code>authors</code>. N'oubliez pas, <code>authors</code> est l&rsquo;<code>id</code> de l&rsquo;<code>ul</code> précédemment créé :</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;

    const ul = document.getElementById('authors');

&lt;/script&gt;
</code></pre>
<p>Créez une variable constante nommée <code>url</code> qui contiendra l'URL de l'API qui renverra dix utilisateurs aléatoires :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api/?results=10';
</code></pre>
<p>Une fois que <code>ul</code> et <code>url</code> sont créés, il est temps pour vous de créer les fonctions qui serviront à créer les éléments de la liste. Créez une fonction appelée <code>createNode</code> qui prend un paramètre appelé <code>element</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {

}
</code></pre>
<p>Plus tard, lorsque vous appellerez <code>createNode</code>, vous devrez transmettre le nom d'un élément HTML réel à créer.</p>

<p>Dans la fonction, ajoutez une instruction <code>return</code> qui renvoie <code>element</code> en utilisant <code>document.createElement()</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}
</code></pre>
<p>En outre, vous devrez créer une fonction appelée <code>append</code> qui intègre deux paramètres : <code>parent</code> et <code>el</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {

}
</code></pre>
<p>Cette fonction ajoutera <code>el</code> au <code>parent</code> en utilisant <code>document.createElement</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {
    return parent.appendChild(el);
}
</code></pre>
<p><code>createNode</code> et <code>append</code> sont tous deux prêts à être utilisés. Maintenant, en utilisant l'API Fetch, appelez l'API Random User en utilisant <code>fetch()</code> avec l'argument <code>url</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre><pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
  .then(function(data) {

    })
  })
  .catch(function(error) {

  });
</code></pre>
<p>Dans le code ci-dessus, vous appelez l'API Fetch et passez l'URL à l'API Random User. Ensuite, vous recevrez une réponse. Cependant, la réponse que vous obtenez n'est pas JSON, mais plutôt un objet avec une série de méthodes utilisables en fonction de ce que vous voulez faire avec les informations. Pour convertir l'objet renvoyé en JSON, utilisez la méthode <code>json()</code>.</p>

<p>Ajoutez la méthode <code>then()</code> qui contiendra une fonction avec un paramètre appelé <code>resp</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; )
</code></pre>
<p>Le paramètre <code>resp</code> prend la valeur de l'objet renvoyé de <code>fetch(url)</code>. Utilisez la méthode <code>json()</code> pour convertir <code>resp</code> en données JSON :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; resp.json())
</code></pre>
<p>Il vous reste encore à traiter les données JSON. Ajoutez une autre instruction <code>then()</code> avec une fonction dont l'argument s'appelle <code>data</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {

    })
})
</code></pre>
<p>Dans cette fonction, créez une variable appelée <code>authors</code>, qui est définie comme égale à <code>data.results</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {
    let authors = data.results;
</code></pre>
<p>Pour chaque auteur dans <code>authors</code>, il vous faudra créer un élément de liste qui affiche leur photographie et leur nom respectifs. La méthode <code>map()</code> est idéale pour faire cela :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let authors = data.results;
return authors.map(function(author) {

})
</code></pre>
<p>Dans votre fonction <code>map</code>, créez une variable appelée <code>li</code> qui sera définie comme égale à <code>createNode</code> avec <code>li</code> (l'élément HTML) comme argument :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">return authors.map(function(author) {
    let li = createNode('li');
})
</code></pre>
<p>Répétez cette manœuvre pour créer un élément <code>span</code> et un élément <code>img</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let li = createNode('li');
let img = createNode('img');
let span = createNode('span');
</code></pre>
<p>L'API propose un nom pour l'auteur et une image qui accompagne le nom. Définissez l&rsquo;<code>img.src</code> sur l'image de l'auteur :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let img = createNode('img');
let span = createNode('span');

img.src = author.picture.medium;
</code></pre>
<p>L'élément <code>span</code> doit contenir le prénom et le nom de famille de l&rsquo;<code>auteur</code>. Pour cela, utilisez la propriété <code>innerHTML</code> et l'interpolation des chaînes de caractères :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">img.src = author.picture.medium;
span.innerHTML = `${author.name.first} ${author.name.last}`;
</code></pre>
<p>Une fois l'image et l'élément de liste créés avec l'élément <code>span</code>, vous pouvez utiliser la fonction <code>append</code> précédemment créée pour afficher ces éléments sur la page :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">append(li, img);
append(li, span);
append(ul, li);
</code></pre>
<p>Une fois les deux fonctions <code>then()</code> terminées, vous pouvez maintenant ajouter la fonction <code>catch()</code>. Cette fonction enregistrera l'erreur potentielle sur la console :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Voici le code complet de la requête que vous avez créée :</p>
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
<p>Vous avez réussi à réaliser une requête GET en utilisant l'API Random User et l'API Fetch. Au cours de la prochaine étape, vous allez apprendre à réaliser des requêtes POST.</p>

<h2 id="Étape-3-—-traitement-des-requêtes-post">Étape 3 — Traitement des requêtes POST</h2>

<p>Fetch utilise par défaut les requêtes GET, mais vous pouvez utiliser tous les autres types de requêtes, changer les en-têtes et envoyer des données. Pour ce faire, vous devez configurer votre objet et le passer comme le deuxième argument de la fonction fetch.</p>

<p>Avant de créer une requête POST, créez les données que vous souhaitez envoyer à l'API. Il s'agira d'un objet appelé <code>data</code> avec le <code>name</code> clé et la valeur <code>Sammy</code> (ou votre nom) :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sammy'
}
</code></pre>
<p>Veillez à bien inclure une variable constante qui contient le lien vers l'API Random User.</p>

<p>Comme il s'agit d'une requête POST, vous devrez l'indiquer de manière explicite. Créez un objet appelé <code>fetchData</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {

}
</code></pre>
<p>Cet objet doit inclure trois clés : <code>method</code>, <code>body</code> et <code>headers</code>. La clé <code>method</code> doit avoir la valeur <code>'POST'</code>. <code>body</code> doit être défini comme égal à l'objet <code>data</code> qui vient d'être créé. <code>headers</code> devrait avoir la valeur des <code>new Headers()</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {
  method: 'POST',
  body: data,
  headers: new Headers()
}
</code></pre>
<p>L'interface <code>Headers</code> est une propriété de l'API Fetch qui vous permet d'effectuer diverses actions sur les en-têtes des requêtes et des réponses HTTP. Si vous souhaitez en savoir plus à ce sujet, vous pourrez trouver de plus amples informations dans l'article suivant : <a href="https://www.digitalocean.com/community/tutorials/nodejs-express-routing">Comment définir les routes et les méthodes de requête HTTP dans Express</a>.</p>

<p>Une fois ce code en place, vous pouvez réaliser la requête POST en utilisant l'API Fetch. Vous allez inclure <code>url</code> et <code>fetchData</code> comme arguments pour votre requête POST <code>fetch</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
</code></pre>
<p>La fonction <code>then()</code> comprendra un code qui gère la réponse reçue du serveur API Random User :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
.then(function() {
    // Handle response you get from the server
});
</code></pre>
<p>Vous disposez également d'une autre option pour créer un objet et utiliser la fonction <code>fetch()</code>. Au lieu de créer un objet comme <code>fetchData</code>, vous pouvez utiliser le constructeur de requêtes pour créer votre objet de requête. Pour ce faire, créez une variable appelée <code>request</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request =
</code></pre>
<p>La variable <code>request</code> doit être configurée comme <code>new Request</code>. La construction <code>New Request</code> prend deux arguments : l'url de l'API url (<code>url</code>) et un objet. L'objet doit également inclure les clés <code>method</code>, <code>body</code> et <code>headers</code> tout comme <code>fetchData</code> :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});
</code></pre>
<p>Maintenant, vous pouvez utiliser <code>request</code> comme le seul argument de <code>fetch()</code> car il inclut également l'url de l'API :</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>L'ensemble de votre code ressemblera à ce qui suit :</p>
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
<p>Vous connaissez maintenant deux méthodes qui vous permettront de créer et d'exécuter des requêtes POST avec l'API Fetch.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Même si la plupart des navigateurs ne prennent pas l'API Fetch en charge, il s'agit d'une superbe alternative à XMLHttpRequest. Si vous souhaitez apprendre à appeler les API Web en utilisant React, <a href="https://www.digitalocean.com/community/tutorials/how-to-call-web-apis-with-the-useeffect-hook-in-react">consultez cet article</a> sur ce même sujet.</p>
