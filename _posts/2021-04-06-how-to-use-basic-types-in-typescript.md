---
title : "How To Use Basic Types in TypeScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript
image: "https://sergio.afanou.com/assets/images/image-midres-13.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://www.typescriptlang.org/">TypeScript</a> is an extension of the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">JavaScript</a> language that uses JavaScript’s runtime with a compile-time type checker. This combination allows developers to use the full JavaScript ecosystem and language features, while also adding optional static type-checking, enum data types, classes, and interfaces. These features provide the developer with the flexibility of JavaScript&rsquo;s dynamic nature, but also allow for a more reliable codebase, where type information can be used at compile-time to detect possible issues that could cause bugs or other unexpected behavior at runtime.</p>

<p>The extra type information also provides better documentation of codebases and improved <a href="https://en.wikipedia.org/wiki/Intelligent_code_completion">IntelliSense</a> (code completion, parameters info, and similar content assist features) in text editors. Teammates can identify exactly what types are expected for any variable or function parameter, without having to go through the implementation itself.</p>

<p>This tutorial will go through type declaration and all the basic types used in TypeScript. It will lead you through examples with different code samples, which you can follow along with in your own TypeScript environment or the <a href="https://www.typescriptlang.org/play?ts=4.2.2#">TypeScript Playground</a>, an online environment that allows you to write TypeScript directly in the browser.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To follow this tutorial, you will need:</p>

<ul>
<li>An environment in which you can execute TypeScript programs to follow along with the examples. To set this up on your local machine, you will need the following.

<ul>
<li>Both <a href="https://nodejs.org/en/about/">Node</a> and <a href="https://docs.npmjs.com/about-npm">npm</a> (or <a href="https://yarnpkg.com/getting-started">yarn</a>) installed in order to run a development environment that handles TypeScript-related packages. This tutorial was tested with Node.js version 14.3.0 and npm version 6.14.5. To install on macOS or Ubuntu 18.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How to Install Node.js and Create a Local Development Environment on macOS</a> or the <strong>Installing Using a PPA</strong> section of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04">How To Install Node.js on Ubuntu 18.04</a>. This also works if you are using the <a href="https://docs.microsoft.com/en-us/windows/wsl/install-win10">Windows Subsystem for Linux (WSL)</a>.</li>
<li>Additionally, you will need the TypeScript Compiler (<code>tsc</code>) installed on your machine. To do this, refer to the <a href="https://www.typescriptlang.org/download">official TypeScript website</a>.</li>
</ul></li>
<li>If you do not wish to create a TypeScript environment on your local machine, you can use the official <a href="https://www.typescriptlang.org/play">TypeScript Playground</a> to follow along.</li>
<li>You will need sufficient knowledge of JavaScript, especially ES6+ syntax, such as <a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript">destructuring, rest operators</a>, and <a href="https://www.digitalocean.com/community/tutorials/understanding-modules-and-import-and-export-statements-in-javascript">imports/exports</a>. If you need more information on these topics, reading our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript series</a> is recommended.</li>
<li>This tutorial will reference aspects of text editors that support TypeScript and show in-line errors. This is not necessary to use TypeScript, but does take more advantage of TypeScript features. To gain the benefit of these, you can use a text editor like <a href="https://code.visualstudio.com/">Visual Studio Code</a>, which has full support for TypeScript out of the box. You can also try out these benefits in the <a href="https://www.typescriptlang.org/play">TypeScript Playground</a>.</li>
</ul>

<p>All examples shown in this tutorial were created using TypeScript version 4.2.2.</p>

<h2 id="declaring-variable-types-in-typescript">Declaring Variable Types in TypeScript</h2>

<p>When writing code in JavaScript, which is a purely dynamic language, you can&rsquo;t specify the <a href="https://www.digitalocean.com/community/tutorials/understanding-data-types-in-javascript">data types</a> of <a href="https://www.digitalocean.com/community/tutorials/understanding-variables-scope-hoisting-in-javascript">variables</a>. You create the variables and assign them a value, but do not specify a type, as shown in the following:</p>
<pre class="code-pre "><code class="code-highlight language-js">const language = {
  name: "JavaScript"
};
</code></pre>
<p>In this code block, <code>language</code> is an <a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">object</a> that holds a string value for the property <code>name</code>. The value type for <code>language</code> and its properties is not explicitly set, and this could cause confusion later if future developers do not know what kind of value <code>language</code> references.</p>

