---
title : "How To Use Functions in TypeScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-functions-in-typescript
image: "https://sergio.afanou.com/assets/images/image-midres-38.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>Creating and using functions is a fundamental aspect of any programming language, and <a href="https://www.typescriptlang.org/">TypeScript</a> is no different. TypeScript fully supports the existing <a href="https://www.digitalocean.com/community/tutorials/how-to-define-functions-in-javascript">JavaScript syntax for functions</a>, while also adding <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript">type information</a> and <a href="https://en.wikipedia.org/wiki/Function_overloading">function overloading</a> as new features. Besides providing extra documentation to the function, type information also reduces the chances of bugs in the code because there’s a lower risk of passing invalid data types to a type-safe function.</p>

<p>In this tutorial, you will start by creating the most basic functions with type information, then move on to more complex scenarios, like using <a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript">rest parameters</a> and function overloading. You will try out different code samples, which you can follow in your own TypeScript environment or the <a href="https://www.typescriptlang.org/play?ts=4.2.2#">TypeScript Playground</a>, an online environment that allows you to write TypeScript directly in the browser.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To follow this tutorial, you will need:</p>

<ul>
<li>An environment in which you can execute TypeScript programs to follow along with the examples. To set this up on your local machine, you will need the following:

<ul>
<li>Both <a href="https://nodejs.org/en/about/">Node</a> and <a href="https://docs.npmjs.com/about-npm">npm</a> (or <a href="https://yarnpkg.com/getting-started">yarn</a>) installed in order to run a development environment that handles TypeScript-related packages. This tutorial was tested with Node.js version 14.3.0 and npm version 6.14.5. To install on macOS or Ubuntu 18.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How to Install Node.js and Create a Local Development Environment on macOS</a> or the <strong>Installing Using a PPA</strong> section of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04">How To Install Node.js on Ubuntu 18.04</a>. This also works if you are using the <a href="https://docs.microsoft.com/en-us/windows/wsl/install-win10">Windows Subsystem for Linux (WSL)</a>.</li>
<li>Additionally, you will need the TypeScript Compiler (<code>tsc</code>) installed on your machine. To do this, refer to the <a href="https://www.typescriptlang.org/download">official TypeScript website</a>.</li>
</ul></li>
<li>If you do not wish to create a TypeScript environment on your local machine, you can use the official <a href="https://www.typescriptlang.org/play">TypeScript Playground</a> to follow along.</li>
<li>You will need sufficient knowledge of JavaScript, especially ES6+ syntax, such as <a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript">destructuring, rest operators</a>, and <a href="https://www.digitalocean.com/community/tutorials/understanding-modules-and-import-and-export-statements-in-javascript">imports/exports</a>. If you need more information on these topics, reading our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript series</a> is recommended.</li>
<li>This tutorial will reference aspects of text editors that support TypeScript and show in-line errors. This is not necessary to use TypeScript, but does take more advantage of TypeScript features. To gain the benefit of these, you can use a text editor like <a href="https://code.visualstudio.com/">Visual Studio Code</a>, which has full support for TypeScript out of the box. You can also try out these benefits in the <a href="https://www.typescriptlang.org/play">TypeScript Playground</a>.</li>
</ul>

<p>All examples shown in this tutorial were created using TypeScript version 4.2.2.</p>

<h2 id="creating-typed-functions">Creating Typed Functions</h2>

<p>In this section, you will create functions in TypeScript, and then add type information to them.</p>

<p>In JavaScript, functions can be declared in a number of ways. One of the most popular is to use the <code>function</code> keyword, as is shown in the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function sum(a, b) {
  return a + b;
}
</code></pre>
<p>In this example, <code>sum</code> is the name of the function, <code>(a, b)</code> are the arguments, and <code>{return a + b;}</code> is the function body.</p>

