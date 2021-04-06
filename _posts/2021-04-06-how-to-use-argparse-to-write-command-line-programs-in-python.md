---
title : "How To Use argparse to Write Command-Line Programs in Python"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-argparse-to-write-command-line-programs-in-python
image: "https://sergio.afanou.com/assets/images/image-midres-11.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>Python&rsquo;s <a href="https://docs.python.org/3/library/argparse.html#module-argparsel"><code>argparse</code> standard library module</a> is a tool that helps you write command-line interfaces (CLI) over your Python code. You may already be familiar with CLIs: programs like <code>git</code>, <code>ls</code>, <code>grep</code>, and <code>find</code> all expose command-line interfaces that allow you to call an underlying program with specific inputs and options. <code>argparse</code> allows you to call your own custom Python code with command-line arguments similar to how you might invoke <code>git</code>, <code>ls</code>, <code>grep</code>, or <code>find</code> using the command line. You might find this useful if you want to allow other developers to run your code from the command line.</p>

<p>In this tutorial, you’ll use some of the utilities exposed by Python&rsquo;s <code>argparse</code> standard library module. You&rsquo;ll write command-line interfaces that accept positional and optional arguments to control the underlying program&rsquo;s behavior. You&rsquo;ll also self-document a CLI by providing help text that can be displayed to other developers who are using your CLI.</p>

<p>For this tutorial, you&rsquo;ll write command-line interfaces for a program that keeps track of fish in a fictional aquarium.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To get the most out of this tutorial, we recommend you have:</p>

<ul>
<li>Some familiarity with programming in Python 3. You can review our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-python-3">How To Code in Python 3</a> tutorial series for background knowledge.</li>
</ul>

<h2 id="writing-a-command-line-program-that-accepts-a-positional-argument">Writing a Command-Line Program that Accepts a Positional Argument</h2>

<p>You can use the <code>argparse</code> module to write a command-line interface that accepts a positional argument. Positional arguments (as opposed to optional arguments, which we&rsquo;ll explore in a subsequent section), are generally used to specify required inputs to your program.</p>

<p>Let&rsquo;s consider an example CLI that prints the fish in an aquarium tank identified by a positional <code>tank</code> argument.</p>

<p>To create the CLI, open a file with your text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano aquarium.py
</li></ul></code></pre>
<p>Then, add the following Python code:</p>
<div class="code-label " title="aquarium.py">aquarium.py</div><pre class="code-pre "><code class="code-highlight language-python">import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str)
args = parser.parse_args()

fish = tank_to_fish.get(args.tank, "")
print(fish)
</code></pre>
<p>You can print out the fish in <code>tank_a</code> by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py tank_a
</li></ul></code></pre>
<p>After running that command, you will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>shark, tuna, herring
</code></pre>
<p>Similarly, if you ran <code>aquarium.py</code> to print out the fish in <code>tank_b</code> with:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py tank_b 
</li></ul></code></pre>
<p>You would receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>cod, flounder
</code></pre>
<p>Let&rsquo;s break down the code in <code>aquarium.py</code>.</p>

<p>First, you import the <code>argparse</code> module to make it available for use in your program. Next, you create a dictionary data structure <code>tank_to_fish</code> that maps tank names (like <code>tank_a</code> and <code>tank_b</code>) to string descriptions of fish held in those tanks.</p>

<p>You instantiate an instance of the <code>ArgumentParser</code> class and bind it to the <code>parser</code> variable. You can think of <code>parser</code> as the main point of entry for configuring your command-line interface. The <code>description</code> string provided to <code>parser</code> is—as you&rsquo;ll learn later—used in the automatically generated help text for the CLI exposed by <code>aquarium.py</code>.</p>

<p>Calling <code>add_argument</code> on parser allows you to add arguments accepted by your command-line interface. In this case, you add a single argument named <code>tank</code> that is a string type. Calling <code>parser.parse_args()</code> instructs <code>parser</code> to process and validate the command-line input passed to <code>aquarium.py</code> (for example, something like <code>tank_a</code>). Accessing the <code>args</code> returned by <code>parser.parse_args()</code> allows you to retrieve the value of the passed in <code>tank</code> argument, and use it to <code>print</code> out the fish in that tank.</p>

<p>At this point, you&rsquo;ve written a command-line interface and executed your program to print fish. Now you need to describe how your CLI works to other developers. <code>argparse</code> has strong support for help text to document your CLIs. You&rsquo;ll learn more about help text next.</p>

<h2 id="viewing-help-text">Viewing Help Text</h2>

<p>The <code>aquarium.py</code> file you just wrote in the previous section actually does more than print the fish in a specific tank. Since you&rsquo;re using <code>argparse</code>, the command-line interface exposed by <code>aquarium.py</code> will automatically include help and usage messages that a user can consult to learn more about your program.</p>

