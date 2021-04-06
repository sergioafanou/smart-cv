---
title : "Cómo indexar, dividir y manipular cadenas en JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-split-and-manipulate-strings-in-javascript-es
image: "https://sergio.afanou.com/assets/images/image-midres-53.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Una <strong>cadena</strong> es una secuencia de uno o más caracteres que pueden ser letras, números o símbolos. Se puede acceder a cada carácter de una cadena de JavaScript mediante un número de índice, y todas las cadenas tienen métodos y propiedades disponibles para ellas.</p>

<p>En este tutorial, aprenderemos la diferencia entre las primitivas de cadena y el objeto <code>String</code>, cómo indexar cadenas, cómo acceder a los caracteres de una cadena, y las propiedades y métodos comunes utilizados en las cadenas.</p>

<h2 id="las-primitivas-de-cadena-y-los-objetos-string">Las primitivas de cadena y los objetos String</h2>

<p>Primero, aclararemos los dos tipos de cadenas. JavaScript distingue entre la <strong>primitiva de cadena</strong>, un tipo de datos inmutable, y el objeto <code>String</code>.</p>

<p>Para probar la diferencia entre ambos tipos, inicializaremos una primitiva de cadena y un objeto de cadena.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Initializing a new string primitive
const stringPrimitive = "A new string.";

// Initializing a new String object
const stringObject = new String("A new string.");  
</code></pre>
<p>Podemos usar el operador <code>typeof</code> para determinar el tipo de un valor. En el primer ejemplo, simplemente asignamos una cadena a una variable.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringPrimitive;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>string
</code></pre>
<p>En el segundo ejemplo, usamos <code>new String()</code> para crear un objeto de cadena y asignarlo a una variable.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringObject;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>object
</code></pre>
<p>La mayoría de veces se crearán primitivas de cadena. JavaScript puede acceder y usar las propiedades y los métodos incorporados de la envoltura de objetos <code>String</code> sin cambiar realmente la primitiva de cadena que se creó en un objeto.</p>

<p>Aunque este concepto es un poco desafiante al principio, debe tener en cuenta la diferencia entre primitivos y objetos. Esencialmente, hay métodos y propiedades disponibles para todas las cadenas, y en segundo plano JavaScript realizará una conversión a objeto y de vuelta a primitivo cada vez que se invoque un método o propiedad.</p>

<h2 id="cómo-se-indexan-las-cadenas">Cómo se indexan las cadenas</h2>

<p>Cada uno de los caracteres de una cadena corresponden a un número de índice, comenzando por <code>0</code>.</p>

<p>Para demostrarlo, crearemos una cadena con el valor <code>How are you?</code>.</p>

<table><thead>
<tr>
<th style="text-align: center">H</th>
<th style="text-align: center">o</th>
<th style="text-align: center">w</th>
<th style="text-align: center"></th>
<th style="text-align: center">A</th>
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

<p>El primer carácter de la cadena es <code>H</code>, que corresponde al índice <code>0</code>. El último carácter es <code>?</code>, que corresponde a <code>11</code>. Los caracteres de espacio en blanco también tienen un índice, el <code>3</code> y <code>7</code>.</p>

<p>El hecho de poder acceder a todos los caracteres de una cadena nos permite trabajar con ella y manipularla de varias maneras.</p>

<h2 id="acceso-a-los-caracteres">Acceso a los caracteres</h2>

<p>Vamos a demostrar cómo acceder a los caracteres e índices con la cadena <code>How are you?</code> .</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?";
</code></pre>
<p>Utilizando la notación de corchetes, podemos acceder a cualquier carácter de la cadena.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?"[5];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>También podemos usar el método <code>charAt()</code> para devolver el carácter usando el número de índice como parámetro.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".charAt(5);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>De manera alternativa, podemos usar <code>indexOf()</code> para devolver el número de índice por la primera instancia de un carácter.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<p>Aunque &ldquo;o&rdquo; aparece dos veces en la cadena <code>How are you?</code>, <code>indexOf()</code> obtendrá la primera instancia.</p>

<p><code>lastIndexOf()</code> se utiliza para encontrar la última instancia.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".lastIndexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>9
</code></pre>
<p>Para ambos métodos, también puede buscar varios caracteres en la cadena. Devolverá el número de índice del primer carácter de la instancia.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("are");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>4
</code></pre>
<p>Por otro lado, el método <code>slice()</code> devuelve los caracteres entre dos números de índice. El primer parámetro será el número de índice de inicio y el segundo parámetro será el número de índice donde debe terminar.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8, 11);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you
</code></pre>
<p>Tenga en cuenta que <code>11</code> es <code>?</code>, pero <code>?</code> no forma parte del resultado devuelto. <code>slice()</code> devolverá lo que está en el medio, pero no incluirá el último parámetro.</p>

<p>Si no se incluye un segundo parámetro, <code>slice()</code> devolverá todo en el resultado, desde el parámetro hasta el final de la cadena.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you?
</code></pre>
<p>Para resumir, <code>charAt()</code> y <code>slice()</code> ayudarán a devolver valores de cadena basados en números de índice, e <code>indexOf()</code> y <code>lastIndexOf()</code> harán lo opuesto y devolverán los números de índice basados en los caracteres de cadena proporcionados.</p>