<p>The syntax for creating functions in TypeScript is the same, except for one major addition: You can let the compiler know what types each argument or parameter should have. The following code block shows the general syntax for this, with the type declarations highlighted:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function functionName(param1<span class="highlight">: Param1Type</span>, param2<span class="highlight">: Param2Type</span>)<span class="highlight">: ReturnType</span> {
  // ... body of the function
}
</code></pre>
<p>Using this syntax, you can then add types to the parameters of the <code>sum</code> function shown earlier:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function sum(a<span class="highlight">: number</span>, b<span class="highlight">: number</span>) {
  return a + b;
}
</code></pre>
<p>This ensures that <code>a</code> and <code>b</code> are <code>number</code> values.</p>

<p>You can also add the type of the returned value:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function sum(a: number, b: number)<span class="highlight">: number</span> {
  return a + b;
}
</code></pre>
<p>Now TypeScript will expect the <code>sum</code> function to return a number value. If you call your function with some parameters and store the result value in a variable called <code>result</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const result = sum(1, 2);
</code></pre>
<p>The <code>result</code> variable is going to have the type <code>number</code>. If you are using the TypeScript playground or are using a text editor that fully supports TypeScript, hovering over <code>result</code> with your cursor will show <code>const result: number</code>, showing that TypeScript has implied its type from the function declaration.</p>

<p>If you called your function with a value that has a type other than the one expected by your function, the TypeScript Compiler (<code>tsc</code>) would give you the error <code>2345</code>. Take the following call to the <code>sum</code> function:</p>
<pre class="code-pre "><code class="code-highlight language-ts">sum('shark', 'whale');
</code></pre>
<p>This would give the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Argument of type 'string' is not assignable to parameter of type 'number'. (2345)
</code></pre>
<p>You can use any type in your functions, not just <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript">basic types</a>. For example, imagine you have a <code>User</code> type that looks like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};
</code></pre>
<p>You could create a function that returns the full name of the user like the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function getUserFullName(user: User): string {
  return `${user.firstName} ${user.lastName}`;
}
</code></pre>
<p>Most of the times TypeScript is smart enough to infer the return type of functions, so you can drop the return type from the function declaration in this case:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function getUserFullName(user: User) {
  return `${user.firstName} ${user.lastName}`;
}
</code></pre>
<p>Notice you removed the <code>: string</code> part, which was the return type of your function. As you are returning a string in the body of your function, TypeScript correctly assumes your function has a string return type.</p>

<p>To call your function now, you must pass an object that has the same shape as the <code>User</code> type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};

function getUserFullName(user: User) {
  return `${user.firstName} ${user.lastName}`;
}

const user: User = {
  firstName: "Jon",
  lastName: "Doe"
};

const userFullName = getUserFullName(user);
</code></pre>
<p>This code will successfully pass the TypeScript type-checker. If you hover over the <code>userFullName</code> constant in your editor, the editor will identify its type as <code>string</code>.</p>

<h2 id="optional-function-parameters-in-typescript">Optional Function Parameters in TypeScript</h2>

<p>Having all parameters is not always required when creating functions. In this section, you will learn how to mark function parameters as optional in TypeScript.</p>

<p>To turn a function parameter into an optional one, add the <code>?</code> modifier right after the parameter name. Given a function parameter <code>param1</code> with type <code>T</code>, you could make <code>param1</code> an optional parameter by adding <code>?</code>, as highlighted in the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">param1<span class="highlight">?</span>: T
</code></pre>
<p>For example, add an optional <code>prefix</code> parameter to your <code>getUserFullName</code> function, which is an optional string that can be added as a prefix to the user&rsquo;s full name:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};

function getUserFullName(user: User, <span class="highlight">prefix?: string</span>) {
  return `<span class="highlight">${prefix ?? ''}</span>${user.firstName} ${user.lastName}`;
}
</code></pre>
<p>In the first highlighted part of this code block, you are adding an optional <code>prefix</code> parameter to your function, and in the second highlighted part you are prefixing the user&rsquo;s full name with it. To do that, you are using the <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator">nullish coalescing operator <code>??</code></a>. This way, you are only going to use the <code>prefix</code> value if it is defined; otherwise, the function will use an empty string.</p>

<p>Now you can call your function with or without the prefix parameter, as shown in the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};

function getUserFullName(user: User, prefix?: string) {
  return `${prefix ?? ''} ${user.firstName} ${user.lastName}`;
}

