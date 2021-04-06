---
title : "Como indexar, dividir e manipular strings no JavaScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-split-and-manipulate-strings-in-javascript-pt
image: "https://sergio.afanou.com/assets/images/image-midres-15.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Uma <strong>string</strong> é uma sequência de um ou mais caracteres que podem consistir em letras, números ou símbolos. Cada caractere em uma string do JavaScript pode ser acessado por um número de índice, e todas as strings possuem métodos e propriedades disponíveis a elas.</p>

<p>Neste tutorial, vamos aprender a diferença entre primitivos de string e o objeto <code>String</code>, como as strings são indexadas, como acessar caracteres em uma string, além de propriedades e métodos comuns usados em strings.</p>

<h2 id="primitivos-de-string-e-objetos-string">Primitivos de string e objetos string</h2>

<p>Primeiramente, vamos definir os dois tipos de strings. O JavaScript trata de forma diferente um <strong>primitivo de string</strong>, que é um tipo de dados imutável, e o objeto <code>String</code>.</p>

<p>Para testar a diferença entre os dois, vamos inicializar um primitivo de string e um objeto string.</p>
<pre class="code-pre "><code class="code-highlight language-js">// Initializing a new string primitive
const stringPrimitive = "A new string.";

// Initializing a new String object
const stringObject = new String("A new string.");  
</code></pre>
<p>Podemos usar o operador <code>typeof</code> para determinar o tipo de um valor. No primeiro exemplo, simplesmente atribuimos uma string a uma variável.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringPrimitive;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>string
</code></pre>
<p>No segundo exemplo, usamos o <code>new String()</code> para criar um objeto string e atribui-lo a uma variável.</p>
<pre class="code-pre "><code class="code-highlight language-js">typeof stringObject;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>object
</code></pre>
<p>Na maioria das vezes, você estará criando primitivos de string. O JavaScript é capaz de acessar e usar as propriedades e métodos integrados do wrapper de objeto <code>String</code>, sem de fato alterar o primitivo de string que você criou em um objeto.</p>

<p>Embora esse conceito seja um pouco desafiador no começo, a diferença entre o primitivo e o objeto deve ser clara. Essencialmente, há métodos e propriedades disponíveis para todas as strings, e em segundo plano o JavaScript fará uma conversão para objeto e de volta para primitivo sempre que um método ou propriedade for chamado.</p>

<h2 id="como-as-strings-são-indexadas">Como as strings são indexadas</h2>

<p>Cada um dos caracteres em uma string corresponde a um número de índice, começando com <code>0</code>.</p>

<p>Para demonstrar, vamos criar uma string com o valor <code>How are you?</code>.</p>

<table><thead>
<tr>
<th style="text-align: center">H</th>
<th style="text-align: center">o</th>
<th style="text-align: center">w</th>
<th style="text-align: center"></th>
<th style="text-align: center">A</th>
<th style="text-align: center">r</th>
<th style="text-align: center">E</th>
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

<p>O primeiro caracter na string é <code>H</code>, que corresponde ao índice <code>0</code>. O último caractere é <code>?</code>, que corresponde a <code>11</code>. Os caracteres de espaço em branco também possuem um índice, em <code>3</code> e <code>7</code>.</p>

<p>A capacidade de acessar todos os caracteres em uma string nos oferece várias maneiras de trabalhar com strings e manipula-las.</p>

<h2 id="acessando-caracteres">Acessando caracteres</h2>

<p>Vamos demonstrar como acessar caracteres e índices com a string <code>How are you?</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?";
</code></pre>
<p>Ao usar a notação de colchetes, podemos acessar qualquer caractere na string.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?"[5];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>Também podemos usar o método <code>charAt()</code> para retornar o caractere usando o número de índice como um parâmetro.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".charAt(5);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>De maneira alternativa, podemos usar o <code>indexOf()</code> para retornar o número de índice da primeira aparição de um caractere.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<p>Embora “o” apareça duas vezes na string <code>How are you?</code>, o <code>indexOf()</code> mostrará a primeira aparição.</p>

<p><code>lastIndexOf()</code> é usado para encontrar a última aparição.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".lastIndexOf("o");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>9
</code></pre>
<p>Para ambos os métodos, também é possível pesquisar múltiplos caracteres na string. Ele retornará o número de índice do primeiro caractere da sequência.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".indexOf("are");
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>4
</code></pre>
<p>O método <code>slice()</code>, por outro lado, retorna os caracteres entre dois números de índice. O primeiro parâmetro será o número de índice de início, e o segundo parâmetro será o número de índice final.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8, 11);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you
</code></pre>
<p>Note que <code>11</code> é <code>?</code>, mas <code>?</code> não faz parte do resultado retornado. O <code>slice()</code> retornará o que está entre eles, mas sem incluir o último parâmetro.</p>

<p>Se um segundo parâmetro não for incluído, <code>slice()</code> retornará tudo começando do primeiro parâmetro até o final da string.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".slice(8);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>you?
</code></pre>
<p>Resumindo, <code>charAt()</code> e <code>slice()</code> ajudarão a retornar valores de string com base em números de índice, e <code>indexOf()</code> e <code>lastIndexOf()</code> farão o oposto, retornando números de índice com base nos caracteres de string fornecidos.</p>

