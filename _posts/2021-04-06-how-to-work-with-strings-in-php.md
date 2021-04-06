---
title : "How To Work with Strings in PHP"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-php
image: "https://sergio.afanou.com/assets/images/image-midres-7.jpg"
---

<p><em>The author selected <a href="https://www.brightfunds.org/organizations/open-sourcing-mental-illness-ltd">Open Sourcing Mental Illness Ltd</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>A string is a sequence of one or more characters that may consist of letters, numbers, or symbols. All written communication is made up of strings. As such, they are fundamental to any programming language.</p>

<p>In this article, you will learn how to create and view the output of strings, how to use escape sequences, how to concatenate strings, how to store strings in variables, and the rules of using quotes, apostrophes, and newlines within strings in PHP.</p>

<h2 id="single-and-double-quoted-strings">Single and Double-Quoted Strings</h2>

<p>You can create a string in PHP by enclosing a sequence of characters in either single or double quotes. PHP will actually interpret the following strings differently:</p>
<pre class="code-pre "><code>'This is a string in single quotes.'
</code></pre><pre class="code-pre "><code>"This is a string in double quotes."
</code></pre>
<p>Before output, double-quoted strings will evaluate and parse any variables or escape sequences within the string. Single-quoted strings will output each character exactly as specified. The exception for single-quoted strings is a single quote (and backslash when needed).</p>

