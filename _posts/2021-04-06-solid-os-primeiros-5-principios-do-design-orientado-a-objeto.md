---
title : "SOLID: os primeiros 5 princípios do design orientado a objeto"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/s-o-l-i-d-the-first-five-principles-of-object-oriented-design-pt
image: "https://sergio.afanou.com/assets/images/image-midres-12.jpg"
---

<h3 id="introdução">Introdução</h3>

<p><em>SOLID</em> é uma sigla para os primeiros cinco princípios do design orientado a objeto (OOD) criada por Robert C. Martin (também conhecido como <a href="http://en.wikipedia.org/wiki/Robert_Cecil_Martin">Uncle Bob</a>).</p>

<p><span class='note'><strong>Nota:</strong> embora esses princípios sejam aplicáveis a várias linguagens de programação, o código de amostra contido neste artigo usará o PHP.<br></span></p>

<p>Esses princípios estabelecem práticas que contribuem para o desenvolvimento de software com considerações de manutenção e extensão à medida que o projeto cresce. A adoção dessas práticas também pode contribuir para evitar problemas de código, refatoração de código e o desenvolvimento ágil e adaptativo de software.</p>

<p>SOLID significa:</p>

<ul>
<li><a href="#single-responsibility-principle"><strong>S</strong> - Single-responsibility Principle</a> (Princípio da responsabilidade única)</li>
<li><a href="#open-closed-principle"><strong>O</strong> - Open-closed Principle</a> (Princípio do aberto-fechado)</li>
<li><a href="#liskov-substitution-principle"><strong>L</strong> - Liskov Substitution Principle</a> (Princípio da substituição de Liskov)</li>
<li><a href="#interface-segregation-principle"><strong>I</strong> - Interface Segregation Principle</a> (Princípio da segregação de interfaces)</li>
<li><a href="#dependency-inversion-principle"><strong>D</strong> - Dependency Inversion Principle</a> (Princípio da inversão de dependência)</li>
</ul>

<p>Neste artigo, cada princípio será apresentado individualmente para que você compreenda como o SOLID pode ajudá-lo(a) a melhorar como desenvolvedor(a).</p>

<h2 id="princípio-da-responsabilidade-única">Princípio da responsabilidade única</h2>

<p>O Princípio da responsabilidade única (SRP) declara:</p>

<blockquote>
<p>Uma classe deve ter um e apenas um motivo para mudar, o que significa que uma classe deve ter apenas uma função.</p>
</blockquote>

<p>Por exemplo, considere um aplicativo que recebe uma coleção de formas — círculos e quadrados — e calcula a soma da área de todas as formas na coleção.</p>

<p>Primeiramente, crie as classes de formas e faça com que os construtores configurem os parâmetros necessários.</p>

<p>Para quadrados, será necessário saber o <code>length</code> (comprimento) de um lado:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square
{
    public $length;

    public function construct($length)
    {
        $this-&gt;length = $length;
    }
}
</code></pre>
<p>Para os círculos, será necessário saber o <code>radius</code> (raio):</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle
{
    public $radius;

    public function construct($radius)
    {
        $this-&gt;radius = $radius;
    }
}
</code></pre>
<p>Em seguida, crie a classe <code>AreaCalculator</code> e então escreva a lógica para somar as áreas de todas as formas fornecidas. A área de um quadrado é calculada pelo quadrado do comprimento. A área de um círculo é calculada por pi multiplicado pelo quadrado do raio.</p>
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
<p>Para usar a classe <code>AreaCalculator</code>, será necessário criar uma instância da classe, passar uma matriz de formas e exibir o resultado no final da página.</p>

<p>Aqui está um exemplo com uma coleção de três formas:</p>

<ul>
<li>um círculo com um raio de 2</li>
<li>um quadrado com um comprimento de 5</li>
<li>um segundo quadrado com um comprimento de 6</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-php">$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);

echo $areas-&gt;output();
</code></pre>
<p>O problema com o método de saída é que o <code>AreaCalculator</code> manuseia a lógica para gerar os dados.</p>

<p>Considere um cenário onde o resultado deve ser convertido em outro formato, como o JSON.</p>

<p>Toda a lógica seria manuseada pela classe <code>AreaCalculator</code>. Isso violaria o princípio da responsabilidade única. A classe <code>AreaCalculator</code> deve estar preocupada somente com a soma das áreas das formas fornecidas. Ela não deve se importar se o usuário quer JSON ou HTML.</p>

<p>Para resolver isso, crie uma classe separada chamada <code>SumCalculatorOutputter</code> e use essa nova classe para lidar com a lógica necessária para gerar os dados para o usuário:</p>
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
<p>A classe <code>SumCalculatorOutputter</code> funcionaria da seguinte forma:</p>
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
<p>Agora, a lógica necessária para gerar os dados para o usuário é manuseada pela classe <code>SumCalculatorOutputter</code>.</p>

