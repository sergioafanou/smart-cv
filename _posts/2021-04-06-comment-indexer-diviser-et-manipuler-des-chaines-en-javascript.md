---
title : "Comment indexer, diviser et manipuler des chaînes en JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-split-and-manipulate-strings-in-javascript-fr
image: "https://sergio.afanou.com/assets/images/image-midres-38.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Une <strong>string</strong> est une séquence d'un ou plusieurs caractères qui peut être composée de lettres, de chiffres ou de symboles. Il est possible d'accéder à chaque caractère d'une chaîne JavaScript en utilisant un numéro d'index. Différentes méthodes et propriétés sont disponibles pour toutes les chaînes.</p>

<p>Au cours de ce tutoriel, nous allons apprendre à faire la différence entre les primitifs de chaînes et l'objet <code>String</code>. Ensuite, nous verrons de quelle manière les chaînes sont indexées et comment accéder aux caractères d'une chaîne. Pour finir, nous aborderons les propriétés et les méthodes couramment utilisées sur les chaînes de caractères.</p>

<h2 id="primitifs-de-chaînes-et-objets-de-chaînes">Primitifs de chaînes et objets de chaînes</h2>

<p>Tout d'abord, nous allons vous donner une explication sur ces deux types de chaînes de caractères. JavaScript fait la distinction entre la <strong>string primitive</strong>, un type de données immuable et l'objet <code>String</code>.</p>

<p>Afin de voir la différence entre les deux, nous allons initialiser un primitif de chaîne et un objet de chaîne.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Initializing a new string primitive
const stringPrimitive = "A new string.";

// Initializing a new String object
const stringObject = new String("A new string.");  
</code></pre>
<p>Nous pouvons utiliser l'opérateur <code>typeof</code> pour déterminer le type d'une valeur. Dans le premier exemple, nous avons tout simplement attribué une chaîne à une variable.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringPrimitive;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>string
</code></pre>
<p>Dans le second exemple, nous avons utilisé <code>new String()</code> pour créer un objet de chaîne et l'affecter à une variable.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringObject;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>object
</code></pre>
<p>La plupart du temps, vous créerez des primitifs de chaîne. JavaScript peut accéder et utiliser les propriétés et méthodes intégrées de l'accès objet <code>String</code> sans réellement modifié le primitif de la chaîne que vous avez créée dans un objet.</p>

<p>Bien que ce concept soit un peu difficile à comprendre au début, vous devriez pouvoir faire la distinction entre un primitif et un objet. En résumé, vous pouvez utiliser des méthodes et des propriétés sur toutes les chaînes. En arrière-plan, JavaScript procèdera à une conversion en objet, puis de nouveau en primitif à chaque fois qu'une méthode ou une propriété sera appelée.</p>

<h2 id="de-quelle-manière-sont-indexées-les-chaînes">De quelle manière sont indexées les chaînes</h2>

<p>Chaque caractère qui se trouve dans une chaîne correspond à un numéro d'index. La numérotation commence par <code>0</code>.</p>

<p>Afin de le démontrer, nous allons créer une chaîne avec la valeur <code>How are you?</code>.</p>

<table><thead>
<tr>
<th style="text-align: center">H</th>
<th style="text-align: center">o</th>
<th style="text-align: center">w</th>
<th style="text-align: center"></th>
<th style="text-align: center">a</th>
<th style="text-align: center">r</th>
<th style="text-align: center">e</th>
<th style="text-align: center"></th>
<th style="text-align: center">y</th>
<th style="text-align: center">o</th>
<th style="text-align: center">u</th>
<th style="text-align: center">?</th>
</tr>
</thead><tbody>
<tr>
<td style="text-align: center">0</td>
<td style="text-align: center">1</td>
<td style="text-align: center">2</td>
<td style="text-align: center">3</td>
<td style="text-align: center">4</td>
<td style="text-align: center">5</td>
<td style="text-align: center">6</td>
<td style="text-align: center">7</td>
<td style="text-align: center">8</td>
<td style="text-align: center">9</td>
<td style="text-align: center">10</td>
<td style="text-align: center">11</td>
</tr>
</tbody></table>