<p>TypeScript has as a main benefit a strict type system. A <em>statically typed language</em> is one where the type of the variables is known at compilation time. In this section, you will try out the syntax used to specify variable types with TypeScript.</p>

<p>Types are extra information that you write directly in your code. The TypeScript compiler uses this extra information to enforce the correct use of the different values depending on their type.</p>

<p>Imagine working with a dynamic language, such as JavaScript, and using a <code>string</code> variable as if it were a <code>number</code>. When you do not have <code>strict</code> unit testing, the possible bug is only going to appear during runtime. If using the type system available with TypeScript, the compiler would not compile the code, giving an error instead, like this:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>The right-hand side of an arithmetic operation must be of type 'any', 'number', 'bigint' or an enum type. (2363)
</code></pre>
<p>To declare a variable with a certain type in TypeScript, use the following syntax:</p>
<pre class="code-pre "><code class="code-highlight language-ts"><span class="highlight">declarationKeyword</span> <span class="highlight">variableName</span>: <span class="highlight">Type</span>
</code></pre>
<p><code>declarationKeyword</code> would be something like <code>let</code>, <code>var</code>, or <code>const</code>. This would be followed by the variable name, a colon (<code>:</code>), and the type of that variable. </p>

<p>Any code you write in TypeScript is, in some way, already using the type system, even if you are not specifying any types. Take this code as an example:</p>
<pre class="code-pre "><code class="code-highlight language-ts">let language = 'TypeScript';
</code></pre>
<p>In TypeScript, this has the same meaning as the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">let language<span class="highlight">: string</span> = 'TypeScript';
</code></pre>
<p>In the first example, you did not set the type of the <code>language</code> variable to <code>string</code>, but TypeScript inferred the type because you assigned a string value when it was declared. In the second example, you are explicitly setting the type of the <code>language</code> variable to <code>string</code>.</p>

<p>If you used <code>const</code> instead of <code>let</code>, it would be the following: </p>
<pre class="code-pre "><code class="code-highlight language-ts">const language = 'TypeScript';
</code></pre>
<p>In this case, TypeScript would use the string literal <code>TypeScript</code> as the type of your variable, as if you typed it this way:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const language: 'TypeScript' = 'TypeScript';
</code></pre>
<p>TypeScript does this because, when using <a href="https://www.digitalocean.com/community/tutorials/understanding-variables-scope-hoisting-in-javascript#constants"><code>const</code></a>, you are not going to assign a new value to the variable after the declaration, <a href="https://www.digitalocean.com/community/tutorials/understanding-variables-scope-hoisting-in-javascript#difference-between-var,-let,-and-const">as doing this would raise an error</a>. </p>

<p><span class='note'><strong>Note:</strong> If you are using an editor that supports TypeScript, hovering over the variables with your cursor will display the type information of each variable.<br></span></p>

<p>If you explicitly set the type of a variable then use a different type as its value, the TypeScript Compiler (<code>tsc</code>) or your editor will show the error <code>2322</code>. Try running the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const myNumber: number = 'look! this is not a number :)';
</code></pre>
<p>This will yield the following error:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type 'string' is not assignable to type 'number'. (2322)
</code></pre>
<p>Now that you&rsquo;ve tried out setting the type of a variable in TypeScript, the next section will show all the basic types supported by TypeScript. </p>

<h2 id="basic-types-used-in-typescript">Basic Types Used in TypeScript</h2>

<p>TypeScript has multiple basic types that are used as building blocks when building more complex types. In the following sections, you are going to examine most of these types. Notice that most variables you are creating throughout this section could have their type omitted because TypeScript would be able to infer them, but you are being explicit about the types for learning purposes.</p>

<h3 id="string"><code>string</code></h3>

<p>The type <code>string</code> is used for textual data types, like string literals or <a href="https://www.digitalocean.com/community/tutorials/understanding-template-literals-in-javascript">template strings</a>.</p>

