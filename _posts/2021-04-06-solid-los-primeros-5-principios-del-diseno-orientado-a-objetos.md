---
title : "SOLID: Los primeros 5 principios del diseño orientado a objetos"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/s-o-l-i-d-the-first-five-principles-of-object-oriented-design-es
image: "https://sergio.afanou.com/assets/images/image-midres-40.jpg"
---

<h3 id="introducción">Introducción</h3>

<p><em>SOLID</em> es un acrónimo de los primeros cinco principios del diseño orientado a objetos (OOD) de Robert C. Martin (también conocido como el <a href="http://en.wikipedia.org/wiki/Robert_Cecil_Martin">Tío Bob</a>).</p>

<p><span class='note'><strong>Nota:</strong> Aunque estos principios pueden aplicarse a varios lenguajes de programación, el código de muestra que se incluye en este artículo usará PHP.<br></span></p>

<p>Estos principios establecen prácticas que se prestan al desarrollo de software con consideraciones para su mantenimiento y expansión a medida que el proyecto se amplía. Adoptar estas prácticas también puede ayudar a evitar los aromas de código, refactorizar el código y aprender sobre el desarrollo ágil y adaptativo de software.</p>

<p>SOLID representa:</p>

<ul>
<li><a href="#single-responsibility-principle"><strong>S</strong>: (Single) Principio de responsabilidad única</a></li>
<li><a href="#open-closed-principle"><strong>O: (Open)</strong> Principio abierto-cerrado</a></li>
<li><a href="#liskov-substitution-principle"><strong>L: (Liskov)</strong> Principio de sustitución de Liskov</a></li>
<li><a href="#interface-segregation-principle"><strong>I: (Interface)</strong> Principio de segregación de interfaz</a></li>
<li><a href="#dependency-inversion-principle"><strong>D: (Dependency)</strong> Principio de inversión de dependencia</a></li>
</ul>

<p>En este artículo, se le presentará cada principio por separado para comprender la forma en que SOLID puede ayudarlo a ser un mejor desarrollador.</p>

<h2 id="principio-de-responsabilidad-única">Principio de responsabilidad única</h2>

<p>El principio de responsabilidad única (SRP) establece:</p>

<blockquote>
<p>Una clase debe tener una y una sola razón para cambiar, lo que significa que una clase debe tener solo un trabajo.</p>
</blockquote>

<p>Por ejemplo, considere una aplicación que toma una colección de formas, entre círculos y cuadrados, y calcula la suma del área de todas las formas de la colección.</p>

<p>Primero, cree las clases de forma y haga que los constructores configuren los parámetros requeridos.</p>

<p>Para las cuadrados, deberá saber la <code>longitud</code> de un lado:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square
{
    public $length;

    public function construct($length)
    {
        $this-&gt;length = $length;
    }
}
</code></pre>
<p>Para los círculos, deberá saber el <code>radio</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle
{
    public $radius;

    public function construct($radius)
    {
        $this-&gt;radius = $radius;
    }
}
</code></pre>
<p>A continuación, cree la clase <code>AreaCalculator</code> y luego escriba la lógica para sumar las áreas de todas las formas proporcionadas. El área de un cuadrado se calcula por longitud al cuadrado. El área de un círculo se calcula mediante pi por el radio al cuadrado.</p>
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
<p>Para usar la clase <code>AreaCalculator</code>, deberá crear una instancia de la clase y pasar una matriz de formas para mostrar el resultado en la parte inferior de la página.</p>

<p>A continuación, se muestra un ejemplo con una colección de tres formas:</p>

<ul>
<li>un círculo con un radio de 2</li>
<li>un cuadrado con una longitud de 5</li>
<li>un segundo cuadrado con una longitud de 6</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-php">$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);

echo $areas-&gt;output();
</code></pre>
<p>El problema con el método de salida es que <code>AreaCalculator</code> maneja la lógica para generar los datos.</p>

<p>Considere un escenario en el que el resultado debe convertirse a otro formato como JSON.</p>

<p>La clase <code>AreaCalculator</code> manejaría toda la lógica. Esto violaría el principio de responsabilidad única. La clase <code>AreaCalculator</code> solo debe ocuparse de la suma de las áreas de las formas proporcionadas. No debería importar si el usuario desea JSON o HTML.</p>

<p>Para abordar esto, puede crear una clase <code>SumCalculatorOutputter</code> por separado y usarla para manejar la lógica que necesita para mostrar los datos al usuario:</p>
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
<p>La clase <code>SumCalculatorOutputter</code> funcionaría así:</p>
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
<p>Ahora, la clase <code>SumCalculatorOutputter</code> maneja cualquier lógica que necesite para enviar los datos al usuario.</p>

