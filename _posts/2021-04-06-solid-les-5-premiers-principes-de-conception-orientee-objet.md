---
title : "SOLID : les 5 premiers principes de conception orientée objet"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/s-o-l-i-d-the-first-five-principles-of-object-oriented-design-fr
image: "https://sergio.afanou.com/assets/images/image-midres-43.jpg"
---

<h3 id="introduction">Introduction</h3>

<p><em>SOLID</em> est un acronyme des cinq premiers principes de la conception orientée objet (OOD) de Robert C. Martin (également connu sous le nom de d&rsquo;<a href="http://en.wikipedia.org/wiki/Robert_Cecil_Martin">oncle Bob</a>).</p>

<p><span class='note'><strong>Remarque :</strong> bien que ces principes puissent s'appliquer aux différents langages de programmation, nous utiliserons le langage PHP dans l'exemple de code utilisé dans cet article.<br></span></p>

<p>Ces principes établissent des pratiques qui appartiennent au développement de logiciels tout en prenant en considération les exigences d'entretien et d'extension à mesure que le projet se développe. L'adoption de ces pratiques peut également contribuer à éviter les mauvaises odeurs, réusiner un code et développer des logiciels agiles et adaptatifs.</p>

<p>SOLID signifie :</p>

<ul>
<li><a href="#single-responsibility-principle"><strong>S</strong> - Responsabilité unique (Single responsibility principle)</a></li>
<li><a href="#open-closed-principle"><strong>O</strong> - Ouvert/fermé (Open/closed principle)</a></li>
<li><a href="#liskov-substitution-principle"><strong>L</strong> - Substitution de Liskov (Liskov substitution principle)</a></li>
<li><a href="#interface-segregation-principle"><strong>I</strong> - Ségrégation des interfaces (Interface segregation principle)</a></li>
<li><a href="#dependency-inversion-principle"><strong>D</strong> - Inversion des dépendances (Dependency inversion principle)</a></li>
</ul>

<p>Au cours de cet article, vous serez initié à chacun de ces principes afin de comprendre comment SOLID peut vous aider à devenir un meilleur développeur.</p>

<h2 id="principe-de-responsabilité-unique">Principe de responsabilité unique</h2>

<p>Le principe de responsabilité unique (SRP) précise ce qui suit :</p>

<blockquote>
<p>Une classe doit avoir une seule et unique raison de changer, ce qui signifie qu'une classe ne doit appartenir qu'à une seule tâche.</p>
</blockquote>

<p>Par exemple, imaginez une application qui prend un ensemble de formes (cercles et carrés) et calcule la somme de la superficie de toutes les formes de l'ensemble.</p>

<p>Tout d'abord, vous devez créer les classes de forme et configurer les paramètres requis des constructeurs.</p>

<p>Pour les carrés, vous devrez connaître la <code>longueur</code> d'un côté :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square
{
    public $length;

    public function construct($length)
    {
        $this-&gt;length = $length;
    }
}
</code></pre>
<p>Pour les cercles, vous devrez connaître le <code>rayon</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle
{
    public $radius;

    public function construct($radius)
    {
        $this-&gt;radius = $radius;
    }
}
</code></pre>
<p>Ensuite, créez la classe <code>AreaCalculator</code> et écrivez la logique pour additionner les superficies de toutes les formes fournies. La superficie d'un carré se calcule avec la longueur au carré. La superficie d'un cercle se calcule en multipliant pi par le rayon au carré.</p>
<pre class="code-pre "><code class="code-highlight language-php">class AreaCalculator
{
    protected $shapes;

    public function __construct($shapes = [])
    {
        $this-&gt;shapes = $shapes;
    }

    public function sum()
    {
        <span class="highlight">foreach ($this-&gt;shapes as $shape) {</span>
            <span class="highlight">if (is_a($shape, 'Square')) {</span>
                <span class="highlight">$area[] = pow($shape-&gt;length, 2);</span>
            <span class="highlight">} elseif (is_a($shape, 'Circle')) {</span>
                <span class="highlight">$area[] = pi() * pow($shape-&gt;radius, 2);</span>
            <span class="highlight">}</span>
        <span class="highlight">}</span>

        <span class="highlight">return array_sum($area);</span>
    }

    public function output()
    {
        return implode('', [
          '',
              'Sum of the areas of provided shapes: ',
              $this-&gt;sum(),
          '',
      ]);
    }
}
</code></pre>
<p>Pour utiliser la classe <code>AreaCalculator</code>, vous devrez instancier la classe, y passer un tableau de formes et afficher la sortie au bas de la page.</p>

