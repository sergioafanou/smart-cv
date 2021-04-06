---
title : "Comment indexer et couper des chaînes dans Python 3"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3-fr
image: "https://sergio.afanou.com/assets/images/image-midres-17.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Le type de données de chaîne Python est une séquence composée d'un ou de plusieurs caractères individuels et constituée de lettres, de chiffres, de caractères d'espacement ou de symboles. Puisqu'une chaîne est une séquence, vous pouvez y accéder de la même manière que pour les autres types de données basés sur des séquences, via l'indexation et le découpage en tranches.</p>

<p>Ce tutoriel vous montrera de quelle manière accéder aux chaînes via l'indexation, les découper en séquences de caractères et passer en revue certaines méthodes de comptage et de localisation de caractères.</p>

<h2 id="de-quelle-manière-sont-indexées-les-chaînes">De quelle manière sont indexées les chaînes</h2>

<p>Comme les éléments des <a href="https://www.digitalocean.com/community/tutorials/understanding-lists-in-python-3">list data type</a> correspondent à un numéro d'index, chacun des caractères d'une chaîne correspondent également à un numéro d'index, en commençant par le numéro d'index 0.</p>

<p>Pour la chaîne <code>Sammy Shark!</code> la répartition de l'index ressemble à ce qui suit  :</p>

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

<p>Comme vous pouvez le voir, le premier <code>S</code> commence à l'index 0 et la chaîne se termine à l'index 11 avec le symbole <code>!</code> .</p>

<p>Nous pouvons également remarquer que le caractère d'espacement entre <code>Sammy</code> et <code>Shark</code> correspond aussi à un numéro d'index qui lui est propre. Dans ce cas, le numéro d'index associé à l'espace est 5.</p>

<p>Le point d'exclamation (<code>!</code>) est également associé à un numéro d'index. Tous les autres symboles ou signes de ponctuation, comme <code>*#$&amp;. ;?</code>, sont aussi considérés comme des caractères et seraient associés à leur propre numéro d'index.</p>

<p>Le fait qu'à chaque caractère d'une chaîne Python correspond un numéro d'index, nous pouvons accéder aux chaînes et les manipuler de la même manière que nous le ferions avec d'autres types de données séquentielles.</p>

<h2 id="accéder-aux-caractères-avec-un-numéro-d-39-index-positif">Accéder aux caractères avec un numéro d'index positif</h2>

<p>En référençant les numéros d'index, nous pouvons isoler l'un des caractères d'une chaîne. Pour cela, nous devons mettre les numéros entre crochets. Déclarons une chaîne, imprimons-la et appelons le numéro d'index entre crochets :</p>
<pre class="code-pre "><code class="code-highlight language-python">ss = "Sammy Shark!"
print(ss[4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>y
</code></pre>
<p>Lorsque nous faisons référence à un numéro d'index particulier d'une chaîne, Python renvoie le caractère qui se trouve dans cette position. Étant donné que la lettre <code>y</code> est au numéro d'index 4 de la chaîne <code>ss = "Sammy Shark!"</code>, lorsque nous imprimons ss[4], nous recevons la sortie y comme la sortie.&ldquo;&rdquo;</p>

<p>Les numéros d'index nous permettent d'accéder à des caractères spécifiques dans une chaîne.</p>

<h2 id="accéder-aux-caractères-par-numéro-d-39-index-négatif">Accéder aux caractères par numéro d'index négatif</h2>

<p>Si nous souhaitons localiser un élément vers la fin d'une longue chaîne, nous pouvons également compter à rebours à partir de la fin de la chaîne, en commençant par le numéro d'index <code>-1</code>.</p>

<p>Pour la même chaîne <code>Sammy Shark!</code> la ventilation de l'index négatif ressemble à ce qui suit :</p>

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

<p>En utilisant des numéros d'index négatifs, nous pouvons imprimer le caractère <code>r</code>, en nous référant à sa position à l'index -3, comme ceci :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-3])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>r
</code></pre>
<p>L'utilisation de numéros d'index négatifs peut être avantageuse pour isoler un caractère unique qui se trouve vers la fin d'une longue chaîne.</p>

<h2 id="découper-des-chaînes">Découper des chaînes</h2>

