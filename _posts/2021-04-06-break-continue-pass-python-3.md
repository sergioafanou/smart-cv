---
title : "Использование выражений Break, Continue и Pass при работе с циклами в Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3-ru
image: "https://sergio.afanou.com/assets/images/image-midres-32.jpg"
---

<h3 id="Введение">Введение</h3>

<p>Использование <strong>циклов for</strong> и <strong><a href="https://www.digitalocean.com/community/tutorials/how-to-construct-while-loops-in-python-3">циклов while</a></strong> в Python помогает эффективно автоматизировать и воспроизводить задачи.</p>

<p>Однако иногда на работу вашей программы может влиять внешний фактор. Когда это произойдет, вы можете захотеть, чтобы ваша программа полностью вышла из цикла, пропустила часть цикла и продолжила его выполнение или игнорировала этот внешний фактор. Для этих действий используются выражения <code>break</code>, <code>continue</code> и <code>pass</code>.</p>

<h2 id="Выражение-break">Выражение Break</h2>

<p>В Python выражение <code>break</code> дает вам возможность выйти из цикла при активации внешнего условия. Выражение <code>break</code> помещается в блок кода внутри выражения loop, обычно после условного выражения <code>if</code>.</p>

<p>Рассмотрим пример использования выражения <code>break</code> в цикле <code>for</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        break    # break here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>В этой небольшой программе переменная <code>number</code> инициализируется как 0. Затем выражение <code>for</code> строит цикл, пока значение переменной <code>number</code> составляет меньше 10.</p>

<p>В цикле <code>for</code> имеется выражение <code>if</code>, которое задает условие, что <em>если</em> значение переменной <code>number</code> равно целому числу 5, <em>то</em> цикл прекращается.</p>

<p>В цикле также содержится выражение <code>print()</code>, которое выполняется с каждой итерацией цикла <code>for</code>, пока цикл не прекращается, поскольку оно располагается после выражения <code>break</code>.</p>

<p>Чтобы узнавать о выходе из цикла, мы добавили завершающее выражение <code>print()</code> вне цикла <code>for</code>.</p>

<p>При выполнении этого кода результат будет выглядеть следующим образом:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Out of loop
</code></pre>
<p>Это показывает, что когда переменная <code>number</code> оценивается как эквивалентная целому числу 5, цикл прекращается, поскольку программа получает соответствующее указание через выражение <code>break</code>.</p>

<p>Выражение <code>break</code> заставляет программу выйти из цикла.</p>

<h2 id="Выражение-continue">Выражение Continue</h2>

<p>Выражение <code>continue</code> дает возможность пропустить часть цикла, где активируется внешнее условие, но при этом выполнить остальную часть цикла. При этом прерывается текущая итерация цикла, но программа возвращается к началу цикла.</p>

<p>Выражение <code>continue</code> размещается в блоке кода под выражением цикла, обычно после условного выражения <code>if</code>.</p>

<p>Мы используем ту же программу с циклом <code>for</code>, что и в разделе <a href="https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3#break-statement">«Выражение Break»</a> выше, но при этом используем выражение <code>continue</code> вместо выражения <code>break</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        continue    # continue here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Отличие выражения <code>continue</code> от выражения <code>break</code> заключается в том, что код продолжит выполняться несмотря на прерывание, если значение переменной <code>number</code> будет оценено как равное 5. Давайте посмотрим на результаты:</p>
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
<p>В этом выводе условие <code>Number is 5</code> никогда не выполняется, но цикл продолжается после этого, чтобы выводить линии для чисел 6–10 до выхода из цикла.</p>

<p>Вы можете использовать выражение <code>continue</code>, чтобы избежать использования глубоко вложенного условного кода или чтобы оптимизировать цикл, устранив часто встречающиеся случаи, которые вы хотите отклонять.</p>

<p>Выражение <code>continue</code> заставляет программу пропустить определенную часть цикла, а затем продолжить выполнение оставшейся части цикла.</p>

<h2 id="Выражение-pass">Выражение Pass</h2>

<p>При активации внешнего условия выражение <code>pass</code> позволяет обрабатывать условия без влияния на цикл; чтение кода будет продолжаться до появления выражения <code>break</code> или другого выражения.</p>

<p>Как и в случае с другими выражениями, выражение <code>pass</code> будет содержаться в блоке кода до выражения loop, обычно после условного выражения <code>if</code>.</p>

<p>Используя тот же код выше, попробуйте заменить выражение <code>break</code> или <code>continue</code> выражением <code>pass</code>:</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        pass    # pass here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Выражение <code>pass</code>, появляющееся после условного выражения <code>if</code>, указывает программе на необходимость продолжить выполнение цикла и игнорировать тот факт, что переменная <code>number</code> оценивается как равная 5 во время одной из итераций.</p>

<p>Мы запустим программу и оценим вывод:</p>
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
<p>Используя выражение <code>pass</code> в этой программе, мы видим, что программа работает точно так же, как если бы в ней не было условного выражения. Выражение <code>pass</code> предписывает программе игнорировать это условие и продолжать обычное выполнение программы.</p>

<p>Выражение <code>pass</code> может создавать минимальные классы или выступать в качестве замещающего элемента при работе с новым кодом и действовать на уровне алгоритмов, прежде чем отрабатывать детали.</p>

<h2 id="Заключение">Заключение</h2>

<p>Выражения <code>break</code>, <code>continue</code> и <code>pass</code> в Python позволяют использовать циклы <code>for</code> и <code>while</code> в вашем коде более эффективно.</p>

<p>Чтобы больше поработать с выражениями <code>break</code> и <code>pass</code>, вы можете выполнить учебный модуль нашего проекта «<a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-twitterbot-with-python-3-and-the-tweepy-library">Создание бота Twitterbot с помощью Python 3 и библиотеки Tweepy</a>».</p>