<p>Voici un exemple avec un ensemble de trois formes :</p>

<ul>
<li>un cercle avec un rayon de 2</li>
<li>un carré avec une longueur de 5</li>
<li>un second carré avec une longueur de 6</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-php">$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);

echo $areas-&gt;output();
</code></pre>
<p>Le problème avec la méthode de sortie est que l’<code>AreaCalculator</code> gère la logique qui génère les données.</p>

<p>Imaginez un scénario où la sortie doit être convertie dans un autre format, comme JSON.</p>

<p>L'ingralité de la logique serait traitée par la classe <code>AreaCalculator</code>. Cela viendrait enfreindre le principe de responsabilité unique. La classe <code>AreaCalculator</code> doit uniquement s'occuper de la somme des superficies des formes fournies. Elle ne doit pas chercher à savoir si l'utilisateur souhaite un format JSON ou HTML.</p>

<p>Pour y remédier, vous pouvez créer une classe <code>SumCalculatorOutputter</code> distincte. Ensuite, utilisez cette nouvelle classe pour gérer la logique dont vous avez besoin pour générer les données sur l'utilisateur :</p>
<pre class="code-pre "><code class="code-highlight language-php">class SumCalculatorOutputter
{
    protected $calculator;

    public function __constructor(AreaCalculator $calculator)
    {
        $this-&gt;calculator = $calculator;
    }

    public function JSON()
    {
        $data = [
          'sum' =&gt; $this-&gt;calculator-&gt;sum(),
      ];

        return json_encode($data);
    }

    public function HTML()
    {
        return implode('', [
          '',
              'Sum of the areas of provided shapes: ',
              $this-&gt;calculator-&gt;sum(),
          '',
      ]);
    }
}
</code></pre>
<p>La classe <code>SumCalculatorOutputter</code> fonctionnerait comme suit :</p>
<pre class="code-pre "><code class="code-highlight language-php">$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);
<span class="highlight">$output = new SumCalculatorOutputter($areas);</span>

<span class="highlight">echo $output-&gt;JSON();</span>
<span class="highlight">echo $output-&gt;HTML();</span>
</code></pre>
<p>Maintenant, la logique dont vous avez besoin pour générer les données pour l'utilisateur est traitée par la classe <code>SumCalculatorOutputter</code>.</p>

<p>Cela répond au principe de responsabilité unique.</p>

<h2 id="ouvert-fermé">Ouvert/fermé</h2>

<p>Le principe ouvert/fermé (S.R.P.) précise ce qui suit :</p>

<blockquote>
<p>Les objets ou entités devraient être ouverts à l'extension mais fermés à la modification.</p>
</blockquote>

<p>Cela signifie qu'une classe doit être extensible mais ne pas modifier la classe en elle-même.</p>

<p>Reprenons la classe <code>AreaCalculator</code> et concentrons sur la méthode <code>sum</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class AreaCalculator
{
    protected $shapes;

    public function __construct($shapes = [])
    {
        $this-&gt;shapes = $shapes;
    }

    public function sum()
    {
        foreach ($this-&gt;shapes as $shape) {
            if (is_a($shape, 'Square')) {
                $area[] = pow($shape-&gt;length, 2);
            } elseif (is_a($shape, 'Circle')) {
                $area[] = pi() * pow($shape-&gt;radius, 2);
            }
        }

        return array_sum($area);
    }
}
</code></pre>
<p>Imaginez qu'un utilisateur souhaite connaître la somme (<code>sum</code>) de formes supplémentaires comme des triangles, des pentagones, des hexagones, etc. Il vous faudrait constamment modifier ce fichier et ajouter des blocs <code>if</code>/<code>else</code>. Cela viendrait enfreindre le principe ouvert/fermé.</p>

<p>Il existe un moyen de rendre cette méthode <code>sum</code> plus simple qui consiste à supprimer la logique qui permet de calculer la superficie de chaque forme de la méthode de la classe <code>AreaCalculator</code> et la joindre à la classe de chaque forme.</p>

