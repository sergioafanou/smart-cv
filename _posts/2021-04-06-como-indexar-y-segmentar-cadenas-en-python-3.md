---
title : "Cómo indexar y segmentar cadenas en Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3-es
image: "https://sergio.afanou.com/assets/images/image-midres-16.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Los tipos de datos cadena de Python es una secuencia formada por uno o más caracteres individuales que pueden ser caracteres de letras, números, espacios en blanco o símbolos. Dado que una cadena es una secuencia, se puede acceder a ella de la misma manera que a otros tipos de datos basados en secuencias, mediante la indexación y la segmentación.</p>

<p>Este tutorial explicará cómo acceder a las cadenas a través de la indexación, segmentándolas en secuencias de caracteres, y repasará algunos métodos de recuento y ubicación de caracteres.</p>

<h2 id="cómo-indexar-cadenas">Cómo indexar cadenas.</h2>

<p>Al igual que el <a href="https://www.digitalocean.com/community/tutorials/understanding-lists-in-python-3">tipo de datos de lista</a> que tiene elementos que corresponden a un número de índice, cada uno de los caracteres de una cadena también corresponde a un número de índice, comenzando por el número de índice 0.</p>

<p>En el caso de la cadena <code>Sammy Shark!</code> el desglose del índice tiene el siguiente aspecto:</p>

<table><thead>
<tr>
<th>S</th>
<th>a</th>
<th>m</th>
<th>m</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>a</th>
<th>r</th>
<th>k</th>
<th>!</th>
</tr>
</thead><tbody>
<tr>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
<td>5</td>
<td>6</td>
<td>7</td>
<td>8</td>
<td>9</td>
<td>10</td>
<td>11</td>
</tr>
</tbody></table>

<p>Como puede ver, la primera <code>S</code> comienza en el índice 0 y la cadena termina en el índice 11 con el símbolo <code>!</code> .</p>

<p>También observamos que el carácter de espacio en blanco entre <code>Sammy</code> y <code>Shark</code> también se corresponde con su propio número de índice. En este caso, el número de índice asociado con el espacio en blanco es 5.</p>

<p>El punto de exclamación (<code>!</code>) también tiene un número de índice asociado con él. Cualquier otro símbolo o signo de puntuación, como <code>*#$&amp;. ;?</code>, también es un carácter y estaría asociado a su propio número de índice.</p>

<p>El hecho de que cada carácter de una cadena de Python tenga un número de índice correspondiente permite acceder y manipular las cadenas de la misma manera que se puede hacer con otros tipos de datos secuenciales.</p>

<h2 id="acceso-a-caracteres-según-el-número-de-índice-positivo">Acceso a caracteres según el número de índice positivo</h2>

<p>Al hacer referencia a los números del índice, se puede aislar uno de los caracteres de una cadena. Eso se hace poniendo los números de índice entre corchetes. Declaremos una cadena, imprimámosla e invoquemos el número de índice entre corchetes:</p>
<pre class="code-pre "><code class="code-highlight language-python">ss = "Sammy Shark!"
print(ss[4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>y
</code></pre>
<p>Cuando nos referimos a un número de índice concreto de una cadena, Python devuelve el carácter que se encuentra en esa posición. Dado que la letra <code>y</code> está en el número de índica 4 de la cadena <code>ss = "Sammy Shark!"</code>, cuando imprimimos <code>ss[4]</code> recibimos <code>y</code> como resultado.</p>

<p>Los números de índice nos permiten acceder a caracteres específicos dentro de una cadena.</p>

<h2 id="acceso-a-caracteres-según-el-número-de-índice-negativo">Acceso a caracteres según el número de índice negativo</h2>

<p>Si tenemos una cadena larga y queremos localizar un elemento al final, también podemos contar hacia atrás desde el final de la cadena, comenzando en el número de índice <code>-1</code>.</p>

<p>En el caso de la misma cadena <code>Sammy Shark!</code>, el desglose del índice negativo tiene el siguiente aspecto:</p>

<table><thead>
<tr>
<th>S</th>
<th>a</th>
<th>m</th>
<th>m</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>a</th>
<th>r</th>
<th>k</th>
<th>!</th>
</tr>
</thead><tbody>
<tr>
<td>-12</td>
<td>-11</td>
<td>-10</td>
<td>-9</td>
<td>-8</td>
<td>-7</td>
<td>-6</td>
<td>-5</td>
<td>-4</td>
<td>-3</td>
<td>-2</td>
<td>-1</td>
</tr>
</tbody></table>

<p>Usando números de índice negativos, podemos imprimir el carácter <code>r</code>, haciendo referencia a su posición en el índice -3, como se muestra a continuación:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-3])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Usar números de índice negativos puede ser ventajoso para aislar un solo carácter al final de una cadena larga.</p>