<p>Isso satisfaz o princípio da responsabilidade única.</p>

<h2 id="princípio-do-aberto-fechado">Princípio do aberto-fechado</h2>

<p>O Princípio do aberto-fechado (S.R.P.) declara:</p>

<blockquote>
<p>Os objetos ou entidades devem estar abertos para extensão, mas fechados para modificação.</p>
</blockquote>

<p>Isso significa que uma classe deve ser extensível sem que seja modificada.</p>

<p>Vamos revisitar a classe <code>AreaCalculator</code> e focar no método <code>sum</code>(soma):</p>
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
<p>Considere um cenário onde o usuário deseja a <code>sum</code> de formas adicionais, como triângulos, pentágonos, hexágonos, etc. Seria necessário editar constantemente este arquivo e adicionar mais blocos de <code>if</code>/<code>else</code>. Isso violaria o princípio do aberto-fechado.</p>

<p>Uma maneira de tornar esse método <code>sum</code> melhor é remover a lógica para calcular a área de cada forma do método da classe <code>AreaCalculator</code> e anexá-la à classe de cada forma.</p>

<p>Aqui está o método <code>area</code> definido em <code>Square</code>:</p>
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
<p>E aqui está o método <code>area</code> definido em <code>Circle</code>:</p>
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
<p>O método <code>sum</code> para <code>AreaCalculator</code> pode então ser reescrito como:</p>
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
<p>Agora, é possível criar outra classe de formas e a passar ao calcular a soma sem quebrar o código.</p>

<p>No entanto, outro problema surge. Como saber que o objeto passado para o <code>AreaCalculator</code> é na verdade uma forma ou se a forma possui um método chamado <code>area</code>?</p>

<p>Programar em uma <a href="https://www.php.net/manual/en/language.oop5.interfaces.php">interface</a> é uma parte integral do SOLID.</p>

<p>Crie uma <code>ShapeInterface</code> que suporte <code>area</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php"><span class="highlight">interface ShapeInterface</span>
<span class="highlight">{</span>
    <span class="highlight">public function area();</span>
<span class="highlight">}</span>
</code></pre>
<p>Modifique suas classes de formas para <code>implement</code> (implementar) a <code>ShapeInterface</code>.</p>

<p>Aqui está a atualização para <code>Square</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Square <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>E aqui está a atualização para <code>Circle</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">class Circle <span class="highlight">implements ShapeInterface</span>
{
    // ...
}
</code></pre>
<p>No método <code>sum</code> para <code>AreaCalculator</code>, verifique se as formas fornecidas são na verdade instâncias de <code>ShapeInterface</code>; caso contrário, lance uma exceção:</p>
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
<p>Isso satisfaz o princípio do aberto-fechado.</p>

<h2 id="princípio-da-substituição-de-liskov">Princípio da substituição de Liskov</h2>

<p>O Princípio da substituição de Liskov declara:</p>

<blockquote>
<p>Seja q(x) uma propriedade demonstrável sobre objetos de x do tipo T. Então q(y) deve ser demonstrável para objetos y do tipo S onde S é um subtipo de T.</p>
</blockquote>

<p>Isso significa que cada subclasse ou classe derivada deve ser substituível pela classe sua classe base ou pai.</p>

<p>Analisando novamente a classe de exemplo <code>AreaCalculator</code>, considere uma nova classe <code>VolumeCalculator</code> que estende a classe <code>AreaCalculator</code>:</p>
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
<p>Lembre-se que a classe <code>SumCalculatorOutputter</code> se assemelha a isto:</p>
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
<p>Se você tentar executar um exemplo como este:</p>
<pre class="code-pre "><code class="code-highlight language-php">$areas = new AreaCalculator($shapes);
<span class="highlight">$volumes = new VolumeCalculator($solidShapes);</span>

$output = new SumCalculatorOutputter($areas);
<span class="highlight">$output2 = new SumCalculatorOutputter($volumes);</span>
</code></pre>
<p>Quando chamar o método <code>HTML</code> no objeto <code>$output2</code>, você irá obter um erro <code>E_NOTICE</code> informando uma conversão de matriz em string.</p>

<p>Para corrigir isso, em vez de retornar uma matriz do método de soma de classe <code>VolumeCalculator</code>, retorne <code>$summedData</code>:</p>
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
<p>O <code>$summedData</code> pode ser um float, duplo ou inteiro.</p>

<p>Isso satisfaz o princípio da substituição de Liskov.</p>

<h2 id="princípio-da-segregação-de-interfaces">Princípio da segregação de interfaces</h2>

<p>O Princípio da segregação de interfaces declara:</p>

