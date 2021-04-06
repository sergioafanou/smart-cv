---
title : "Cómo cambiar la opacidad de una imagen de fondo en CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-change-a-css-background-images-opacity-es
image: "https://sergio.afanou.com/assets/images/image-midres-10.jpg"
---

<p>Con CSS y CSS3 puede hacer muchas cosas, pero establecer una opacidad en un fondo de CSS no es una de ellas. Sin embargo, si usa la creatividad, hay muchas soluciones creativas para hacer que parezca que está cambiando la opacidad de la imagen de fondo de CSS. Ambos métodos tienen una <strong>excelente compatibilidad con navegadores</strong>, que llega hasta Internet Explorer 8.</p>

<h2 id="método-1-utilizar-el-posicionamiento-absoluto-y-una-imagen">Método 1: Utilizar el posicionamiento absoluto y una imagen</h2>

<p>Este método significa exactamente lo que dice. Simplemente se utiliza el posicionamiento absoluto en una etiqueta <code>img</code> normal y se hace que parezca que se usó la propiedad de <code>background-image</code> de CSS. Lo único que debe hacer es poner la imagen dentro de un contenedor <code>position: relative;</code>. Generalmente, así es como se ve el formato HTML:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
  &lt;img src="https://assets.digitalocean.com/labs/images/community_bg.png"&gt;
&lt;/div&gt;
</code></pre>
<p>Y así es como se verá su CSS:</p>
<pre class="code-pre "><code class="code-highlight language-css">.demo_wrap {
    position: relative;
    overflow: hidden;
    padding: 16px;
    border: 1px dashed green;
}
.demo_wrap h1 {
    padding: 100px;
    <span class="highlight">position: relative;</span>
    <span class="highlight">z-index: 2;</span>
}
.demo_wrap img {
    <span class="highlight">position: absolute;</span>
    left: 0;
    top: 0;
    width: 100%;
    height: auto;
    <span class="highlight">opacity: 0.6;</span>
}
</code></pre>
<p>El truco aquí es realizar un posicionamiento absoluto de la etiqueta img y estirarla para que llene el contenedor principal por completo. Y posicionar <strong>relativamente</strong> todo lo demás para poder establecer un <code>z-index</code> que lo arrastre por encima de img.</p>

<p>A continuación, se muestra una demostración en tiempo real:</p>

<style>
.demo_wrap {
    position: relative;
    overflow: hidden;
    padding: 16px;
    border: 1px dashed green;
}
.demo_wrap h1 {
    padding: 50px;
    position: relative;
    z-index: 2;
}
.demo_wrap img {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: auto;
    opacity: 0.6;
    border: none;
}
</style>

<div class="demo_wrap">
  <h1>Hello World!</h1>
  <img src="https://assets.digitalocean.com/labs/images/community_bg.png">
</div>

<h2 id="método-2-usar-los-seudoelementos-de-css">Método 2: Usar los seudoelementos de CSS</h2>

<p>Este método luce sencillo a primera vista y definitivamente es el método preferido para hacerlo. Al usar los seudoelementos de CSS de <code>:before</code> o <code>:after</code>, puede crear un div con una imagen de fondo y establece una opacidad en ella. A continuación, se muestra el aspecto que tendría su formato HTML de forma aproximada:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
&lt;/div&gt;
</code></pre>
<p>Y así es como se vería el CSS:</p>
<pre class="code-pre "><code class="code-highlight language-css">   .demo_wrap {
    position: relative;
    background: #5C97FF;
    overflow: hidden;
}
.demo_wrap h1 {
    padding: 50px;
    <span class="highlight">position: relative;</span>
    <span class="highlight">z-index: 2;</span>
}
/* You could use :after - it doesn't really matter */
.demo_wrap:before {
    content: ' ';
    display: block;
    <span class="highlight">position: absolute;</span>
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    <span class="highlight">z-index: 1;</span>
    <span class="highlight">opacity: 0.6;</span>
    background-image: url('https://assets.digitalocean.com/labs/images/community_bg.png');
    background-repeat: no-repeat;
    background-position: 50% 0;
    background-size: cover;
}
</code></pre>
<p>Aquí también debemos mover el índice z del contenido (en este caso el <code>&lt;h1&gt;</code>) por encima del seudoelemento de fondo y debemos definir explícitamente <code>position: absolute;</code> y <code>z-index: 1</code> en el seudoelemento <code>:before</code>.</p>

<p>El resto de los atributos del seudoelemento existe para posicionarlo de manera que se superponga al 100% del elemento principal y también hacen uso de una nueva e inteligente propiedad de CSS: <code>background-size: cover</code> que dimensiona el fondo para cubrir el elemento sin cambiar las proporciones. A continuación, se muestra una pequeña demostración de este método:</p>

<style>
.demo_wrap2 {
    position: relative;
    overflow: hidden;
    padding: 16px;
    border: 1px dashed green;
}
.demo_wrap2 h1 {
    padding: 50px;
    position: relative;
    z-index: 2;
}
.demo_wrap2:before {
    content: ' ';
    display: block;
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    opacity: 0.6;
    background-image: url('https://assets.digitalocean.com/labs/images/community_bg.png');
    background-repeat: no-repeat;
    background-position: 50% 0;
    background-size: cover;
}
</style>

<div class="demo_wrap2">
  <h1>Hello World!</h1>
</div>
