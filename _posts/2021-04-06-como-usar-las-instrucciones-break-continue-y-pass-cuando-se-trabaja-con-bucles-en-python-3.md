---
title : "Cómo usar las instrucciones break, continue y pass cuando se trabaja con bucles en Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3-es
image: "https://sergio.afanou.com/assets/images/image-midres-11.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Usar <strong>bucles for</strong> y <strong><a href="https://www.digitalocean.com/community/tutorials/how-to-construct-while-loops-in-python-3">bucles while</a></strong> en Python le permite automatizar y repetir tareas de manera eficiente.</p>

<p>Sin embargo, a veces, es posible que un factor externo influya en la forma en que se ejecuta su programa. Cuando esto sucede, es posible que prefiera que su programa cierre un bucle por completo, omita parte de un bucle antes de continuar o ignore ese factor externo. Puede hacer estas acciones con las instrucciones <code>break</code>, <code>continue</code> y <code>pass</code>.</p>

<h2 id="instrucción-break">Instrucción break</h2>

<p>En Python, la instrucción <code>break</code> le proporciona la oportunidad de cerrar un bucle cuando se activa una condición externa. Debe poner la instrucción <code>break</code> dentro del bloque de código bajo la instrucción de su bucle, generalmente después de una instrucción <code>if</code> condicional.</p>

<p>Veamos un ejemplo en el que se utiliza la instrucción <code>break</code> en un bucle <code>for</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        break    # break here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>En este pequeño programa, la variable <code>number</code> se inicia en 0. Luego, una instrucción <code>for</code> construye el bucle siempre que la variable <code>number</code> sea inferior a 10.</p>

<p>En el bucle <code>for</code>, existe una instrucción <code>if</code> que presenta la condición de que <em>si</em> la variable <code>number</code> es equivalente al entero 5, <em>entonces</em> el bucle se romperá.</p>

<p>En el bucle también existe una instrucción <code>print()</code> que se ejecutará con cada iteración del bucle <code>for</code> hasta que se rompa el bucle, ya que está después de la instrucción <code>break</code>.</p>

<p>Para saber cuándo estamos fuera del bucle, hemos incluido una instrucción <code>print()</code> final fuera del bucle <code>for</code>.</p>

<p>Cuando ejecutemos este código, el resultado será el siguiente:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Out of loop
</code></pre>
<p>Esto muestra que una vez que se evalúa el entero <code>number</code> como equivalente a 5, el bucle se rompe porque se indica al programa que lo haga con la instrucción <code>break</code>.</p>

<p>La instrucción <code>break</code> hace que un programa interrumpa un bucle.</p>

<h2 id="instrucción-continue">Instrucción continue</h2>

<p>La instrucción <code>continue</code> da la opción de omitir la parte de un bucle en la que se activa una condición externa, pero continuar para completar el resto del bucle. Es decir, la iteración actual del bucle se interrumpirá, pero el programa volverá a la parte superior del bucle.</p>

<p>La instrucción <code>continue</code> se encuentra dentro del bloque de código abajo de la instrucción del bucle, generalmente después de una instrucción <code>if</code> condicional.</p>

<p>Usando el mismo programa de bucle <code>for</code> que en la sección anterior <a href="https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3#break-statement">Instrucción break</a>, emplearemos la instrucción <code>continue</code> en vez de la instrucción <code>break</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        continue    # continue here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>La diferencia al usar la instrucción <code>continue</code> en vez de una instrucción <code>break</code> radica en que nuestro código continuará a pesar de la interrupción cuando la variable <code>number</code> se evalúe como equivalente a 5. Veamos el resultado:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Number is 6
Number is 7
Number is 8
Number is 9
Out of loop
</code></pre>
<p>Aquí, <code>Number is 5</code> nunca aparece en el resultado, pero el bucle continúa después de ese punto para imprimir líneas para los números 6 a 10 antes de cerrarse.</p>

<p>Puede usar la instrucción <code>continue</code> para evitar código condicional profundamente anidado o para optimizar un bucle eliminando los casos frecuentes que desee rechazar.</p>

<p>La instrucción <code>continue</code> hace que un programa omita determinados factores que surgen dentro de un bucle, pero luego continuará con resto de este.</p>

<h2 id="instrucción-pass">Instrucción Pass</h2>

<p>Cuando se activa una condición externa, la instrucción <code>pass</code> permite manejar la condición sin que el bucle se vea afectado de ninguna manera; todo el código continuará leyéndose a menos que se produzca la instrucción <code>break</code> u otra instrucción.</p>

<p>Igual que con las demás instrucciones, la instrucción <code>pass</code> se encuentra dentro del bloque de código abajo de la instrucción del bucle, normalmente después de una instrucción <code>if</code> condicional.</p>

<p>Usando el mismo bloque de código que antes, sustituiremos la instrucción <code>break</code> o <code>continue</code> con una instrucción <code>pass</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        pass    # pass here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>La instrucción <code>pass</code> que se produce después de la instrucción condicional <code>if</code> le indica al programa que continúe ejecutando el bucle e ignore el hecho de que la variable <code>number</code> se evalúa como equivalente a 5 durante una de sus iteraciones.</p>

<p>Ejecutaremos el programa y consideraremos el resultado:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Number is 5
Number is 6
Number is 7
Number is 8
Number is 9
Out of loop
</code></pre>
<p>Al usar la instrucción <code>pass</code> en este programa, observamos que el programa se ejecuta exactamente como lo haría si no hubiera instrucción condicional en el programa. La instrucción <code>pass</code> le indica al programa que ignore esa condición y continúe ejecutando el programa como de costumbre.</p>

<p>La instrucción <code>pass</code> puede crear clases mínimas o actuar como marcador de posición al trabajar en un nuevo código y pensar en un nivel de algoritmo antes de preparar detalles.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Las instrucciones <code>break</code>, <code>continue</code> y <code>pass</code> en Python le permitirán usar los bucles <code>for</code> y los bucles <code>while</code> en su código de manera más eficaz.</p>

<p>Para trabajar más con las instrucciones <code>break</code> y <code>pass</code>, puede seguir el tutorial de nuestro proyecto “<a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-twitterbot-with-python-3-and-the-tweepy-library">Cómo crear un Twitterbot con Python 3 y la Biblioteca Tweepy</a>”.</p>
