---
title : "Comprendre la date et l'heure en JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-date-and-time-in-javascript-fr
image: "https://sergio.afanou.com/assets/images/image-midres-47.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>La date et l'heure font partie de nos vies quotidiennes et occupent donc une place importante dans la programmation informatique. En JavaScript, vous aurez éventuellement à créer un site Internet avec un calendrier, un programme de formation ou une interface de prise de rendez-vous. Ces applications doivent afficher des heures applicables en fonction de la zone horaire actuelle de l'utilisateur, ou effectuer des calculs sur les heures d'arrivée et de départ ou de démarrage et de fin. En outre, vous devrez utiliser JavaScript pour générer un rapport quotidien à un certain moment la journée ou appliquer un filtre pour obtenir les restaurants et les établissements actuellement ouverts.</p>

<p>Pour atteindre tous ces objectifs et plus encore, JavaScript intègre l'objet <code>Date</code> et les méthodes connexes. Dans ce tutoriel, vous allez apprendre à formater et utiliser la date et l'heure en JavaScript.</p>

<h2 id="l-39-objet-date">L'objet Date</h2>

<p>L&rsquo;<a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">objet</a> <code>Date</code> est un objet intégré en JavaScript qui stocke la date et l'heure. Il offre un certain nombre de méthodes intégrées de formater et de gérer ces données.</p>

<p>Par défaut, une nouvelle instance <code>Date</code> sans arguments crée un objet correspondant à la date et à l'heure actuelle. Il sera créé en fonction des paramètres du système de l'ordinateur actuellement utilisé.</p>

<p>Pour expliquer l'objet <code>Date</code> en JavaScript, créons une variable et attribuons-lui la date du jour. Cet article a été rédigé le mercredi 18 octobre à Londres (GMT). Il s'agit donc de la date, de l'heure et du fuseau horaire actuels qui sont utilisés ci-dessous.</p>
<div class="code-label " title="now.js">now.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Set variable to current date and time
const now = new Date();

// View the output
now;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Wed Oct 18 2017 12:41:34 GMT+0000 (UTC)
</code></pre>
<p>En examinant la sortie, nous voyons que nous disposons d'une chaîne de date contenant les éléments suivants :</p>

<table><thead>
<tr>
<th style="text-align: center">Jour de la semaine</th>
<th style="text-align: center">mois</th>
<th style="text-align: center">Jour</th>
<th style="text-align: center">Année</th>
<th style="text-align: center">heure</th>
<th style="text-align: center">minute</th>
<th style="text-align: center">seconde</th>
<th style="text-align: center">Fuseau horaire</th>
</tr>
</thead><tbody>
<tr>
<td style="text-align: center">Mer.</td>
<td style="text-align: center">Oct</td>
<td style="text-align: center">18</td>
<td style="text-align: center">2017</td>
<td style="text-align: center">12</td>
<td style="text-align: center">41</td>
<td style="text-align: center">34</td>
<td style="text-align: center">GMT+0000 (UTC)</td>
</tr>
</tbody></table>

<p>La date et l'heure sont décomposées et imprimées de manière compréhensible par l'humain.</p>

<p>Cependant, JavaScript comprend la date en se basant sur un <strong>timestamp</strong> dérivé d’<a href="https://en.wikipedia.org/wiki/Unix_time#History">Unix time</a> qui est une valeur composée du nombre de millisecondes passées depuis le 1er janvier 1970. Nous pouvons obtenir the en utilisant la méthode <code>getTime()</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Get the current timestamp
now.getTime()
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1508330494000
</code></pre>
<p>Le grand nombre qui apparaît dans notre résultat pour l'horodatage actuel représente la même valeur que celle ci-dessus, le 18 octobre 2017.</p>

<p><strong>L'heure Epoch</strong> est également appelée notice nemo, représentée par la chaîne de date <code>01 janvier, 1970 00:00:00 Universal Time (UTC)</code> et par la <code>0</code>. Nous pouvons tester cela dans le navigateur en créant une nouvelle variable et en l'assignant à une nouvelle instance <code>Date</code> sur la base d'un horadatage de <code>0</code>.</p>
<div class="code-label " title="epoch.js">epoch.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Assign the timestamp 0 to a new variable
const epochTime = new Date(0);

