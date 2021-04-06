---
title : "Comment utiliser les instructions Break, Continue et Pass pour travailler avec des boucles en Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3-fr
image: "https://sergio.afanou.com/assets/images/image-midres-37.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>En utilisant les <strong>for loops</strong> et les <strong><a href="https://www.digitalocean.com/community/tutorials/how-to-construct-while-loops-in-python-3">while loops</a></strong> en Python, vous pouvez automatiser et répéter efficacement des tâches.</p>

<p>Cependant, il arrive parfois qu'un facteur externe vienne influencer la façon dont votre programme fonctionne. Le cas échéant, vous voudrez éventuellement que votre programme quitte complètement une boucle, saute une partie d'une boucle avant de continuer, ou ignorer ce facteur externe. Pour cela, vous pouvez utiliser les instructions <code>break</code>, <code>continue</code> et <code>pass</code>.</p>

<h2 id="instruction-break">Instruction Break</h2>

<p>Sous Python, l'instruction <code>break</code> vous donne la possibilité de quitter une boucle au moment où une condition externe est déclenchée. Vous intégrerez l'instruction <code>break</code> dans le bloc du code qui se trouve en dessous de votre instruction de boucle, généralement après une instruction conditionnelle <code>if</code>.</p>

<p>Examinons d'un peu plus près un exemple qui utilise l'instruction <code>break</code> dans une boucle <code>for</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        break    # break here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>Dans ce court programme, le <code>number</code> de variables est initialisé à 0. Ensuite, une instruction <code>for</code> créé la boucle à condition que le <code>number</code> de variables soit inférieur à 10.</p>

<p>Dans la boucle <code>for</code> se trouve une instruction <code>if</code> qui présente la condition suivante : <em>if</em> le <code>number</code> de variables est équivalent à l'entier 5, <em>then</em> la boucle se brisera.</p>

<p>Dans la boucle se trouve également une instruction <code>print()</code> qui s'exécutera avec chaque itération de la boucle <code>for</code> jusqu'à ce que la boucle se brise, étant donné qu'elle se trouve après l'instruction <code>break</code>.</p>

<p>Pour savoir de quelle manière nous sommes en-dehors de la boucle, nous avons inclus une dernière instruction <code>print()</code> en dehors de la boucle <code>for</code>.</p>

<p>Une fois que nous exécutons ce code, nous obtenons la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Number is 0
Number is 1
Number is 2
Number is 3
Number is 4
Out of loop
</code></pre>
<p>Cela montre qu'une fois le <code>nombre</code> entier évalué comme équivalent à 5, la boucle se brise, car le programme est instruit de le faire avec l'instruction <code>break</code>.</p>

<p>L'instruction <code>break</code> provoque la rupture d'une boucle d'un programme.</p>

<h2 id="instruction-continue">Instruction Continue</h2>

<p>L'instruction <code>continue</code> vous donne la possibilité de passer la partie d'une boucle où une condition externe est déclenchée mais de tout de même passer à l'exécution du reste de la boucle. C'est-à-dire que l'itération actuelle de la boucle sera interrompue, mais le programme reviendra au haut de la boucle.</p>

<p>L'instruction <code>continue</code> se trouvera dans le bloc de code sous l'instruction de boucle, généralement après une instruction conditionnelle <code>if</code>.</p>

<p>En utilisant le même programme de boucle <code>for</code> que dans la section <a href="https://www.digitalocean.com/community/tutorials/how-to-use-break-continue-and-pass-statements-when-working-with-loops-in-python-3#break-statement">Instruction Break</a>, nous utiliserons une instruction <code>continue</code> plutôt qu’une instruction <code>break</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        continue    # continue here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>La différence entre le fait d'utiliser l'instruction <code>continue</code> plutôt qu'une instruction <code>break</code> est que notre code se poursuivra malgré la perturbation lorsque le <code>number</code> de variables sera évalué comme équivalent à 5. Examinons le résultat que nous obtenons :</p>
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
<p>Ici, <code>Number is 5</code> n'apparaît jamais dans la sortie, mais la boucle continue après pour imprimer des lignes des numéros 6 à10 avant de quitter la boucle.</p>

<p>Vous pouvez utiliser l'instruction <code>continue</code> pour éviter un code conditionnel profondément imbriqué ou optimiser une boucle en supprimant les cas fréquemment répétés que vous souhaitez rejeter.</p>

<p>L'instruction <code>continue</code> pousse un programme à sauter certains facteurs qui se trouvent dans une boucle, puis à continuer le reste de la boucle.</p>

<h2 id="instruction-pass">Instruction Pass</h2>

<p>Au déclenchement d'une condition externe, l'instruction <code>pass</code> vous permet de gérer la condition sans toucher à la boucle. La lecture de l'intégralité du code continuera, à moins qu'un <code>break</code> ou une autre instruction ne se produise.</p>

<p>Comme pour les autres instructions, l'instruction <code>pass</code> se trouvera dans le bloc de code en dessous de l'instruction de la boucle, généralement après une instruction conditionnelle <code>if</code>.</p>

<p>En utilisant le même bloc de code que celui ci-dessus, remplaçons l'instruction <code>break</code> ou <code>continue</code> par une instruction <code>pass</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">number = 0

for number in range(10):
    if number == 5:
        pass    # pass here

    print('Number is ' + str(number))

print('Out of loop')

</code></pre>
<p>L'instruction <code>pass</code> qui se produit après l'instruction conditionnelle <code>if</code> indique au programme de continuer à exécuter la boucle et d'ignorer le fait que le <code>number</code> de variables est évalué comme étant équivalent à 5 pendant l'une de ses itérations.</p>

<p>Nous allons exécuter le programme et examiner le résultat :</p>
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
<p>En utilisant l'instruction <code>pass</code> de ce programme, nous remarquons que le programme fonctionne exactement comme s'il n'y avait pas d'instruction conditionnelle. L'instruction <code>pass</code> indique au programme de ne pas tenir compte de cette condition et de continuer à exécuter le programme normalement.</p>

<p>L'instruction <code>pass</code> peut créer des classes minimales, ou agir comme un espace réservé lorsqu'on travaille sur un nouveau code et l'on pense à un niveau algorithmique avant de mettre en place des détails.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Sous Python, les instructions <code>break</code>, <code>continue</code> et <code>pass</code> vous permettront d'utiliser des boucles <code>for</code> et des boucles <code>while</code> plus efficacement dans votre code.</p>

<p>Pour vous exercer à travailler avec les instructions <code>break</code> et <code>pass</code>, vous pouvez suivre notre tutoriel de projet « <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-twitterbot-with-python-3-and-the-tweepy-library">Comment créer un Twitterbot avec Python 3 et la bibliothèque Tweepy</a>. »</p>
