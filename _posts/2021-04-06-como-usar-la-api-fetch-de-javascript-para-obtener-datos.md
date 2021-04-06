---
title : "Cómo usar la API Fetch de JavaScript para obtener datos"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-fetch-api-to-get-data-es
image: "https://sergio.afanou.com/assets/images/image-midres-6.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Hubo un tiempo en el que se utilizaba <code>XMLHttpRequest</code> para realizar solicitudes de API. No incluía promesas y no permitía un código JavaScript limpio. Al usar jQuery, se utilizó la sintaxis más limpia con <code>jQuery.ajax()</code>.</p>

<p>Ahora, JavaScript tiene su propia forma integrada de hacer solicitudes de API. Esta es la API Fetch, un nuevo estándar para realizar solicitudes de servidor con promesas, pero incluye muchas otras funciones.</p>

<p>En este tutorial, creará solicitudes GET y POST usando la API Fetch.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para completar este tutorial, necesitará lo siguiente:</p>

<ul>
<li>La última versión de Node instalada en su computadora. Para instalar Node en macOS, siga los pasos descritos en este tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">Cómo instalar Node.js y crear un entorno de desarrollo local en macOS</a>.</li>
<li>Un entendimiento básico de la codificación en JavaScript, acerca de la cual puede obtener más información en la serie <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">Cómo codificar en JavaScript</a>.</li>
<li>Un entendimiento de las promesas en JavaScript. Lea la <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">sección Promesas</a> de este artículo sobre <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">el bucle de eventos, devoluciones de llamada, promesas y async/await</a> en JavaScript.</li>
</ul>

<h2 id="paso-1-introducción-a-la-sintaxis-de-la-api-fetch">Paso 1: Introducción a la sintaxis de la API Fetch</h2>

<p>Para usar la API Fetch, invoque el método <code>fetch</code>, que acepta la URL de la API como parámetro:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre>
<p>Después del método <code>fetch()</code>, incluya el método de promesa <code>then()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function() {

})
</code></pre>
<p>Con el método <code>fetch()</code> se obtiene una promesa. Si la promesa devuelta es <code>resolve</code>, se ejecuta la función dentro del método <code>then()</code>. Esa función contiene el código para gestionar los datos recibidos de la API.</p>

<p>Debajo del método <code>then()</code>, incluya el método <code>catch()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function() {

});
</code></pre>
<p>La API que invocó mediante <code>fetch()</code> puede estar inactiva o pueden producirse otros errores. Si esto sucede, se mostrará la promesa de <code>reject</code>. El método <code>catch</code> se usa para gestionar <code>reject</code>. El código dentro de <code>catch()</code> se ejecutará si se produce un error al invocar la API de su elección.</p>

<p>Para resumir, usar la API Fetch se verá así:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then(function() {

})
.catch(function() {

});
</code></pre>
<p>Con un conocimiento sobre la sintaxis para usar la API Fetch, ahora puede continuar usando <code>fetch()</code> en una API verdadera.</p>

<h2 id="paso-2-usar-fetch-para-obtener-datos-de-una-api">Paso 2: Usar Fetch para obtener datos de una API</h2>

<p>Las siguientes muestras de código se basarán en la <a href="https://randomuser.me">API de Random User</a>. Al usar la API, obtendrá diez usuarios y se mostrarán en la página usando Vanilla JavaScript.</p>

<p>La idea es obtener todos los datos de la API de Random User y mostrarlos en los elementos de la lista dentro de la lista del autor. Primero cree un archivo HTML y añada un encabezado y una lista desordenada con la <code>id</code> de <code>authors:</code></p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;h1&gt;Authors&lt;/h1&gt;
&lt;ul id="authors"&gt;&lt;/ul&gt;
</code></pre>
<p>Ahora, añada las etiquetas <code>script</code> a la parte inferior de su archivo HTML y utilice un selector DOM para capturar la <code>ul</code>. Use <code>getElementById</code> con <code>authors</code> como argumento. Recuerde que <code>authors</code> es el <code>id</code> para la <code>ul</code> creada anteriormente:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;

    const ul = document.getElementById('authors');