epochTime;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>01 January, 1970 00:00:00 Universal Time (UTC)
</code></pre>
<p>L'heure Epoch a été choisie comme standard pour les ordinateurs qui permettent de mesurer le temps par les jours précédents de la programmation. Il est important de comprendre le concept de l'horodatage et de la chaîne de date, car les deux peuvent être utilisés en fonction des paramètres et de l'objectif d'une application.</p>

<p>Pour le moment, nous avons appris à créer une nouvelle instance <code>Date</code> en fonction de l'heure actuelle et à en créer une en fonction d'un horodatage. Au total, il existe quatre formats par lesquels vous pouvez créer une nouvelle <code>Date</code> en JavaScript. En plus de l'heure par défaut et de l'horodatage actuelle, vous pouvez également utiliser une chaîne de date ou spécifier des dates et des heures particulières.</p>

<table><thead>
<tr>
<th>Création</th>
<th>résultat</th>
</tr>
</thead><tbody>
<tr>
<td><code>new Date()</code></td>
<td>La date et l'heure actuelles</td>
</tr>
<tr>
<td><code>new Date(timestamp)</code></td>
<td>Crée la date en fonction de millisecondes</td>
</tr>
<tr>
<td><code>new Date(date string)</code></td>
<td>Crée la date en fonction de la chaîne de date</td>
</tr>
<tr>
<td><code>new Date(year, month, day, hours, minutes, seconds, milliseconds)</code></td>
<td>Crée la date en fonction de la date et de l'heure spécifiées</td>
</tr>
</tbody></table>

<p>Pour expliquer les différentes manières de faire référence à une date spécifique, nous allons créer de nouveaux objets <code>Date</code> qui représenteront le 4 juillet 1776, 12 h 30 GMT de trois manières différentes.</p>
<div class="code-label " title="usa.js">usa.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Timestamp method
new Date(-6106015800000);

// Date string method
new Date("January 31 1980 12:30");

// Date and time method
new Date(1776, 6, 4, 12, 30, 0, 0);
</code></pre>
<p>Les trois exemples ci-dessus créent une date contenant les mêmes informations.</p>

<p>Vous remarquerez que la méthode de l'horodatage a un numéro négatif.</p>

<p>Dans la méthode date et time, nos secondes et millisecondes sont configurées sur <code>0</code>. Si un numéro est absent de la création de <code>Date</code>, un <code>0</code> sera renseigné par défaut. Cependant, l'ordre ne peut être modifié. Gardez donc cela en tête si vous décidez de retirer un numéro. Vous remarquerez également que le mois de juillet est représenté par un <code>6</code>, non pas par le <code>7</code> habituel. En effet, les numéros pour la date et l'heure commencent à partir du <code>0</code>, comme la plupart des éléments de programmation. Consultez le tableau détaillé donné à la section suivante.</p>

<h2 id="récupérer-la-date-avec-get">Récupérer la Date avec <code>get</code></h2>

<p>Une fois que nous avons une date, nous pouvons accéder à tous les composants de la date avec diverses méthodes intégrées. Les méthodes retourneront chaque partie de la date en fonction du fuseau horaire local. Chacune de ces méthodes commence par <code>get</code> et renvoie le numéro correspondant. Le tableau ci-dessous détaille les méthodes <code>get</code> de l'objet <code>Date</code>.</p>

<table><thead>
<tr>
<th>Date/Heure</th>
<th>Méthode</th>
<th>Plage</th>
<th>Exemple</th>
</tr>
</thead><tbody>
<tr>
<td>Année</td>
<td><code>getFullYear()</code></td>
<td>AAAA</td>
<td>1970</td>
</tr>
<tr>
<td>mois</td>
<td><code>getMonth()</code></td>
<td>0-11</td>
<td>0 = janvier</td>
</tr>
<tr>
<td>Jour (du mois)</td>
<td><code>getDate()</code></td>
<td>1-31</td>
<td>1 = 1er du mois</td>
</tr>
<tr>
<td>Jour (de la semaine)</td>
<td><code>getDay()</code></td>
<td>0-6</td>
<td>0 = dimanche</td>
</tr>
<tr>
<td>heure</td>
<td><code>getHours()</code></td>
<td>0-23</td>
<td>0 = minuit</td>
</tr>
<tr>
<td>minute</td>
<td><code>getMinutes()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Seconde</td>
<td><code>getSeconds()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Milliseconde</td>
<td><code>getMilliseconds()</code></td>
<td>0-999</td>
<td></td>
</tr>
<tr>
<td>Horadatage</td>
<td><code>getTime()</code></td>
<td>Millisecondes depuis l'heure Epoch</td>
<td></td>
</tr>
</tbody></table>