<p>Consider, for example, the usage message <code>aquarium.py</code> prints if you provide invalid arguments on the command line. Try invoking <code>aquarium.py</code> with the wrong arguments on the command line by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py --not-a-valid-argument
</li></ul></code></pre>
<p>If you run this command, you&rsquo;ll receive output like this:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>usage: aquarium.py [-h] tank
aquarium.py: error: the following arguments are required: tank
</code></pre>
<p>The output printed on the command line indicates that there was an error trying to run <code>aquarium.py</code>. The output indicates that the user needs to invoke <code>aquarium.py</code> with a <code>tank</code> argument. Something else you might notice is the <code>-h</code> in-between <code>[]</code> characters. This denotes that <code>-h</code> is an optional argument that you can provide as well.</p>

<p>Now you&rsquo;ll find out what happens when you call <code>aquarium.py</code> with the <code>-h</code> option. Try invoking <code>aquarium.py</code> with the <code>-h</code> argument on the command line by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py -h
</li></ul></code></pre>
<p>If you run this command, you&rsquo;ll receive output like this:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>usage: aquarium.py [-h] tank

List fish in aquarium.

positional arguments:
  tank

optional arguments:
  -h, --help  show this help message and exit
</code></pre>
<p>As you may have guessed, the <code>-h</code> option is short for, &ldquo;help.&rdquo; Running <code>python3 aquarium.py -h</code> (or, equivalently, the longer variant <code>python3 aquarium.py --help</code>) prints out the help text. The help text, effectively, is a longer version of the usage text that was outputted in the previous example when you supplied invalid arguments. Notably, the help text also includes the custom <code>description</code> string of <code>List fish in an aquarium</code> that you instantiated the <code>ArgumentParser</code> with earlier on in this tutorial.</p>

<p>By default, when you write a CLI using <code>argparse</code> you&rsquo;ll automatically get help and usage text that you can use to document your CLI for other developers.</p>

<p>So far, you&rsquo;ve written a CLI that accepts a required positional argument. In the next section you&rsquo;ll add an optional argument to your interface to expand on its capabilities.</p>

<h2 id="adding-an-optional-argument">Adding an Optional Argument</h2>

<p>Sometimes, it&rsquo;s helpful to include optional arguments in your command-line interface. These options are typically prefixed with two dash characters, for example <code>--some-option</code>. Let&rsquo;s rewrite <code>aquarium.py</code> with the following adjusted content that adds an <code>--upper-case</code> option to your CLI:</p>
<div class="code-label " title="aquarium.py">aquarium.py</div><pre class="code-pre "><code class="code-highlight language-python">import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str)
<span class="highlight">parser.add_argument("--upper-case", default=False, action="store_true")</span>
args = parser.parse_args()

fish = tank_to_fish.get(args.tank, "")

<span class="highlight">if args.upper_case:</span>
    <span class="highlight">fish = fish.upper()</span>

print(fish)
</code></pre>
<p>Try invoking <code>aquarium.py</code> with the new <code>--upper-case</code> argument by running the following:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py --upper-case tank_a
</li></ul></code></pre>
<p>If you run this command, you&rsquo;ll receive output like this:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SHARK, TUNA, HERRING
</code></pre>
<p>The fish in <code>tank_a</code> are now outputted in upper case. You accomplished this by adding a new <code>--upper-case</code> option when you called <code>parser.add_argument("--upper-case", default=False, action="store_true")</code>. The <code>"--upper-case"</code> string is the name of the argument you&rsquo;d like to add. </p>

<p>If the <code>--upper-case</code> option isn&rsquo;t provided by the user of the CLI, <code>default=False</code> ensures that its value is set to <code>False</code> by default. <code>action="store_true"</code> controls what happens when the <code>--upper-case</code> option is provided by the CLI user. There are a number of <a href="https://docs.python.org/3/library/argparse.html#action">different possible strings supported by the <code>action</code> parameter</a>, but <code>"store_true"</code> stores the value <code>True</code> into the argument, if it is provided on the command line.</p>

<p>Note that, although the argument is two words separated by a dash (<code>upper-case</code>), <code>argparse</code> makes it available to your code as <code>args.upper_case</code> (with an underscore separator) after you call <code>parser.parse_args()</code>. In general, <code>argparse</code> <a href="https://docs.python.org/dev/library/argparse.html#dest">converts any dashes in the provided arguments into underscores</a> so that you have valid Python identifiers to reference after you call <code>parse_args()</code>.</p>

<p>As before, <code>argparse</code> automatically creates a <code>--help</code> option and documents your command-line interface (including the <code>--upper-case</code> option you just added).</p>

<p>Try invoking <code>aquarium.py</code> with the <code>--help</code> option again to receive the updated help text:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py --help
</li></ul></code></pre>
<p>Your output will be similar to:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank

optional arguments:
  -h, --help    show this help message and exit
  --upper-case