<p>Voici la méthode <code>area</code> définie dans <code>Square</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square
{
    public $length;

    public function __construct($length)
    {
        $this-&gt;length = $length;
    }

    <span class="highlight">public function area()</span>
    <span class="highlight">{</span>
        <span class="highlight">return pow($this-&gt;length, 2);</span>
    <span class="highlight">}</span>
}
</code></pre>
<p>Et voici la méthode <code>area</code> définie dans <code>Circle</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle
{
    public $radius;

    public function construct($radius)
    {
        $this-&gt;radius = $radius;
    }

    <span class="highlight">public function area()</span>
    <span class="highlight">{</span>
        <span class="highlight">return pi() * pow($shape-&gt;radius, 2);</span>
    <span class="highlight">}</span>
}
</code></pre>
<p>Vous pourrez alors réécrire la méthode <code>sum</code> pour <code>AreaCalculator</code> de la manière suivante :</p>
<pre class="code-pre "><code class="code-highlight language-php">class AreaCalculator
{
    // ...

    public function sum()
    {
        foreach ($this-&gt;shapes as $shape) {
            <span class="highlight">$area[] = $shape-&gt;area();</span>
        }

        return array_sum($area);
    }
}
</code></pre>
<p>Maintenant, vous pouvez créer une autre classe de forme et la passer dans le calcul de la somme sans briser le code.</p>

<p>Cependant, un autre problème se pose. Comment savoir si l'objet passé dans l’<code>AreaCalculator</code> est réellement une forme ou si la forme a une méthode nommée <code>area</code> ?</p>

<p>Le codage vers une <a href="https://www.php.net/manual/en/language.oop5.interfaces.php">interface</a> fait partie intégrante de SOLID.</p>

<p>Créez une <code>ShapeInterface</code> qui prend en charge <code>area</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php"><span class="highlight">interface ShapeInterface</span>
<span class="highlight">{</span>
    <span class="highlight">public function area();</span>
<span class="highlight">}</span>
</code></pre>
<p>Modifiez vos classes de forme pour <code>implémenter</code> la <code>ShapeInterface</code>.</p>

<p>Voici la mise à jour faite à <code>Square</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>Et voici la mise à jour faite à <code>Circle</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>Dans la méthode <code>sum</code> pour <code>AreaCalculator</code>, vous pouvez vérifier si les formes fournies sont effectivement des instances de la <code>ShapeInterface</code>. Dans le cas contraire, lancez une exception :</p>
<pre class="code-pre "><code class="code-highlight language-php"> class AreaCalculator
{
    // ...

    public function sum()
    {
        foreach ($this-&gt;shapes as $shape) {
            <span class="highlight">if (is_a($shape, 'ShapeInterface')) {</span>
                $area[] = $shape-&gt;area();
                <span class="highlight">continue;</span>
            <span class="highlight">}</span>

            <span class="highlight">throw new AreaCalculatorInvalidShapeException();</span>
        }

        return array_sum($area);
    }
}
</code></pre>
<p>Cela satisfait au principe ouvert/fermé.</p>

<h2 id="substitution-de-liskov">Substitution de Liskov</h2>

<p>Le principe de substitution de Liskov précise ce qui suit :</p>

<blockquote>
<p>Si q(x) est une propriété démontrable pour tout objet x de type T, alors q(y) est vraie pour tout objet y de type S tel que S est un sous-type de T.</p>
</blockquote>

<p>Cela signifie que chaque sous-classe ou classe dérivée doit être substituable au niveau de leur classe de base ou parent.</p>

<p>En reprenant l'exemple de la classe <code>AreaCalculator</code>, imaginez une nouvelle classe <code>VolumeCalculator</code> qui étend la classe <code>AreaCalculator</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class VolumeCalculator extends AreaCalculator
{
    public function construct($shapes = [])
    {
        parent::construct($shapes);
    }

    public function sum()
    {
        // logic to calculate the volumes and then return an array of output
        return [$summedData];
    }
}
</code></pre>
<p>Rappelez-vous que la classe <code>SumCalculatorOutputter</code> ressemble à ce qui suit :</p>
<pre class="code-pre "><code class="code-highlight language-php">class SumCalculatorOutputter {
    protected $calculator;

    public function __constructor(AreaCalculator $calculator) {
        $this-&gt;calculator = $calculator;
    }

    public function JSON() {
        $data = array(
            'sum' =&gt; $this-&gt;calculator-&gt;sum();
        );

        return json_encode($data);
    }

    public function HTML() {
        return implode('', array(
            '',
                'Sum of the areas of provided shapes: ',
                $this-&gt;calculator-&gt;sum(),
            ''
        ));
    }
}
</code></pre>
<p>Si vous avez essayé d'exécuter un exemple comme celui-ci :</p>
<pre class="code-pre "><code class="code-highlight language-php">$areas = new AreaCalculator($shapes);
<span class="highlight">$volumes = new VolumeCalculator($solidShapes);</span>

