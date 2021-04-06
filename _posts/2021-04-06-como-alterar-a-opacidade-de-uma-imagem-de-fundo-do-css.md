---
title : "Como alterar a opacidade de uma imagem de fundo do CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-change-a-css-background-images-opacity-pt
image: "https://sergio.afanou.com/assets/images/image-midres-27.jpg"
---

<p>Com o CSS e o CSS3, é possível fazer muitas coisas, mas definir a opacidade em uma tela de fundo do CSS não é uma delas. No entanto, se você for criativo, existem várias alternativas criativas que darão a impressão de que a opacidade da imagem de fundo do CSS está sendo alterada. Ambos os métodos a seguir têm um <strong>excelente suporte de navegadores</strong>, indo até o Internet Explorer 8.</p>

<h2 id="método-1-use-o-posicionamento-absoluto-e-uma-imagem">Método 1: use o posicionamento absoluto e uma imagem</h2>

<p>Este método é exatamente o que parece. Você simplesmente usa o posicionamento absoluto em uma etiqueta <code>img</code> normal e faz com que a propriedade <code>background-image</code> do CSS pareça estar sendo usada. Tudo o que você precisa fazer é colocar a imagem dentro de um contêiner <code>position: relative;</code>. Aqui está como a marcação do HTML, no geral, se parece:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
  &lt;img src="https://assets.digitalocean.com/labs/images/community_bg.png"&gt;
&lt;/div&gt;
</code></pre>
<p>E aqui está como o seu CSS ficará:</p>
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
<p>O truque aqui é colocar a imagem de forma absoluta e estendê-la de forma a preencher todo o contêiner pai. E posicionar todo o resto <strong>relativamente</strong>, para que seja possível definir um <code>z-index</code> que coloca tudo acima da imagem.</p>

<p>Aqui está uma demonstração:</p>

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

<h2 id="método-2-usando-os-pseudoelementos-do-css">Método 2: usando os pseudoelementos do CSS</h2>

<p>Este método já parece ser simples assim que você o vê, e é definitivamente minha maneira preferida de fazer isso. Usando os pseudoelementos do CSS <code>:before</code> ou <code>:after</code>, você aplica o div com uma imagem de fundo e define uma opacidade nela. Aqui está como sua marcação HTML irá ficar:</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
&lt;/div&gt;
</code></pre>
<p>E aqui está como o CSS irá ficar:</p>
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
<p>Aqui, é necessário mover novamente o z-index do conteúdo (neste caso, o <code>&lt;h1&gt;</code>) para cima do pseudoelemento de fundo e também precisamos definir explicitamente <code>position: absolute;</code> e <code>z-index: 1</code> no pseudoelemento <code>:before</code>.</p>

<p>O resto dos atributos no pseudoelemento existem para posicioná-lo de forma a sobrepor 100% do pai, além de fazer uso de uma nova propriedade inteligente do CSS: <code>background-size: cover</code> que ajusta o fundo para cobrir o elemento sem alterar proporções. Aqui está uma pequena demonstração desse método:</p>

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
