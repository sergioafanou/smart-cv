---
title : "Comment créer un style de barre de défilement avec CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/css-scrollbars-fr
image: "https://sergio.afanou.com/assets/images/image-midres-32.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>En septembre 2018, les <a href="https://www.w3.org/TR/2018/WD-css-scrollbars-1-20180925">barres de défilement CSS</a> de W3C ont défini des spécifications pour personnaliser l'apparence des barres de défilement avec CSS.</p>

<p>En 2020, <a href="https://caniuse.com/#feat=css-scrollbar">96 % des utilisateurs d'Internet</a> exécutent des navigateurs qui prennent en charge la création de style de barres de défilement CSS. Cependant, afin de couvrir les navigateurs Blink, WebKit et Firefox, vous devrez écrire deux ensembles de règles CSS.</p>

<p>Au cours de ce tutoriel, vous allez apprendre à utiliser CSS pour personnaliser des barres de défilement qui soient prises en charge par les navigateurs modernes.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour suivre le déroulement de cet article, vous aurez besoin de :</p>

<ul>
<li>Notions sur les concepts de <a href="https://developer.mozilla.org/en-US/docs/Glossary/Vendor_Prefix">vendor prefixes</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements">pseudo-elements</a> et <a href="https://developer.mozilla.org/en-US/docs/Glossary/Graceful_degradation">graceful degradation</a>.</li>
</ul>

<h2 id="créer-le-style-des-barres-de-défilement-dans-chrome-edge-et-safari">Créer le style des barres de défilement dans Chrome, Edge et Safari</h2>

<p>Actuellement, vous pouvez utiliser le pseudo-élément du préfixe vendeur <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar"><code>-webkit-scrollbar</code></a> pour créer le style de vos barres de défilement pour Chrome, Edge et Safari.</p>

<p>Voici un exemple qui utilise les pseudo-éléments <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code> et <code>::webkit-scrollbar-thumb</code> :</p>
<pre class="code-pre "><code class="code-highlight language-css">body::-webkit-scrollbar {
  width: 12px; /* width of the entire scrollbar */
}

body::-webkit-scrollbar-track {
  background: orange; /* color of the tracking area */
}

body::-webkit-scrollbar-thumb {
  background-color: blue; /* color of the scroll thumb */
  border-radius: 20px; /* roundness of the scroll thumb */
  border: 3px solid orange; /* creates padding around scroll thumb */
}
</code></pre>
<p>Voici une capture d'écran de la barre de défilement obtenue avec ces règles CSS :</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-0.png" alt="Capture d'écran d'un exemple de page web avec une barre de défilement personnalisée bleue sur un fond orange."></p>

<p>Ce code fonctionne avec les dernières versions de Chrome, Edge et Safari.</p>

<p>Malheureusement, cette spécification a été officiellement <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/::-webkit-scrollbar">abandonnée par W3C</a> et sera probablement à éviter au fil du temps.</p>

<h2 id="créer-le-style-des-barres-de-défilement-dans-firefox">Créer le style des barres de défilement dans Firefox</h2>

<p>Actuellement, vous pouvez créer le style des barres de défilement sous Firefox avec les nouveaux <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Scrollbars">CSS Scrollbars</a>.</p>

<p>Voici un exemple qui utilise les propriétés <code>scrollbar-width</code> et <code>scrollbar-color</code> :</p>
<pre class="code-pre "><code class="code-highlight language-css">body {
  scrollbar-width: thin; /* "auto" or "thin" */
  scrollbar-color: blue orange; /* scroll thumb and track */
}
</code></pre>
<p>Voici une capture d'écran de la barre de défilement obtenue avec ces règles CSS :</p>

<p><img src="https://assets.digitalocean.com/articles/alligator/css/css-scrollbars/scrollbar-styling-3.png" alt="Capture d'écran d'un exemple de page web avec une barre de défilement personnalisée bleue sur un fond orange."></p>

<p>Cette spécification partage quelques points communs avec la spécification <code>-webkit-scrollbar</code> pour contrôler la couleur de la barre de défilement. Cependant, il n'existe actuellement aucune spécification permettant de modifier le remplissage et l'arrondi de la « piste de défilement ».</p>

<h2 id="créer-des-styles-de-barre-de-défilement-à-l-39-épreuve-du-temps">Créer des styles de barre de défilement à l'épreuve du temps</h2>

<p>Vous pouvez écrire votre CSS de manière à prendre en charge les spécifications <code>-webkit-scrollbar</code> et <code>CSS Scrollbar</code>.</p>

<p>Voici un exemple qui utilise <code>scrollbar-width</code>, <code>scrollbar-color</code>, <code>::-webkit-scrollbar</code>, <code>::-webkit-scrollbar-track</code>, <code>::webkit-scrollbar-thumb</code> :</p>
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
<p>Les navigateurs Blink et WebKit ignoreront les règles qu'ils ne reconnaitront pas et appliqueront les règles <code>-webkit-scrollbar</code>. Les navigateurs Firefox ignoreront les règles qu'ils ne reconnaitront pas et appliqueront les règles <code>CSS Scrollbars</code>. Une fois que les navigateurs Blink et WebKit ne prendront plus en charge la spécification <code>-webkit-scrollbar</code>, ils reviendront gracieusement à la nouvelle spécification <code>CSS Scrollbars</code>.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Au cours de cet article, vous avez été initié à l'utilisation de CSS pour créer le style des barres de défilement tout en veillant à ce que ces styles soient reconnus par la plupart des navigateurs modernes.</p>

<p>Vous pouvez également simuler une barre de défilement en cachant la barre de défilement par défaut et en utilisant JavaScript pour détecter la hauteur et la position de défilement. Cependant, ces approches présentent des limitations en termes de reproduction d'expériences comme le défilement à inertie (par exemple, mouvement décroissant pendant le défilement avec des pistes de défilement).</p>

<p>Si vous souhaitez en savoir plus sur CSS, veuillez consulter <a href="https://www.digitalocean.com/community/tags/css">notre page thématique CSS</a> dans laquelle vous trouverez des exercices et des projets de programmation.</p>
