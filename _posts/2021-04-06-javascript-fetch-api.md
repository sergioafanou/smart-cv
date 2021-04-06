---
title : "Использование JavaScript Fetch API для получения данных"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-the-javascript-fetch-api-to-get-data-ru
image: "https://sergio.afanou.com/assets/images/image-midres-51.jpg"
---

<h3 id="Введение">Введение</h3>

<p>Было время, когда для запросов API использовался <code>XMLHttpRequest</code>. В нем не было промисов, и он не позволял создавать чистый код JavaScript. В jQuery мы использовали более чистый синтаксис с <code>jQuery.ajax()</code>.</p>

<p>Сейчас JavaScript имеется собственный встроенный способ отправки запросов API. Это Fetch API, новый стандарт создания серверных запросов с промисами, также включающий много других возможностей.</p>

<p>В этом учебном модуле мы создадим запросы GET и POST, используя Fetch API.</p>

<h2 id="Предварительные-требования">Предварительные требования</h2>

<p>Для этого обучающего модуля вам потребуется следующее:</p>

<ul>
<li>Последняя версия Node, установленная на вашем компьютере. Чтобы установить Node в macOS, выполните указания учебного модуля <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">Установка Node.js и создание локальной среды разработки в macOS</a>.</li>
<li>Базовое понимание принципов программирования в JavaScript, о которых можно узнать более подробно в серии <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">Программирование на JavaScript</a>.</li>
<li>Понимание понятия промисов в JavaScript. Прочитайте <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">раздел «Промисы»</a> этой статьи, чтобы узнать больше о <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">циклах событий, обратных вызовах, промисах и асинхронном/ожидающем</a> в JavaScript.</li>
</ul>

<h2 id="Шаг-1-—-Введение-в-синтаксис-fetch-api">Шаг 1 — Введение в синтаксис Fetch API</h2>

<p>Чтобы использовать Fetch API, вызовите метод <code>fetch</code>, который принимает URL API в качестве параметра:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre>
<p>После метода <code>fetch()</code> нужно включить метод промиса <code>then()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function() {

})
</code></pre>
<p>Метод <code>fetch()</code> возвращает промис. Если возвращается промис <code>resolve</code>, будет выполнена функция метода <code>then()</code>. Эта функция содержит код для обработки данных, получаемых от API.</p>

<p>Под методом <code>then()</code> следует включить метод <code>catch()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function() {

});
</code></pre>
<p>API, вызываемый с помощью метода <code>fetch()</code>, может не работать или на нем могут возникнуть ошибки. Если это произойдет, будет возвращен промис <code>reject</code>. Метод <code>catch</code> используется для обработки <code>reject</code>. Код метода <code>catch()</code> выполняется в случае возникновения ошибки при вызове выбранного API.</p>

<p>В целом, использование Fetch API выглядит следующим образом:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then(function() {

})
.catch(function() {

});
</code></pre>
<p>Теперь мы понимаем синтаксис использования Fetch API и можем переходить к использованию <code>fetch()</code> с реальным API.</p>

<h2 id="Шаг-2-—-Использование-fetch-для-получения-данных-от-api">Шаг 2 — Использование Fetch для получения данных от API</h2>

<p>Следующие примеры кода основаны на <a href="https://randomuser.me">Random User API</a>. Используя API, вы получаете десять пользователей и выводите их на странице, используя Vanilla JavaScript.</p>

<p>Идея заключается в том, чтобы получить все данные от Random User API и вывести их в элементах списка внутри списка автора. Для начала следует создать файл HTML и добавить в него заголовок и неупорядоченный список с <code>идентификатором</code> <code>authors</code>:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;h1&gt;Authors&lt;/h1&gt;
&lt;ul id="authors"&gt;&lt;/ul&gt;
</code></pre>
<p>Теперь добавьте теги <code>script</code> в конец файла HTML и используйте селектор DOM для получения <code>ul</code>. Используйте <code>getElementById</code> с аргументом <code>authors</code>. Помните, что <code>authors</code> — это <code>идентификатор</code> ранее созданного <code>ul</code>:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;

    const ul = document.getElementById('authors');