<p>Nous pouvons également appeler une plage de caractères  de la chaîne. Disons que nous souhaitons juste imprimer le mot <code>Shark</code>. Nous pouvons le faire en créant une <strong>slice</strong>, qui est une séquence de caractères dans une chaîne d'origine. Avec les slices, nous pouvons appeler plusieurs valeurs de caractères en créant une plage de numéros d'index séparés par deux points <code>[x:y]</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Lorsque vous créez une slice, comme dans <code>[6:11]</code>, le premier numéro d'index correspond à l'endroit où la slice commence (inclusive), le second numéro d'index correspond à l'endroit où la slice se termine (exclusive). C'est pour cette raison que dans notre exemple ci-dessus, la plage doit correspondre au numéro d'index qui se trouve juste après la fin de la chaîne.</p>

<p>Lorsque nous découpons des chaînes, nous créons une <strong>substring</strong>, qui est en réalité une chaîne qui existe dans une autre. Lorsque nous appelons <code>ss[6:11]</code>, nous appelons la sous-chaîne <code>Shark</code> qui existe dans la chaîne <code>Sammy Shark!</code></p>

<p>Si nous souhaitons inclure l'une des extrémités d'une chaîne, nous pouvons omettre l'un des numéros dans la syntaxe suivante : <code>string [n:n]</code>. Par exemple, pour imprimer le premier mot de la chaîne <code>ss</code> - « Sammy » - nous pouvons saisir :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[:5])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy
</code></pre>
<p>Nous avons procédé à cette opération en omettant le numéro d'index avant les deux points dans la syntaxe de slice et en incluant uniquement le numéro d'index qui se trouve après. Ce dernier fait référence à la fin de la sous-chaîne.</p>

<p>Pour imprimer une sous-chaîne qui commence au milieu d'une chaîne et continue jusqu'à à la fin, nous devons inclure uniquement le numéro d'index avant les deux points :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[7:])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>hark!
</code></pre>
<p>En incluant uniquement le numéro d'index avant les deux points et en laissant le second numéro d'index hors de la syntaxe, la sous-chaîne ira du caractère qui correspond au numéro d'index appelé à la fin de la chaîne.</p>

<p>Vous pouvez également utiliser des numéros d'index négatifs pour découper une chaîne. Comme nous l'avons vu auparavant, les index négatifs d'une chaîne par commencent par -1. Ensuite, comptez à partir de ce chiffre jusqu'à atteindre le début de la chaîne. Lorsque nous utilisons des numéros d'index négatifs, nous devons tout d'abord commencer par l'endroit où le plus petit chiffre se trouve plus tôt dans la chaîne.</p>

<p>Utilisons deux numéros d'index négatifs pour découper la chaîne <code>ss</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[-4:-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ark
</code></pre>
<p>La sous-chaîne « ark » est imprimée à partir de la chaîne « Sammy Shark! » car le caractère « a » se trouve à la position du numéro d'index -4, et le caractère « k » se trouve juste avant la position du numéro d'index -1.</p>

<h2 id="spécifier-une-stride-lors-du-découpage-des-chaînes">Spécifier une stride lors du découpage des chaînes</h2>

<p>En plus des deux numéros d'index, le découpage de chaîne peut accepter un troisième paramètre. Le troisième paramètre spécifie la <strong>stride</strong>, qui renvoie à la quantité de caractères à passer une fois que le premier caractère est récupéré à partir de la chaîne. Jusqu'à présent, nous avons omis le paramètre stride. Python utilise la stride 1 par défaut afin que chaque caractère entre deux numéros d'index soit bien récupéré.</p>

<p>Examinons à nouveau l'exemple ci-dessus dans lequel la sous-chaîne « Shark » est imprimée :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Nous pouvons obtenir les mêmes résultats en incluant un troisième paramètre avec une stride de 1 :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[6:11:1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Shark
</code></pre>
<p>Ainsi, avec une stride de 1, chaque caractère qui se trouve entre deux numéros d'index d'une slice sera récupéré. Si nous omettons le paramètre stride, Python utilisera la valeur de 1 par défaut.</p>

<p>Si, au lieu de cela, nous augmentons la stride, nous verrons que les caractères sont ignorés :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SmySak
</code></pre>
<p>En spécifiant la stride de 2 comme dans le dernier paramètre de la syntaxe <code>ss[0:12:2]</code>, Python ignore tout autre caractère. Examinons les caractères imprimés en rouge :</p>

<p><span class="highlight">S</span>a<span class="highlight">m</span>m<span class="highlight">y</span> <span class="highlight">S</span>h<span class="highlight">a</span>r<span class="highlight">k</span>!</p>

<p>Notez que, si vous spécifiez une stride de 2, le caractère d'espacement de l'index numéro 5 est également ignoré.</p>

