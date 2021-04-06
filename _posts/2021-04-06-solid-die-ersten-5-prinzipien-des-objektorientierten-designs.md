---
title : "SOLID: Die ersten 5 Prinzipien des objektorientierten Designs"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/s-o-l-i-d-the-first-five-principles-of-object-oriented-design-de
image: "https://sergio.afanou.com/assets/images/image-midres-32.jpg"
---

<h3 id="einführung">Einführung</h3>

<p><em>SOLID</em> ist ein Akronym für die ersten fünf Prinzipien des objektorientierten Designs (OOD) von Robert C. Martin (auch bekannt als <a href="http://en.wikipedia.org/wiki/Robert_Cecil_Martin">Onkel Bob</a>).</p>

<p><span class='note'><strong>Anmerkung:</strong> Obwohl diese Prinzipien auf verschiedene Programmiersprachen angewendet werden können, wird der in diesem Artikel enthaltene Beispielcode PHP verwendet.<br></span></p>

<p>Diese Prinzipien legen Praktiken fest, die sich für die Entwicklung von Software mit Überlegungen zur Aufrechterhaltung und Erweiterung eignen, wenn das Projekt wächst. Die Übernahme dieser Praktiken kann auch zur Vermeidung von Code Smells, Refactoring von Code und agiler oder adaptiver Softwareentwicklung beitragen.</p>

<p>SOLID steht für:</p>

<ul>
<li><a href="#single-responsibility-principle"><strong>S</strong> – Single-Responsibility-Prinzip (Prinzip der eindeutigen Verantwortlichkeit)</a></li>
<li><a href="#open-closed-principle"><strong>O</strong> – Open-Closed-Prinzip (Prinzip der Offen- und Verschlossenheit)</a></li>
<li><a href="#liskov-substitution-principle"><strong>L</strong> – Liskovsches Substitutionsprinzip</a></li>
<li><a href="#interface-segregation-principle"><strong>I</strong> – Interface-Segregation-Prinzip (Prinzip der Schnittstellentrennung)</a></li>
<li><a href="#dependency-inversion-principle"><strong>D</strong> – Dependency-Inversion-Prinzip (Abhängigkeit-Umkehr-Prinzip)</a></li>
</ul>

<p>In diesem Artikel werden Sie jedes Prinzip einzeln kennenlernen, um zu verstehen, wie SOLID Ihnen dabei helfen kann, ein besserer Entwickler zu werden.</p>

<h2 id="single-responsibility-prinzip">Single-Responsibility-Prinzip</h2>

<p>Das Single-Responsibility-Prinzip (SRP) besagt:</p>

<blockquote>
<p>Eine Klasse sollte einen und nur einen Grund haben, sich zu ändern, d. h. eine Klasse sollte nur eine Aufgabe haben.</p>
</blockquote>

<p>Betrachten Sie beispielsweise eine Anwendung, die eine Sammlung von Formen – Kreise und Quadrate – nimmt und die Summe der Fläche aller Formen in der Sammlung berechnet.</p>

<p>Erstellen Sie zunächst die Formklassen und lassen Sie die Konstruktoren die erforderlichen Parameter einrichten.</p>

<p>Für Quadrate müssen Sie die <code>length</code> einer Seite kennen:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square
{
    public $length;

    public function construct($length)
    {
        $this-&gt;length = $length;
    }
}
</code></pre>
<p>Für Kreise müssen Sie den <code>radius</code> kennen:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle
{
    public $radius;

    public function construct($radius)
    {
        $this-&gt;radius = $radius;
    }
}
</code></pre>
<p>Erstellen Sie anschließend die Klasse <code>AreaCalculator</code> und schreiben Sie dann die Logik, um die Fläche aller bereitgestellten Formen zu summieren. Der Flächeninhalt eines Quadrats wird durch die Länge zum Quadrat berechnet. Der Flächeninhalt eines Kreises wird durch Pi mal Radius zum Quadrat berechnet.</p>
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
<p>Um die Klasse <code>AreaCalculator</code> zu verwenden, müssen Sie die Klasse instanziieren und ein Array von Formen übergeben und die Ausgabe am Ende der Seite anzeigen.</p>

<p>Hier ist ein Beispiel mit einer Sammlung von drei Formen:</p>