const user: User = {
  firstName: "Jon",
  lastName: "Doe"
};

const userFullName = getUserFullName(user);
const mrUserFullName = getUserFullName(user, 'Mr. ');
</code></pre>
<p>In this case, the value of <code>userFullName</code> will be <code>Jon Doe</code>, and the value of <code>mrUserFullName</code> will be <code>Mr. Jon Doe</code>.</p>

<p>Note that you cannot add an optional parameter before a required one; it must be listed last in the series, as is done with <code>(user: User, prefix?: string)</code>. Listing it first would make the TypeScript Compiler return the error <code>1016</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>A required parameter cannot follow an optional parameter. (1016)
</code></pre>
<h2 id="typed-arrow-function-expressions">Typed Arrow Function Expressions</h2>

<p>So far, this tutorial has shown how to type normal functions in TypeScript, defined with the <code>function</code> keyword. But in JavaScript, you can define a function in more than one way, such as with <a href="https://www.digitalocean.com/community/tutorials/understanding-arrow-functions-in-javascript">arrow functions</a>. In this section, you will add types to arrow functions in TypeScript.</p>

<p>The syntax for adding types to arrow functions is almost the same as adding types to normal functions. To illustrate this, change your <code>getUserFullName</code> function into an arrow function expression:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const getUserFullName = (user: User, prefix?: string) =&gt; `${prefix ?? ''}${user.firstName} ${user.lastName}`;
</code></pre>
<p>If you wanted to be explicit about the return type of your function, you would add it after the <code>()</code>, as shown in the highlighted code in the following block:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const getUserFullName = (user: User, prefix?: string)<span class="highlight">: string</span> =&gt; `${prefix ?? ''}${user.firstName} ${user.lastName}`;
</code></pre>
<p>Now you can use your function exactly like before:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};

const getUserFullName = (user: User, prefix?: string) =&gt; `${prefix ?? ''}${user.firstName} ${user.lastName}`;

const user: User = {
  firstName: "Jon",
  lastName: "Doe"
};

const userFullName = getUserFullName(user);
</code></pre>
<p>This will pass the TypeScript type-checker with no error.</p>

<p><span class='note'><strong>Note:</strong> Remember that everything valid for functions in JavaScript is also valid for functions in TypeScript. For a refresher on these rules, check out our <a href="https://www.digitalocean.com/community/tutorials/how-to-define-functions-in-javascript">How To Define Functions in JavaScript tutorial</a>.<br></span></p>

<h2 id="function-types">Function Types</h2>

<p>In the previous sections, you added types to the parameters and return values for functions in TypeScript. In this section, you are going to learn how to create function types, which are types that represent a specific function signature. Creating a type that matches a specific function is especially useful when passing functions to other functions, like having a parameter that is itself a function. This is a common pattern when creating functions that accept <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">callbacks</a>.</p>

<p>The syntax for creating your function type is similar to creating an arrow function, with two differences:</p>

<ul>
<li>You remove the function body.</li>
<li>You make the function declaration return the <code>return</code> type itself.</li>
</ul>

<p>Here is how you would create a type that matches the <code>getUserFullName</code> function you have been using:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  firstName: string;
  lastName: string;
};

<span class="highlight">type PrintUserNameFunction = (user: User, prefix?: string) =&gt; string;</span>
</code></pre>
<p>In this example, you used the <code>type</code> keyword to declare a new type, then provided the type for the two parameters in the parentheses and the type for the return value after the arrow.</p>

<p>For a more concrete example, imagine you are creating an <a href="https://www.digitalocean.com/community/tutorials/understanding-events-in-javascript">event listener function</a> called <code>onEvent</code>, which receives as the first parameter the event name, and as the second parameter the event callback. The event callback itself would receive as the first parameter an object with the following type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type EventContext = {
  value: string;
};
</code></pre>
<p>You can then write your <code>onEvent</code> function like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type EventContext = {
  value: string;
};

