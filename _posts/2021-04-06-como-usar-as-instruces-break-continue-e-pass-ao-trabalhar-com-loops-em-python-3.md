---
title : "Como usar as instruções break, continue, e pass ao trabalhar com loops em Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3-pt
image: "https://sergio.afanou.com/assets/images/image-midres-30.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>O uso <strong>de loops do tipo &ldquo;for&rdquo;</strong> e <strong><a href="https://www.digitalocean.com/community/tutorials/how-to-construct-while-loops-in-python-3">loops do tipo &ldquo;while&rdquo;</a></strong> em Python permite que você automatize e repita tarefas de maneira eficiente.</p>

<p>No entanto, pode acontecer de um fator externo influenciar a maneira como seu programa é executado. Quando isso ocorre, é desejável que seu programa saia de um loop completamente, ignore parte de um loop antes de continuar, ou ignore aquele fator externo. É possível realizar essas ações com as instruções <code>break</code>, <code>continue</code> e <code>pass</code>.</p>

<h2 id="instrução-break">Instrução break</h2>

<p>Em Python, a instrução <code>break</code> oferece a possibilidade de sair de um loop quando uma condição externa é acionada. A instrução <code>break</code> será colocada dentro do bloco de código abaixo da sua instrução de loop, geralmente após uma instrução condicional <code>if</code>.</p>

<p>Vamos ver um exemplo que usa a instrução <code>break</code> em um loop do tipo <code>"for"</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        break    # break here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Neste pequeno programa, o <code>number</code> da variável é inicializado em 0. Em seguida, uma instrução <code>for</code> constrói o loop, desde que o <code>number</code> da variável seja menor que 10.</p>

<p>Dentro do loop <code>for</code>, há uma instrução <code>if</code> que apresenta a condição que <em>se</em> o <code>number</code> da variável for equivalente ao número inteiro 5, <em>então</em> o loop será quebrado.</p>

<p>Dentro do loop também há uma instrução <code>print()</code> que será executada com cada iteração do loop <code>for</code> até que o loop seja quebrado, uma vez que está localizada após a instrução <code>break</code>.</p>

<p>Para saber quando estamos fora do loop, incluímos uma instrução final <code>print()</code> fora do loop <code>for</code>.</p>

<p>Quando executarmos esse código, nosso resultado será o seguinte:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Out of loop
</code></pre>
<p>Isso mostra que assim que o <code>number</code> é avaliado como equivalente a 5, o loop é interrompido, uma vez que o programa é orientado a fazer isso com a instrução <code>break</code>.</p>

<p>A instrução <code>break</code> faz com que um programa seja interrompido para fora de um loop.</p>

<h2 id="instrução-continue">Instrução continue</h2>

<p>A instrução <code>continue</code> dá a opção de ignorar a parte de um loop onde uma condição externa é acionada, mas continuar e completar o resto do loop. Ou seja, a iteração atual do loop será interrompida, mas o programa retornará ao topo do loop.</p>

<p>A instrução <code>continue</code> ficará dentro do bloco de código abaixo da instrução de loop, geralmente após uma instrução condicional <code>if</code>.</p>

<p>Usando o mesmo programa de loop <code>for</code> que na seção <a href="https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3#break-statement">anterior da Instrução break</a>, usaremos uma instrução <code>continue</code>, em vez de uma instrução <code>break</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        continue    # continue here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>A diferença entre usar a instrução <code>continue</code>, em vez de uma instrução <code>break</code>, é que o nosso código continuará apesar da interrupção quando a variável <code>number</code> for avaliada como equivalente a 5. Vamos ver nosso resultado:</p>
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
<p>Aqui, <code>Number is 5</code> nunca ocorre no resultado, mas o loop continua após esse ponto e imprime linhas para os números 6-10 antes de ser finalizado.</p>

<p>Você pode usar a instrução <code>continue</code> para evitar um código condicional extremamente aninhado, ou para otimizar um loop, eliminando casos que ocorram com frequência e que você gostaria de rejeitar.</p>

<p>A instrução <code>continue</code> faz com que um programa pule certos fatores que surjam dentro de um loop, mas depois continuem pelo restante do loop.</p>

<h2 id="declaração-pass">Declaração pass</h2>

<p>Quando uma condição externa é acionada, a instrução <code>pass</code> permite lidar com a condição sem que o loop seja impactado; todo o código continuará sendo lido a menos que um <code>break</code> ou outra instrução ocorra.</p>

<p>Assim como ocorre com outras instruções, a instrução <code>pass</code> ficará dentro do bloco de código abaixo da instrução de loop, normalmente após uma instrução condicional <code>if</code>.</p>

<p>Usando o mesmo bloco de código anterior, vamos substituir a instrução <code>break</code> ou <code>continue</code> por uma instrução <code>pass</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        pass    # pass here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>A instrução <code>pass</code> que ocorre após a instrução condicional <code>if</code> está dizendo ao programa para continuar executando o loop e ignorar o fato de que a variável <code>number</code> é avaliada como equivalente a 5 durante uma das iterações.</p>

<p>Vamos executar o programa e verificar o resultado:</p>
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
<p>Ao usar a instrução <code>pass</code> neste programa, notamos que o programa é executado exatamente como seria se não houvesse nenhuma instrução condicional no programa. A instrução <code>pass</code> diz ao programa para desconsiderar essa condição e continuar executando o programa como sempre.</p>

<p>A instrução <code>pass</code> pode criar classes mínimas, ou agir como um espaço reservado enquanto estamos trabalhando em novos códigos e pensando em um nível algorítmico antes de construir detalhes.</p>

<h2 id="conclusão">Conclusão</h2>

<p>As instruções <code>break</code>, <code>continue</code> e <code>pass</code> em Python permitem que você use loops <code>for</code> e <code>while</code> com maior efetividade em seu código.</p>

<p>Para trabalhar mais com as instruções <code>break</code> e <code>pass</code>, siga nosso tutorial de projeto &ldquo;<a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-twitterbot-with-python-3-and-the-tweepy-library">Como criar um Twitterbot com Python 3 e a biblioteca Tweepy</a>.&rdquo;</p>