<p>Créons une nouvelle date, sur la base du 31 juillet 1980 et attribuons-lui une variable.</p>
<div class="code-label " title="harryPotter.js">harryPotter.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Initialize a new birthday instance
const birthday = new Date(1980, 6, 31);
</code></pre>
<p>Maintenant, nous pouvons utiliser toutes nos méthodes pour obtenir chaque composant de la date, de l'année à la milliseconde.</p>
<div class="code-label " title="getDateComponents.js">getDateComponents.js</div><pre class="code-pre "><code class="code-highlight language-js">
birthday.getFullYear();      // 1980
birthday.getMonth();         // 6
birthday.getDate();          // 31
birthday.getDay();           // 4
birthday.getHours();         // 0
birthday.getMinutes();       // 0
birthday.getSeconds();       // 0
birthday.getMilliseconds();  // 0
birthday.getTime();          // 333849600000 (for GMT)
</code></pre>
<p>Vous aurez parfois besoin d'extraire une seule partie d'une date. Pour cela, vous pouvez utiliser les méthodes <code>get</code> intégrées, les outils que vous pouvez utiliser pour y remédier.</p>

<p>Pour illustrer cela par un exemple, nous pouvons tester la date actuelle par rapport au jour et au mois du 3 octobre pour vérifier s'il s'agit bien du 3 octobre ou pas.</p>
<div class="code-label " title="oct3.js">oct3.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Get today's date
const today = new Date();

// Compare today with October 3rd
if (today.getDate() === 3 &amp;&amp; today.getMonth() === 9) {
  console.log("It's October 3rd.");
} else {
  console.log("It's not October 3rd.");
}
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>It's not October 3rd.
</code></pre>
<p>Étant donné qu'au moment de l'écriture nous ne sommes pas le 3 octobre, la console l'indique.</p>

<p>Les méthodes <code>Date</code> intégrées qui commencent par nous <code>get</code> nous permettent d'accéder aux composants de la date qui renvoient le numéro associé à ce que nous récupérons à partir de l'objet instancié.</p>

<h2 id="modifier-la-date-avec-set">Modifier la Date avec <code>set</code></h2>

<p>Pour toutes les méthodes <code>get</code> que nous avons abordées ci-dessus, il existe une méthode <code>set</code> correspondante. Lorsque vous utilisez <code>get</code> pour récupérer un composant spécifique à partir d'une date, <code>set</code> vous permet de modifier les composants d'une date. Le tableau ci-dessous détaille les méthodes <code>set</code> de l'objet <code>Date</code>.</p>

<table><thead>
<tr>
<th>Date/Heure</th>
<th>Méthode</th>
<th>Plage</th>
<th>Exemple</th>
</tr>
</thead><tbody>
<tr>
<td>Année</td>
<td><code>setFullYear()</code></td>
<td>AAAA</td>
<td>1970</td>
</tr>
<tr>
<td>mois</td>
<td><code>setMonth()</code></td>
<td>0-11</td>
<td>0 = janvier</td>
</tr>
<tr>
<td>Jour (du mois)</td>
<td><code>setDate()</code></td>
<td>1-31</td>
<td>1 = 1er du mois</td>
</tr>
<tr>
<td>Jour (de la semaine)</td>
<td><code>setDay()</code></td>
<td>0-6</td>
<td>0 = dimanche</td>
</tr>
<tr>
<td>heure</td>
<td><code>setHours()</code></td>
<td>0-23</td>
<td>0 = minuit</td>
</tr>
<tr>
<td>minute</td>
<td><code>setMinutes()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Seconde</td>
<td><code>setSeconds()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Milliseconde</td>
<td><code>setMilliseconds()</code></td>
<td>0-999</td>
<td></td>
</tr>
<tr>
<td>Horadatage</td>
<td><code>setTime()</code></td>
<td>Millisecondes depuis l'heure Epoch</td>
<td></td>
</tr>
</tbody></table>