<p>Le premier caractère de la chaîne est <code>H</code> auquel correspond l'index <code>0</code>. Le dernier caractère est <code>?</code>, qui correspond à <code>11</code>. Les espaces sont également indexés, le <code>3</code> et le <code>7</code>.</p>

<p>Le fait de pouvoir accéder à chaque caractère d'une chaîne nous donne plusieurs manières de travailler avec les chaînes et de les manipuler.</p>

<h2 id="accéder-aux-caractères">Accéder aux caractères</h2>

<p>Nous allons vous montrer comment accéder aux caractères et index avec la chaîne <code>How are you?</code> .</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?";
</code></pre>
<p>En utilisant des crochets, nous pouvons accéder à n'importe quel caractère de la chaîne.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?"[5];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Nous pouvons également utiliser la méthode <code>charAt()</code> pour renvoyer le caractère en utilisant le numéro d'index comme paramètre.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".charAt(5);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Sinon, nous pouvons utiliser <code>indexOf()</code> pour renvoyer le numéro d'index par la première instance d'un caractère.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<p>Bien que « o » apparaisse deux fois dans la chaîne <code>How are you?</code> , <code>indexOf()</code> obtiendra la première instance.</p>

<p><code>lastIndexOf()</code> permet de trouver la dernière instance.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".lastIndexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>9
</code></pre>
<p>Avec ces deux méthodes, vous pouvez également rechercher plusieurs caractères dans la chaîne. Elles renverront le numéro d'index du premier caractère dans l'instance.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("are");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>4
</code></pre>
<p>De son côté, la méthode <code>slice()</code> renvoie les caractères entre deux numéros d'index. Le premier paramètre correspondra au numéro d'index du début. Le second paramètre correspondra au numéro d'index où il devrait se terminer.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8, 11);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you
</code></pre>
<p>Notez que <code>11</code> correspond à <code>?</code>, mais que <code>?</code> ne fait pas partie de la sortie renvoyée. <code>slice()</code> renverra ce qui se trouve entre deux mais ne comprendra pas le dernier paramètre.</p>

<p>Si un second paramètre n'est pas inclus, <code>slice()</code> renverra tout ce qui se trouve entre le paramètre et la fin de la chaîne.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you?
</code></pre>
<p>Pour résumer, <code>charAt()</code> et <code>slice()</code> vous aideront à renvoyer les valeurs de chaîne en fonction des numéros d'index. <code>indexOf()</code> et <code>lastIndexOf()</code> feront l'inverse. Ils renverront les numéros d'index en fonction des caractères de la chaîne fournis.</p>

<h2 id="déterminer-la-longueur-d-39-une-chaîne">Déterminer la longueur d'une chaîne</h2>

<p>En utilisant la propriété <code>length</code>, nous pouvons renvoyer le nombre de caractères dans une chaîne.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".length;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>N'oubliez pas que la propriété <code>length</code> renvoie le nombre réel de caractères commençant par 1, ce qui résulte à 12 et non pas le numéro d'index final, qui commence par <code>0</code> et se termine par <code>11</code>.</p>

<h2 id="convertir-des-majuscules-en-minuscules">Convertir des majuscules en minuscules</h2>

<p>Les deux méthodes intégrées <code>toUpperCase()</code> et <code>toLowerCase()</code> vous permettront de formater facilement un texte et de faire des comparaisons textuelles en JavaScript.</p>

<p><code>toUpperCase()</code> convertira tous les caractères en majuscules.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toUpperCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HOW ARE YOU?
</code></pre>
<p><code>toLowerCase()</code> convertira tous les caractères en minuscules.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toLowerCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>how are you?
</code></pre>
<p>Ces deux méthodes de formatage n'utilisent aucun paramètre supplémentaire.</p>

<p>Il convient de noter que ces méthodes ne modifient pas la chaîne d'origine.</p>

