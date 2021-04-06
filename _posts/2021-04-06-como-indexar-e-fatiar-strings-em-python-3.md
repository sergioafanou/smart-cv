---
title : "Como indexar e fatiar strings em Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3-pt
image: "https://sergio.afanou.com/assets/images/image-midres-9.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>O tipo de dados string do Python é uma sequência composta por um ou mais caracteres individuais, que consistem em letras, números, caracteres de espaço em branco ou símbolos. Como uma string é uma sequência, ela pode ser acessada das mesmas maneiras que outros tipos de dados baseados em sequências o são, através da indexação e divisão.</p>

<p>Este tutorial ensinará como acessar strings através da indexação e dividir suas sequências de caracteres, e abordará alguns métodos de contagem e localização de caracteres.</p>

<h2 id="como-as-strings-são-indexadas">Como as strings são indexadas</h2>

<p>Assim como o <a href="https://www.digitalocean.com/community/tutorials/understanding-lists-in-python-3">tipo de dados lista</a>, que possui itens que correspondem a um número de índice, cada um dos caracteres de uma string também correspondem a um número de índice, começando com o número de índice 0.</p>

<p>Para a string <code>Sammy Shark!</code> o detalhamento do índice se parece com isto:</p>

<table><thead>
<tr>
<th>S</th>
<th>A</th>
<th>M</th>
<th>M</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>A</th>
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

<p>Como se vê, o primeiro <code>S</code> começa no índice 0, e a string termina no índice 11 com o símbolo <code>!</code></p>

<p>Também notamos que o caractere de espaço em branco entre <code>Sammy</code> e <code>Shark</code> também corresponde com seu próprio número de índice. Neste caso, o número de índice associado ao espaço em branco é 5.</p>

<p>O ponto de exclamação (<code>!</code>) também possui um número de índice associado a ele. Qualquer outro símbolo ou sinal de pontuação, como <code>*#$&amp;. ;?</code>, também é um caractere e estaria associado ao seu próprio número de índice.</p>

<p>O fato de cada caractere em uma string Python possuir um número de índice correspondente nos permite acessar e manipular strings das mesmas maneiras que faríamos com outros tipos de dados sequenciais.</p>

<h2 id="acessando-caracteres-por-um-número-de-índice-positivo">Acessando caracteres por um número de índice positivo</h2>

<p>Ao referenciar os números de índice, podemos isolar um dos caracteres em uma string. Fazemos isso colocando os números de índice entre colchetes. Vamos declarar uma string e imprimi-la, e então chamar o número de índice entre colchetes:</p>
<pre class="code-pre "><code class="code-highlight language-python">ss = "Sammy Shark!"
print(ss[4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>y
</code></pre>
<p>Quando nos referimos a um número de índice específico de uma string, o Python retorna o caractere que está naquela posição. Como a letra <code>y</code> está no índice de número 4 da string <code>ss = "Sammy Shark!"</code>, quando imprimimos <code>ss[4]</code>, recebemos <code>y</code> como resultado.</p>

<p>Os números de índice nos permitem acessar caracteres específicos dentro de uma string.</p>

<h2 id="acessando-caracteres-por-um-número-de-índice-negativo">Acessando caracteres por um número de índice negativo</h2>

<p>Se tivermos uma string longa e quisermos selecionar um item perto do final, também podemos contar de trás para frente a partir do final da string, começando no número de índice <code>-1</code>.</p>

<p>Para a mesma string <code>Sammy Shark!</code> o detalhamento do índice negativo se parece com isto:</p>

<table><thead>
<tr>
<th>S</th>
<th>A</th>
<th>M</th>
<th>M</th>
<th>y</th>
<th></th>
<th>S</th>
<th>h</th>
<th>A</th>
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

<p>Ao usar números de índice negativos, podemos imprimir o caractere <code>r</code>, referindo-nos à sua posição de -3 no índice, desta forma:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-3])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>O uso de números de índice negativos pode ser vantajoso para isolar um único caractere no final de uma string longa.</p>

<h2 id="dividindo-strings">Dividindo strings</h2>