&lt;/script&gt;
</code></pre>
<p>Создайте постоянную переменную <code>url</code>, в которой будет храниться URL-адрес API, который вернет десять случайных пользователей:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api/?results=10';
</code></pre>
<p>Теперь у нас есть <code>ul</code> и <code>url</code>, и мы можем создать функции, которые будут использоваться для создания элементов списка. Создайте функцию под названием <code>createNode</code>, принимающую параметр с именем <code>element</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {

}
</code></pre>
<p>Впоследствии, при вызове <code>createNode</code>, вам нужно будет передать имя создаваемого элемента HTML.</p>

<p>Добавьте в функцию выражение <code>return</code>, возвращающее <code>element</code>, с помощью <code>document.createElement()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function createNode(element) {
    return document.createElement(element);
}
</code></pre>
<p>Также вам нужно будет создать функцию с именем <code>append</code>, которая принимает два параметра: <code>parent</code> и <code>el</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {

}
</code></pre>
<p>Эта функция будет добавлять <code>el</code> к <code>parent</code>, используя <code>document.createElement</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">function append(parent, el) {
    return parent.appendChild(el);
}
</code></pre>
<p>Теперь и <code>createNode</code>, и <code>append</code> готовы к использованию. Используя Fetch API, вызовите Random User API, добавив к <code>fetch()</code> аргумент <code>url</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
</code></pre><pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
  .then(function(data) {

    })
  })
  .catch(function(error) {

  });
</code></pre>
<p>В вышеуказанном коде вы вызываете Fetch API и передаете URL в Random User API. После этого поступает ответ. Однако ответ вы получите не в формате JSON, а в виде объекта с серией методов, которые можно использовать в зависимости от того, что вы хотите делать с информацией. Чтобы конвертировать возвращаемый объект в формат JSON, используйте метод <code>json()</code>.</p>

<p>Добавьте метод <code>then()</code>, содержащий функцию с параметром <code>resp</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; )
</code></pre>
<p>Параметр <code>resp</code> принимает значение объекта, возвращаемого <code>fetch(url)</code>. Используйте метод <code>json()</code>, чтобы конвертировать <code>resp</code> в данные JSON:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url)
.then((resp) =&gt; resp.json())
</code></pre>
<p>При этом данные JSON все равно необходимо обработать. Добавьте еще одно выражение <code>then()</code> с функцией, имеющей аргумент с именем <code>data</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {

    })
})
</code></pre>
<p>Создайте в этой функции переменную с именем <code>authors</code>, принимающую значение <code>data.results</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.then(function(data) {
    let authors = data.results;
</code></pre>
<p>Для каждого автора в переменной <code>authors</code> нам нужно создать элемент списка, выводящий портрет и имя автора. Для этого отлично подходит метод <code>map()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let authors = data.results;
return authors.map(function(author) {

})
</code></pre>
<p>Создайте в функции <code>map</code> переменную <code>li</code>, которая будет равна <code>createNode</code> с <code>li</code> (элемент HTML) в качестве аргумента:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">return authors.map(function(author) {
    let li = createNode('li');
})
</code></pre>
<p>Повторите эту процедуру, чтобы создать элемент <code>span</code> и элемент <code>img</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let li = createNode('li');
let img = createNode('img');
let span = createNode('span');
</code></pre>
<p>Предлагает имя автора и портрет, идущий вместе с именем. Установите в <code>img.src</code> портрет автора:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let img = createNode('img');
let span = createNode('span');

img.src = author.picture.medium;
</code></pre>
<p>Элемент <code>span</code> должен содержать имя и фамилию <code>автора</code>. Для этого можно использовать свойство <code>innerHTML</code> и интерполяцию строк:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">img.src = author.picture.medium;
span.innerHTML = `${author.name.first} ${author.name.last}`;
</code></pre>
<p>Когда изображение и элемент списка созданы вместе с элементом <code>span</code>, вы можете использовать функцию <code>append</code>, которую мы ранее добавили для отображения этих элементов на странице:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">append(li, img);
append(li, span);
append(ul, li);
</code></pre>
<p>Выполнив обе функции <code>then()</code>, вы сможете добавить функцию <code>catch()</code>. Эта функция поможет зарегистрировать потенциальную ошибку на консоли:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">.catch(function(error) {
  console.log(error);
});
</code></pre>
<p>Это полный код запроса, который вы создали:</p>
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
<p>Вы только что успешно выполнили запрос GET, используя Random User API и Fetch API. На следующем шаге вы научитесь выполнять запросы POST.</p>