<p>Try out the following code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const language: string = 'TypeScript';
const message: string = `I'm programming in ${language}!`;
</code></pre>
<p>In this code block, both <code>language</code> and <code>message</code> are assigned the <code>string</code> type. The template literal is still a string, even though it is determined dynamically.</p>

<p>Since strings are common in JavaScript programming, this is probably one of the types you are going to use most.</p>

<h3 id="boolean"><code>boolean</code></h3>

<p>The type <code>boolean</code> is used to represent <code>true</code> or <code>false</code>.</p>

<p>Try out the code in the following block:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const hasErrors: boolean = true;
const isValid: boolean = false;
</code></pre>
<p>Since <code>hasErrors</code> and <code>isValid</code> were declared as booleans, they can only be assigned the values <code>true</code> and <code>false</code>. Note that <a href="https://developer.mozilla.org/en-US/docs/Glossary/Truthy">truthy</a> and <a href="https://developer.mozilla.org/en-US/docs/Glossary/Falsy">falsy</a> values are not converted into their boolean equivalents and will throw an error if used with these variables. </p>

<h3 id="number"><code>number</code></h3>

<p>The type <code>number</code> is used to represent <a href="https://www.digitalocean.com/community/tutorials/understanding-data-types-in-javascript#numbers">integers and floats</a>, as in the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const pi: number = 3.14159;
const year: number = 2021;
</code></pre>
<p>This is another common type that is used often in JavaScript development, so this declaration will be common in TypeScript.</p>

<h3 id="bigint"><code>bigint</code></h3>

<p>The type <code>bigint</code> is a type that can be used when targetting ES2020. It&rsquo;s used to represent <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt"><code>BigInt</code></a>, which is a new datatype to store integers bigger than <code>2^53</code>.</p>

<p>Try the following code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const bigNumber: bigint = 9007199254740993n;
</code></pre>
<p><span class='note'><strong>Note:</strong> If this code throws an error, it is possible that TypeScript is not set up to target <code>ES2020</code>. This can be changed in your <a href="https://www.typescriptlang.org/docs/handbook/tsconfig-json.html"><code>tsconfig.json</code> file</a>.<br></span></p>

<p>If you are working with numbers bigger than <code>2^53</code> or with some Math libraries, <code>bigint</code> will be a common type declaration.</p>

<h3 id="symbol"><code>symbol</code></h3>

<p>The <code>symbol</code> type is used to represent the <a href="https://developer.mozilla.org/en-US/docs/Glossary/Symbol"><code>Symbol</code></a> primitive value. This will create a unique, unnamed value.</p>

<p>Run the following code using the <code>Symbol()</code> constructor function:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const mySymbol: symbol = Symbol('unique-symbol-value');
</code></pre>
<p>The uniqueness of these values can be used to avoid reference collisions. For more on symbols in JavaScript, read the <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol">symbol article on Mozilla Developer Network (MDN)</a>.</p>

<h3 id="arrays">Arrays</h3>

<p>In TypeScript, <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">arrays</a> are typed based on the elements they are expected to have. There are two ways to type an array:</p>

<ul>
<li>Appending <code>[]</code> to the expected type of the array elements. For example, if you want to type an array that holds multiple <code>number</code> values, you could do it like this:</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-ts">const primeNumbers: number[] = [2, 3, 5, 7, 11];
</code></pre>
<p>If you assigned a string value to this array, TypeScript would give you an error.</p>

<ul>
<li>Using the <code>Array&lt;<span class="highlight">T</span>&gt;</code> Generic, where <code>T</code> is the expected type of the elements in that array. Using the previous example, it would become this:</li>
</ul>
<pre class="code-pre "><code class="code-highlight language-ts">const primeNumbers: Array&lt;number&gt; = [2, 3, 5, 7, 11];
</code></pre>
<p>Both ways are identical, so pick one and try using only that format to represent arrays. This will keep the codebase consistent, which is often more important than choosing one style over the other.</p>