&lt;/script&gt;
</code></pre>
<p>Cree una variable constante llamada <code>url</code> que albergará la URL de la API que mostrará diez usuarios al azar:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api/?results=10';
</code></pre>
<p>Habiendo ya establecido <code>ul</code> y <code>url</code>, es momento de crear las funciones que se utilizarán para crear los elementos de la lista. Cree una función llamada <code>createNode</code> que tome un parámetro llamado <code>element</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {

}
</code></pre>
<p>Más tarde, cuando se invoque <code>createNode</code>, deberá pasar el nombre de un elemento HTML real para crear.</p>

<p>Dentro de la función, añada la instrucción <code>return</code> que devuelve el <code>elemento</code> usando <code>document.createElement()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}
</code></pre>
<p>También necesitará crear una función llamada <code>append</code> que tome dos parámetros: <code>parent</code> y <code>el</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {

}
</code></pre>
<p>Esta función añadirá <code>el</code> a <code>parent</code> usando <code>document.createElement</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {
    return parent.appendChild(el);
}
</code></pre>
<p>Tanto <code>CreateNode</code> como <code>append</code> están listos para funcionar. Ahora usando la API Fetch, invoque la API de Random User usando <code>fetch()</code> con <code>url</code> como argumento:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre><pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
  .then(function(data) {

    })
  })
  .catch(function(error) {

  });
</code></pre>
<p>En el código anterior, está invocando la API Fetch y pasando la URL a la API de Random User. Luego, se recibe una respuesta. Sin embargo, la respuesta que se obtiene no es JSON, sino un objeto con una serie de métodos que pueden usarse dependiendo de lo que se quiera hacer con la información. Para convertir el objeto devuelto en JSON, utilice el método <code>json()</code>.</p>

<p>Añada el método <code>then()</code> que contendrá una función con un parámetro llamado <code>resp</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; )
</code></pre>
<p>El parámetro <code>resp</code> toma el valor del objeto devuelto de <code>fetch(url)</code>. Utilice el método <code>json()</code> para convertir <code>resp</code> en datos JSON:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; resp.json())
</code></pre>
<p>Aún se deben procesar los datos JSON. Añada otra instrucción <code>then()</code> con una función que tiene un argumento llamado <code>data</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {

    })
})
</code></pre>
<p>Dentro de esta función, cree una variable llamada <code>authors</code> que se establece igual a <code>data.results</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {
    let authors = data.results;
</code></pre>
<p>Para cada autor en <code>authors</code>, le convendrá crear un elemento de lista que muestre una imagen de ellos y muestre su nombre. El método <code>map()</code> funciona muy bien para esto:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let authors = data.results;
return authors.map(function(author) {

})
</code></pre>
<p>Con su función de <code>map</code>, cree una variable llamada <code>li</code> que se establecerá igual a <code>createNode</code> con <code>li</code> (el elemento HTML) como argumento:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">return authors.map(function(author) {
    let li = createNode('li');
})
</code></pre>
<p>Repita esto para crear un elemento <code>span</code> y un elemento <code>img</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let li = createNode('li');
let img = createNode('img');
let span = createNode('span');
</code></pre>
<p>La API ofrece un nombre para el autor y una imagen que acompaña al nombre. Establezca <code>img.src</code> en la imagen del autor:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let img = createNode('img');
let span = createNode('span');