<h2 id="segmentación-de-cadenas">Segmentación de cadenas</h2>

<p>También podemos invocar un rango de caracteres de la cadena. Digamos que solo queremos imprimir la palabra <code>Shark</code>. Podemos hacerlo creando una <strong>rebanada</strong>, que es una secuencia de caracteres dentro de una cadena original. Con las rebanadas, podemos invocar varios valores de caracteres creando un rango de números de índice separados por dos puntos <code>[x:y]</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Cuando se crea una rebanada, como en <code>[6:11]</code>, el primer número de índice es donde comienza la rebanada (inclusivo), y el segundo número de índice es donde termina la rebanada (exclusivo), que es el motivo por el que en nuestro ejemplo anterior el rango tiene que ser el número de índice que ocurriría justo después de que la cadena termine.</p>

<p>Cuando se segmentan cadenas, se está creando una <strong>subcadena</strong>, que es esencialmente una cadena que existe dentro de otra cadena. Cuando invocamos <code>ss[6:11]</code>, invocamos la subcadena <code>Shark</code> que existe dentro de la cadena <code>Sammy Shark!</code>.</p>

<p>Si queremos incluir cualquiera de los extremos de una cadena, podemos omitir uno de los números en la sintaxis <code>string[n:n]</code>. Por ejemplo, si queremos imprimir la primera palabra de cadena <code>ss</code>, — “Sammy” — , podemos hacerlo escribiendo lo siguiente:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[:5])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy
</code></pre>
<p>Para eso, omitimos el número de índice antes de los dos puntos en la sintaxis de la rebanada y solo incluimos el número de índice después de los dos puntos, que hace referencia al final de la subcadena.</p>

<p>Para imprimir una subcadena que comienza en el medio de una cadena y se imprime hasta el final, podemos hacerlo incluyendo solo el número de índice antes de los dos puntos, como se muestra a continuación:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[7:])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>hark!
</code></pre>
<p>Al incluir solo el número de índice antes de los dos puntos y dejar el segundo número de índice fuera de la sintaxis, la subcadena irá desde el carácter del número de índice invocado hasta el final de la cadena.</p>

<p>También puede usar números de índice negativos para segmentar una cadena. Como ya mencionamos anteriormente, los números de índice negativos de una cadena comienzan en -1 y se cuentan hacia abajo desde ahí hasta llegar al inicio de la cadena. Al utilizar números de índice negativos, empezaremos por el número más bajo, ya que aparece antes en la cadena.</p>

<p>Utilicemos dos números de índice negativos para segmentar la cadena <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-4:-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ark
</code></pre>
<p>La subcadena “ark” se imprime a partir de la cadena “Sammy Shark!” porque el carácter &ldquo;a&rdquo; aparece en la posición del número de índice -4, y el carácter &ldquo;k&rdquo; aparece justo antes de la posición del número de índice -1.</p>

<h2 id="especificación-de-zancadas-mientras-se-segmenta-la-cadena">Especificación de zancadas mientras se segmenta la cadena</h2>

<p>La segmentación de cadenas puede aceptar un tercer parámetro además de dos números de índice.  El tercer parámetro especifica la <strong>zancada</strong>, que hace referencia a la cantidad de caracteres que se deben seguir después de recuperar el primer carácter de la cadena. Hasta ahora, hemos omitido el parámetro zancada y Python utiliza por defecto la zancada de 1, de modo que cada carácter entre dos números de índice se recupere.</p>

<p>Veamos nuevamente el ejemplo anterior que imprime la subcadena “Shark”:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Podemos obtener los mismos resultados incluyendo un tercer parámetro con una zancada de 1:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11:1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Por lo tanto, una zancada de 1 tomará cada carácter entre dos números de índice de una rebanada. Si omitimos el parámetro de zancada, Python pondrá por defecto 1.</p>

