---
title : "Comment changer l’opacité d'une image d'arrière-plan CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-change-a-css-background-images-opacity-fr
image: "https://sergio.afanou.com/assets/images/image-midres-40.jpg"
---

<p>Même si avec CSS et CSS3 vous pouvez faire un grand nombre de choses, ils ne vous permettent pas de configurer une opacité sur un arrière-plan CSS. Cependant, en faisant preuve de créativité, il existe une tonne de solutions originales qui donneront l'impression que l'opacité de l'image d'arrière-plan CSS a été modifiée. Ces deux méthodes suivantes intègrent une <strong>excellente prise en charge des navigateurs</strong>, Internet Explorer 8 inclus.</p>

<h2 id="méthode-1 -utiliser-un-positionnement-absolu-et-une-image">Méthode 1 : utiliser un positionnement absolu et une image</h2>

<p>Cette méthode porte parfaitement son nom. Il vous suffit, tout simplement, d'utiliser le positionnement absolu sur une balise <code>img</code> normale pour donner l'impression d'avoir utilisé la propriété <code>background-image</code> de CSS. Tout ce que vous avez à faire, c'est de positionner l'image à l'intérieur d'un conteneur <code>position: relative;</code>. Voici à quoi ressemble généralement le balisage HTML :</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
  &lt;img src="https://assets.digitalocean.com/labs/images/community_bg.png" /&gt;
&lt;/div&gt;
</code></pre>
<p>Et voici à quoi ressemblera votre CSS :</p>
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
<p>Ici, l'astuce est de positionner l'img de manière absolue et de l'étirer jusqu'à remplir l'intégralité du conteneur parent. Et de positionner <strong>relatively</strong> tout le reste de façon à pouvoir configurer un <code>z-index</code> qui le positionnera au-dessus de l'img.</p>

<p>Voici une démo en direct :</p>

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

<h2 id="méthode-2 -utiliser-des-pseudo-éléments-css">Méthode 2 : utiliser des pseudo-éléments CSS</h2>

<p>Une fois que vous saurez comment ça marche, cette méthode est relativement simple à comprendre. Il s'agit sans nul doute de la méthode que je préfère. En utilisant les pseudo-éléments CSS <code>:before</code> ou <code>:after</code>, vous obtenez un div avec une image d'arrière-plan et configurez une opacité au-dessus. Voici à quoi devrait ressembler votre balisage HTML :</p>
<pre class="code-pre "><code class="code-highlight language-html">&lt;div class="demo_wrap"&gt;
  &lt;h1&gt;Hello World!&lt;/h1&gt;
&lt;/div&gt;
</code></pre>
<p>Et voici à quoi ressemble le CSS :</p>
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
<p>Ici, nous devons à nouveau déplacer le z-index de contenu (dans le cas présent, le <code>&lt;h1&gt;</code>) au-dessus du pseudo-élément de l'arrière-plan et explicitement configurer la <code>position: absolute;</code> et le <code>z-index: 1</code> sur le pseudo-élément <code>:before</code>.</p>

<p>Les autres attributs du pseudo-élément sont là pour le positionner de manière à chevaucher à 100 % le parent. Ils peuvent également utiliser une nouvelle propriété CSS intelligente : <code>background-size: cover</code> qui dimensionne l'arrière-plan de manière à couvrir l'élément sans en changer les proportions. Voici une belle petite démo de cette méthode :</p>

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