$output = new SumCalculatorOutputter($areas);
<span class="highlight">$output2 = new SumCalculatorOutputter($volumes);</span>
</code></pre>
<p>Une fois que vous appelez la méthode <code>HTML</code> sur l'objet <code>$output2</code>, vous obtiendrez une erreur <code>E_NOTICE</code> vous informant de la conversion d'un tableau en chaînes de caractères.</p>

<p>Pour corriger ce problème, au lieu de renvoyer un tableau à partir de la méthode de somme de la classe <code>VolumeCalculator</code>, renvoyez <code>$summedData</code> :</p>
<pre class="code-pre "><code class="code-highlight language-php">class VolumeCalculator extends AreaCalculator
{
    public function construct($shapes = [])
    {
        parent::construct($shapes);
    }

    public function sum()
    {
        // logic to calculate the volumes and then return a value of output
        <span class="highlight">return $summedData;</span>
    }
}
</code></pre>
<p>Le <code>$summedData</code> peut être un décimal, un double ou un entier.</p>

<p>Cela satisfait au principe de substitution de Liskov.</p>

<h2 id="ségrégation-des-interfaces">Ségrégation des interfaces</h2>

<p>Le principe de ségrégation des interfaces indique ce qui suit :</p>

<blockquote>
<p>Un client ne doit jamais être forcé à installer une interface qu'il n'utilise pas et les clients ne doivent pas être forcés à dépendre de méthodes qu'ils n'utilisent pas.</p>
</blockquote>

<p>Toujours en se basant sur l'exemple précédent de <code>ShapeInterface</code>, vous devrez prendre en charge les nouvelles formes tri-dimensionnelles de <code>Cuboid</code> et <code>Spheroid</code>. Ces formes devront également calculer <code>volume</code>.</p>

<p>Imaginons ce qui se passerait si vous deviez modifier la <code>ShapeInterface</code> pour ajouter un autre contrat :</p>
<pre class="code-pre "><code class="code-highlight language-php">interface ShapeInterface
{
    public function area();

    <span class="highlight">public function volume();</span>
}
</code></pre>
<p>Maintenant, toute forme que vous créez doit implémenter la méthode <code>volume</code>. Cependant, vous savez que les carrés sont des formes plates et qu'ils n'ont pas de volume. Ainsi, cette interface forcera la classe <code>Square</code> à implémenter une méthode dont elle n'a pas besoin.</p>

<p>Cela viendrait enfreindre le principe de ségrégation des interfaces. Au lieu de cela, vous pouvez créer une autre interface appelée <code>ThreeDimensionalShapeInterface</code> qui a le contrat <code>volume</code> et les formes tri-dimensionnelles peuvent implémenter cette interface :</p>
<pre class="code-pre "><code class="code-highlight language-php">interface ShapeInterface
{
    public function area();
}

<span class="highlight">interface ThreeDimensionalShapeInterface</span>
<span class="highlight">{</span>
    public function volume();
<span class="highlight">}</span>

class Cuboid implements ShapeInterface, <span class="highlight">ThreeDimensionalShapeInterface</span>
{
    public function area()
    {
        // calculate the surface area of the cuboid
    }

    public function volume()
    {
        // calculate the volume of the cuboid
    }
}
</code></pre>
<p>Il s'agit d'une bien meilleure approche. Mais faites attention à ne pas tomber dans le piège lors de la saisie de ces interfaces. Au lieu d'utiliser une <code>ShapeInterface</code> ou une <code>ThreeDimensionalShapeInterface</code>, vous pouvez créer une autre interface, peut-être <code>ManageShapeInterface</code>. Vous pourrez ensuite l'implémenter sur les formes à la fois plates et tri-dimensionnelles.</p>

<p>Ainsi, vous pourrez gérer les formes avec une application unique :</p>
<pre class="code-pre "><code class="code-highlight language-php"><span class="highlight">interface ManageShapeInterface</span>
<span class="highlight">{</span>
    <span class="highlight">public function calculate();</span>
<span class="highlight">}</span>

class Square implements ShapeInterface, <span class="highlight">ManageShapeInterface</span>
{
    public function area()
    {
        // calculate the area of the square
    }