<p>Si, en cambio, aumentamos la zancada, veremos que se saltan caracteres:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SmySak
</code></pre>
<p>Especificar la zancada de 2 como el último parámetro en la sintaxis de Python, <code>ss[0:12:2]</code> se salta cualquier otro carácter. Veamos los caracteres que aparecen en rojo:</p>

<p><span class="highlight">S</span>a<span class="highlight">m</span>m<span class="highlight">y</span> <span class="highlight">S</span>h<span class="highlight">a</span>r<span class="highlight">k</span>!</p>

<p>Tenga en cuenta que el carácter de espacio en blanco en el número de índice 5 también se salta con una zancada de 2 especificada.</p>

<p>Si usamos un número más grande para nuestro parámetro de zancada, tendremos una subcadena significativamente más pequeña:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Especificar la zancada de 4 como el último parámetro en la sintaxis de Python, <code>ss[0:12:4]</code> se imprime solo uno de cada cuatro caracteres. De nuevo, veamos los caracteres que aparecen en rojo:</p>

<p><span class="highlight">S</span>amm<span class="highlight">y</span> Sh<span class="highlight">a</span>rk!</p>

<p>En este ejemplo, también se omite el carácter de espacio en blanco.</p>

<p>Dado que estamos imprimiendo toda la cadena, podemos omitir los dos números de índice y mantener los dos puntos dentro de la sintaxis para conseguir el mismo resultado:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Si se omiten los dos números de índice y se conservan los dos puntos, se mantendrá toda la cadena dentro del rango, mientras que si se añade un parámetro final para la zancada se especificará el número de caracteres que se van a saltar.</p>

<p>Además, puede indicar un valor numérico negativo para la zancada, que podemos utilizar para imprimir la cadena original en orden inverso si establecemos la zancada en -1:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!krahS ymmaS
</code></pre>
<p>Los dos puntos sin parámetro especificado incluirán todos los caracteres de la cadena original, una zancada de 1 incluirá todos los caracteres sin saltárselos, y la negación de esa zancada invertirá el orden de los caracteres.</p>

<p>Hagamos esto de nuevo pero con una zancada de -2:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!rh ma
</code></pre>
<p>En este ejemplo, <code>ss[::-2]</code>, estamos tratando con la totalidad de la cadena original, ya que no se incluyen números de índice en los parámetros e invirtiendo la cadena a través de la zancada negativa. Además, al tener una zancada de -2 estamos saltando una de cada dos letras de la cadena invertida:</p>

<p><span class="highlight">! </span>k<span class="highlight">r</span>a<span class="highlight">h</span>S<span class="highlight">[espacio en blanco]</span>y<span class="highlight">m</span>m<span class="highlight">a</span>S</p>

<p>El carácter de espacio en blanco se imprime en este ejemplo.</p>

<p>Cuando especifica el tercer parámetro de la sintaxis de segmentación de Python, está indicando la zancada de la subcadena que está sacando de la cadena original.</p>

<h2 id="métodos-de-conteo">Métodos de conteo</h2>

<p>Mientras pensamos en los números de índice relevantes que corresponden a los caracteres dentro de las cadenas, vale la pena repasar algunos de los métodos que cuentan cadenas o devuelven números de índice. Esto puede ser útil para limitar el número de caracteres que queremos aceptar dentro de un formulario de entrada del usuario, o para comparar cadenas. Igual que otros tipos de datos secuenciales, las cadenas se pueden segmentar mediante diversos métodos.</p>

<p>Primero, veremos el método <code>len()</code> que puede obtener la longitud de cualquier tipo de datos que sea una secuencia, ya sea ordenada o desordenada, incluyendo cadenas, listas, <a href="https://www.digitalocean.com/community/tutorials/understanding-tuples-in-python-3">tuplas</a> y <a href="https://www.digitalocean.com/community/tutorials/understanding-dictionaries-in-python-3">diccionarios</a>.</p>

<p>Vamos a imprimir la longitud de la cadena <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len(ss))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>La longitud de la cadena “Sammy Shark!” es de 12 caracteres de largo, incluyendo el carácter de espacio en blanco y el símbolo de exclamación.</p>