<p>One important aspect of using variables that hold arrays in TypeScript is that most of the time you will have to type them. Try the following code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const myArray = [];
</code></pre>
<p>TypeScript is not able to infer the correct type expected by this array. Instead, it uses <code>any[]</code>, which means an array of anything. This is not type-safe, and could cause confusion later in your code.</p>

<p>To make your code more robust, it is recommended to be explicit about the types of the array. For example, this would make sure the array has number elements:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const myArray: number[] = [];
</code></pre>
<p>This way, if you try to push an invalid value to the array, TypeScript will yield an error. Try out the following code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const myArray: number[] = [];

myArray.push('some-text');
</code></pre>
<p>The TypeScript Compiler will show error <code>2345</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Argument of type 'string' is not assignable to parameter of type 'number'. (2345)
</code></pre>
<h3 id="tuples">Tuples</h3>

<p>Tuples are arrays with a specific number of elements. One common use-case for this is storing 2D coordinates in the format <code>[x, y]</code>. If you are working with <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-react-js">React</a> and using <a href="https://www.digitalocean.com/community/tutorials/how-to-manage-state-with-hooks-on-react-components">Hooks</a>, the result from most Hooks is also a tuple, like <code>const [isValid, setIsValid] = React.useState(false)</code>.</p>

<p>To type a tuple, as opposed to when typing an array, you wrap the type of the elements inside a <code>[]</code>, separating them with commas. Imagine you are creating a literal array with the types of the elements:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const position: [number, number] = [1, 2];
</code></pre>
<p>If you try to pass less, or more, than the number of elements that the tuple expects, the TypeScript Compiler is going to show the error <code>2322</code>.</p>

<p>Take the following code, for example:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const position: [number, number] = [1, 2, 3];
</code></pre>
<p>This would yield the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type '[number, number, number]' is not assignable to type '[number, number]'.
Source has 3 element(s) but target allows only 2. (2322)
</code></pre>
<h3 id="any"><code>any</code></h3>

<p>In certain situations it may be too hard to specify the types of a value, such as if that value is coming from a third-party library or from code that was initially written without TypeScript. This can be especially common when migrating a JavaScript codebase to TypeScript in small steps. In these scenarios, it is possible to use a special type called <code>any</code>, which means any type. Using <code>any</code> means opting-out of type checking, and is the same as making the TypeScript Compiler ignore that value.</p>

<p>Take the following code block:</p>
<pre class="code-pre "><code class="code-highlight language-ts">let thisCanBeAnything: any = 12345;

thisCanBeAnything = "I can be anything - Look, I'm a string now";

thisCanBeAnything = ["Now I'm an array - This is almost like pure JavaScript!"];
</code></pre>
<p>None of these declarations will give an error in TypeScript, since the type was declared as <code>any</code>.</p>

<p><span class='note'><strong>Note:</strong> Most of the time, if you can, you should avoid using <code>any</code>. Using this loses one of the main benefits of TypeScript: having statically typed code.<br></span></p>

<h3 id="unknown"><code>unknown</code></h3>

<p>The <code>unknown</code> type is like a type-safe counterpart of the <code>any</code> type. You can use <code>unknown</code> when you want to type something that you can not determine the value of, but still want to make sure that any code using that value is correctly checking the type before using it. This is useful for library authors with functions in their library that may accept a broad range of values from their users and do not want to type the value explicitly.</p>

<p>For example, if you have a variable called <code>code</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">let code: unknown;
</code></pre>
<p>Then later in the program you can assign different values to that field, like <code>35</code> (<code>number</code>), or completely unrelated values, like arrays or even objects.</p>

<p><span class='note'><strong>Note:</strong> You are using <a href="https://www.digitalocean.com/community/tutorials/understanding-variables-scope-hoisting-in-javascript#difference-between-var,-let,-and-const"><code>let</code></a> because you are going to assign a new value to that variable.<br></span></p>