    <span class="highlight">public function calculate()</span>
    <span class="highlight">{</span>
        <span class="highlight">return $this-&gt;area();</span>
    <span class="highlight">}</span>
}

class Cuboid implements ShapeInterface, ThreeDimensionalShapeInterface, <span class="highlight">ManageShapeInterface</span>
{
    public function area()
    {
        // calculate the surface area of the cuboid
    }

    public function volume()
    {
        // calculate the volume of the cuboid
    }

    <span class="highlight">public function calculate()</span>
    <span class="highlight">{</span>
        <span class="highlight">return $this-&gt;area();</span>
    <span class="highlight">}</span>
}
</code></pre>
<p>Maintenant dans la classe <code>AreaCalculator</code>, vous pouvez remplacer l'appel à la méthode <code>area</code> avec <code>calculate</code>, et également vérifier si l'objet est une instance de <code>ManageShapeInterface</code> et non de la <code>ShapeInterface</code>.</p>

<p>Cela satisfait au principe de ségrégation des interfaces.</p>

<h2 id="inversion-des-dépendances">Inversion des dépendances</h2>

<p>Le principe d'inversion des dépendances précise :</p>

<blockquote>
<p>Les entités doivent dépendre des abstractions, pas des implémentations. Il indique que le module de haut niveau ne doit pas dépendre du module de bas niveau, mais qu'ils doivent dépendre des abstractions.</p>
</blockquote>

<p>Ce principe permet le découplage.</p>

<p>Voici l'exemple d'un <code>PasswordReminder</code> qui se connecte à une base de données MySQL :</p>
<pre class="code-pre "><code class="code-highlight language-php">class MySQLConnection
{
    public function connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}

class PasswordReminder
{
    private $dbConnection;

    public function __construct(MySQLConnection $dbConnection)
    {
        $this-&gt;dbConnection = $dbConnection;
    }
}
</code></pre>
<p>Tout d'abord, le <code>MySQLConnection</code> est le module de bas niveau tandis que le <code>PasswordReminder</code> est celui de haut niveau, mais selon la définition du <strong>D</strong> de SOLID, qui indique de <em>Depend on abstraction, not on concretions</em>. Ce fragment de code ci-dessus enfreint ce principe, car la classe <code>PasswordReminder</code> est forcée de dépendre de la classe <code>MySQLConnection</code>.</p>

<p>Plus tard, si vous venez à devoir modifier le moteur de la base de données, vous aurez également à modifier la classe <code>PasswordReminder</code>, ce qui enfreindrait l&rsquo;<em>open-close principle</em>.</p>

<p>La classe <code>PasswordReminder</code> ne doit pas se préoccuper de la base de données utilisée par votre application. Pour résoudre ces problèmes, vous pouvez coder sur une interface étant donné que les modules de haut et de bas niveau doivent dépendre de l'abstraction :</p>
<pre class="code-pre "><code class="code-highlight language-php">interface DBConnectionInterface
{
    public function connect();
}
</code></pre>
<p>L'interface a une méthode de connexion et la classe <code>MySQLConnection</code> implémente cette interface. De plus, au lieu de directement indiquer la classe <code>MySQLConnection</code> dans le constructeur du <code>PasswordReminder</code>, vous devriez plutôt indiquer le type de la <code>DBConnectionInterface</code>. Et, quel que soit le type de base de données utilisée par votre application, la classe <code>PasswordReminder</code> pourra se connecter à la base de données sans aucun problème tout en respectant le principe ouvert-fermé.</p>
<pre class="code-pre "><code class="code-highlight language-php">class MySQLConnection <span class="highlight">implements DBConnectionInterface</span>
{
    public function connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}

class PasswordReminder
{
    private $dbConnection;

    public function __construct(<span class="highlight">DBConnectionInterface $dbConnection</span>)
    {
        $this-&gt;dbConnection = $dbConnection;
    }
}
</code></pre>
<p>Ce code établit que les modules génériques et détaillés dépendent de l'abstraction.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Au cours de cet article, nous vous avons présenté les cinq principes du code SOLID. Les projets qui adhèrent aux principes SOLID peuvent être partagés avec des collaborateurs, étendus, modifiés, testés et remaniés plus facilement.</p>

<p>Continuez votre apprentissage en consultant d'autres pratiques de <a href="https://en.wikipedia.org/wiki/Agile_software_development">développement de logiciels agiles</a> et <a href="https://en.wikipedia.org/wiki/Adaptive_software_development">adaptatifs</a>.</p>