<p>Também podemos chamar uma faixa de caracteres da string. Suponha que queiramos imprimir apenas a palavra <code>Shark</code>. Podemos fazer isso criando uma <strong>slice</strong> (fatia), que é uma sequência de caracteres dentro de uma string original. Com fatias, podemos chamar diversos valores de caracteres criando uma faixa de números de índice separados por dois pontos <code>[x:y]</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Ao construir uma fatia, como em <code>[6:11]</code>, o primeiro número de índice é onde ela começa (com ele incluso), e o segundo número de índice é onde a fatia termina (sem ele incluso), razão pela qual no nosso exemplo acima o intervalo precisa terminar com o número de índice que ocorreria logo após a string terminar.</p>

<p>Ao dividir strings, estamos criando uma <strong>substring</strong>, que é essencialmente uma string que existe dentro de outra string. Quando chamamos <code>ss[6:11]</code>, estamos chamando a substring <code>Shark</code> existente dentro da string <code>Sammy Shark!</code>.</p>

<p>Se quisermos incluir qualquer uma das extremidades de uma string, podemos omitir um dos números na sintaxe <code>string[n:n]</code>. Por exemplo, se quisermos imprimir a primeira palavra da string <code>ss</code> — &ldquo;Sammy&rdquo; — podemos fazer isso digitando:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[:5])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy
</code></pre>
<p>Fizemos isso omitido o número do índice antes dos dois pontos na sintaxe de fatia e incluindo apenas o número de índice após os dois pontos, que se referem ao final da substring.</p>

<p>Para imprimir uma substring que começa no meio de uma string e vai até o seu final, podemos incluir apenas o número de índice antes dos dois pontos, desta forma:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[7:])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>hark!
</code></pre>
<p>Ao incluir apenas o número de índice antes dos dois pontos, deixando o segundo número de índice fora da sintaxe, a substring será iniciada no caractere do número de índice chamado e irá até o final da string.</p>

<p>Também é possível usar números de índice negativos para dividir uma string. Conforme mostramos anteriormente, os números de índice negativos de uma string começam em -1 e vão sendo contados regressivamente até o início da string. Ao usar números de índice negativos, começamos com o número menor primeiro, uma vez que ele ocorre mais cedo na string.</p>

<p>Vamos usar dois números de índice negativos para dividir a string <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-4:-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ark
</code></pre>
<p>A substring &ldquo;ark&rdquo; é impressa a partir da string “Sammy Shark!” porque o caractere &ldquo;a&rdquo; ocorre na posição de número de índice -4 e o caractere “k” ocorre logo antes da posição de número de índice -1.</p>

<h2 id="especificando-o-deslocamento-ao-dividir-strings">Especificando o deslocamento ao dividir strings</h2>

<p>A divisão de strings pode aceitar um terceiro parâmetro, além dos dois números de índice. O terceiro parâmetro especifica o <strong>stride</strong> (deslocamento), que diz respeito a quantos caracteres devem ser pulados após o primeiro caractere ser recuperado da string. Até agora, omitimos o parâmetro stride e o Python utiliza o valor padrão do stride de 1, para que todos os caracteres entre dois números de índice sejam recuperados.</p>

<p>Vamos observar novamente o exemplo acima que imprime a substring &ldquo;Shark&rdquo;:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Podemos obter os mesmos resultados incluindo um terceiro parâmetro com um deslocamento de 1:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11:1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Assim, um deslocamento de 1 irá abranger todos os caracteres entre dois números de índice de uma fatia. Se omitirmos o parâmetro deslocamento, então o Python usará o padrão 1.</p>

<p>Se, ao invés disso, aumentarmos o deslocamento, veremos que os caracteres são ignorados:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SmySak
</code></pre>
<p>Especificar o deslocamento de 2 como o último parâmetro na sintaxe do Python <code>ss[0:12:2]</code> ignora um caractere a cada dois. Vamos ver os caracteres que são impressos em vermelho:</p>

<p><span class="highlight">S</span>a<span class="highlight">m</span>m<span class="highlight">y</span> <span class="highlight">S</span>h<span class="highlight">a</span>r<span class="highlight">k</span>!!</p>