<ul>
<li>Ein Kreis mit einem Radius von 2</li>
<li>Ein Quadrat mit einer Länge von 5</li>
<li>Ein zweites Quadrat mit einer Länge von 6</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-php">$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);

echo $areas-&gt;output();
</code></pre>
<p>Das Problem mit der Ausgabemethode ist, dass der <code>AreaCalculator</code> die Logik zur Ausgabe der Daten bearbeitet.</p>

<p>Bedenken Sie ein Szenario, in dem die Ausgabe in ein anderes Format wie JSON konvertiert werden soll.</p>

<p>Die gesamte Logik würde von der Klasse <code>AreaCalculator</code> bearbeitet werden. Dies würde gegen das Single-Responsibility-Prinzip verstoßen. Die Klasse <code>AreaCalculator</code> sollte nur mit der Summe der Flächen der bereitgestellten Formen befasst sein. Sie sollte sich nicht damit befassen, ob der Benutzer JSON oder HTML wünscht.</p>

<p>Um dies zu beheben, können Sie eine separate Klasse <code>SumCalculatorOutputter</code> erstellen und diese neue Klasse verwenden, um die Logik zu bearbeiten, die Sie für die Ausgabe der Daten an den Benutzer benötigen:</p>
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
<p>Die Klasse <code>SumCalculatorOutputter</code> würde wie folgt funktionieren:</p>
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
<p>Jetzt wird die Logik, die Sie zur Ausgabe der Daten an den Benutzer benötigen, von der Klasse <code>SumCalculatorOutputter</code> bearbeitet.</p>

<p>Das erfüllt das Single-Responsibility-Prinzip.</p>

<h2 id="open-closed-prinzip">Open-Closed-Prinzip</h2>

<p>Das Open-Closed-Prinzip (S.R.P.) besagt:</p>

<blockquote>
<p>Objekte oder Entitäten sollten offen für Erweiterungen, aber geschlossen für Änderungen sein.</p>
</blockquote>

<p>Das bedeutet, dass eine Klasse erweiterbar sein sollte, ohne die Klasse selbst zu modifizieren.</p>

<p>Gehen wir noch einmal auf die Klasse <code>AreaCalculator</code> ein und konzentrieren uns auf die Methode <code>sum</code>:</p>
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
<p>Bedenken Sie ein Szenario, in dem der Benutzer die Summe <code>sum</code> zusätzlicher Formen wie Dreiecke, Fünfecke, Sechsecke usw. wünscht. Sie müssten diese Datei ständig bearbeiten und weitere <code>if</code>/<code>else</code>-Blöcke hinzufügen. Das würde das Open-Closed-Prinzip verletzen.</p>

<p>Eine Möglichkeit, diese Methode <code>sum</code> zu verbessern, besteht darin, die Logik zur Berechnung der Fläche jeder Form aus der Klassenmethode <code>AreaCalculator</code> zu entfernen und sie an die Klasse jeder Form anzuhängen.</p>

<p>Hier ist die in <code>Square</code> definierte Methode <code>area</code>:</p>
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
<p>Und hier ist die in <code>Circle</code> definierte Methode <code>area</code>:</p>
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
<p>Die Methode <code>sum</code> für <code>AreaCalculator</code> kann dann umgeschrieben werden als:</p>
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
<p>Jetzt können Sie eine andere Formklasse erstellen und diese bei der Berechnung der Summe übergeben, ohne den Code zu verändern.</p>

<p>Es ergibt sich jedoch ein weiteres Problem. Woher wissen Sie, dass das an den <code>AreaCalculator</code> übergebene Objekt tatsächlich eine Form ist oder ob die Form eine Methode namens <code>area</code> aufweist?</p>

<p>Die Codierung auf eine <a href="https://www.php.net/manual/en/language.oop5.interfaces.php">Schnittstelle</a> ist ein integraler Bestandteil von SOLID.</p>

<p>Erstellen Sie ein <code>ShapeInterface</code>, das <code>area</code> unterstützt:</p>
<pre class="code-pre "><code class="code-highlight language-php"><span class="highlight">interface ShapeInterface</span>
<span class="highlight">{</span>
    <span class="highlight">public function area();</span>
<span class="highlight">}</span>
</code></pre>
<p>Ändern Sie Ihre Formklassen, um das <code>ShapeInterface</code> mit <code>implement</code> zu implementieren.</p>

