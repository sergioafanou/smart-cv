---
title : "Utiliser Grep et les expressions régulières pour rechercher des modèles de texte en Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux-fr
image: "https://sergio.afanou.com/assets/images/image-midres-34.jpg"
---

<h3 id="introduction">Introduction</h3>

<p><code>grep</code> est l'une des commandes les plus pratiques dans un environnement de terminaux Linux. L'accronyme <code>grep</code> signifie « global regular expression print » (rechercher globalement les correspondances avec l'expression régulière). Cela signifie que vous pouvez utiliser <code>grep</code> pour voir si l'entrée qu'il reçoit correspond à un modèle spécifié. Apparemment trivial, ce programme est extrêmement puissant. Sa capacité à trier les entrées en fonction de règles complexes fait qu'il est couramment utilisé dans de nombreuses chaînes de commande.</p>

<p>Au cours de ce tutoriel, vous allez explorer les options de la commande <code>grep</code>. Ensuite, vous allez approfondir vos connaissances dans l'utilisation des expressions régulières qui vous permettront d'effectuer des recherches plus avancées.</p>

<p></p><div data-env="bash" data-js="terminal-button" class="button blue-button hidden">Launch an Interactive Terminal!</div>

<h2 id="utilisation-de-base">Utilisation de base</h2>

<p>Au cours de ce tutoriel, vous allez utiliser <code>grep</code> pour rechercher plusieurs mots et plusieurs phrases dans <a href="https://www.gnu.org/licenses/gpl-3.0.en.html">GNU General Public License version 3</a>.</p>

<p>Si vous utilisez un système Ubuntu, vous pouvez trouver le fichier dans le dossier <code>/usr/share/common-licenses</code>. Copiez-le dans votre répertoire home :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp /usr/share/common-licenses/GPL-3 .
</li></ul></code></pre>
<p>Si vous êtes sur un autre système, utilisez la commande <code>curl</code> pour en télécharger une copie :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -o GPL-3 https://www.gnu.org/licenses/gpl-3.0.txt
</li></ul></code></pre>
<p>Vous allez également utiliser le fichier de licence BSD de ce tutoriel. Sous Linux, vous pouvez utiliser la commande suivante pour le copier dans votre répertoire home :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp /usr/share/common-licenses/BSD .
</li></ul></code></pre>
<p>Si vous êtes sur un autre système, créez le fichier en utilisant la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat &lt;&lt; 'EOF' &gt; BSD
</li><li class="line" data-prefix="$">Copyright (c) The Regents of the University of California.
</li><li class="line" data-prefix="$">All rights reserved.
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">Redistribution and use in source and binary forms, with or without
</li><li class="line" data-prefix="$">modification, are permitted provided that the following conditions
</li><li class="line" data-prefix="$">are met:
</li><li class="line" data-prefix="$">1. Redistributions of source code must retain the above copyright
</li><li class="line" data-prefix="$">   notice, this list of conditions and the following disclaimer.
</li><li class="line" data-prefix="$">2. Redistributions in binary form must reproduce the above copyright
</li><li class="line" data-prefix="$">   notice, this list of conditions and the following disclaimer in the
</li><li class="line" data-prefix="$">   documentation and/or other materials provided with the distribution.
</li><li class="line" data-prefix="$">3. Neither the name of the University nor the names of its contributors
</li><li class="line" data-prefix="$">   may be used to endorse or promote products derived from this software
</li><li class="line" data-prefix="$">   without specific prior written permission.
</li><li class="line" data-prefix="$">
</li><li class="line" data-prefix="$">THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS'' AND
</li><li class="line" data-prefix="$">ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
</li><li class="line" data-prefix="$">IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
</li><li class="line" data-prefix="$">ARE DISCLAIMED.  IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS BE LIABLE
</li><li class="line" data-prefix="$">FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
</li><li class="line" data-prefix="$">DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
</li><li class="line" data-prefix="$">OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
</li><li class="line" data-prefix="$">HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
</li><li class="line" data-prefix="$">LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
</li><li class="line" data-prefix="$">OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
</li><li class="line" data-prefix="$">SUCH DAMAGE.
</li><li class="line" data-prefix="$">EOF
</li></ul></code></pre>
<p>Maintenant que vous avez les fichiers, vous pouvez commencer à travailler avec <code>grep</code>.</p>

<p>Dans sa forme la plus basique, vous pouvez utiliser <code>grep</code> pour trouver les correspondances avec des modèles littéraux dans un fichier texte. Cela signifie que si vous passez une commande <code>grep</code> pour rechercher un mot, le système imprimera chaque ligne du fichier qui contient le mot en question.</p>

<p>Exécutez la commande suivante pour utiliser <code>grep</code> et trouver ainsi toutes les lignes qui contiennent le mot <code>GNU</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "GNU" GPL-3
</li></ul></code></pre>
<p>Le premier argument, <code>GNU</code>, est le modèle que vous recherchez alors que le second, <code>GPL-3</code>, est le fichier saisi dans lequel vous souhaitez faire votre recherche.</p>

<p>La sortie affichera chacune des lignes qui contient le texte modèle :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>                    <span class="highlight">GNU</span> GENERAL PUBLIC LICENSE
  The <span class="highlight">GNU</span> General Public License is a free, copyleft license for
the <span class="highlight">GNU</span> General Public License is intended to guarantee your freedom to
<span class="highlight">GNU</span> General Public License for most of our software; it applies also to
  Developers that use the <span class="highlight">GNU</span> GPL protect your rights with two steps:
  "This License" refers to version 3 of the <span class="highlight">GNU</span> General Public License.
  13. Use with the <span class="highlight">GNU</span> Affero General Public License.
under version 3 of the <span class="highlight">GNU</span> Affero General Public License into a single
...
...
</code></pre>
<p>Sur certains systèmes, le modèle que vous recherchez sera mis en surbrillance dans la sortie.</p>

<h3 id="options-courantes">Options courantes</h3>

<p>Par défaut, <code>grep</code> recherchera le modèle exact spécifié dans le fichier d'entrée et renverra les lignes qu'il trouvera. Vous pouvez rendre ce comportement plus pratique en ajoutant quelques balises facultatives à <code>grep</code>.</p>

<p>Si vous souhaitez que <code>grep</code> ignore la « case » de votre paramètre de recherche et que vous recherchez des variations à la fois en majuscule et en minuscule, vous pouvez spécifier l'option <code>-i</code> ou <code>--ignore-case</code>.</p>

<p>Recherchez chaque instance du mot <code>license</code> (en majuscule, minuscule ou les deux) dans le même fichier qu'auparavant avec la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -i "license" GPL-3
</li></ul></code></pre>
<p>Les résultats incluent : <code>LICENSE</code>, <code>license</code> et <code>License</code> :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>                    GNU GENERAL PUBLIC <span class="highlight">LICENSE</span>
 of this <span class="highlight">license</span> document, but changing it is not allowed.
  The GNU General Public <span class="highlight">License</span> is a free, copyleft <span class="highlight">license</span> for
  The <span class="highlight">license</span>s for most software and other practical works are designed
the GNU General Public <span class="highlight">License</span> is intended to guarantee your freedom to
GNU General Public <span class="highlight">License</span> for most of our software; it applies also to
price.  Our General Public <span class="highlight">License</span>s are designed to make sure that you
(1) assert copyright on the software, and (2) offer you this <span class="highlight">License</span>
  "This <span class="highlight">License</span>" refers to version 3 of the GNU General Public <span class="highlight">License</span>.
  "The Program" refers to any copyrightable work <span class="highlight">license</span>d under this
...
...
</code></pre>
<p>S'il y avait une instance avec <code>LiCeNsE</code>, elle aurait également été renvoyée.</p>

<p>Si vous souhaitez trouver toutes les lignes qui <strong>ne contiennent pas</strong> le modèle spécifié, vous pouvez utiliser l'option <code>-v</code> ou <code>--invert-match</code>.</p>

<p>Recherchez chaque ligne qui ne contient pas le mot <code>the</code> dans la licence BSD en exécutant la commande suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -v "the" BSD
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>All rights reserved.

Redistribution and use in source and binary forms, with or without
are met:
    may be used to endorse or promote products derived from this software
    without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY <span class="highlight">THE</span> REGENTS AND CONTRIBUTORS ``AS IS'' AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, <span class="highlight">THE</span>
...
...
</code></pre>
<p>Étant donné que vous n'avez pas précisé l'option « ignorer case », les deux derniers éléments ont été renvoyés comme ne comportant pas le mot <code>the</code>.</p>

<p>Il vous sera souvent utile de connaître le numéro de la ligne sur laquelle les correspondances sont trouvées. Pour se faire, vous pouvez utiliser l'option <code>-n</code> ou <code>--line-number</code>. Ré-exécutez l'exemple précédent en ajoutant la balise précédente :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -vn "the" BSD
</li></ul></code></pre>
<p>Vous verrez le texte suivant :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">2:</span>All rights reserved.
<span class="highlight">3:</span>
<span class="highlight">4:</span>Redistribution and use in source and binary forms, with or without
<span class="highlight">6:</span>are met:
<span class="highlight">13:</span>   may be used to endorse or promote products derived from this software
<span class="highlight">14:</span>   without specific prior written permission.
<span class="highlight">15:</span>
<span class="highlight">16:</span>THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS'' AND
<span class="highlight">17:</span>ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
...
...
</code></pre>
<p>Vous pouvez maintenant référencer le numéro des lignes dans le cas où vous souhaiteriez modifier chacune des lignes qui ne contient pas <code>the</code>. Cela est particulièrement pratique lorsque vous travaillez avec un code source.</p>

<h2 id="expressions-régulières">Expressions régulières</h2>

<p>Au cours de l'introduction, vous avez appris que <code>grep</code> signifiait « rechercher globalement les correspondances avec l'expression rationnelle ». Une « expression régulière » est une chaîne de texte qui décrit un modèle de recherche spécifique.</p>

<p>La manière dont les diverses applications et langages de programmation implémentent les expressions régulières est légèrement différente. Au cours de ce tutoriel, vous allez uniquement aborder un petit sous-ensemble de la façon dont <code>grep</code> décrit ses modèles.</p>

<h3 id="correspondances-littérales">Correspondances littérales</h3>

<p>Dans les exemples précédents de ce tutoriel, lorsque vous avez recherché les mots <code>GNU</code> et <code>the</code>, en réalité, vous avez recherché les expressions régulières de base qui correspondaient à la chaîne exacte de caractères <code>GNU</code> et <code>the</code>. Les modèles qui spécifient exactement les caractères à mettre en correspondance se nomment « literals » car ils correspondent littéralement au modèle, character-for-character.</p>

<p>Il est plus facile de les considérer comme une correspondance à une chaîne de caractères plutôt qu'à un mot. La distinction deviendra plus importante à mesure que les modèles que vous aborderez seront plus complexes.</p>

<p>La correspondance se fera sur tous les caractères alphabétiques et numériques (ainsi que certains autres caractères) littéralement à moins qu'un autre mécanisme d'expression ne vienne la modifier.</p>

<h3 id="correspondances-avec-les-ancres">Correspondances avec les ancres</h3>

<p>Les ancres sont des caractères spéciaux qui spécifient à quel endroit de la ligne une correspondance est considérée comme valable.</p>

<p>Par exemple, en utilisant des ancres, vous pouvez spécifier que vous souhaitez uniquement obtenir les lignes qui comportent <code>GNU</code> tout au début de la ligne. Pour cela, vous pouvez utiliser la balise <code>^</code> avant la chaîne littérale.</p>

<p>Exécutez la commande suivante pour rechercher le fichier <code>GPL-3</code> et trouver les lignes où <code>GNU</code> se trouve au tout début d'une ligne :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "^GNU" GPL-3
</li></ul></code></pre>
<p>Vous verrez apparaître les deux lignes suivantes :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">GNU</span> General Public License for most of our software; it applies also to
<span class="highlight">GNU</span> General Public License, you may choose any version ever published
</code></pre>
<p>De la même façon, vous devez utiliser l'ancre <code>$</code> à la fin d'un modèle pour indiquer que la correspondance ne sera valable que si elle se trouve à la fin d'une ligne.</p>

<p>Cette commande permettra de faire la correspondance avec chaque ligne se terminant par le mot <code>and</code> dans le fichier <code>GPL-3</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "and$" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>that there is no warranty for this free software.  For both users' <span class="highlight">and</span>
  The precise terms and conditions for copying, distribution <span class="highlight">and</span>
  License.  Each licensee is addressed as "you".  "Licensees" <span class="highlight">and</span>
receive it, in any medium, provided that you conspicuously <span class="highlight">and</span>
    alternative is allowed only occasionally and noncommercially, <span class="highlight">and</span>
network may be denied when the modification itself materially <span class="highlight">and</span>
adversely affects the operation of the network or violates the rules <span class="highlight">and</span>
provisionally, unless and until the copyright holder explicitly <span class="highlight">and</span>
receives a license from the original licensors, to run, modify <span class="highlight">and</span>
make, use, sell, offer for sale, import and otherwise run, modify <span class="highlight">and</span>
</code></pre>
<h3 id="correspondance-avec-tout-caractère">Correspondance avec tout caractère</h3>

<p>Dans les expressions régulières, le caractère du point (.) signifie que tout caractère individuel peut exister à l'endroit spécifié.</p>

<p>Par exemple, pour trouver toute correspondance dans le fichier <code>GPL-3</code> qui contient deux caractères et puis la chaine <code>cept</code>, vous devez utiliser le modèle suivant :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "..cept" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>use, which is precisely where it is most un<span class="highlight">accept</span>able.  Therefore, we
infringement under applicable copyright law, <span class="highlight">except</span> executing it on a
tells the user that there is no warranty for the work (<span class="highlight">except</span> to the
License by making <span class="highlight">except</span>ions from one or more of its conditions.
form of a separately written license, or stated as <span class="highlight">except</span>ions;
  You may not propagate or modify a covered work <span class="highlight">except</span> as expressly
  9. <span class="highlight">Accept</span>ance Not Required for Having Copies.
...
...
</code></pre>
<p>Comme vous pouvez le voir, la sortie affiche des instances à la fois de <code>accept</code> et <code>except</code> ainsi que des variations des deux mots. Le modèle devrait également trouver les correspondances avec <code>z2cept</code> s'il y en avait.</p>

<h3 id="expressions-entre-parenthèses">Expressions entre parenthèses</h3>

<p>En plaçant un groupe de caractères entre parenthèses (<code>\[</code> et <code>\]</code>), vous pouvez spécifier que le caractère qui se trouve à cette position peut être l'un des caractères du groupe entre parenthèses.</p>

<p>Par exemple, pour trouver les lignes qui en contiennent <code>too</code> ou <code>two</code>, vous devez spécifier ces variations succinctement en utilisant le modèle suivant :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "t[wo]o" GPL-3
</li></ul></code></pre>
<p>La sortie montre que les deux variations existent dans le fichier :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>your programs, <span class="highlight">too</span>.
freedoms that you received.  You must make sure that they, <span class="highlight">too</span>, receive
  Developers that use the GNU GPL protect your rights with <span class="highlight">two</span> steps:
a computer ne<span class="highlight">two</span>rk, with no transfer of a copy, is not conveying.
System Libraries, or general-purpose <span class="highlight">too</span>ls or generally available free
    Corresponding Source from a ne<span class="highlight">two</span>rk server at no charge.
...
...
</code></pre>
<p>La notation des parenthèses vous offre quelques options intéressantes. Vous pouvez utiliser un modèle qui trouve toutes les correspondances <strong>sauf</strong> les caractères entre parenthèses en commençant la liste de caractères entre parenthèses par un <code>^</code></p>

<p>Dans cet exemple, nous trouverons le modèle <code>.ode</code> mais ne fera pas correspondre le modèle <code>code</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "[^c]ode" GPL-3
</li></ul></code></pre>
<p>Voici la sortie qui s'affichera :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>  1. Source <span class="highlight">Code</span>.
    <span class="highlight">mode</span>l, to give anyone who possesses the object code either (1) a
the only significant <span class="highlight">mode</span> of use of the product.
notice like this when it starts in an interactive <span class="highlight">mode</span>:
</code></pre>
<p>Notez que dans la seconde ligne renvoyée, il y a, en fait, le mot <code>code</code>. Il ne s'agit pas d'un échec de l'expression régulière ou de grep. Au contraire, cette ligne a été renvoyée car plus tôt dans la ligne, le système a trouvé le modèle <code>mode</code> qui se trouve dans le mot <code>model</code>. La ligne a été renvoyée car il y avait une instance qui correspond au modèle.</p>

<p>Une autre des fonctionnalités utiles des parenthèses est que vous pouvez spécifier une gamme de caractères au lieu de saisir chaque caractère disponible individuellement.</p>

<p>Ainsi, si vous le souhaitez, vous pouvez trouver toutes les lignes qui commencent par une lettre majuscule avec le modèle suivant :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "^[A-Z]" GPL-3
</li></ul></code></pre>
<p>Voici la sortie que vous verrez :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">G</span>NU General Public License for most of our software; it applies also to
<span class="highlight">S</span>tates should not allow patents to restrict development and use of
<span class="highlight">L</span>icense.  Each licensee is addressed as "you".  "Licensees" and
<span class="highlight">C</span>omponent, and (b) serves only to enable use of the work with that
<span class="highlight">M</span>ajor Component, or to implement a Standard Interface for which an
<span class="highlight">S</span>ystem Libraries, or general-purpose tools or generally available free
<span class="highlight">S</span>ource.
<span class="highlight">U</span>ser Product is transferred to the recipient in perpetuity or for a
...
...
</code></pre>
<p>À cause de certains des problèmes de tri hérités, il est souvent plus juste d'utiliser les catégories de caractères POSIX à la place des gammes de caractères que vous venez d'utiliser.</p>

<p>Il existe plusieurs caractères qui sont en-dehors du champ de ce guide, mais voici un exemple qui accomplira la même procédure que celle de l'exemple précédent en utilisant la catégorie de caractères <code>\[:upper:\]</code> dans un sélectionneur de parenthèses :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "^[[:upper:]]" GPL-3
</li></ul></code></pre>
<p>La sortie sera la même qu'auparavant.</p>

<h3 id="répéter-le-modèle-zero-ou-more-times">Répéter le modèle Zero ou More Times</h3>

<p>Enfin, l'un des méta-caractères les plus couramment utilisés est l'astérisque ou <code>*</code>, qui signifie « répétez le caractère précédent ou l'expression zero ou more times ».</p>

<p>Pour trouver chaque ligne dans le fichier <code>GPL-3</code> qui contient des parenthèses d'ouverture et de fermeture, avec seulement des lettres et des espaces uniques entre les deux, utilisez l'expression suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "([A-Za-z ]*)" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div> Copyright <span class="highlight">(C)</span> 2007 Free Software Foundation, Inc.
distribution <span class="highlight">(with or without modification)</span>, making available to the
than the work as a whole, that <span class="highlight">(a)</span> is included in the normal form of
Component, and <span class="highlight">(b)</span> serves only to enable use of the work with that
<span class="highlight">(if any)</span> on which the executable work runs, or a compiler used to
    <span class="highlight">(including a physical distribution medium)</span>, accompanied by the
    <span class="highlight">(including a physical distribution medium)</span>, accompanied by a
    place <span class="highlight">(gratis or for a charge)</span>, and offer equivalent access to the
...
...
</code></pre>
<p>Jusqu'à présent, vous avez utilisé les points, les astérisques et d'autres caractères dans vos expressions. Cependant, vous aurez parfois besoin de rechercher ces caractères spécifiques.</p>

<h3 id="Éviter-le-méta-caractères">Éviter le méta-caractères</h3>

<p>Parfois, vous aurez besoin de trouver un point ou une parenthèse d'ouverture en tant que tel, tout particulièrement lorsque vous travaillerez avec un code source ou des fichiers de configuration. Étant donné que ces caractères ont une signification spéciale dans les expressions régulières, vous devez « échapper » ces caractères pour indiquer à <code>grep</code> que vous ne souhaitez pas utiliser leur signification spéciale dans ce cas.</p>

<p>Vous pouvez éviter des caractères en utilisant un caractère de barre oblique inverse (<code>\</code>) devant le caractère qui devrait normalement avoir une signification spéciale.</p>

<p>Par exemple, pour trouver toutes les lignes qui commencent par une lettre majuscule et se termine par un point, utilisez l'expression suivante qui évite le point final afin qu'elle représente un point littéral et non pas le sens habituel « any character » :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "^[A-Z].*\.$" GPL-3
</li></ul></code></pre>
<p>Voici la sortie qui s'affichera :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Source.
License by making exceptions from one or more of its conditions.
License would be to refrain entirely from conveying the Program.
ALL NECESSARY SERVICING, REPAIR OR CORRECTION.
SUCH DAMAGES.
Also add information on how to contact you by electronic and paper mail.
</code></pre>
<p>Prenons maintenant en considération d'autres options d'expressions régulières.</p>

<h2 id="expressions-régulières-étendues">Expressions régulières étendues</h2>

<p>La commande <code>grep</code> prend en charge un langage d'expression plus vaste en utilisant la balise <code>-E</code> ou en appelant la commande <code>egrep</code> au lieu de <code>grep</code>.</p>

<p>Ces options vous donne la possibilité d'utiliser des « expressions régulières étendues ». Les expressions régulières étendues incluent tous les méta-caractères de base ainsi que les méta-caractères supplémentaires qui permettent d'exprimer des caractères plus complexes.</p>

<h3 id="regroupement">Regroupement</h3>

<p>L'une des capacités les plus utiles qu'offrent les expressions régulières étendues est la possibilité de regrouper des expressions ensemble et de les manipuler ou d'y faire référence en tant qu'une seule unité.</p>

<p>Groupez les expressions ensemble à l'aide de parenthèses. Si vous souhaitez utiliser des parenthèses sans utiliser des expressions régulières étendues, vous pouvez les éviter avec la barre oblique inverse pour activer cette fonctionnalité.</p>

<p>Les trois expressions suivantes se valent en termes de fonctionnalité :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep "\(grouping\)" file.txt
</li><li class="line" data-prefix="$">grep -E "(grouping)" file.txt
</li><li class="line" data-prefix="$">egrep "(grouping)" file.txt
</li></ul></code></pre>
<h3 id="alternance">Alternance</h3>

<p>Tout comme les expressions entre parenthèses peuvent spécifier différents choix possibles de correspondance avec des caractères uniques, l'alternance vous permet de spécifier des correspondances alternatives pour les chaînes de caractères ou ensembles d'expression.</p>

<p>Pour indiquer une alternance, utilisez le caractère de barre droite <code>|</code>. Ils sont souvent utilisés dans des regroupements entre parenthèses pour spécifier qu'une sur deux ou plusieurs des possibilités devraient être considérées comme une correspondance.</p>

<p>La commande suivante trouvera soit <code>GPL</code> ou <code>General Public Licence</code> dans le texte :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -E "(GPL|General Public License)" GPL-3
</li></ul></code></pre>
<p>Le résultat ressemblera à ce qui suit :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>  The GNU <span class="highlight">General Public License</span> is a free, copyleft license for
the GNU <span class="highlight">General Public License</span> is intended to guarantee your freedom to
GNU <span class="highlight">General Public License</span> for most of our software; it applies also to
price.  Our <span class="highlight">General Public License</span>s are designed to make sure that you
  Developers that use the GNU <span class="highlight">GPL</span> protect your rights with two steps:
  For the developers' and authors' protection, the <span class="highlight">GPL</span> clearly explains
authors' sake, the <span class="highlight">GPL</span> requires that modified versions be marked as
have designed this version of the <span class="highlight">GPL</span> to prohibit the practice for those
...
...
</code></pre>
<p>Avec l'alternance, vous pouvez faire votre sélection entre plus de deux options en ajoutant des choix supplémentaires dans le groupe de sélection séparé par des caractères supplémentaires de barre oblique (|).</p>

<h3 id="quantificateurs">Quantificateurs</h3>

<p>Comme le méta-caractère <code>*</code> qui correspond au caractère précédent ou au caractère défini sur zero ou more times, d'autres méta-caractères disponibles dans des expressions régulières étendues permettent de spécifier le nombre d'événements.</p>

<p>Pour trouver une correspondance de caractère zero ou one times, vous pouvez utiliser le <code>?</code> . Cela rend le caractère ou les ensembles de caractères précédents optionnels, par essence.</p>

<p>La commande suivante cherche les correspondances de <code>copyright</code> et <code>right</code> en plaçant <code>copy</code> dans un groupe optionnel :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -E "(copy)?right" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div> Copy<span class="highlight">right</span> (C) 2007 Free Software Foundation, Inc.
  To protect your <span class="highlight">right</span>s, we need to prevent others from denying you
these <span class="highlight">right</span>s or asking you to surrender the <span class="highlight">right</span>s.  Therefore, you have
know their <span class="highlight">right</span>s.
  Developers that use the GNU GPL protect your <span class="highlight">right</span>s with two steps:
(1) assert <span class="highlight">copyright</span> on the software, and (2) offer you this License
  "Copy<span class="highlight">right</span>" also means <span class="highlight">copyright</span>-like laws that apply to other kinds of
...
</code></pre>
<p>Le caractère <code>+</code> correspond à l'apparition une ou plusieurs fois d'une expression. Il est presque similaire au méta-caractère <code>*</code>, mais avec le caractère <code>+</code>, l'expression <em>doit</em> correspondre au moins une fois.</p>

<p>L'expression suivante correspond à la chaîne <code>free</code> plus un ou plusieurs caractères qui ne sont pas des caractères d'espacement :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -E "free[^[:space:]]+" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>  The GNU General Public License is a <span class="highlight">free,</span> copyleft license for
to take away your <span class="highlight">freedom</span> to share and change the works.  By contrast,
the GNU General Public License is intended to guarantee your <span class="highlight">freedom</span> to
  When we speak of free software, we are referring to <span class="highlight">freedom,</span> not
have the <span class="highlight">freedom</span> to distribute copies of free software (and charge for
you modify it: responsibilities to respect the <span class="highlight">freedom</span> of others.
<span class="highlight">freedoms</span>s that you received.  You must make sure that they, too, receive
protecting users' <span class="highlight">freedom</span> to change the software.  The systematic
of the GPL, as needed to protect the <span class="highlight">freedom</span> of users.
patents cannot be used to render the program non-<span class="highlight">free.</span>
</code></pre>
<h3 id="spécifier-la-répétition-de-correspondance">Spécifier la répétition de correspondance</h3>

<p>Pour spécifier le nombre de fois qu'une correspondance est répétée, utilisez les caractères d'accolade (<code>{</code> et <code>}</code>). Ces caractères vous permettent de spécifier avec exactitude un numéro, une plage ou une limite supérieure ou inférieure pour le nombre de fois qu'une correspondance à une expression est trouvée.</p>

<p>Utilisez l'expression suivante pour trouver toutes les lignes dans le fichier <code>GPL-3</code> qui contiennent trois voyelles :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -E "[AEIOUaeiou]{3}" GPL-3
</li></ul></code></pre>
<p>Chaque ligne renvoyée contient un mot avec trois voyelles :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>changed, so that their problems will not be attributed erron<span class="highlight">eou</span>sly to
authors of prev<span class="highlight">iou</span>s versions.
receive it, in any medium, provided that you conspic<span class="highlight">uou</span>sly and
give under the prev<span class="highlight">iou</span>s paragraph, plus a right to possession of the
covered work so as to satisfy simultan<span class="highlight">eou</span>sly your obligations under this
</code></pre>
<p>Pour trouver la correspondance avec tous les mots qui ont entre 16 et 20 caractères, utilisez l'expression suivante :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">grep -E "[[:alpha:]]{16,20}" GPL-3
</li></ul></code></pre>
<p>Vous verrez la sortie suivante :</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>    certain <span class="highlight">responsibilities</span> if you distribute copies of the software, or if
    you modify it: <span class="highlight">responsibilities</span> to respect the freedom of others.
        c) Prohibiting <span class="highlight">misrepresentation</span> of the origin of that material, or
</code></pre>
<p>Seules les lignes contenant des mots de cette longueur s'afficheront.</p>

<h2 id="conclusion">Conclusion</h2>

<p><code>grep</code> est une fonctionnalité pratique pour trouver des modèles dans des fichiers ou la hiérarchie du système de fichiers. Il est donc conseiller de passer un peu de temps pour se familiariser avec ses options et sa syntaxe.</p>

<p>Les expressions régulières sont encore plus versatiles et peuvent être utilisées avec plusieurs programmes populaires. Par exemple, de nombreux éditeurs de texte implémentent des expressions régulières pour rechercher et remplacer du texte.</p>

<p>En outre, la plupart des langages de programmation modernes utilisent des expressions régulières pour exécuter des procédures sur des données spécifiques. Une fois que vous aurez compris les expressions régulières, vous pourrez transférer ces connaissances sur plusieurs tâches informatiques courantes, de la recherche avancée dans votre éditeur de texte à la validation des entrées de l'utilisateur.</p>