<p>Note que o caractere de espaço em branco no número de índice 5 também é ignorado com um deslocamento especificado de 2.</p>

<p>Se usarmos um número maior para nosso parâmetro de deslocamento, teremos uma substring significativamente menor:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Especificar o deslocamento de 4 como o último parâmetro na sintaxe do Python <code>ss[0:12:4]</code> imprime apenas um a cada quatro caracteres. Mais uma vez, vamos ver os caracteres que são impressos em vermelho:</p>

<p><span class="highlight">S</span>amm<span class="highlight">y</span> Sh<span class="highlight">a</span>rk!</p>

<p>Neste exemplo, o caractere de espaço em branco também é ignorado.</p>

<p>Como estamos imprimindo toda a string, podemos omitir os dois números de índice e manter os dois sinais de dois pontos dentro da sintaxe para alcançar o mesmo resultado:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>Omitir os dois números de índice mantendo os dois pontos irá considerar a string inteira dentro do intervalo, ao mesmo tempo que adicionando um parâmetro final para o deslocamento especificará o número de caracteres a serem pulados.</p>

<p>Além disso, é possível indicar um valor numérico negativo para o stride, que podemos usar para imprimir a string original em ordem reversa se definirmos o deslocamento para -1:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!krahS ymmaS
</code></pre>
<p>Os dois sinais de dois pontos sem parâmetro especificado incluirão todos os caracteres da string original, um deslocamento de 1 incluirá todos os caracteres sem pular nenhum, e o deslocamento negativo inverterá a ordem dos caracteres.</p>

<p>Vamos fazer isso novamente mas com um deslocamento de -2:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!rh ma
</code></pre>
<p>Neste exemplo, <code>ss[:-2]</code>, estamos lidando com a totalidade da string original, uma vez que nenhum número de índice foi incluído nos parâmetros e invertendo a string ao utilizar o deslocamento negativo. Além disso, por termos um deslocamento de -2, estamos ignorando um caractere a cada dois na string invertida:</p>

<p><span class="highlight">! </span>k<span class="highlight">r</span>a<span class="highlight">h</span>S<span class="highlight">[whitespace]</span>y<span class="highlight">m</span>m<span class="highlight">a</span>S</p>

<p>O caractere de espaço em branco é impresso neste exemplo.</p>

<p>Ao especificar o terceiro parâmetro da sintaxe de fatia do Python, você está indicando o deslocamento da substring que está sendo gerada a partir da string original.</p>

<h2 id="métodos-de-contagem">Métodos de contagem</h2>

<p>Enquanto estamos pensando nos números de índice relevantes que correspondem a caracteres dentro de strings, vale a pena abordar alguns dos métodos que contam strings ou retornam números de índice. Isso pode ser útil para limitar o número de caracteres que gostaríamos de aceitar dentro de um formulário com entradas de usuário, ou comparar strings. Assim como outros tipos de dados sequenciais, as strings podem ser contadas com a utilização de vários métodos.</p>

<p>Vamos primeiro ver o método <code>len()</code>, que pode obter o comprimento de qualquer tipo de dados que seja uma sequência, ordenada ou não ordenada, incluindo strings, listas, <a href="https://www.digitalocean.com/community/tutorials/understanding-tuples-in-python-3">tuplas</a> e <a href="https://www.digitalocean.com/community/tutorials/understanding-dictionaries-in-python-3">dicionários</a>.</p>

<p>Vamos imprimir o comprimento da string <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len(ss))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>O comprimento da string &ldquo;Sammy Shark!&rdquo; é de 12 caracteres, incluindo o caractere de espaço em branco e o símbolo de ponto de exclamação.</p>

<p>Ao invés de usar uma variável, podemos também passar uma string diretamente para o método <code>len()</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len("Let's print the length of this string."))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>38
</code></pre>
<p>O método <code>len()</code> conta o número total de caracteres dentro de uma string.</p>