<h2 id="diviser-des-chaînes">Diviser des chaînes</h2>

<p>JavaScript intègre une méthode très utile qui permet de diviser une chaîne par caractère et de créer un nouveau <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">tableau</a> à partir des sections. Nous allons utiliser la méthode <code>split()</code> pour diviser le tableau par un caractère d'espacement, représenté par <code>" "</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?";

// Split string by whitespace character
const splitString = originalString.split(" ");

console.log(splitString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[ 'How', 'are', 'you?' ]
</code></pre>
<p>Maintenant que nous avons un nouveau tableau dans la variable <code>splitString</code>, nous pouvons accéder à chaque section en utilisant un numéro d'index.</p>
<pre class="code-pre "><code class="code-highlight language-js">splitString[1];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>are
</code></pre>
<p>Si un paramètre donné reste vide, <code>split()</code> créera un tableau séparé par des virgules avec chaque caractère dans la chaîne.</p>

<p>En divisant les chaînes, vous pouvez établir le nombre de mots qui se trouvent dans une phrase et utiliser la méthode pour déterminer quels sont les prénoms et noms de famille des personnes.</p>

<h2 id="retirer-les-espaces">Retirer les espaces</h2>

<p>La méthode <code>trim()</code> de JavaScript supprime les espaces qui se trouvent aux deux extrémités d'une chaîne, mais pas ceux qui se trouvent entre deux. Les espaces peuvent correspondre à des onglets ou des espaces.</p>
<pre class="code-pre "><code class="code-highlight language-js">const tooMuchWhitespace = "     How are you?     ";

const trimmed = tooMuchWhitespace.trim();

console.log(trimmed);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>How are you?
</code></pre>
<p>La méthode <code>trim()</code> est un moyen simple de procéder à la tâche courante qui consiste à supprimer les espaces excédentaires.</p>

<h2 id="trouver-et-remplacer-les-valeurs-d-39-une-chaîne">Trouver et remplacer les valeurs d'une chaîne</h2>

<p>Vous pouvez recherche une valeur dans une chaîne et la remplacer avec une nouvelle en utilisant la méthode <code>replace()</code>. Le premier paramètre correspondra à la valeur à trouver et le second paramètre correspondra la valeur avec laquelle elle sera remplacée.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?"

// Replace the first instance of "How" with "Where"
const newString = originalString.replace("How", "Where");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Where are you?
</code></pre>
<p>Nous pouvons non seulement remplacer une valeur avec une autre valeur de chaîne, mais également utiliser des <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">Regular Expressions</a> pour rendre <code>replace()</code> plus puissant. Par exemple, <code>replace()</code> affecte uniquement la première valeur. Cependant, nous pouvons utiliser la balise <code>g</code> (global) pour récupérer toutes les instances d'une valeur et la balise <code>i</code> (sensible à la casse) pour ignorer la casse.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "Javascript is a programming language. I'm learning javascript."

// Search string for "javascript" and replace with "JavaScript"
const newString = originalString.replace(/javascript/gi, "JavaScript");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>JavaScript is a programming language. I'm learning JavaScript.
</code></pre>
<p>Il s'agit d'une manière très commune d'utiliser des expressions régulières. Consultez <a href="http://regexr.com/">Regexr</a> pour vous faire la main sur d'autres exemples de RegEx.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Les chaînes sont l'un des types de données les plus fréquemment utilisés, avec lesquels nous pouvons faire un grand nombre de choses.</p>

<p>Au cours de ce tutoriel, nous avons appris à faire la différence entre la primitive d'une chaîne et l'objet <code>String</code>. Nous avons également vu de quelle manière les chaînes sont indexées. Enfin, nous avons appris à utiliser les méthodes et propriétés intégrées dans les chaînes pour accéder aux caractères, formater un texte et trouver et remplacer des valeurs.</p>

<p>Pour avoir un aperçu plus général sur les chaînes, lisez le tutoriel « <a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-javascript">Comment travailler avec des chaînes en JavaScript</a>. »</p>