function onEvent(eventName: string, <span class="highlight">eventCallback: (target: EventContext) =&gt; void</span>) {
  // ... implementation
}
</code></pre>
<p>Notice that the type of the <code>eventCallback</code> parameter is a function type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">eventCallback: (target: EventTarget) =&gt; void
</code></pre>
<p>This means that your <code>onEvent</code> function expects another function to be passed in the <code>eventCallback</code> parameter. This function should accept a single argument of the type <code>EventTarget</code>. The return type of this function is ignored by your <code>onEvent</code> function, and so you are using <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript"><code>void</code></a> as the type.</p>

<h2 id="using-typed-asynchronous-functions">Using Typed Asynchronous Functions</h2>

<p>When working with JavaScript, it is relatively common to have <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript">asynchronous functions</a>. TypeScript has a specific way to deal with this. In this section, you are going to create asynchronous functions in TypeScript.</p>

<p>The syntax for creating asynchronous functions is the same as the one used for JavaScript, with the addition of allowing types:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function asyncFunction(param1: number) {
  // ... function implementation ...
}
</code></pre>
<p>There is one major difference between adding types to a normal function and adding types to an asynchronous function: In an asynchronous function, the return type must always be the <code>Promise&lt;T&gt;</code> generic. The <code>Promise&lt;T&gt;</code> generic represents the Promise object that is returned by an asynchronous function, where <code>T</code> is the type of the value the promise resolves to.</p>

<p>Imagine you have a <code>User</code> type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  firstName: string;
};
</code></pre>
<p>Imagine also that you have a few user objects in a data store. This data could be stored anywhere, like in a file, a database, or behind an API request. For simplicity, in this example you will be using an <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">array</a>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  firstName: string;
};