<p>Se quisermos contar o número de vezes que um caractere em particular ou uma sequência de caracteres aparece em uma string, fazemos isso com o método <code>str.count()</code>. Vamos trabalhar com nossa string <code>ss = "Sammy Shark!"</code> e contar o número de vezes que o caractere &ldquo;a&rdquo; aparece:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("a"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2
</code></pre>
<p>Podemos pesquisar por outro caractere:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("s"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>0
</code></pre>
<p>Embora a letra &ldquo;S&rdquo; esteja na string, é importante lembrar que todo caractere possui distinção entre maiúsculas e minúsculas. Se quisermos pesquisar todas as letras em uma string desconsiderando se são maiúsculas ou minúsculas, podemos usar primeiro o método <code>str.lower()</code> para converter a string inteira para letras minúsculas. Leia mais sobre esse método em “<a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3#making-strings-upper-and-lower-case">Uma introdução aos métodos de string em Python 3</a>.”</p>

<p>Vamos tentar o <code>str.count()</code> com uma sequência de caracteres:</p>
<pre class="code-pre "><code class="code-highlight language-python">likes = "Sammy likes to swim in the ocean, likes to spin up servers, and likes to smile."
print(likes.count("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>3
</code></pre>
<p>Na string <code>likes</code>, a sequência de caracteres equivalente a &ldquo;likes&rdquo; ocorre 3 vezes na string original.</p>

<p>Também podemos descobrir em qual posição um caractere ou sequência de caracteres ocorre em uma string. Podemos fazer isso com o método <code>str.find()</code>, e ele retornará a posição do caractere com base em seu número de índice.</p>

<p>Podemos verificar quando o primeiro &ldquo;m&rdquo; ocorre na string <code>ss</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.find("m"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>2
</code></pre>
<p>O primeiro caractere &ldquo;m” ocorre na posição de índice 2 na string &quot;Sammy Shark!” Podemos revisar as posições dos números de índice da string <code>ss</code> <a href="https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3#how-strings-are-indexed">acima</a>.</p>

<p>Vamos ver onde a primeira sequência de caracteres &quot;likes” ocorre na string <code>likes</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>6
</code></pre>
<p>A primeira ocorrência da sequência de caracteres &quot;likes” começa na posição de número de índice 6, que é onde o caractere <code>l</code> da sequência <code>likes</code> está posicionado.</p>

<p>E se quisermos ver onde a segunda sequência de &quot;likes” começa? Podemos fazer isso passando um segundo parâmetro ao método <code>str.find()</code>, que começará em um número de índice em particular. Assim, ao invés de começar no início da string vamos começar após o número de índice 9:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 9))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>34
</code></pre>
<p>Neste segundo exemplo que começa no número de índice 9, a primeira ocorrência da sequência de caracteres &quot;likes” começa no número de índice 34.</p>

<p>Além disso, podemos especificar um final para o intervalo como um terceiro parâmetro. Assim como na divisão em fatias, podemos fazer isso contando de trás para frente usando um número de índice negativo:</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 40, -6))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>64
</code></pre>
<p>Este último exemplo procura a posição da sequência &quot;likes” entre os números de índice 40 e -6. Como o parâmetro final inserido é um número negativo, ele contará a partir do final da string original.</p>

<p>Os métodos de string <code>len()</code>, <code>str.count()</code> e <code>str.find()</code> podem ser usados para determinar o comprimento, contagens de caracteres ou sequências de caracteres, e o índice das posições dos caracteres ou sequências de caracteres dentro de strings.</p>

<h2 id="conclusão">Conclusão</h2>

<p>A capacidade de chamar números de índice específicos das strings, ou uma fatia em particular de uma string oferece uma maior flexibilidade ao trabalhar com esse tipo de dados. Como as strings são um tipo de dados baseado em sequências, assim como as listas e tuplas, elas podem ser acessadas através da indexação e divisão em fatias.</p>

<p>Leia mais sobre a <a href="https://www.digitalocean.com/community/tutorials/how-to-format-text-in-python-3">formatação de strings</a> e <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3">métodos de strings</a> para continuar aprendendo sobre as strings.</p>
