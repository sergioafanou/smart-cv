---
title : "Индексация, разделение и управление строками в JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-split-and-manipulate-strings-in-javascript-ru
image: "https://sergio.afanou.com/assets/images/image-midres-27.jpg"
---

<h3 id="Введение">Введение</h3>

<p><strong>Строка</strong> — это последовательность из одного или нескольких символов, которая может включать цифры, буквы или специальные символы. Для доступа к каждому символу в JavaScript можно использовать индекс, для всех строк доступны методы и свойства.</p>

<p>В этом учебном модуле мы изучим разницу между примитивами строк и объектом <code>String</code>, узнаем об индексации строк, доступе к символам в строке и общих свойствах и методах, используемых при работе со строками.</p>

<h2 id="Примитивы-и-объекты-string">Примитивы и объекты String</h2>

<p>Для начала мы расскажем о двух типах строк. JavaScript различает <strong>примитив string</strong>, или неизменный тип данных, и объект <code>String</code>.</p>

<p>Чтобы показать различия, мы инициализируем примитив строки и объект строки.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Initializing a new string primitive
const stringPrimitive = "A new string.";

// Initializing a new String object
const stringObject = new String("A new string.");  
</code></pre>
<p>Для определения типа значения можно использовать оператор <code>typeof</code>. В первом примере мы просто назначили строку переменной.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringPrimitive;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>string
</code></pre>
<p>Во втором примере мы использовали <code>new String()</code> для создания объекта строки и назначения его переменной.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringObject;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>object
</code></pre>
<p>Большую часть времени вы будете создавать примитивы строк. JavaScript обеспечивает возможность доступа и использования встроенных свойств и методов оболочки объекта <code>String</code> без фактического изменения примитива строк, который мы создали в объекте.</p>

<p>Хотя эта концепция довольно сложная, вам следует понимать различия между примитивом и объектом. Методы и свойства доступны всем строкам, и JavaScript в фоновом режиме выполняет преобразование примитивов в объекты и обратно каждый раз, когда вызываются метод или свойство.</p>

<h2 id="Процедура-индексации-строк">Процедура индексации строк</h2>

<p>Каждому из символов в строке соответствует числовой индекс, начиная с <code>0</code>.</p>

<p>Для наглядности мы создадим строку со значением <code>How are you?</code>.</p>

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

<p>Первый символ этой строки — <code>H</code>, ему соответствует индекс <code>0</code>. Последний символ — <code>?</code>, соответствующий символу <code>11</code>. У символов пробела также имеются индексы на позициях <code>3</code> и <code>7</code>.</p>

<p>Возможность доступа к каждому символу строки даст нам множество способов для работы со строками и совершения манипуляций с ними.</p>

<h2 id="Доступ-к-символам">Доступ к символам</h2>

<p>Мы продемонстрируем, как получить доступ к символам и индексам в строке <code>How are you?</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?";
</code></pre>
<p>Используя обозначения в квадратных скобках, мы можем получить доступ к любому символу в строке.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?"[5];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Также мы можем использовать метод <code>charAt()</code> для возврата символа, используя числовой индекс в качестве параметра.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".charAt(5);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Другой способ: мы можем использовать <code>indexOf()</code> для возврата числового индекса для первого экземпляра символа.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<p>Хотя &ldquo;o&rdquo; появляется в строке <code>How are you?</code> дважды, метод <code>indexOf()</code> выдаст результат для первого экземпляра.</p>

<p><code>lastIndexOf()</code> используется для поиска последнего экземпляра.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".lastIndexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>9
</code></pre>
<p>С помощью обоих этих методов также можно найти несколько символов в строке. Он возвращает индекс первого символа в экземпляре.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("are");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>4
</code></pre>
<p>С другой стороны, метод <code>slice()</code> возвращает символы между двумя числовыми индексами. Первым параметром будет начальный числовой индекс, а вторым — конечный числовой индекс.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8, 11);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you
</code></pre>
<p>Обратите внимание, что <code>11</code> соответствует <code>?</code>, но <code>?</code> не является частью вывода, поскольку метод <code>slice()</code> возвращает символы между индексами, не включая последний параметр.</p>

<p>Если второй параметр не указывается, <code>slice()</code> возвращает все символы от первого параметра до конца строки.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you?
</code></pre>
<p>Таким образом, <code>charAt()</code> и <code>slice()</code> возвращают значения строк по числовым индексам, а <code>indexOf()</code> и <code>lastIndexOf()</code> имеют противоположное действие, возвращая числовые индексы по заданным символам строки.</p>