<p>Eso cumple con el principio de responsabilidad única.</p>

<h2 id="principio-abierto-cerrado">Principio abierto-cerrado</h2>

<p>Principio abierto-cerrado (S.R.P.) establece:</p>

<blockquote>
<p>Los objetos o entidades deben estar abiertos por extensión, pero cerrados por modificación.</p>
</blockquote>

<p>Esto significa que una clase debe ser ampliable sin modificar la clase en sí.</p>

<p>Volvamos a ver la clase <code>AreaCalculator</code> y enfoquémonos en el método <code>sum</code>:</p>
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
<p>Considere un escenario en el que el usuario desea la <code>sum</code> de formas adicionales como triángulos, pentágonos, hexágonos, etc. Tendría que editar constantemente este archivo y añadir más bloques <code>if</code>/<code>else</code>. Eso violaría el principio abierto-cerrado.</p>

<p>Una forma de mejorar el método <code>sum</code> es eliminar la lógica para calcular el área de cada forma fuera del método de la clase <code>AreaCalculator</code> y adjuntarlo a la clase de cada forma.</p>

<p>A continuación, se muestra <code>area</code> definido en <code>Square</code>:</p>
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
<p>Y aquí es el método <code>area</code> definido en <code>Circle</code>:</p>
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
<p>El método <code>sum</code> para <code>AreaCalculator</code> puede reescribirse así:</p>
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
<p>Ahora, puede crear otra clase de forma y pasarla al calcular la suma sin romper el código.</p>

<p>Sin embargo, ahora surge otro problema: ¿Cómo sabe que el objeto pasado a <code>AreaCalculator</code> es realmente una forma o si la forma tiene un método llamado <code>area</code>?</p>

<p>La codificación de una <a href="https://www.php.net/manual/en/language.oop5.interfaces.php">interface</a> es una parte integral de SOLID.</p>

<p>Cree un <code>ShapeInterface</code> que sea compatible con <code>area</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php"><span class="highlight">interface ShapeInterface</span>
<span class="highlight">{</span>
    <span class="highlight">public function area();</span>
<span class="highlight">}</span>
</code></pre>
<p>Modifique sus clases de forma para <code>implement</code> el <code>ShapeInterface</code>.</p>

<p>A continuación, se muestra la actualización a <code>Square</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>Y aquí está la actualización a <code>Circle</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>En el método <code>sum</code> para <code>AreaCalculator</code>, puede verificar si las formas proporcionadas son realmente instancias de <code>ShapeInterface</code>; de lo contrario, lanzamos una excepción:</p>
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
<p>Eso cumple con el principio abierto-cerrado.</p>

<h2 id="principio-de-sustitución-de-liskov">Principio de sustitución de Liskov</h2>

<p>El principio de sustitución de Liskov establece:</p>

<blockquote>
<p>Digamos que q(x) sea una propiedad demostrable sobre objetos de x, de tipo T. Entonces, q(y) debe ser demostrable para los objetos y, de tipo S, donde S es un subtipo de T.</p>
</blockquote>

<p>Esto significa que cada subclase o clase derivada debe ser sustituible por su clase base o clase principal.</p>

<p>A partir de la clase <code>AreaCalculator</code> mostrada como ejemplo, considere una nueva clase <code>VolumeCalculator</code> que extiende la clase <code>AreaCalculator</code>:</p>
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
<p>Recuerde que la clase <code>SumCalculatorOutputter</code> se asemeja a esto:</p>
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
<p>Si intenta ejecutar un ejemplo como este:</p>
<pre class="code-pre "><code class="code-highlight language-php">$areas = new AreaCalculator($shapes);
<span class="highlight">$volumes = new VolumeCalculator($solidShapes);</span>

$output = new SumCalculatorOutputter($areas);
<span class="highlight">$output2 = new SumCalculatorOutputter($volumes);</span>
</code></pre>
<p>Cuando invoca el método <code>HTML</code> en el objeto <code>$output2</code>, obtendrá un error <code>E_NOTICE</code> que le informará de conversión de matriz a cadena.</p>

<p>Para solucionar esto, en vez de devolver una matriz desde el método sum de la clase <code>VolumeCalculator</code>, devuelva <code>$summedData</code>:</p>
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
<p><code>$summedData</code> puede ser float, double o integer.</p>

<p>Eso cumple con el principio de sustitución de Liskov.</p>

<h2 id="principio-de-segregación-de-interfaz">Principio de segregación de interfaz</h2>

<p>El principio de segregación de interfaz establece:</p>