<p>Si nous utilisons un numéro plus grand pour notre paramètre stride, la sous-chaîne que nous obtiendrons sera significativement plus petite :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[0:12:4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>En utilisant la stride de 4 pour le dernier paramètre de la syntaxe <code>ss[0:12:4]</code>, Python imprime uniquement tous les quatre caractères. Encore une fois, examinons les caractères imprimés en rouge :</p>

<p><span class="highlight">S</span>amm<span class="highlight">y</span> Sh<span class="highlight">a</span>rk!</p>

<p>Dans cet exemple, le caractère d'espacement est également ignoré.</p>

<p>Étant donné que nous imprimons l'ensemble de la chaîne, dans la syntaxe, nous pouvons omettre les deux numéros d'index et conserver les deux points pour obtenir le même résultat :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::4])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sya
</code></pre>
<p>En omettant les deux numéros d'index et en conservant les deux points, l'ensemble de la chaîne restera dans la plage. Dans le même temps, en ajoutant un paramètre final pour la stride, vous spécifierez le nombre de caractères à ignorer.</p>

<p>En outre, vous pouvez indiquer une valeur numérique négative pour la stride, que nous pouvons utiliser pour imprimer la chaîne d'origine dans le sens inverse si nous configurons la stride sur -1 :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-1])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!krahS ymmaS
</code></pre>
<p>L'utilisation des deux points sans spécifier de paramètre vous permettra d'inclure tous les caractères de la chaîne d'origine. Une stride de 1 inclura chaque caractère sans en sauter aucun et en utilisant une valeur négative pour cette stride, vous inverserez l'ordre des caractères.</p>

<p>Faisons-le à nouveau, mais avec une stride de -2 :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss[::-2])
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>!rh ma
</code></pre>
<p>Dans cet exemple, <code>ss[::-2]</code>, nous traitons l'intégralité de la chaîne d'origine, car aucun numéro d'index n'est inclus dans les paramètres et nous inversons la chaîne à l'aide d'une stride négative. En outre, en utilisant une stride de -2, nous ignorons toutes les autres lettres de la chaîne inversée :</p>

<p><span class="highlight">! </span>k<span class="highlight">r</span>a<span class="highlight">h</span>S<span class="highlight">[espace]</span>y<span class="highlight">m</span>m<span class="highlight">a</span>S</p>

<p>Dans cet exemple, le caractère d'espacement est imprimé.</p>

<p>Spécifiez le troisième paramètre de la syntaxe de la tranche Python pour indiquer la stride de la sous-chaîne que vous extrayez de la chaîne d'origine.</p>

<h2 id="méthodes-de-comptage">Méthodes de comptage</h2>

<p>Bien que nous devions réfléchir pour trouver les numéros d'index pertinents qui correspondent aux caractères dans les chaînes, il vaut la peine d'étudier certaines des méthodes qui permettent de compter les chaînes ou renvoyer les numéros d'index. Cette méthode peut vous être utile pour limiter le nombre de caractères acceptés dans un formulaire que les utilisateurs devront compléter ou pour comparer les chaînes. Comme pour les autres types de données séquentielles, vous pouvez utiliser plusieurs méthodes pour compter les chaînes.</p>

<p>Nous allons tout d'abord découvrir la méthode <code>len()</code> qui vous permet d'obtenir la longueur de tout type de données qui se trouvent dans une séquence, que ce soit dans l'ordre ou pas et notamment des chaînes, des listes, des <a href="https://www.digitalocean.com/community/tutorials/understanding-tuples-in-python-3">tuples</a> et des <a href="https://www.digitalocean.com/community/tutorials/understanding-dictionaries-in-python-3">dictionaries</a>.</p>

<p>Imprimons la longueur de la chaîne <code>ss</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len(ss))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>12
</code></pre>
<p>La longueur de la chaîne « Sammy Shark! » est de 12 caractères, parmi lesquels se trouvent le caractère d'espacement et le symbole du point d'exclamation.</p>

<p>Au lieu d'utiliser une variable, nous pouvons également passer une chaîne directement dans la méthode <code>len()</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(len("Let's print the length of this string."))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>38
</code></pre>
<p>La méthode <code>len()</code> compte le nombre total de caractères dans une chaîne.</p>