<p>If you were to <code>echo</code> this string in PHP:</p>
<pre class="code-pre "><code>'Sammy says: "This string\'s in single quotes." It required a backslash (\) before the apostrophes (\\\'), but do not use (\") with the double quotes.'
</code></pre>
<p>It would return this output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy says: "This string's in single quotes." It required a backslash (\) before the apostrophes (\'), but do not use (\") with the double quotes.
</code></pre>
<p>If you don&rsquo;t include a backslash before the apostrophe in the single-quoted string, PHP will end the string at that point, which will cause an error. Since you&rsquo;re using single quotes to create our string, you can include double quotes within it to be part of the final string that PHP outputs. </p>

<p>If you want to render the <code>\'</code> sequence, you must use <strong>three</strong> backslashes (<code>\\\'</code>). First <code>\\</code> to render the backslash itself, and then <code>\'</code> to render the apostrophe. The sequence <code>\"</code> is rendered exactly as specified.</p>
<pre class="code-pre "><code>"Sammy says: \"This string's in double quotes.\" It requires a backslash (\) before the double quotes (\\\"), but you MUST NOT add a backslash before the apostrophe (\')."
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy says: "This string's in double quotes." It requires a backslash (\) before the double quotes (\"), but you MUST NOT add a backslash before the apostrophe (\').
</code></pre>
<p>As with the single-quoted string, if a backslash is not included before the double quotes in the double-quoted string, PHP will end the string at that point, which will cause an error. Since the double-quoted string is not ended with a single quote, you add the apostrophe directly to a double-quoted string. A double-quoted string will output <code>\'</code> with either a single or double backslash used with the apostrophe. </p>

<p>To output the <code>\"</code> sequence, you must use <strong>three</strong> backslashes. First <code>\\</code> to render the backslash itself, and then <code>\"</code> to render the double quote. The sequence <code>\'</code> is rendered exactly as specified.</p>

<p>The <code>\</code> is known as an escape character. Combined with a secondary character, it makes up an <em>escape sequence</em>. Now that you have an understanding of strings, let&rsquo;s review escape sequences.</p>

<h3 id="escape-sequences">Escape Sequences</h3>

<p>An <a href="https://www.php.net/manual/en/language.types.string.php#language.types.string.syntax.double">escape sequence</a> tells the program to stop the normal operating procedure and evaluate the following characters differently. </p>

<p>In PHP, an escape sequence starts with a backslash <code>\</code>. Escape sequences apply to double-quoted strings. A single-quoted string only uses the escape sequences for a single quote or a backslash. </p>

<p>Here are some common escape sequences for double-quoted strings:</p>

<ul>
<li><code>\"</code> for a double quote</li>
<li><code>\\</code> for a backslash</li>
<li><code>\$</code> to render a dollar sign instead of expanding the variable</li>
<li><code>\n</code> for a new line</li>
<li><code>\t</code> for a tab</li>
</ul>

<p>Here is an example of how you can use these sequences in a string:</p>
<pre class="code-pre "><code>"\"What type of \$ do sharks use?\"\n\tSand dollars!"
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>"What type of $ do sharks use?"
    Sand dollars!
</code></pre>
<p>Using escape sequences gives us the ability to build any string required while including these special characters.</p>

<h2 id="creating-and-viewing-the-output-of-strings">Creating and Viewing the Output of Strings</h2>

<p>The most important feature of double-quoted strings is the fact that variable names will be expanded, giving you the value of the variable. You can use a variable to stand in for a string or use a string directly. You output the string by calling the <code>echo</code> function:</p>
<pre class="code-pre "><code class="code-highlight language-php">$my_name = "Sammy";
echo 'Name is specified using the variable $my_name.';
echo "\n"; // escape sequence for newline character
echo "Hello, my name is $my_name. It's stored in the variable \$my_name.";
</code></pre>
<p>The <code>$my_name</code> variable is created on the first line. On the second line, the <code>echo</code> function is used to output a string in single quotes. Using the <code>$my_name</code> variable within this single-quoted string displays the characters exactly as they are written, so we will see the variable name instead of its value. </p>

<p>On the fourth line, we use the <code>echo</code> function again, but we are using double quotes this time. This time the variable is expanded to show the value in the first sentence. In the next sentence, there is a <code>\</code> before the <code>$</code> to explicitly tell the string to display a <code>$</code> character and not expand the variable.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Name is specified using the variable $my_name.
Hello, my name is Sammy. It's stored in the variable $my_name.
</code></pre>
<p><span class='note'><strong>Note:</strong> When string evaluation is not a concern, you may choose to use either single quotes or double quotes, but whichever you decide on, you should be consistent within a program. Single quotes may be <em>marginally faster</em>.<br></span></p>

<p>With an understanding of how to create and view the output of strings, let&rsquo;s move on to see how you can manipulate strings.</p>

<h2 id="string-concatenation">String Concatenation</h2>

<p><em>Concatenation</em> means joining strings together, end-to-end, to build a new string. In PHP, there are two main ways to concatenate a string.</p>

<p>The first is to include a string variable within a double-quoted string. This was shown in the previous step and in the following:</p>
<pre class="code-pre "><code class="code-highlight language-php">$answer = "Chews wisely.";
echo "What do sharks do when they have a big choice to make? $answer";
</code></pre>
<p>Running this code will combine the string and the <code>$answer</code> variable, which is set to <code>Chews wisely.</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>What do sharks do when they have a big choice to make? Chews wisely.
</code></pre>
<p>A second way to concatenate strings is to use the <code>.</code> operator.</p>

<p>Let’s combine the strings <code>"Sammy"</code> and <code>"Shark"</code> together with concatenation through an <code>echo</code> statement:</p>
<pre class="code-pre "><code class="code-highlight language-php">echo "Sammy" . "Shark";
</code></pre>
<p>This code uses the <code>.</code> operator to combine the <code>"Sammy"</code> string and the <code>"Shark"</code> string without a space in between.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>SammyShark
</code></pre>
<p>If you would like whitespace between the two strings, you must include the whitespace within a string, like after the word <code>Sammy</code>:</p>
<pre class="code-pre "><code class="code-highlight language-php">echo "Sammy " . "Shark";
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy Shark
</code></pre>
<p>You cannot use concatenation to combine a string with an integer:</p>
<pre class="code-pre "><code class="code-highlight language-php">echo "Sammy" . 27;
</code></pre>
<p>This will produce an error:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Parse error: syntax error, unexpected '.27' (T_DNUMBER), expecting ';' or ',' in php shell code on line 1
</code></pre>
<p>If you put <code>"27"</code> within quotes, it will evaluate as a string. </p>

<p>PHP is a <a href="https://en.wikipedia.org/wiki/Strong_and_weak_typing"><em>loosely typed</em> language</a>, which means that it will try to convert the data it is given based on the request. If you set a variable to <code>27</code>, when used in concatenation with a string, PHP will parse the variable as a string:</p>
<pre class="code-pre "><code class="code-highlight language-php">$my_int = 27;
echo "Sammy" . $my_int;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy27
</code></pre>
<p>You&rsquo;ve covered the two main ways to concatenate, or combine, strings. Sometimes you may want to replace, or add to, the string completely. Next, let&rsquo;s explore how PHP allows you to overwrite or update a string.</p>

<h2 id="updating-a-string">Updating a String</h2>

<p>Normal variables in PHP are <em>mutable</em>, which means they can be changed or overwritten. Let&rsquo;s explore what happens when you change the value for the <code>$my_name</code> variable:</p>
<pre class="code-pre "><code class="code-highlight language-php">$my_name = "Sammy";
echo $my_name . "\n";
$my_name = "Shark";
echo $my_name;
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy
Shark
</code></pre>
<p>First, the variable was set to <code>"Sammy"</code> and displayed using <code>echo</code>. Then it was set to <code>"Shark"</code>, overwriting the variable, so that when <code>echo</code> was called a second time, it displayed the new value of <code>"Shark"</code>.</p>

<p>Instead of overwriting the variable, you can use the concatenating assignment operator <code>.=</code> to append to the end of a string:</p>
<pre class="code-pre "><code class="code-highlight language-php">$my_name = "Sammy";
$my_name .= " Shark";
echo $my_name;
</code></pre>
<p>First, you set the <code>$my_name</code> variable to <code>"Sammy"</code>, then used the <code>.=</code> operator to add <code>" Shark"</code> to the end of it. The new value for <code>$my_name</code> is <code>Sammy Shark</code>.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy Shark
</code></pre>
<p>To <em>prepend</em> to the beginning of a string, you would overwrite while using the original string:</p>
<pre class="code-pre "><code class="code-highlight language-php">$my_name = "Shark";
$my_name = "Sammy " . $my_name;
echo $my_name;
</code></pre>
<p>This time, you first set the <code>$my_name</code> variable to <code>"Shark"</code>, then used the <code>=</code> operator to override the <code>$my_name</code> variable with the new string <code>"Sammy "</code>, combined with the previous value of the <code>$my_name</code> variable, which before being overridden is <code>"Shark"</code>. The final value for <code>$my_name</code> is <code>Sammy Shark</code>.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Sammy Shark
</code></pre>
<p>Overwriting, appending, and prepending give us the ability to make changes and build the strings required for our applications.</p>

<h2 id="whitespace-in-strings">Whitespace in Strings</h2>

<p>Because PHP does not care about whitespace, you can put as many spaces or line breaks within your quotes as you would like.</p>
<pre class="code-pre "><code class="code-highlight language-php">echo "Sammy
The           (silly)
Shark";
</code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="TEXT Output">TEXT Output</div>Sammy
The           (silly)
Shark
</code></pre>
<p>Keep in mind that HTML renders whitespace differently. New lines require a <code>&lt;br&gt;</code> tag, so even though your source may have new lines, you will not see those new lines displayed on a web page. Similarly, no matter how many spaces there are in your code, only a single space is displayed between characters.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="HTML Output">HTML Output</div>Sammy The (silly) Shark
</code></pre>
<p>Clean and consistent use of whitespace is one of the best tools for making code more readable. Since PHP essentially ignores whitespace, you have a lot of flexibility that you can use to your advantage. An <a href="https://www.digitalocean.com/community/tutorials/what-is-an-integrated-development-environment">integrated development environment</a> (IDE) can help you stay consistent with your code and use of whitespace. </p>

<h2 id="conclusion">Conclusion</h2>

<p>Being able to control the way our strings are rendered is essential for communicating with an application&rsquo;s end user. By updating and combining variables that include special characters, you can clearly communicate while keeping repetition to a minimum. </p>

<p>As you continue to work with strings, keep in mind these three aspects: </p>

<ol>
<li>Pay special attention to quotes within your strings.</li>
<li>Use concatenation to combine your strings.</li>
<li>Use variables to make your strings reusable.</li>
</ol>

<p>If you would like to read more about PHP, check out the <a href="https://www.digitalocean.com/community/tags/php">PHP topic page</a>.</p>