<blockquote>
<p>Un cliente nunca debe ser forzado a implementar una interfaz que no usan ni los clientes no deben ser forzados a depender de métodos que no usan.</p>
</blockquote>

<p>Siguiendo con el ejemplo anterior de <code>ShapeInterface</code>, tendrá que admitir las nuevas formas tridimensionales de <code>Cuboid</code> y <code>Spheroid</code>, y estas formas también tendrán que calcular el <code>volumen</code>.</p>

<p>Consideraremos lo que sucedería si modificara <code>ShapeInterface</code> para añadir otro contrato:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface ShapeInterface
{
    public function area();

    <span class="highlight">public function volume();</span>
}
</code></pre>
<p>Ahora, cualquier forma que cree debe implementar el método <code>volume</code>, pero sabemos que las cuadrados son formas planas y que no tienen volumen, por lo que esta interfaz forzaría a la clase <code>Square</code> a implementar un método que no usa.</p>

<p>Esto violaría el principio de segregación de interfaz. En su lugar, podría crear otra interfaz llamada <code>ThreeDimensionalShapeInterface</code> que tiene el contrato de <code>volume</code> y las formas tridimensionales pueden implementar esta interfaz:</p>
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
<p>Este es un enfoque mucho mejor, pero hay que tener cuidado cuando se trata de escribir estas interfaces En vez de usar un <code>ShapeInterface</code> o un <code>ThreeDimensionalShapeInterface</code>, puede crear otra interfaz, quizá <code>ManageShapeInterface</code> e implementarla en las formas planas y en las tridimensionales.</p>

<p>De esta manera, puede tener una sola API para administrar las formas:</p>
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
<p>Ahora en la clase <code>AreaCalculator</code>, puede sustituir la invocación al método <code>area</code> con <code>calculate</code> y también verificar si el objeto es una instancia de <code>ManageShapeInterface</code> y no de <code>ShapeInterface</code>.</p>

<p>Eso cumple con el principio de segregación de interfaz.</p>

<h2 id="principio-de-inversión-de-dependencia">Principio de inversión de dependencia</h2>

<p>El principio de inversión de dependencia establece:</p>

<blockquote>
<p>Las entidades deben depender de abstracciones, no de concreciones. Indica que el módulo de alto nivel no debe depender del módulo de bajo nivel, sino que deben depender de las abstracciones.</p>
</blockquote>

<p>Este principio permite el desacoplamiento.</p>

<p>A continuación, se muestra un ejemplo de un <code>PasswordReminder</code> que se conecta a una base de datos de MySQL:</p>
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
<p>Primero, <code>MySQLConnection</code> es el módulo de bajo nivel mientras que <code>PasswordReminder</code> es de alto nivel, pero según la definición de <strong>D</strong> en SOLID, que establece que <em>Depende de la abstracción y no de las concreciones</em>. Este fragmento de código anterior viola este principio, ya que se está forzando a la clasev<code>PasswordReminder</code> a depender de la clase <code>MySQLConnection</code>.</p>

<p>Si más adelante cambiara el motor de la base de datos, también tendría que editar la clase <code>PasswordReminder</code>, y esto violaría el <em>principio abierto-cerrado</em>.</p>

<p>A la clase <code>PasswordReminder</code> no le debe importar qué base de datos usa su aplicación. Para solucionar estos problemas, se puede codificar a una interfaz, ya que los módulos de alto nivel y bajo nivel deben depender de la abstracción:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface DBConnectionInterface
{
    public function connect();
}
</code></pre>
<p>La interfaz tiene un método connect y la clase <code>MySQLConnection</code> implementa esta interfaz. Además, en lugar de escribir directamente la clase <code>MySQLConnection</code> en el constructor del <code>PasswordReminder</code>, se indica la clase <code>DBConnectionInterface</code> y, sin importar el tipo de base de datos que utilice su aplicación, la clase <code>PasswordReminder</code> puede conectarse sin ningún problema a la base de datos y no se viola el principio abierto-cerrado.</p>
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
<p>Este código establece que los módulos de alto nivel y los de bajo nivel dependen de la abstracción.</p>

<h2 id="conclusión">Conclusión</h2>

<p>En este artículo, se le presentaron los cinco principios del código SOLID. Los proyectos que se adhieren a los principios SOLID pueden compartirse con los colaboradores, ampliarse, modificarse, probarse y refactorizarse con menos complicaciones.</p>

<p>Continúe aprendiendo leyendo sobre otras prácticas para el desarrollo <a href="https://en.wikipedia.org/wiki/Agile_software_development">ágil</a> y <a href="https://en.wikipedia.org/wiki/Adaptive_software_development">adaptativo de software</a>.</p>