const users: User[] = [
  { id: 1, firstName: "Jane" },
  { id: 2, firstName: "Jon" }
];
</code></pre>
<p>If you wanted to create a type-safe function that retrieves a user by ID in an asynchronous way, you could do it like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function getUserById(userId: number): Promise&lt;User | null&gt; {
  const foundUser = users.find(user =&gt; user.id === userId);

  if (!foundUser) {
    return null;
  }

  return foundUser;
}
</code></pre>
<p>In this function, you are first declaring your function as asynchronous:</p>
<pre class="code-pre "><code class="code-highlight language-ts"><span class="highlight">async</span> function getUserById(userId: number): Promise&lt;User | null&gt; {
</code></pre>
<p>Then you are specifying that it accepts as the first parameter the user ID, which must be a <code>number</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function getUserById(<span class="highlight">userId: number</span>): Promise&lt;User | null&gt; {
</code></pre>
<p>The return type of <code>getUserById</code> is a <a href="https://www.digitalocean.com/community/tutorials/understanding-the-event-loop-callbacks-promises-and-async-await-in-javascript#promises">Promise</a> that resolves to either <code>User</code> or <code>null</code>. You are using the <a href="https://www.digitalocean.com/community/tutorials/how-to-create-custom-types-in-typescript">union type</a> <code>User | null</code> as the type parameter to the <code>Promise</code> generic.</p>

<p><code>User | null</code> is the <code>T</code> in <code>Promise&lt;T&gt;</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function getUserById(userId: number): <span class="highlight">Promise&lt;User | null&gt;</span> {
</code></pre>
<p>Call your function using <code>await</code> and store the result in a variable called <code>user</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  firstName: string;
};

const users: User[] = [
  { id: 1, firstName: "Jane" },
  { id: 2, firstName: "Jon" }
];

async function getUserById(userId: number): Promise&lt;User | null&gt; {
  const foundUser = users.find(user =&gt; user.id === userId);

  if (!foundUser) {
    return null;
  }

  return foundUser;
}

async function runProgram() {
  const user = await getUserById(1);
}
</code></pre>
<span class='note'><p>
<strong>Note:</strong> You are using a wrapper function called <code>runProgram</code> because you cannot use <code>await</code> in the top level of a file. Doing so would cause the TypeScript Compiler to emit the error <code>1375</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>'await' expressions are only allowed at the top level of a file when that file is a module, but this file has no imports or exports. Consider adding an empty 'export {}' to make this file a module. (1375)
</code></pre>
<p></p></span>

<p>If you hover over <code>user</code> in your editor or in the TypeScript Playground, you&rsquo;ll find that <code>user</code> has the type <code>User | null</code>, which is exactly the type the promise returned by your <code>getUserById</code> function resolves to.</p>

<p>If you remove the <code>await</code> and just call the function directly, the Promise object is returned:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function runProgram() {
  const userPromise = getUserById(1);
}
</code></pre>
<p>If you hover over <code>userPromise</code>, you&rsquo;ll find that it has the type <code>Promise&lt;User | null&gt;</code>.</p>

<p>Most of the time, TypeScript can infer the return type of your async function, just like it does with non-async functions. You can therefore omit the return type of the <code>getUserById</code> function, as it is still correctly inferred to have the type <code>Promise&lt;User | null&gt;</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">async function getUserById(userId: number) {
  const foundUser = users.find(user =&gt; user.id === userId);

  if (!foundUser) {
    return null;
  }

  return foundUser;
}
</code></pre>
<h2 id="adding-types-to-rest-parameters">Adding Types to Rest Parameters</h2>

<p><a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript#rest-parameters">Rest parameters</a> are a feature in JavaScript that allows a function to receive many parameters as a single array. In this section, you will use rest parameters with TypeScript.</p>

<p>Using rest parameters in a type-safe way is completely possible by using the rest parameter followed by the type of the resulting array. Take for example the following code, where you have a function called <code>sum</code> that accepts a variable amount of numbers and returns their total sum:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function sum(<span class="highlight">...args: number[]</span>) {
  return args.reduce((accumulator, currentValue) =&gt; {
    return accumulator + currentValue;
  }, 0);
}
</code></pre>
<p>This function uses the <a href="https://www.digitalocean.com/community/tutorials/how-to-use-array-methods-in-javascript-iteration-methods"><code>.reduce</code> Array method</a> to iterate over the array and add the elements together. Notice the rest parameter <code>args</code> highlighted here. The type is being set to an array of numbers: <code>number[]</code>.</p>

<p>Calling your function works normally:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function sum(...args: number[]) {
  return args.reduce((accumulator, currentValue) =&gt; {
    return accumulator + currentValue;
  }, 0);
}

const sumResult = sum(<span class="highlight">2, 4, 6, 8</span>);
</code></pre>
<p>If you call your function using anything other than a number, like:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const sumResult = sum(2, <span class="highlight">"b"</span>, 6, 8);
</code></pre>
<p>The TypeScript Compiler will emit the error <code>2345</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Argument of type 'string' is not assignable to parameter of type 'number'. (2345)
</code></pre>
<h2 id="using-function-overloads">Using Function Overloads</h2>

<p>Programmers sometime need a function to accept different parameters depending on how the function is called. In JavaScript, this is normally done by having a parameter that may assume different types of values, like a string or a number. Setting multiple implementations to the same function name is called <em>function overloading</em>.</p>

<p>With TypeScript, you can create function overloads that explicitly describe the different cases that they address, improving the developer experience by document each implementation of the overloaded function separately. This section will go through how to use function overloading in TypeScript.</p>

<p>Imagine you have a <code>User</code> type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  email: string;
  fullName: string;
  age: number;
};
</code></pre>
<p>And you want to create a function that can look up a user using any of the following information:</p>

<ul>
<li><code>id</code></li>
<li><code>email</code></li>
<li><code>age</code> and <code>fullName</code></li>
</ul>

<p>You could create such a function like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function getUser(idOrEmailOrAge: number | string, fullName?: string): User | undefined {
  // ... code
}
</code></pre>
<p>This function uses the <code>|</code> operator to compose a union of types for <code>idOrEmailOrAge</code> and for the return value.</p>

<p>Next, add function overloads for each way you want your function to be used, as shown in the following highlighted code:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  email: string;
  fullName: string;
  age: number;
};

<span class="highlight">function getUser(id: number): User | undefined;</span>
<span class="highlight">function getUser(email: string): User | undefined;</span>
<span class="highlight">function getUser(age: number, fullName: string): User | undefined;</span>