<blockquote>
<p>Um cliente nunca deve ser forçado a implementar uma interface que ele não usa, ou os clientes não devem ser forçados a depender de métodos que não usam.</p>
</blockquote>

<p>Ainda utilizando o exemplo anterior do <code>ShapeInterface</code>, você precisará suportar as novas formas tridimensionais <code>Cuboid</code> e <code>Spheroid</code>, e essas formas também precisarão ter o <code>volume</code> calculado.</p>

<p>Vamos considerar o que aconteceria se você modificasse a <code>ShapeInterface</code> para adicionar outro contrato:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface ShapeInterface
{
    public function area();

    <span class="highlight">public function volume();</span>
}
</code></pre>
<p>Agora, qualquer forma criada deve implementar o método <code>volume</code>, mas você sabe que os quadrados são formas planas que não têm volume, de modo que essa interface forçaria a classe <code>Square</code> a implementar um método sem utilidade para ela.</p>

<p>Isso violaria o princípio da segregação de interfaces. Ao invés disso, você poderia criar outra interface chamada <code>ThreeDimensionalShapeInterface</code> que possui o contrato <code>volume</code> e as formas tridimensionais poderiam implementar essa interface:</p>
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
<p>Essa é uma abordagem muito mais vantajosa, mas uma armadilha a ser observada é quando sugerir o tipo dessas interfaces. Ao invés de usar uma <code>ShapeInterface</code> ou uma <code>ThreeDimensionalShapeInterface</code>, você pode criar outra interface, talvez <code>ManageShapeInterface</code>, e implementá-la tanto nas formas planas quanto tridimensionais.</p>

<p>Dessa forma, é possível ter uma única API para gerenciar todas as formas:</p>
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
<p>Agora, na classe <code>AreaCalculator</code>, substitua a chamada do método <code>area</code> por <code>calculate</code> e verifique se o objeto é uma instância da <code>ManageShapeInterface</code> e não da <code>ShapeInterface</code>.</p>

<p>Isso satisfaz o princípio da segregação de interfaces.</p>

<h2 id="princípio-da-inversão-de-dependência">Princípio da inversão de dependência</h2>

<p>O princípio da inversão de dependência declara:</p>

<blockquote>
<p>As entidades devem depender de abstrações, não de implementações. Ele declara que o módulo de alto nível não deve depender do módulo de baixo nível, mas devem depender de abstrações.</p>
</blockquote>

<p>Esse princípio permite a desestruturação.</p>

<p>Aqui está um exemplo de um <code>PasswordReminder</code> que se conecta a um banco de dados MySQL:</p>
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
<p>Primeiramente, o <code>MySQLConnection</code> é o módulo de baixo nível, enquanto o <code>PasswordReminder</code> é de alto nível. No entanto, de acordo com a definição de <strong>D</strong> em SOLID, que declara <em>Dependa de abstrações e não de implementações</em>, Esse trecho de código acima viola esse princípio, uma vez que a classe <code>PasswordReminder</code> está sendo forçada a depender da classe <code>MySQLConnection</code>.</p>

<p>Mais tarde, se você alterasse o mecanismo do banco de dados, também teria que editar a classe <code>PasswordReminder</code> e isso violaria o <em>princípio do aberto-fechado</em>.</p>

<p>A classe <code>PasswordReminder</code> não deve se importar com qual banco de dados seu aplicativo usa. Para resolver esses problemas, programe em uma interface, uma vez que os módulos de alto e baixo nível devem depender de abstrações:</p>
<pre class="code-pre "><code class="code-highlight language-php">interface DBConnectionInterface
{
    public function connect();
}
</code></pre>
<p>A interface possui um método de conexão e a classe <code>MySQLConnection</code> implementa essa interface. Além disso, em vez de sugerir o tipo diretamente da classe <code>MySQLConnection</code> no construtor do <code>PasswordReminder</code>, você sugere o tipo de <code>DBConnectionInterface</code>. Sendo assim, independentemente do tipo de banco de dados que seu aplicativo usa, a classe <code>PasswordReminder</code> poderá se conectar ao banco de dados sem problemas e o princípio do aberto-fechado não será violado.</p>
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
<p>Esse código estabelece que tanto os módulos de alto quanto de baixo nível dependem de abstrações.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Neste artigo, os cinco princípios do Código SOLID foram-lhe apresentados. Projetos que aderem aos princípios SOLID podem ser compartilhados com colaboradores, estendidos, modificados, testados e refatorados com menos complicações.</p>

<p>Continue seu aprendizado lendo sobre outras práticas para o <a href="https://en.wikipedia.org/wiki/Adaptive_software_development">desenvolvimento de software ágil</a> e <a href="https://en.wikipedia.org/wiki/Agile_software_development">adaptativo</a>.</p>