<p>Si nous souhaitons compter le nombre de fois qu'un caractère particulier ou une séquence de caractères apparaît dans une chaîne, nous pouvons le faire en utilisant la méthode <code>str.count()</code>. Prenons notre chaîne <code>ss = "Sammy Shark!"</code> et comptons le nombre de fois que le caractère « a » apparaît :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("a"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2
</code></pre>
<p>Nous pouvons rechercher un autre caractère :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.count("s"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>0
</code></pre>
<p>Bien que la lettre « S » soit dans la chaîne, n'oubliez surtout pas que chaque caractère est sensible à la casse. Pour rechercher toutes les lettres dans une chaîne quelle que soit la cassse, nous pouvons utiliser la méthode <code>str.lower()</code> afin de tout d'abord convertir la chaîne en minuscules. Vous pouvez en savoir plus sur cette méthode, dans « <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3#making-strings-upper-and-lower-case">Introduction aux méthodes de chaîne en Python 3</a>. »</p>

<p>Essayons <code>str.count()</code> avec une séquence de caractères :</p>
<pre class="code-pre "><code class="code-highlight language-python">likes = "Sammy likes to swim in the ocean, likes to spin up servers, and likes to smile."
print(likes.count("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>3
</code></pre>
<p>Dans la chaîne <code>likes</code>, la séquence de caractères équivalente à « likes » apparaît 3 fois dans la chaîne d'origine.</p>

<p>Nous pouvons également trouver la position à laquelle un caractère ou une séquence de caractères se trouve dans une chaîne. Pour cela, nous pouvons utiliser la méthode <code>str.find()</code> et renvoyer la position du caractère en fonction du numéro d'index.</p>

<p>Nous pouvons vérifier à quel endroit se trouve le premier « m » dans la chaîne <code>ss</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(ss.find("m"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>2
</code></pre>
<p>Le premier caractère « m » se trouve à la position de l'index 2 dans la chaîne « Sammy Shark! » Nous pouvons examiner les positions du numéro d'index de la chaîne <code>ss</code> <a href="https://www.digitalocean.com/community/tutorials/how-to-index-and-slice-strings-in-python-3#how-strings-are-indexed">ci-dessus</a>.</p>

<p>Voyons à quel endroit se trouve la première séquence de caractères « likes » qui se trouve dans la chaîne <code>likes</code> :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes"))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Ouput">Ouput</div>6
</code></pre>
<p>La première instance de la séquence de caractères « likes » commence à la position numéro d'index 6, qui correspond à l'endroit où est positionné le caractère <code>l</code> dans la séquence <code>likes</code>.</p>

<p>Et que faire pour savoir à quel endroit commence la deuxième séquence de « likes » ? Pour cela, nous devons passer un second paramètre dans la méthode <code>str.find()</code> qui commencera par un numéro d'index spécifique. Donc, au lieu de commencer au début de la chaîne, commençons après l'index numéro 9 :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 9))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>34
</code></pre>
<p>Dans ce second exemple qui commence par l'index numéro 9, la première occurrence de la séquence de caractères « likes » commence à l'index numéro 34.</p>

<p>Nous pouvons également ajouter un troisième paramètre pour spécifier la fin de la plage. Comme pour le découpage, nous pouvons le faire en comptant à rebours, en utilisant un numéro d'index négatif :</p>
<pre class="code-pre "><code class="code-highlight language-python">print(likes.find("likes", 40, -6))
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>64
</code></pre>
<p>Dans ce dernier exemple, nous recherchons la position de la séquence « likes » entre les numéros d'index 40 et -6. Étant donné que le paramètre final saisi est un numéro négatif, le décompte se fera à partir de la fin de la chaîne d'origine.</p>

<p>Vous pouvez utiliser les méthodes de chaîne <code>len()</code>, <code>str.count()</code> et <code>str.find()</code> pour déterminer la longueur, le nombre de caractères ou de séquences de caractères ainsi que la position de l'index de caractères ou de séquences de caractères dans les chaînes.</p>

<h2 id="conclusion">Conclusion</h2>

<p>En ayant cette capacité d'appeler des numéros d'index spécifiques de chaînes ou une tranche particulière d'une chaîne, nous pouvons travailler avec ce type de données de manière plus flexible. Étant donné que les chaînes, comme les listes et les tuples, sont un type de données basées sur des séquences, vous pouvez y accéder via l'indexation et le découpage.</p>

<p>Pour continuer à développer vos connaissances sur les chaînes, n'hésitez à en lire davantage sur le <a href="https://www.digitalocean.com/community/tutorials/how-to-format-text-in-python-3">formatage des chaînes</a> et les <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-string-methods-in-python-3">méthodes de chaînes</a>.</p>