<p>Later in the same code, you could set <code>code</code> to a number:</p>
<pre class="code-pre "><code class="code-highlight language-ts">code = 35;
</code></pre>
<p>But then later you could assign it to an array:</p>
<pre class="code-pre "><code class="code-highlight language-ts">code = [12345];
</code></pre>
<p>You could even re-assign it to an object:</p>
<pre class="code-pre "><code class="code-highlight language-ts">code = {};
</code></pre>
<p>If later in the code you want to compare that value against some other <code>number</code>, like:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const isCodeGreaterThan100 = code &gt; 100;
</code></pre>
<p>The TypeScript compiler is going to display the error <code>2571</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Object is of type 'unknown'. (2571)
</code></pre>
<p>This happens because <code>code</code> needs to be a <code>number</code> type for this comparison, not an <code>unknown</code> type. When doing any operation with a value of type <code>unknown</code>, TypeScript needs to make sure that the type is the one it expects. One example of doing this is using the <code>typeof</code> operator that already exists in JavaScript. Examine the following code block:</p>
<pre class="code-pre "><code class="code-highlight language-ts">if (typeof code === 'number') {
  const isCodeGreaterThan100 = code &gt; 60;
  // ...
} else {
  throw new Error('Invalid value received as code');
}
</code></pre>
<p>In this example, you are checking if <code>code</code> is a number using the <code>typeof</code> operator. When you do that, TypeScript is going to <em>coerce</em> the type of your variable to <code>number</code> inside that <code>if</code> block, because at runtime the code inside the <code>if</code> block is only going to be executed if <code>code</code> is currently set to a number. Otherwise, you will throw a JavaScript error saying that the value passed is invalid.</p>

<p>To understand the differences between the <code>unknown</code> and <code>any</code> types, you can think of <code>unknown</code> as &ldquo;I do not know the type of that value&rdquo; and <code>any</code> as &ldquo;I do not care what type this value holds&rdquo;.</p>

<h3 id="void"><code>void</code></h3>

<p>You can use the <code>void</code> type to define the variable in question as holding no type at all. If you assign the result of a function that returns no value to a variable, that variable is going to have the type <code>void</code>.</p>

<p>Take the following code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function doSomething() {};

const resultOfVoidFunction: void = doSomething();
</code></pre>
<p>You will rarely have to use the <code>void</code> type directly in TypeScript.</p>

<h3 id="null-and-undefined"><code>null</code> and <code>undefined</code></h3>

<p><code>null</code> and <code>undefined</code> values in TypeScript have their own unique types that are called by the same name:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const someNullField: null = null;
const someUndefinedField: undefined = undefined;
</code></pre>
<p>These are especially useful when creating your own custom types, which will be covered later in this series.</p>

<h3 id="never"><code>never</code></h3>

<p>The <code>never</code> type is the type of a value that will never exist. For example, imagine you create a numeric variable:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const year: number = 2021;
</code></pre>
<p>If you create an <code>if</code> block to run some code if <code>year</code> is not a <code>number</code>, it could be like the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">if (typeof year !== "number") {
  year;
}
</code></pre>
<p>The type of the variable <code>year</code> inside that <code>if</code> block is going to be <code>never</code>. This is because, since <code>year</code> is typed as <code>number</code>, the condition for this <code>if</code> block will never be met. You can think of the <code>never</code> type as an impossible type because that variable can&rsquo;t have a value at this point.</p>

<h3 id="object"><code>object</code></h3>

<p>The <code>object</code> type represents any type that is not a primitive type. This means that it is not one of the following types:</p>

<ul>
<li><code>number</code></li>
<li><code>string</code></li>
<li><code>boolean</code></li>
<li><code>bigint</code></li>
<li><code>symbol</code></li>
<li><code>null</code></li>
<li><code>undefined</code></li>
</ul>

<p>The <code>object</code> type is commonly used to describe object literals because any object literal can be assigned to it:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const programmingLanguage: object = {
  name: "TypeScript"
};
</code></pre>
<p><span class='note'><strong>Note:</strong> There is a much better type than <code>object</code> that could be used in this case called <code>Record</code>. This has to do with creating custom types and is covered in a later tutorial in this series.<br></span></p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you tried out the different basic types that are available in TypeScript. These types are going to be frequently used when working in a TypeScript codebase and are the main building blocks to create more complex, custom types.</p>

<p>For more tutorials on TypeScript, check out our <a href="https://www.digitalocean.com/community/tags/typescript">TypeScript Topic page</a>.</p>