img.src = author.picture.medium;
</code></pre>
<p>El elemento <code>span</code> debe contener el primer nombre y el apellido del <code>author</code>. La propiedad <code>innerHTML</code> y la interpolación de cadenas le permitirán hacer esto:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">img.src = author.picture.medium;
span.innerHTML = `${author.name.first} ${author.name.last}`;
</code></pre>
<p>Con la imagen y el elemento de lista ya creados junto con el elemento <code>span</code>, puede usar la función <code>append</code> que se creó anteriormente para mostrar estos elementos en la página:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">append(li, img);
append(li, span);
append(ul, li);
</code></pre>
<p>Una vez que se completan ambas funciones <code>then()</code>, puede añadir la función <code>catch()</code>. Esta función registrará el posible error en la consola:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Este es el código completo de la solicitud que creó:</p>
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
<p>Acaba de realizar con éxito una solicitud GET usando la API de Random User y la API Fetch. En el siguiente paso, aprenderá a hacer solicitudes POST.</p>

<h2 id="paso-3-gestionar-solicitudes-post">Paso 3: Gestionar solicitudes POST</h2>

<p>Fetch se ajusta de manera determinada a las solicitudes GET, pero puede usar todos los demás tipos de solicitudes, cambiar los encabezados y enviar datos. Para ello, debe establecer su objeto y pasarlo como el segundo argumento de la función fetch.</p>

<p>Antes de crear una solicitud POST, cree los datos que desea enviar a la API. Este será un objeto llamado <code>data</code> con la clave <code>name</code> y el valor <code>Sammy</code> (o su nombre):</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sammy'
}
</code></pre>
<p>Asegúrese de incluir una variable constante que contenga el enlace de la API de Random User.</p>

<p>Dado que se trata de una solicitud POST, deberá indicarlo de forma explícita. Cree un objeto llamado <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {

}
</code></pre>
<p>Este objeto debe incluir tres claves: <code>method</code>, <code>body</code> y <code>headers</code>. La clave <code>method</code> debe tener el valor <code>'POST'</code>. <code>body</code> debe establecerse igual al objeto <code>data</code> que acaba de crear. <code>headers</code> debe tener el valor de <code>new Headers()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {
  method: 'POST',
  body: data,
  headers: new Headers()
}
</code></pre>
<p>La interfaz <code>Headers</code> es una propiedad de la API Fetch, que le permite realizar varias acciones en los encabezados de las solicitudes y respuestas HTTP. Si desea obtener más información sobre esto, este artículo llamado <a href="https://www.digitalocean.com/community/tutorials/nodejs-express-routing">Cómo definir rutas y métodos de solicitud HTTP en Express</a> puede proporcionarle más información.</p>

<p>Una vez establecido este código, se puede realizar la solicitud POST usando la API Fetch. Incluirá <code>url</code> y <code>fetchData</code> como argumentos para su solicitud POST <code>fetch</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
</code></pre>
<p>La función <code>then()</code> incluirá el código que maneja la respuesta recibida del servidor de API de Random User:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
.then(function() {
    // Handle response you get from the server
});
</code></pre>
<p>Para crear un objeto y usar la función <code>fetch()</code>, también hay otra opción. En vez de crear un objeto como <code>fetchData</code>, puede usar el constructor de solicitudes para crear su objeto de solicitud. Para ello, cree una variable llamada <code>request</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request =
</code></pre>
<p>La variable <code>request</code> debe establecerse igual a <code>new Request</code>. La construcción <code>new Request</code> incluye dos argumentos: la url de la API (<code>url</code>) y un objeto. El objeto también debe incluir las claves <code>method</code>, <code>body</code> y <code>headers</code>, así como <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});
</code></pre>
<p>Ahora, <code>request</code> puede usarse como el único argumento para <code>fetch()</code>, ya que también incluye la URL de API:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>En su totalidad, su código se verá así:</p>
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
<p>Ahora conoce dos métodos para crear y ejecutar solicitudes POST con la API Fetch.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Aunque la API Fetch aún no es compatible con todos los navegadores, es una excelente alternativa a XMLHttpRequest. Si desea aprender a invocar las API web usando React, <a href="https://www.digitalocean.com/community/tutorials/how-to-call-web-apis-with-the-useeffect-hook-in-react">consulte este artículo</a> sobre ese mismo tema.</p>