<p>En vez de usar una variable, también podemos pasar una cadena directamente al método <code>len()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len("Let's print the length of this string."))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>38
</code></pre>
<p>El método <code>len()</code> cuenta el número total de caracteres dentro de una cadena.</p>

<p>Si queremos contar el número de veces que aparece un carácter en particular o una secuencia de caracteres en una cadena, podemos hacerlo con el método <code>str.count()</code>. Usemos nuestra cadena <code>ss = "Sammy Shark!"</code> y contemos el número de veces que aparece el carácter “a”:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("a"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2
</code></pre>
<p>Podemos buscar otro carácter:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("s"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>0
</code></pre>
<p>Aunque la letra “S” está en la cadena, es importante tener en cuenta que cada carácter distingue entre mayúsculas y minúsculas. Si queremos buscar todas las letras de una cadena independientemente de las mayúsculas y minúsculas, podemos usar el método <code>str.lower()</code> para convertir todos los caracteres de la cadena en mayúsculas primero. Puede obtener más información sobre este método en “<a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3#making-strings-upper-and-lower-case">Introducción a los métodos de cadena en Python 3</a>”.</p>

<p>Probemos <code>str.count()</code> con una secuencia de caracteres:</p>
<pre class="code-pre "><code class="code-highlight language-python">likes = "Sammy likes to swim in the ocean, likes to spin up servers, and likes to smile."
print(likes.count("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>3
</code></pre>
<p>En la cadena <code>likes</code>, la secuencia de caracteres que equivale a “likes” aparece 3 veces en la cadena original.</p>

<p>También podemos encontrar en qué posición de la cadena aparece un carácter o secuencia de caracteres. Podemos hacerlo con el método <code>str.find()</code> y devolverá la posición del carácter según el número de índice.</p>

<p>Podemos comprobar dónde aparece la primera &ldquo;m&rdquo; en la cadena <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.find("m"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>2
</code></pre>
<p>El primer carácter “m” aparece en la posición de índice de 2 en la cadena “Sammy Shark!” Podemos revisar las posiciones del número de índice de la cadena <code>ss</code> <a href="https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3#how-strings-are-indexed">anterior</a>.</p>

<p>Veamos dónde se encuentra la primera secuencia de caracteres “likes” en la cadena <code>likes</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>6
</code></pre>
<p>La primera instancia de la secuencia de caracteres “likes” comienza en la posición del número de índice 6, que es donde se encuentra el carácter <code>I</code> de la secuencia <code>likes</code>.</p>

<p>¿Qué pasa si queremos ver dónde comienza la segunda secuencia de “likes”? Podemos hacerlo pasando un segundo parámetro al método <code>str.find()</code> que comenzará en un número de índice determinado. Por lo tanto, en vez de comenzar por el principio de la cadena, vamos a comenzar después del número de índice 9:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 9))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>34
</code></pre>
<p>En este segundo ejemplo que comienza en el número de índice 9, la primera aparición de la secuencia de caracteres “likes” comienza en el número de índice 34.</p>

<p>Además, podemos especificar un final al rango como tercer parámetro. Igual que la segmentación, podemos hacerlo contando hacia atrás usando un número de índice negativo:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 40, -6))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>64
</code></pre>
<p>Este último ejemplo busca la posición de la secuencia “likes” entre los números de índice 40 y -6. Dado que el último parámetro ingresado es un número negativo, se contará desde el final de la cadena original.</p>

<p>Los métodos de cadena de <code>len()</code>, <code>str.count()</code> y <code>str.find()</code> pueden usarse para determinar la longitud, el conteo de caracteres o secuencias de caracteres, y las posiciones de índice de los caracteres o secuencias de caracteres dentro de las cadenas.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Poder invocar números de índice específicos de cadenas o una porción en particular de una cadena, brinda una mayor flexibilidad al trabajar con este tipo de datos. Dado que las cadenas, como las listas y las tuplas son un tipo de datos basados en secuencias, se puede acceder a ella mediante la indexación y la segmentación.</p>

<p>Puede obtener más información sobre <a href="https://www.digitalocean.com/community/tutorials/how-to-format-text-in-python-3">el formato</a> de las cadenas y <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3">los métodos de cadena</a> para continuar aprendiendo sobre las cadenas.</p>
