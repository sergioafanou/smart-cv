---
title : "Cómo cambiar el estilo de las barras de desplazamiento con CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/css-scrollbars-es
image: "https://sergio.afanou.com/assets/images/image-midres-39.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>En septiembre de 2018, las <a href="https://www.w3.org/TR/2018/WD-css-scrollbars-1-20180925">barras de desplazamiento de CSS</a> de W3C definieron especificaciones para personalizar el aspecto de las barras de desplazamiento con CSS.</p>

<p>A partir de 2020, <a href="https://caniuse.com/#feat=css-scrollbar">96 % de los usuarios de Internet</a> ejecutan navegadores que admiten el estilo de barra de desplazamiento de CSS. Sin embargo, deberá escribir dos conjuntos de reglas de CSS para abarcar Blink y WebKit y también los navegadores de Firefox.</p>

<p>En este tutorial, aprenderá a usar CSS para personalizar barras de desplazamiento para admitir navegadores modernos.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para seguir este artículo, necesitará lo siguiente:</p>

<ul>
<li>Conocimientos sobre los conceptos de <a href="https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix">prefijos de proveedores</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements">seudoelementos</a> y <a href="https://developer.mozilla.org/en-US/docs/Glossary/Graceful_degradation">degradación elegante</a>.</li>
</ul>

<h2 id="agregar-estilo-a-las-barras-de-desplazamiento-en-chrome-edge-y-safari">Agregar estilo a las barras de desplazamiento en Chrome, Edge y Safari</h2>

<p>Actualmente, agregar estilo a las barras de desplazamiento para Chrome, Edge y Safari está disponible con el seudoelemento del prefijo de proveedor <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar"><code>-webkit-scrollbar</code></a>.</p>

<p>A continuación, se muestra un ejemplo que utiliza los seudoelementos <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code> y <code>::webkit-scrollbar-thumb:</code></p>
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
<p>A continuación, se muestra una captura de pantalla de la barra de desplazamiento que se genera con estas reglas de CSS:</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-0.png" alt="Captura de pantalla de una página web de ejemplo con una barra de desplazamiento personalizada con una barra de desplazamiento azul en un recuadro de desplazamiento anaranjado."></p>

<p>Este código funciona en las últimas versiones de Chrome, Edge y Safari.</p>

<p>Desafortunadamente, <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar">W3C abandonó formalmente esta especificación</a> y probablemente quedará obsoleta con el paso del tiempo.</p>

<h2 id="agregar-estilo-a-las-barras-de-desplazamiento-en-firefox">Agregar estilo a las barras de desplazamiento en Firefox</h2>

<p>Actualmente, aplicar estilo a las barras de desplazamiento en Firefox está disponible con las nuevas <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Scrollbars">barras de desplazamiento de CSS</a>.</p>

<p>A continuación, se muestra un ejemplo que utiliza las propiedades <code>scrollbar-width</code> y <code>scrollbar-color</code>:</p>
<pre class="code-pre "><code class="code-highlight language-css">body {
  scrollbar-width: thin;          /* "auto" or "thin" */
  scrollbar-color: blue orange;   /* scroll thumb and track */
}
</code></pre>
<p>A continuación, se muestra una captura de pantalla de la barra de desplazamiento que se genera con estas reglas de CSS:</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-3.png" alt="Captura de pantalla de una página web de ejemplo con una barra de desplazamiento personalizada con una barra de desplazamiento azul en un recuadro de desplazamiento anaranjado."></p>

<p>Esta especificación comparte algunos puntos en común con la especificación <code>-webkit-scrollbar</code> para controlar el color de la barra de desplazamiento. Sin embargo, actualmente no se puede modificar el relleno y la redondez del &ldquo;botón de desplazamiento&rdquo;.</p>

<h2 id="crear-estilos-de-barra-de-desplazamiento-a-prueba-del-futuro">Crear estilos de barra de desplazamiento a prueba del futuro</h2>

<p>Puede escribir su CSS de tal manera que sea compatible con las especificaciones <code>-webkit-scrollbar</code> y <code>CSS Scrollbars</code>.</p>

<p>A continuación, se muestra un ejemplo que utiliza <code>scrollbar-width</code>, <code>scrollbar-color</code>, <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code>, <code>::webkit-scrollbar-thumb</code>:</p>
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
<p>Los navegadores Blink y WebKit ignorarán las reglas que no reconocen y aplicarán las reglas <code>-webkit-scrollbar</code>. Los navegadores de Firefox ignorarán las reglas que no reconocen y aplicarán las reglas de <code>CSS Scrollbars</code>. Una vez que los navegadores Blink y WebKit ya no sean compatibles por completo con la especificación <code>-webkit-scrollbar</code>, regresarán fácilmente a la nueva especificación de las barras de <code>CSS Scrollbar</code>.</p>

<h2 id="conclusión">Conclusión</h2>

<p>En este artículo, aprendió a usar CSS para agregar estilo a las barras de desplazamiento y garantizar que estos estilos sean reconocidos en la mayoría de los navegadores modernos.</p>

<p>También es posible simular una barra de desplazamiento ocultando la barra de desplazamiento predeterminada y usando JavaScript para detectar la altura y la posición de desplazamiento. Sin embargo, estos enfoques tienen limitaciones a la hora de reproducir experiencias como el desplazamiento por inercia (por ejemplo, desactivar el movimiento al desplazarse con trackpads).</p>

<p>Si desea obtener más información sobre CSS, consulte <a href="https://www.digitalocean.com/community/tags/css">nuestra página del tema CSS</a> para consultar ejercicios y proyectos de programación.</p>