<h2 id="Шаг-3-—-Обработка-запросов-post">Шаг 3 — Обработка запросов POST</h2>

<p>По умолчанию Fetch использует запросы GET, но вы также можете использовать и все другие типы запросов, изменять заголовки и отправлять данные. Для этого нужно задать объект и передать его как второй аргумент функции fetch.</p>

<p>Прежде чем создать запрос POST, создайте данные, которые вы хотите отправить в API. Это будет объект с именем <code>data</code> с ключом <code>name</code> и значением <code>Sammy</code> (или вашим именем):</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sammy'
}
</code></pre>
<p>Обязательно добавьте постоянную переменную, хранящую ссылку на Random User API.</p>

<p>Поскольку это запрос POST, ее нужно будет указать явно. Создайте объект с именем <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {

}
</code></pre>
<p>Этот объект должен содержать три ключа: <code>method</code>, <code>body</code> и <code>headers</code>. Ключ <code>method</code> должен иметь значение <code>'POST'</code>. Для <code>body</code> следует задать значение только что созданного объекта <code>data</code>. Для <code>headers</code> следует задать значение <code>new Headers()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">let fetchData = {
  method: 'POST',
  body: data,
  headers: new Headers()
}
</code></pre>
<p>Интерфейс <code>Headers</code> является свойством Fetch API, который позволяет выполнять различные действия с заголовками запросов и ответов HTTP. Если вы захотите узнать об этом больше, вы можете найти более подробную информацию в статье под названием <a href="https://www.digitalocean.com/community/tutorials/nodejs-express-routing">Определение маршрутов и методов запросов HTTP в Express</a>.</p>

<p>С этим кодом можно составлять запросы POST, используя Fetch API. Мы добавим <code>url</code> и <code>fetchData</code> как аргументы запроса <code>fetch</code> POST:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
</code></pre>
<p>Функция <code>then()</code> будет включать код, обрабатывающий ответ, получаемый от сервера Random User API:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(url, fetchData)
.then(function() {
    // Handle response you get from the server
});
</code></pre>
<p>Есть и другая опция, позволяющая создать объект и использовать функцию <code>fetch()</code>. Вместо того, чтобы создавать такой объект как <code>fetchData</code>, вы можете использовать конструктор запросов для создания объекта запроса. Для этого нужно создать переменную с именем <code>request</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">const url = 'https://randomuser.me/api';

let data = {
  name: 'Sara'
}

var request =
</code></pre>
<p>Для переменной <code>request</code> следует задать значение <code>new Request</code>. Конструкт <code>new Request</code> принимает два аргумента: URL API (<code>url</code>) и объект. В объекте также должны содержаться ключи <code>method</code>, <code>body</code> и <code>headers</code>, как и в <code>fetchData</code>:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">var request = new Request(url, {
    method: 'POST',
    body: data,
    headers: new Headers()
});
</code></pre>
<p>Теперь <code>request</code> можно использовать как единственный аргумент для <code>fetch()</code>, поскольку он также включает URL-адрес API:</p>
<pre class="code-pre "><code class="code-highlight language-javascript">fetch(request)
.then(function() {
    // Handle response we get from the API
})
</code></pre>
<p>В целом код будет выглядеть следующим образом:</p>
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
<p>Теперь вы знаете два метода создания и выполнения запросов POST с помощью Fetch API.</p>

<h2 id="Заключение">Заключение</h2>

<p>Хотя Fetch API поддерживается еще не всеми браузерами, он представляет собой отличную альтернативу XMLHttpRequest. Если вы хотите узнать, как вызывать Web API с помощью React, <a href="https://www.digitalocean.com/community/tutorials/how-to-call-web-apis-with-the-useeffect-hook-in-react">ознакомьтесь с этой статьей</a> по данной теме.</p>
