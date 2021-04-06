---
title : "Como estilizar barras de rolagem com o CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/css-scrollbars-pt
image: "https://sergio.afanou.com/assets/images/image-midres-18.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Em setembro de 2018, a W3C <a href="https://www.w3.org/TR/2018/WD-css-scrollbars-1-20180925">CSS Scrollbars</a> definiu especificações para a personalização da aparência de barras de rolagem com o CSS.</p>

<p>Desde 2020, <a href="https://caniuse.com/#feat=css-scrollbar">96% dos usuários da internet</a> já estão utilizando navegadores que suportam a estilização da barra de rolagem do CSS. No entanto, você precisará escrever dois conjuntos de regras CSS para cobrir o Blink e WebKit, bem como os navegadores Firefox.</p>

<p>Neste tutorial, você irá aprender a como usar o CSS para personalizar barras de rolagem para dar suporte a navegadores modernos.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para acompanhar este artigo, você irá precisar do seguinte:</p>

<ul>
<li>Familiaridade com os conceitos de <a href="https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix">prefixos de fabricantes</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements">pseudoelementos</a> e <a href="https://developer.mozilla.org/en-US/docs/Glossary/Graceful_degradation">degradação suave</a>.</li>
</ul>

<h2 id="estilizando-barras-de-rolagem-no-chrome-edge-e-safari">Estilizando barras de rolagem no Chrome, Edge e Safari</h2>

<p>Atualmente, a estilização de barras de rolagem para o Chrome, Edge e o Safari está disponível com o pseudoelemento do prefixo de fabricante <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar"><code>-webkit-scrollbar</code></a>.</p>

<p>Aqui está um exemplo que usa os pseudoelementos <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code> e <code>::webkit-scrollbar-thumb</code>:</p>
<pre class="code-pre "><code class="code-highlight language-css">body::-webkit-scrollbar {
  width: 12px;               /* width of the entire scrollbar */
}

body::-webkit-scrollbar-track {
  background: orange;        /* color of the tracking area */
}

body::-webkit-scrollbar-thumb {
  background-color: blue;    /* color of the scroll thumb */
  border-radius: 20px;       /* roundness of the scroll thumb */
  border: 3px solid orange;  /* creates padding around scroll thumb */
}
</code></pre>
<p>Aqui está uma captura de tela da barra de rolagem produzida com essas regras do CSS:</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-0.png" alt="Captura de tela de uma página de exemplo com uma barra de rolagem personalizada sendo uma barra de rolagem azul em uma faixa laranja."></p>

<p>Este código funciona nas versões mais recentes do Chrome, Edge e Safari.</p>

<p>Infelizmente, essa especificação foi oficialmente <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar">abandonada pela W3C</a> e provavelmente será descontinuada ao longo do tempo.</p>

<h2 id="estilizando-barras-de-rolagem-no-firefox">Estilizando barras de rolagem no Firefox</h2>

<p>Atualmente, a estilização de barras para o Firefox está disponível com as novas <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Scrollbars">CSS Scrollbars</a>.</p>

<p>Aqui está um exemplo que usa as propriedades <code>scrollbar-width</code> e <code>scrollbar-color</code>:</p>
<pre class="code-pre "><code class="code-highlight language-css">body {
  scrollbar-width: thin;          /* "auto" or "thin" */
  scrollbar-color: blue orange;   /* scroll thumb and track */
}
</code></pre>
<p>Aqui está uma captura de tela da barra de rolagem produzida com essas regras do CSS:</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-3.png" alt="Captura de tela de uma página de exemplo com uma barra de rolagem personalizada sendo uma barra de rolagem azul em uma faixa laranja."></p>

<p>Essa especificação possui algumas características em comum com a especificação <code>-webkit-scrollbar</code> para o controle da cor da barra de rolagem. No entanto, atualmente, não há suporte para modificar o preenchimento e arredondamento para o &ldquo;track thumb&rdquo;.</p>

<h2 id="construindo-estilos-de-barra-de-rolagem-à-prova-do-tempo">Construindo estilos de barra de rolagem à prova do tempo</h2>

<p>É possível escrever seu CSS de forma a suportar tanto as especificações de <code>-webkit-scrollbar</code> quanto das <code>CSS Scrollbars</code>.</p>

<p>Aqui está um exemplo que usa <code>scrollbar-width</code>, <code>scrollbar-color</code>, <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code> e <code>::webkit-scrollbar-thumb</code>:</p>
<pre class="code-pre "><code class="code-highlight language-css">/* Works on Firefox */
* {
  scrollbar-width: thin;
  scrollbar-color: blue orange;
}

/* Works on Chrome, Edge, and Safari */
*::-webkit-scrollbar {
  width: 12px;
}

*::-webkit-scrollbar-track {
  background: orange;
}

*::-webkit-scrollbar-thumb {
  background-color: blue;
  border-radius: 20px;
  border: 3px solid orange;
}
</code></pre>
<p>Os navegadores Blink e WebKit irão ignorar as regras que eles não reconhecem e aplicar as regras de <code>-webkit-scrollbar</code>. Os navegadores Firefox irão ignorar as regras que não reconhecem e aplicar as regras de <code>CSS Scrollbars</code> . Assim que os navegadores Blink e WebKit descontinuarem totalmente a especificação <code>-webkit-scrollbar</code>, eles passarão a utilizar as novas especificações das <code>CSS Scrollbars</code>.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Neste artigo, foi apresentada a utilização do CSS para estilizar barras de rolagem e como garantir que esses estilos sejam reconhecidos na maioria dos navegadores modernos.</p>

<p>Também é possível simular uma barra de rolagem escondendo a barra de rolagem padrão e usando o JavaScript para detectar a altura e a posição de rolagem. No entanto, essas abordagens enfrentam limitações com a reprodução de experiências como a rolagem por inércia (por exemplo, o movimento em decadência ao fazer a rolagem com trackpads).</p>

<p>Se quiser aprender mais sobre o CSS, confira <a href="https://www.digitalocean.com/community/tags/css">nossa página de tópico do CSS</a> para exercícios e projetos de programação.</p>