<p>Hier ist die Aktualisierung für <code>Square</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>Und hier ist die Aktualisierung für <code>Circle</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>In der Methode <code>sum</code> für <code>AreaCalculator</code> können Sie überprüfen, ob die bereitgestellten Formen tatsächlich Instanzen des <code>ShapeInterface</code> sind; andernfalls verwenden Sie „throw“ für eine Ausnahme:</p>
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
<p>Damit ist das Open-Closed-Prinzip erfüllt.</p>

<h2 id="liskovsches-substitutionsprinzip">Liskovsches Substitutionsprinzip</h2>

<p>Das Liskovsche Substitutionsprinzip besagt:</p>

<blockquote>
<p>Lassen Sie q(x) eine Eigenschaft sein, die für Objekte x von Typ T beweisbar ist. Dann soll q(y) für Objekte y von Typ S beweisbar sein, wobei S ein Untertyp von T ist.</p>
</blockquote>

<p>Das bedeutet, dass jede Unterklasse oder abgeleitete Klasse für ihre Basis- oder übergeordnete Klasse ersetzbar sein sollte.</p>

<p>Bedenken Sie, aufbauend auf dem Beispiel der Klasse <code>AreaCalculator</code>, eine neue Klasse <code>VolumeCalculator</code>, die die Klasse <code>AreaCalculator</code> erweitert:</p>
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
<p>Erinnern Sie sich daran, dass die Klasse <code>SumCalculatorOutputter</code> dem ähnelt:</p>
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
<p>Wenn Sie versuchen würden, ein Beispiel wie dieses auszuführen:</p>
<pre class="code-pre "><code class="code-highlight language-php">$areas = new AreaCalculator($shapes);
<span class="highlight">$volumes = new VolumeCalculator($solidShapes);</span>

$output = new SumCalculatorOutputter($areas);
<span class="highlight">$output2 = new SumCalculatorOutputter($volumes);</span>
</code></pre>
<p>Wenn Sie die Methode <code>HTML</code> auf dem Objekt <code>$output2</code> aufrufen, erhalten Sie einen Fehler <code>E_NOTICE</code>, der Sie über eine Array-zu-String-Konvertierung informiert.</p>

<p>Um dies zu beheben, geben Sie anstelle der Rückgabe eines Arrays aus der Summenmethode der Klasse <code>VolumeCalculator</code> <code>$summedData</code> zurück:</p>
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
<p>Das <code>$summedData</code> können ein Float, Double oder Integer sein.</p>

<p>Damit ist das Liskovsche Substitutionsprinzip erfüllt.</p>

<h2 id="das-interface-segregation-prinzip">Das Interface-Segregation-Prinzip</h2>

<p>Das Interface-Segregation-Prinzip besagt:</p>

<blockquote>
<p>Ein Client sollte nie gezwungen werden, eine Schnittstelle zu implementieren, die er nicht verwendet, oder Clients sollten nicht gezwungen werden, von Methoden abzuhängen, die sie nicht verwenden.</p>
</blockquote>

<p>Weiterhin aufbauend auf dem vorherigen Beispiel <code>ShapeInterface</code>, müssen Sie die neuen dreidimensionalen Formen <code>Cuboid</code> und <code>Spheroid</code> unterstützen, und diese Formen müssen auch das <code>Volumen</code> berechnen.</p>

<p>Bedenken wir, was passieren würde, wenn Sie das <code>ShapeInterface</code> modifizieren würden, um einen weiteren Vertrag hinzuzufügen:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface ShapeInterface
{
    public function area();

    <span class="highlight">public function volume();</span>
}
</code></pre>
<p>Nun muss jede Form, die Sie erstellen, die Methode <code>volume</code> implementieren, aber Sie wissen, dass Quadrate flache Formen sind und kein Volumen haben, also würde diese Schnittstelle die Klasse <code>Square</code> zwingen, eine Methode zu implementieren, die sie nicht braucht.</p>