<h2 id="descobrindo-o-comprimento-de-uma-string">Descobrindo o comprimento de uma string</h2>

<p>Usando a propriedade <code>length</code>, podemos retornar o número de caracteres em uma string.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".length;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>Lembre-se de que a propriedade <code>length</code> está retornando o número real de caracteres que começa com 1, totalizando 12, e não o número de índice final, que começa em <code>0</code> e termina em <code>11</code>.</p>

<h2 id="convertendo-para-maiúsculas-ou-minúsculas">Convertendo para maiúsculas ou minúsculas</h2>

<p>Os dois métodos integrados <code>toUpperCase()</code> e <code>toLowerCase()</code> representam maneiras úteis de formatar o texto e fazer comparações textuais no JavaScript.</p>

<p><code>toUpperCase()</code> irá converter todos os caracteres para caracteres maiúsculos.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toUpperCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HOW ARE YOU?
</code></pre>
<p><code>toLowerCase()</code> converterá todos os caracteres para caracteres minúsculos.</p>
<pre class="code-pre "><code class="code-highlight language-js">"How are you?".toLowerCase();
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>how are you?
</code></pre>
<p>Esses dois métodos de formatação não recebem parâmetros adicionais.</p>

<p>Vale notar que esses métodos não alteram a string original.</p>

<h2 id="dividindo-strings">Dividindo strings</h2>

<p>O JavaScript possui um método muito útil para dividir uma string por um caractere e criar uma <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">matriz</a> das seções. Vamos usar o método <code>split()</code> para separar a matriz por um caractere de espaço em branco, representado por  <code>“ ”</code>.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?";

// Split string by whitespace character
const splitString = originalString.split(" ");

console.log(splitString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[ 'How', 'are', 'you?' ]
</code></pre>
<p>Agora que temos uma nova matriz na variável <code>splitString</code>, podemos acessar cada seção com um número de índice.</p>
<pre class="code-pre "><code class="code-highlight language-js">splitString[1];
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>are
</code></pre>
<p>Se um parâmetro vazio for dado, <code>split()</code> criará uma matriz separada por vírgulas com cada caractere na string.</p>

<p>Ao dividir strings, é possível determinar quantas palavras estão em uma sentença, e usar o método como uma maneira de determinar os primeiros nomes e os sobrenomes de pessoas, por exemplo.</p>

<h2 id="filtragem-de-espaços-em-branco">Filtragem de espaços em branco</h2>

<p>O método <code>trim()</code> do JavaScript remove o espaço em branco de ambas as extremidades de uma string, mas não altera nenhum lugar intermediário. Os espaços em branco podem ser Tabs ou espaços.</p>
<pre class="code-pre "><code class="code-highlight language-js">const tooMuchWhitespace = "     How are you?     ";

const trimmed = tooMuchWhitespace.trim();

console.log(trimmed);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>How are you?
</code></pre>
<p>O método <code>trim()</code> é uma maneira simples de realizar a tarefa comum de remover espaços em branco em excesso.</p>

<h2 id="descobrindo-e-substituindo-valores-de-string">Descobrindo e substituindo valores de string</h2>

<p>Podemos pesquisar uma string em busca de um valor e substituí-lo por um novo valor usando o método <code>replace()</code>. O primeiro parâmetro será o valor a ser encontrado, e o segundo parâmetro será o valor para substituí-lo.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "How are you?"

// Replace the first instance of "How" with "Where"
const newString = originalString.replace("How", "Where");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Where are you?
</code></pre>
<p>Além da capacidade de substituir um valor por outro valor de string, também podemos usar as <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions">Expressões Regulares</a> para tornar o <code>replace()</code> mais poderoso. Por exemplo, o <code>replace()</code> afeta apenas o primeiro valor, mas podemos usar o sinalizador <code>g</code> (global) para capturar todas as ocorrências de um valor e o sinalizador <code>i</code> (insensibilidade ao caso) para ignorar a diferenciação entre maiúsculas e minúsculas.</p>
<pre class="code-pre "><code class="code-highlight language-js">const originalString = "Javascript is a programming language. I'm learning javascript."

// Search string for "javascript" and replace with "JavaScript"
const newString = originalString.replace(/javascript/gi, "JavaScript");

console.log(newString);
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>JavaScript is a programming language. I'm learning JavaScript.
</code></pre>
<p>Essa é uma tarefa muito comum que utiliza as Expressões Regulares. Visite o <a href="http://regexr.com/">Regexr</a> para praticar mais exemplos de RegEx.</p>

<h2 id="conclusão">Conclusão</h2>

<p>As strings são um dos tipos de dados mais usados e há muitas coisas que podemos fazer com elas.</p>

<p>Neste tutorial, aprendemos a diferença entre o primitivo de string e objeto <code>String</code>, como as strings são indexadas e como usar os métodos e propriedades integrados das strings para acessar caracteres, formatar texto e encontrar e substituir valores.</p>

<p>Para uma visão geral sobre strings, leia o tutorial “<a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-javascript">Como trabalhar com strings no JavaScript</a>.”</p>