function getUser(idOrEmailOrAge: number | string, fullName?: string): User | undefined {
  // ... code
}
</code></pre>
<p>This function has three overloads, one for each way to retrieve a user. When creating function overloads, you add the function overloads before the function implementation itself. The function overloads do not have a body; they just have the list of parameters and the return type.</p>

<p>Next, you implement the function itself, which should have a parameter list that is compatible with all function overloads. In the previous example, your first parameter can be either a number or a string, since it can be the <code>id</code>, the <code>email</code>, or the <code>age</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function getUser(<span class="highlight">id: number</span>): User | undefined;
function getUser(<span class="highlight">email: string</span>): User | undefined;
function getUser(<span class="highlight">age: number</span>, fullName: string): User | undefined;

function getUser(<span class="highlight">idOrEmailOrAge: number | string</span>, fullName?: string): User | undefined {
  // ... code
}
</code></pre>
<p>You therefore set the type of the <code>idOrEmailorAge</code> parameter in your function implementation to be <code>number | string</code>. This way, it is compatible with all the overloads of your <code>getUser</code> function.</p>

<p>You are also adding an optional parameter to your function, for when the user is passing a <code>fullName</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function getUser(id: number): User | undefined;
function getUser(email: string): User | undefined;
function getUser(age: number, <span class="highlight">fullName: string</span>): User | undefined;

function getUser(idOrEmailOrAge: number | string, <span class="highlight">fullName?: string</span>): User | undefined {
  // ... code
}
</code></pre>
<p>Implementing your function could be like the following, where you are using a <code>users</code> array as the data store of your users:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type User = {
  id: number;
  email: string;
  fullName: string;
  age: number;
};

const users: User[] = [
  { id: 1, email: "jane_doe@example.com", fullName: "Jane Doe" , age: 35 },
  { id: 2, email: "jon_do@example.com", fullName: "Jon Doe", age: 35 }
];

function getUser(id: number): User | undefined;
function getUser(email: string): User | undefined;
function getUser(age: number, <span class="highlight">fullName: string</span>): User | undefined;

function getUser(idOrEmailOrAge: number | string, <span class="highlight">fullName?: string</span>): User | undefined {
  if (typeof idOrEmailOrAge === "string") {
    return users.find(user =&gt; user.email === idOrEmailOrAge);
  }

  if (typeof fullName === "string") {
    return users.find(user =&gt; user.age === idOrEmailOrAge &amp;&amp; user.fullName === fullName);
  } else {
    return users.find(user =&gt; user.id === idOrEmailOrAge);
  }
}

const userById = getUser(1);
const userByEmail = getUser("jane_doe@example.com");
const userByAgeAndFullName = getUser(35, "Jon Doe");
</code></pre>
<p>In this code, if <code>idOrEmailOrAge</code> is a string, then you can search for the user with the <code>email</code> key. The following conditional assumes <code>idOrEmailOrAge</code> is a number, so it is either the <code>id</code> or the <code>age</code>, depending on if <code>fullName</code> is defined.</p>

<p>One interesting aspect of function overloads is that in most editors, including VS Code and the TypeScript Playground, as soon as you type the function name and open the first parenthesis to call the function, a pop-up will appear with all the overloads available, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67669/1.gif" alt="Overloads Popup"></p>

<p>If you add a comment to each function overload, the comment will also be in the pop-up as a source of documentation. For example, add the following highlighted comments to the example overloads:</p>
<pre class="code-pre "><code class="code-highlight language-ts">...
<span class="highlight">/**</span>
 <span class="highlight">* Get a user by their ID.</span>
 <span class="highlight">*/</span>
function getUser(id: number): User | undefined;
<span class="highlight">/**</span>
 <span class="highlight">* Get a user by their email.</span>
 <span class="highlight">*/</span>
function getUser(email: string): User | undefined;
<span class="highlight">/**</span>
 <span class="highlight">* Get a user by their age and full name.</span>
 <span class="highlight">*/</span>
function getUser(age: number, fullName: string): User | undefined;
...
</code></pre>
<p>Now when you hover over these functions, the comment will show up for each overload, as shown in the following animation:</p>