<h2 id="búsqueda-de-la-longitud-de-una-cadena">Búsqueda de la longitud de una cadena</h2>

<p>Al usar la propiedad <code>length</code>, podemos devolver el número de caracteres en una cadena.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".length;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>Recuerde que la propiedad <code>length</code> devuelve el número real de caracteres comenzando por 1, que salen en 12, no el número de índice final, que comienza en <code>0</code> y termina en <code>11</code>.</p>

<h2 id="conversión-a-mayúsculas-o-minúsculas">Conversión a mayúsculas o minúsculas</h2>

<p>Los dos métodos incorporados <code>toUpperCase()</code> y <code>toLowerCase()</code> son formas útiles de dar formato al texto y hacer comparaciones textuales en JavaScript.</p>

<p><code>toUpperCase()</code> convertirá todos los caracteres en mayúsculas.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toUpperCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HOW ARE YOU?
</code></pre>
<p><code>toLowerCase()</code> convertirá todos los caracteres en minúsculas.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toLowerCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>how are you?
</code></pre>
<p>Estos dos métodos para crear formato no requieren parámetros adicionales.</p>

<p>Cabe destacar que estos métodos no modifican la cadena original.</p>

<h2 id="división-de-cadenas">División de cadenas</h2>

<p>JavaScript tiene un método muy útil para dividir una cadena por un carácter y crear una nueva <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">matriz</a> desde las secciones. Usaremos el método <code>split()</code> para separar la matriz por un carácter de espacio en blanco, representado por <code>" "</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?";

// Split string by whitespace character
const splitString = originalString.split(" ");

console.log(splitString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[ 'How', 'are', 'you?' ]
</code></pre>
<p>Ahora que tenemos una nueva matriz en la variable <code>splitString</code>, podemos acceder a cada sección con un número de índice.</p>
<pre class="code-pre "><code class="code-highlight language-js">splitString[1];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>are
</code></pre>
<p>Si se proporciona un parámetro vacío, <code>split()</code> creará una matriz separada por comas con cada carácter de la cadena.</p>

<p>Al dividir cadenas, se puede determinar la cantidad de palabras en una frase y usar el método como una forma de determinar los nombres y apellidos de las personas, por ejemplo.</p>

<h2 id="recorte-de-espacios-en-blanco">Recorte de espacios en blanco</h2>

<p>El método <code>trim()</code> de JavaScript elimina los espacios en blanco de los dos extremos de una cadena, pero no en el medio de la misma. Los espacios en blanco pueden ser tabulaciones o espacios.</p>
<pre class="code-pre "><code class="code-highlight language-js">const tooMuchWhitespace = "     How are you?     ";

const trimmed = tooMuchWhitespace.trim();

console.log(trimmed);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>How are you?
</code></pre>
<p>El método <code>trim()</code> es una forma sencilla de realizar la tarea común de eliminar el exceso de espacios en blanco.</p>

<h2 id="búsqueda-y-sustitución-de-valores-de-cadenas">Búsqueda y sustitución de valores de cadenas</h2>

<p>Podemos buscar un valor en una cadena y sustituirlo por un nuevo valor usando el método <code>replace()</code>. El primer parámetro será el valor que a encontrar, y el segundo parámetro será el valor a sustituir.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?"

// Replace the first instance of "How" with "Where"
const newString = originalString.replace("How", "Where");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Where are you?
</code></pre>
<p>Además de poder sustituir un valor con otro valor de cadena, también podemos usar las <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">Expresiones regulares</a> para hacer que <code>replace()</code> sea más potente. Por ejemplo, <code>replace()</code> solo afecta al primer valor, pero podemos usar el indicador (global) <code>g</code> para capturar todas las instancias de un valor y el indicador (que no distingue entre mayúsculas y minúsculas) <code>i</code> para ignorar las mayúsculas y minúsculas.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "Javascript is a programming language. I'm learning javascript."

// Search string for "javascript" and replace with "JavaScript"
const newString = originalString.replace(/javascript/gi, "JavaScript");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>JavaScript is a programming language. I'm learning JavaScript.
</code></pre>
<p>Esta es una tarea muy común que utiliza las Expresiones regulares. Visite <a href="http://regexr.com/">Regexr</a> para practicar más ejemplos de expresiones regulares (RegEx).</p>

<h2 id="conclusión">Conclusión</h2>

<p>Las cadenas son uno de los tipos de datos usados con mayor frecuencia, y hay muchas cosas que podemos hacer con ellas.</p>

<p>En este tutorial, aprendimos la diferencia entre una primitiva de cadena y un objeto <code>String</code>, cómo indexar las cadenas y cómo utilizar los métodos y propiedades incorporados de las cadenas para acceder a los caracteres, dar formato al texto y las cadenas (encontrando) formateando, y encontrar y reemplazar valores.</p>

<p>Para una descripción más general sobre las cadenas, consulte el tutorial &ldquo;<a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-javascript">Cómo trabajar con cadenas en JavaScript</a>&rdquo;.</p>