<h2 id="Определение-длины-строки">Определение длины строки</h2>

<p>Свойство <code>length</code> позволяет вывести количество символов в строке.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".length;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>Помните, что свойство <code>length</code> возвращает фактическое количество символов, начиная с 1, то есть, 12, а не последний числовой индекс диапазона, который начинается с <code>0</code> и заканчивается <code>11</code>.</p>

<h2 id="Преобразование-в-верхний-или-нижний-регистр">Преобразование в верхний или нижний регистр</h2>

<p>Встроенные методы <code>toUpperCase()</code> и <code>toLowerCase()</code> можно использовать для форматирования и сравнения текста в JavaScript.</p>

<p><code>toUpperCase()</code> конвертирует все символы в верхний регистр.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toUpperCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HOW ARE YOU?
</code></pre>
<p><code>toLowerCase()</code> конвертирует все символы в нижний регистр.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toLowerCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>how are you?
</code></pre>
<p>Эти два метода форматирования не принимают дополнительных параметров.</p>

<p>Следует отметить, что эти методы не изменяют первоначальную строку.</p>

<h2 id="Разделение-строк">Разделение строк</h2>

<p>В JavaScript имеется полезный метод разделения строк по символу и создания нового <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">массива</a> из полученных частей. Мы используем метод <code>split()</code> для разделения массива по символу пробела, представленного <code>" "</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?";

// Split string by whitespace character
const splitString = originalString.split(" ");

console.log(splitString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[ 'How', 'are', 'you?' ]
</code></pre>
<p>Теперь мы получили новый массив в переменной <code>splitString</code> и можем получить доступ к каждому разделу с помощью числового индекса.</p>
<pre class="code-pre "><code class="code-highlight language-js">splitString[1];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>are
</code></pre>
<p>Если указан пустой параметр, <code>split()</code> создаст разделенный запятыми массив, содержащий все символы в строке.</p>

<p>Разделяя строки, вы можете определить, сколько слов содержится в предложении, или использовать метод для определения имен и фамилий людей.</p>

<h2 id="Обрезка-пробелов">Обрезка пробелов</h2>

<p>Метод JavaScript <code>trim()</code> удаляет пробелы с обоих концов строки, но не из промежутка между ними. Пробелами могут являться обычные пробелы или символы табуляции.</p>
<pre class="code-pre "><code class="code-highlight language-js">const tooMuchWhitespace = "     How are you?     ";

const trimmed = tooMuchWhitespace.trim();

console.log(trimmed);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>How are you?
</code></pre>
<p>Метод <code>trim()</code> — это простой способ выполнения распространенной задачи по удалению лишних пробелов.</p>

<h2 id="Поиск-и-замена-значений-строк">Поиск и замена значений строк</h2>

<p>Мы можем использовать метод <code>replace()</code>, чтобы найти в строке значение и заменить его новым значением. Первым параметром будет искомое значение, а вторым параметром — значение, которое его заменит.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?"

// Replace the first instance of "How" with "Where"
const newString = originalString.replace("How", "Where");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Where are you?
</code></pre>
<p>Помимо замены значений в строках, мы также можем использовать <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">регулярные выражения</a> для расширения возможностей метода <code>replace()</code>. Например, <code>replace()</code> влияет только на первое значение, но мы можем использовать флаг <code>g</code> (глобальный), чтобы найти все экземпляры значения, и флаг <code>i</code> (без учета регистра), чтобы игнорировать регистр.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "Javascript is a programming language. I'm learning javascript."

// Search string for "javascript" and replace with "JavaScript"
const newString = originalString.replace(/javascript/gi, "JavaScript");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>JavaScript is a programming language. I'm learning JavaScript.
</code></pre>
<p>Это очень распространенная задача с использованием регулярных выражений. Посетите <a href="http://regexr.com/">Regexr</a>, чтобы потренироваться с другими примерами регулярных выражений.</p>

<h2 id="Заключение">Заключение</h2>

<p>Строки — один из самых распространенных типов данных, и с ними можно выполнять множество разных операций.</p>

<p>В этом учебном модуле мы изучили различия между примитивами строк и объектом <code>String</code>, узнали об индексации строк и о том, как использовать встроенные методы и свойства строк для доступа к символам, форматирования текста, поиска и замены значений.</p>

<p>Более общий обзор строк можно найти в учебном модуле <a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-javascript">Работа со строками в JavaScript</a>.</p>