<p><img src="https://assets.digitalocean.com/articles/67669/2.gif" alt="Overloads Popup with Comments"></p>

<h2 id="user-defined-type-guards">User-Defined Type Guards</h2>

<p>The last feature of functions in TypeScript that this tutorial will examine is <em>user-defined type guards</em>, which are special functions that allow TypeScript to better infer the type of some value. These guards enforce certain types in conditional code blocks, where the type of a value may be different depending on the situation. These are especially useful when using the <code>Array.prototype.filter</code> function to return a filtered array of data.</p>

<p>One common task when adding values conditionally to an array is to check for some conditions and then only add the value if the condition is true. If the value is not true, the code adds a <code>false</code> <a href="https://www.digitalocean.com/community/tutorials/understanding-data-types-in-javascript">Boolean</a> to the array. Before using that array, you can filter it using <code>.filter(<span class="highlight">Boolean</span>)</code> to make sure only truthy values are returned.</p>

<p>When called with a value, the Boolean constructor returns <code>true</code> or <code>false</code>, depending on if this value is a <code>Truthy</code> or <code>Falsy</code> value.</p>

<p>For example, imagine you have an array of strings, and you only want to include the string <code>production</code> to that array if some other flag is true:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const isProduction = false

const valuesArray = ['some-string', isProduction &amp;&amp; 'production']

function processArray(array: string[]) {
  // do something with array
}

processArray(valuesArray.filter(Boolean))
</code></pre>
<p>While this is, at runtime, perfectly valid code, the TypeScript Compiler will give you the error <code>2345</code> during compilation:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Argument of type '(string | boolean)[]' is not assignable to parameter of type 'string[]'.
 Type 'string | boolean' is not assignable to type 'string'.
   Type 'boolean' is not assignable to type 'string'. (2345)
</code></pre>
<p>This error is saying that, at compile-time, the value passed to <code>processArray</code> is interpreted as an array of <code>false | string</code> values, which is not what the <code>processArray</code> expected. It expects an array of strings: <code>string[]</code>.</p>

<p>This is one case where TypeScript is not smart enough to infer that by using <code>.filter(Boolean)</code> you are removing all <code>falsy</code> values from your array. However, there is one way to give this hint to TypeScript: using user-defined type guards.</p>

<p>Create a user-defined type guard function called <code>isString</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">function isString(value: any): <span class="highlight">value is string</span> {
  return typeof value === "string"
}
</code></pre>
<p>Notice the return type of the <code>isString</code> function. The way to create user-defined type guards is by using the following syntax as the return type of a function:</p>
<pre class="code-pre "><code class="code-highlight language-ts"><span class="highlight">parameterName</span> is <span class="highlight">Type</span>
</code></pre>
<p>Where <code>parameterName</code> is the name of the parameter you are testing, and <code>Type</code> is the expected type the value of this parameter has if this function returns <code>true</code>.</p>

<p>In this case, you are saying that <code>value</code> is a <code>string</code> if <code>isString</code> returns <code>true</code>. You are also setting the type of your <code>value</code> parameter to <code>any</code>, so it works with <code>any</code> type of value.</p>

<p>Now, change your <code>.filter</code> call to use your new function instead of passing it the <code>Boolean</code> constructor:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const isProduction = false

const valuesArray = ['some-string', isProduction &amp;&amp; 'production']

function processArray(array: string[]) {
  // do something with array
}

function isString(value: any): value is string {
  return typeof value === "string"
}

processArray(valuesArray.filter(<span class="highlight">isString</span>))
</code></pre>
<p>Now the TypeScript compiler correctly infers that the array passed to <code>processArray</code> only contains strings, and your code compiles correctly.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Functions are the building block of applications in TypeScript, and in this tutorial you learned how to build type-safe functions in TypeScript and how to take advantage of function overloads to better document all variants of a single function. Having this knowledge will allow for more type-safe and easy-to-maintain functions throughout your code.</p>

<p>For more tutorials on TypeScript, check out our <a href="https://www.digitalocean.com/community/tags/typescript">TypeScript Topic page</a>.</p>