<p>Nous pouvons utiliser ces méthodes <code>set</code> pour modifier un, plusieurs ou tous les composants d'une date. Par exemple, nous pouvons modifier l'année de notre variable <code>birthday</code> ci-dessus en remplaçant <code>1980</code> par <code>1997</code>.</p>
<div class="code-label " title="harryPotter.js">harryPotter.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Change year of birthday date
birthday.setFullYear(1997);

birthday;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Thu Jul 31 1997 00:00:00 GMT+0000 (UTC)
</code></pre>
<p>Dans l'exemple ci-dessus, nous pouvons voir que lorsqu'on appelle la variable <code>birthday</code>, le résultat que nous recevons comporte la nouvelle année.</p>

<p>Les méthodes intégrées commençant par <code>set</code> nous permettent de modifier différentes parties d'un objet <code>Date</code>.</p>

<h2 id="méthodes-de-date-avec-utc">Méthodes de Date avec UTC</h2>

<p>Les méthodes <code>get</code> discutées ci-dessus récupèrent les composants de la date en fonction des paramètres du fuseau horaire de l'utilisateur. Pour avoir un meilleur contrôle sur les dates et les heures, vous pouvez utiliser les méthodes <code>getUTC</code>, qui sont exactement les mêmes que les méthodes <code>get</code>, à la différence qu'elles calculent l'heure en fonction de la norme <a href="https://en.wikipedia.org/wiki/Coordinated_Universal_Time">UTC (Coordinated Universal Time)</a>. Le tableau ci-dessous répertorie les méthodes UTC pour l'objet <code>Date</code> en JavaScript.</p>

<table><thead>
<tr>
<th>Date/Heure</th>
<th>Méthode</th>
<th>Plage</th>
<th>Exemple</th>
</tr>
</thead><tbody>
<tr>
<td>Année</td>
<td><code>getUTCFullYear()</code></td>
<td>AAAA</td>
<td>1970</td>
</tr>
<tr>
<td>mois</td>
<td><code>getUTCMonth()</code></td>
<td>0-11</td>
<td>0 = janvier</td>
</tr>
<tr>
<td>Jour (du mois)</td>
<td><code>getUTCDate()</code></td>
<td>1-31</td>
<td>1 = 1er du mois</td>
</tr>
<tr>
<td>Jour (de la semaine)</td>
<td><code>getUTCDay()</code></td>
<td>0-6</td>
<td>0 = dimanche</td>
</tr>
<tr>
<td>heure</td>
<td><code>getUTCHours()</code></td>
<td>0-23</td>
<td>0 = minuit</td>
</tr>
<tr>
<td>minute</td>
<td><code>getUTCMinutes()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Seconde</td>
<td><code>getUTCSeconds()</code></td>
<td>0-59</td>
<td></td>
</tr>
<tr>
<td>Milliseconde</td>
<td><code>getUTCMilliseconds()</code></td>
<td>0-999</td>
<td></td>
</tr>
</tbody></table>

<p>Vous pouvez exécuter le code suivant pour tester la différence entre les méthodes <code>Get</code> locales et UTC.</p>
<div class="code-label " title="UTC.js">UTC.js</div><pre class="code-pre "><code class="code-highlight language-js">
// Assign current time to a variable
const now = new Date();

// Print local and UTC timezones
console.log(now.getHours());
console.log(now.getUTCHours());
</code></pre>
<p>En exécutant ce code, le système imprimera l'heure actuelle et l'heure au fuseau horaire UTC. Si vous êtes actuellement dans le fuseau horaire UTC, les valeurs que vous obtiendrez après l'exécution du programme ci-dessus seront les mêmes.</p>

<p>UTC est utile dans le sens où il offre une référence internationale standard de l'heure et vous permet de préserver une certaine cohérence dans les codes utilisés par rapport aux fuseaux horaires si cela est applicable à ce que vous développez.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Au cours de ce tutoriel, nous avons appris à créer une instance de l'objet <code>Date</code> et à utiliser ses méthodes intégrées pour accéder et modifier les composants d'une date spécifique. Pour mieux comprendre les dates et les heures en JavaScript, vous pouvez lire la <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date">référence Date sur le réseau de développement Mozilla</a>.</p>

<p>Pour exécuter de nombreuses tâches courantes en JavaScript, il est essentiel que vous sachiez comment travailler avec les dates. Vous pourrez alors faire plusieurs choses, que ce soit pour configurer l'exécution répétitive de rapports ou afficher les dates et les calendriers dans le fuseau horaire applicable.</p>