<p>Dies würde das Interface-Segregation-Prinzip verletzen. Stattdessen könnten Sie eine andere Schnittstelle namens <code>ThreeDimensionalShapeInterface</code> erstellen, die den Vertrag <code>volume</code> hat und dreidimensionale Formen können diese Schnittstelle implementieren:</p>
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
<p>Dies ist ein wesentlich besserer Ansatz, aber ein Fallstrick, auf den Sie achten müssen, wenn Sie diese Schnittstellen mit Typ-Hinweisen versehen. Anstatt ein <code>ShapeInterface</code> oder ein <code>ThreeDimensionalShapeInterface</code> zu verwenden, können Sie eine andere Schnittstelle erstellen, vielleicht <code>ManageShapeInterface</code>, und diese sowohl für die flachen als auch für die dreidimensionalen Formen implementieren.</p>

<p>Auf diese Weise können Sie eine einzige API für die Verwaltung der Formen haben:</p>
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
<p>In der Klasse <code>AreaCalculator</code> können Sie den Aufruf für die Methode <code>area</code> durch <code>calculate</code> ersetzen und außerdem überprüfen, ob das Objekt eine Instanz des <code>ManageShapeInterface</code> und nicht des <code>ShapeInterface</code> ist.</p>

<p>Das erfüllt das Interface-Segregation-Prinzip.</p>

<h2 id="das-dependency-inversion-prinzip">Das Dependency-Inversion-Prinzip</h2>

<p>Das Dependency-Inversion-Prinzip besagt:</p>

<blockquote>
<p>Entitäten müssen von Abstraktionen abhängen, nicht von Konkretionen. Es besagt, dass das Modul auf hoher Ebene nicht vom Modul auf niedriger Ebene abhängen darf, sondern diese von Abstraktionen abhängen sollten.</p>
</blockquote>

<p>Dieses Prinzip ermöglicht die Entkopplung.</p>

<p>Hier ist ein Beispiel für einen <code>PasswordReminder</code> der sich mit einer MySQL-Datenbank verbindet:</p>
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
<p>Zuerst ist die <code>MySQLConnection</code> das Modul auf niedriger Ebene, während der <code>PasswordReminder</code> auf hoher Ebene angesiedelt ist, aber gemäß der Definition von <strong>D</strong> in SOLID, die besagt, <em>von der Abstraktion abzuhängen und nicht von Konkretionen</em>. Dieses obige Snippet verletzt dieses Prinzip, da die Klasse <code>PasswordReminder</code> gezwungen wird, von der Klasse <code>MySQLConnection</code> abzuhängen.</p>

<p>Wenn Sie später die Datenbank-Engine ändern würden, müssten Sie auch die Klasse <code>PasswordReminder</code> bearbeiten, und das würde das <em>Open-Close-Prinzip</em> verletzen.</p>

<p>Die Klasse <code>PasswordReminder</code> sollte sich nicht darum kümmern, welche Datenbank Ihre Anwendung verwendet. Um diese Probleme zu beheben, können Sie an eine Schnittstelle kodieren, da Module auf hoher Ebene und niedriger Ebene von der Abstraktion abhängen sollten:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface DBConnectionInterface
{
    public function connect();
}
</code></pre>
<p>Die Schnittstelle hat eine Verbindungsmethode und die Klasse <code>MySQLConnection</code> implementiert diese Schnittstelle. Anstatt die Klasse <code>MySQLConnection</code> im Konstruktor von <code>PasswordReminder</code>, direkt zu typisieren, geben Sie stattdessen das <code>DBConnectionInterface</code> an, und unabhängig davon, welchen Datenbanktyp Ihre Anwendung verwendet, kann die Klasse <code>PasswordReminder</code> ohne Probleme eine Verbindung zur Datenbank herstellen und das Open-Close-Prinzip wird nicht verletzt.</p>
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
<p>Dieser Code verdeutlicht, dass sowohl die Module auf hoher Ebene als auch auf niedriger Ebene von der Abstraktion abhängen.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>In diesem Artikel wurden Ihnen die fünf Prinzipien von SOLID Code vorgestellt. Projekte, die sich an die SOLID-Prinzipien halten, können mit weniger Komplikationen mit anderen Mitarbeitern geteilt, erweitert, modifiziert, getestet und refraktorisiert werden.</p>

<p>Lernen Sie weiter, indem Sie über andere Praktiken für die <a href="https://en.wikipedia.org/wiki/Agile_software_development">Agile</a> und <a href="https://en.wikipedia.org/wiki/Adaptive_software_development">Adaptive Softwareentwicklung</a> lesen.</p>