</code></pre>
<p><code>argparse</code> automatically documented the positional <code>tank</code> argument, the optional <code>--upper-case</code> option, and the built-in <code>--help</code> option as well.</p>

<p>This help text is useful, but you can improve it with additional information to help users better understand how they can invoke your program. You&rsquo;ll explore how to enhance the help text in the next section.</p>

<h2 id="exposing-additional-help-text-to-your-users">Exposing Additional Help Text to Your Users</h2>

<p>Developers use the help text provided by your command-line interfaces to understand what your program is capable of and how they should use it. Let&rsquo;s revise <code>aquarium.py</code> again so it includes better help text. You can specify argument-level information by specifying <code>help</code> strings in the <code>add_argument</code> calls:</p>
<div class="code-label " title="aquarium.py">aquarium.py</div><pre class="code-pre "><code class="code-highlight language-python">import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(description="List fish in aquarium.")
parser.add_argument("tank", type=str<span class="highlight">, help="Tank to print fish from."</span>)
parser.add_argument(
    "--upper-case",
    default=False,
    action="store_true",
    <span class="highlight">help="Upper case the outputted fish.",</span>
)
args = parser.parse_args()

<span class="highlight">fish = tank_to_fish[args.tank]</span>

if args.upper_case:
    fish = fish.upper()

print(fish)
</code></pre>
<p>Try invoking <code>aquarium.py</code> with the <code>--help</code> option again to receive the updated help text:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py --help
</li></ul></code></pre>
<p>Your output will be the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank          Tank to print fish from.

optional arguments:
  -h, --help    show this help message and exit
  --upper-case  Upper case the outputted fish.
</code></pre>
<p>In this latest output, notice that the <code>tank</code> positional argument and the <code>--upper-case</code> optional argument both include custom help text. You provided this extra help text by supplying strings to the <code>help</code> part of <code>add_argument</code>. (For example, <code>parser.add_argument("tank", type=str, help="Tank to print fish from.")</code>.) <code>argparse</code> takes these strings and renders them for you in the help text output.</p>

<p>You can improve your help text further by having <code>argparse</code> print out any default values you have defined.</p>

<h2 id="displaying-default-values-in-help-text">Displaying Default Values in Help Text</h2>

<p>If you use a custom <code>formatter_class</code> when you instantiate your <code>ArgumentParser</code> instance, <code>argparse</code> will include default values in the help text output. Try adding <code>argparse.ArgumentDefaultsHelpFormatter</code> as your <code>ArgumentParser</code> formatter class:</p>
<div class="code-label " title="aquarium.py">aquarium.py</div><pre class="code-pre "><code class="code-highlight language-python">import argparse

tank_to_fish = {
    "tank_a": "shark, tuna, herring",
    "tank_b": "cod, flounder",
}

parser = argparse.ArgumentParser(
    description="List fish in aquarium.",
    <span class="highlight">formatter_class=argparse.ArgumentDefaultsHelpFormatter,</span>
)
parser.add_argument("tank", type=str, help="Tank to print fish from.")
parser.add_argument(
    "--upper-case",
    default=False,
    action="store_true",
    help="Upper case the outputted fish.",
)
args = parser.parse_args()

fish = tank_to_fish[args.tank]

if args.upper_case:
    fish = fish.upper()

print(fish)
</code></pre>
<p>Now, try invoking <code>aquarium.py</code> with the <code>--help</code> option again to check the updated help text:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 aquarium.py --help
</li></ul></code></pre>
<p>After running this command, you&rsquo;ll receive output like this:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>usage: aquarium.py [-h] [--upper-case] tank

List fish in aquarium.

positional arguments:
  tank          Tank to print fish from.

optional arguments:
  -h, --help    show this help message and exit
  --upper-case  Upper case the outputted fish. (default: False)
</code></pre>
<p>In this latest output, notice that the documentation for <code>--upper-case</code> ends with an indication of the default value for the <code>--upper-case</code> option (<code>default: False</code>). By including <code>argparse.ArgumentDefaultsHelpFormatter</code> as the <a href="https://docs.python.org/3/library/argparse.html#formatter-class"><code>formatter_class</code></a> of your <code>ArgumentParser</code>, <code>argparse</code> automatically started rendering default value information in its help text.</p>

<h2 id="conclusion">Conclusion</h2>

<p>The <code>argparse</code> module is a powerful part of the Python standard library that allows you to write command-line interfaces for your code. This tutorial introduced you to the foundations of <code>argparse</code>: you wrote a command-line interface that accepted positional and optional arguments, and exposed help text to the user.</p>

<p><code>argparse</code> supports many more feautures that you can use to write command-line programs with sophisticated sets of inputs and validations. From here, you can use <a href="https://docs.python.org/3/library/argparse.html#module-argparse">the <code>argparse</code> module&rsquo;s documentation</a> to learn more about other available classes and utilities.</p>
