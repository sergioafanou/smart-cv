---
title : "How To Create Custom Types in TypeScript"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-create-custom-types-in-typescript
image: "https://sergio.afanou.com/assets/images/image-midres-39.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://www.typescriptlang.org/">TypeScript</a> is an extension of the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">JavaScript</a> language that uses JavaScript’s runtime with a compile-time type checker. This combination allows developers to use the full JavaScript ecosystem and language features, while also adding optional static type-checking, enums, classes, and interfaces on top of it.</p>

<p>Though the pre-made, <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript">basic types in TypeScript</a> will cover many use cases, creating your own custom types based on these basic types will allow you to ensure the type checker validates the data structures specific to your project. This will reduce the chance of bugs in your project, while also allowing for better documentation of the data structures used throughout the code.</p>

<p>This tutorial will show you how to use custom types with TypeScript, how to compose those types together with unions and intersections, and how to use utility types to add flexibility to your custom types. It will lead you through different code samples, which you can follow in your own TypeScript environment or the <a href="https://www.typescriptlang.org/play?ts=4.2.2#">TypeScript Playground</a>, an online environment that allows you to write TypeScript directly in the browser.</p>

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

<h2 id="creating-custom-types">Creating Custom Types</h2>

<p>In cases where programs have complex data structures, using TypeScript&rsquo;s basic types may not completely describe the data structures you are using. In these cases, declaring your own type will help you address the complexity. In this section, you are going create types that can be used to describe any object shape you need to use in your code.</p>

<h3 id="custom-type-syntax">Custom Type Syntax</h3>

<p>In TypeScript, the syntax for creating custom types is to use the <code>type</code> keyword followed by the type name and then an assignment to a <code>{}</code> block with the type properties. Take the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor: string[];
};
</code></pre>
<p>The syntax resembles an object literal, where the key is the name of the property and the value is the type this property should have. This defines a type <code>Programmer</code> that must be an <a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">object</a> with the <code>name</code> key that holds a string value and a <code>knownFor</code> key that holds an array of strings.</p>

<p>As shown in the earlier example, you can use <code>;</code> as the separator between each property. It is also possible to use a comma, <code>,</code>, or to completely omit the separator, as shown here:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string
  knownFor: string[]
};
</code></pre>
<p>Using your custom type is the same as using any of the basic types. Add a double colon and then add your type name:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor: string[];
};

const ada<span class="highlight">: Programmer</span> = {
  name: 'Ada Lovelace',
  knownFor: ['Mathematics', 'Computing', 'First Programmer']
};
</code></pre>
<p>The <code>ada</code> constant will now pass the type checker without throwing an error.</p>

<p>If you write this example in any editor with full support of TypeScript, like in the TypeScript Playground, the editor will suggest the fields expected by that object and their types, as shown in the following animation:</p>

<p><img src="https://assets.digitalocean.com/articles/67755/1.gif" alt='An animation showing suggestions to add the "name" and "knownFor" key to a new instance of the "Programmer" type'></p>

<p>If you add comments to the fields using the <a href="https://tsdoc.org/">TSDoc</a> format, a popular style of TypeScript comment documentation, they are also suggested in code completion. Take the following code with explanations in comments:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  /**
   * The full name of the Programmer
   */
  name: string;
  /**
   * This Programmer is known for what?
   */
  knownFor: string[];
};

const ada: Programmer = {
  name: 'Ada Lovelace',
  knownFor: ['Mathematics', 'Computing', 'First Programmer']
};
</code></pre>
<p>The commented descriptions will now appear with the field suggestions:</p>

<p><img src="https://assets.digitalocean.com/articles/67755/2.gif" alt="Code completion with TSDoc comments"></p>

<p>When creating an object with the custom type <code>Programmer</code>, if you assign a value with an unexpected type to any of the properties, TypeScript will throw an error. Take the following code block, with a highlighted line that does not adhere to the type declaration:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor: string[];
};

const ada: Programmer = {
  <span class="highlight">name: true</span>,
  knownFor: ['Mathematics', 'Computing', 'First Programmer']
};
</code></pre>
<p>The TypeScript Compiler (<code>tsc</code>) will show the error <code>2322</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type 'boolean' is not assignable to type 'string'. (2322)
</code></pre>
<p>If you omitted any of the properties required by your type, like in the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor: string[];
};

const ada: Programmer = {
  name: 'Ada Lovelace'
};
</code></pre>
<p>The TypeScript Compiler will give the error <code>2741</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Property 'knownFor' is missing in type '{ name: string; }' but required in type 'Programmer'. (2741)
</code></pre>
<p>Adding a new property not specified in the original type will also result in an error:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor: string[];
};

const ada: Programmer = {
  name: "Ada Lovelace",
  knownFor: ['Mathematics', 'Computing', 'First Programmer'],
  <span class="highlight">age: 36</span>
};
</code></pre>
<p>In this case, the error shown is the <code>2322</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type '{ name: string; knownFor: string[]; age: number; }' is not assignable to type 'Programmer'.
Object literal may only specify known properties, and 'age' does not exist in type 'Programmer'.(2322)
</code></pre>
<h3 id="nested-custom-types">Nested Custom Types</h3>

<p>You can also nest custom types together. Imagine you have a <code>Company</code> type that has a <code>manager</code> field that adheres to a <code>Person</code> type. You could create those types like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Person = {
  name: string;
};

type Company = {
  name: string;
  manager: Person;
};
</code></pre>
<p>Then you could create a value of type <code>Company</code> like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const manager: Person = {
  name: 'John Doe',
}

const company: Company = {
  name: 'ACME',
  manager,
}
</code></pre>
<p>This code would pass the type checker, since the <code>manager</code> constant fits the type designated for the <code>manager</code> field. Note that this uses the <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#property_definitions">object property shorthand</a> to declare <code>manager</code>.</p>

<p>You can omit the type in the <code>manager</code> constant because it has the same shape as the <code>Person</code> type. TypeScript is not going to raise an error when you use an object with the same shape as the one expected by the type of the <code>manager</code> property, even if it is not set explicitly to have the <code>Person</code> type</p>

<p>The following will not throw an error:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const manager = {
  name: 'John Doe'
}

const company: Company = {
  name: 'ACME',
  manager
}
</code></pre>
<p>You can even go one step further and set the <code>manager</code> directly inside this <code>company</code> object literal:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const company: Company = {
  name: 'ACME',
  manager: {
    name: 'John Doe'
  }
};
</code></pre>
<p>All these scenarios are valid.</p>

<p>If writing these examples in an editor that supports TypeScript, you will find that the editor will use the available type information to document itself. For the previous example, as soon as you open the <code>{}</code> object literal for <code>manager</code>, the editor will expect a <code>name</code> property of type <code>string</code>:</p>

<p><img src="https://assets.digitalocean.com/articles/67755/3.png" alt="TypeScript Code Self-Documenting"></p>

<p>Now that you have gone through some examples of creating your own custom type with a fixed number of properties, next you&rsquo;ll try adding optional properties to your types.</p>

<h3 id="optional-properties">Optional Properties</h3>

<p>With the custom type declaration in the previous sections, you cannot omit any of the properties when creating a value with that type. There are, however, some cases that require optional properties that can pass the type checker with or without the value. In this section, you will declare these optional properties.</p>

<p>To add optional properties to a type, add the <code>?</code> modifier to the property. Using the <code>Programmer</code> type from the previous sections, turn the <code>knownFor</code> property into an optional property by adding the following highlighted character:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor<span class="highlight">?</span>: string[];
};
</code></pre>
<p>Here you are adding the <code>?</code> modifier after the property name. This makes TypeScript consider this property as optional and not raise an error when you omit that property:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Programmer = {
  name: string;
  knownFor?: string[];
};

const ada: Programmer = {
  name: 'Ada Lovelace'
};
</code></pre>
<p>This will pass without an error.</p>

<p>Now that you know how to add optional properties to a type, it is time to learn how to create a type that can hold an unlimited number of fields.</p>

<h2 id="indexable-types">Indexable Types</h2>

<p>The previous examples showed that you cannot add properties to a value of a given type if that type does not specify those properties when it was declared. In this section, you will create <em>indexable types</em>, which are types that allow for any number of fields if they follow the index signature of the type.</p>

<p>Imagine you had a <code>Data</code> type to hold an unlimited number of properties of the <code>any</code> type. You could declare this type like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = {
  [key: string]: any;
};
</code></pre>
<p>Here you create a normal type with the type definition block in curly brackets (<code>{}</code>), and then add a special property in the format of <code>[key: <span class="highlight">typeOfKeys</span>]: <span class="highlight">typeOfValues</span></code>, where <code>typeOfKeys</code> is the type the keys of that object should have, and <code>typeOfValues</code> is the type the values of those keys should have.</p>

<p>You can then use it normally like any other type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = {
  [key: string]: any;
};

const someData: Data = {
  someBooleanKey: true,
  someStringKey: 'text goes here'
  // ...
}
</code></pre>
<p>Using indexable types, you can assign an unlimited number of properties, as long as they match the <em>index signature</em>, which is the name used to describe the types of the keys and values of an indexable type. In this case, the keys have a <code>string</code> type, and the values have <code>any</code> type.</p>

<p>It is also possible to add specific properties that are always required to your indexable type, just like you could with a normal type. In the following highlighted code, you are adding the <code>status</code> property to your <code>Data</code> type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = {
  <span class="highlight">status: boolean</span>;
  [key: string]: any;
};

const someData: Data = {
  status: true,
  someBooleanKey: true,
  someStringKey: 'text goes here'
  // ...
}
</code></pre>
<p>This would mean that a <code>Data</code> type object must have a <code>status</code> key with a <code>boolean</code> value to pass the type checker.</p>

<p>Now that you can create an object with different numbers of elements, you can move on to learning about arrays in TypeScript, which can have a custom number of elements or more.</p>

<h2 id="creating-arrays-with-number-of-elements-or-more">Creating Arrays with Number of Elements or More</h2>

<p>Using both the <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript#basic-types-used-in-typescript">array and tuple basic types</a> available in TypeScript, you can create custom types for arrays that should have a minimum amount of elements. In this section, you will use the TypeScript <a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript#rest-parameters">rest operator</a> <code>...</code> to do this.</p>

<p>Imagine you have a function responsible for merging multiple strings. This function is going to take a single array parameter. This array must have at least two elements, each of which should be strings. You can create a type like this with the folowing:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type MergeStringsArray = [string, string, ...string[]];
</code></pre>
<p>The <code>MergeStringsArray</code> type is taking advantage of the fact that you can use the rest operator with an array type and uses the result of that as the third element of a tuple. This means that the first two strings are required, but additional string elements after that are not required.</p>

<p>If an array has less than two string elements, it will be invalid, like the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const invalidArray: MergeStringsArray = ['some-string']
</code></pre>
<p>The TypeScript Compiler is going to give error <code>2322</code> when checking this array:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type '[string]' is not assignable to type 'MergeStringsArray'.
Source has 1 element(s) but target requires 2. (2322)
</code></pre>
<p>Up to this point, you have created your own custom types from a combination of basic types. In the next section, you will make a new type by composing two or more custom types together.</p>

<h2 id="composing-types">Composing Types</h2>

<p>This section will go through two ways that you can compose types together. These will use the <em>union operator</em> to pass any data that adheres to one type or the other and the <em>intersection operator</em> to pass data that satisfies all the conditions in both types.</p>

<h3 id="unions">Unions</h3>

<p>Unions are created using the <code>|</code> (pipe) operator, which represents a value that can have any of the types in the union. Take the following example:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type ProductCode = number | string
</code></pre>
<p>In this code, <code>ProductCode</code> can be either a <code>string</code> or a <code>number</code>. The following code will pass the type checker:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type ProductCode = number | string;

const productCodeA: ProductCode = 'this-works';

const productCodeB: ProductCode = 1024;
</code></pre>
<p>A union type can be created from a union of any valid TypeScript types.</p>

<h3 id="intersections">Intersections</h3>

<p>You can use intersection types to create a completely new type that has all the properties of all the types being intersected together.</p>

<p>For example, imagine you have some common fields that always appear in the response of your API calls, then specific fields for some endpoints:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type StatusResponse = {
  status: number;
  isValid: boolean;
};

type User = {
  name: string;
};

type GetUserResponse = {
  user: User;
};
</code></pre>
<p>In this case, all responses will have <code>status</code> and <code>isValid</code> properties, but only user resonses will have the additional <code>user</code> field. To create the resulting response of a specific API User call using an intersection type, combine both <code>StatusResponse</code> and <code>GetUserResponse</code> types:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type ApiGetUserResponse = StatusResponse &amp; GetUserResponse;
</code></pre>
<p>The type <code>ApiGetUserResponse</code> is going to have all the properties available in <code>StatusResponse</code> and those available in <code>GetUserResponse</code>. This means that data will only pass the type checker if it satisfies all the conditions of both types. The following example will work:</p>
<pre class="code-pre "><code class="code-highlight language-ts">let response: ApiGetUserResponse = {
    status: 200,
    isValid: true,
    user: {
        name: 'Sammy'
    }
}
</code></pre>
<p>Another example would be the type of the rows returned by a database client for a query that contains joins. You would be able to use an intersection type to specify the result of such a query:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRoleRow = {
  role: string;
}

type UserRow = {
  name: string;
};

type UserWithRoleRow = UserRow &amp; UserRoleRow;
</code></pre>
<p>Later, if you used a <code>fetchRowsFromDatabase()</code> function like the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const joinedRows: UserWithRoleRow = fetchRowsFromDatabase()
</code></pre>
<p>The resulting constant <code>joinedRows</code> would have to have a <code>role</code> property and a <code>name</code> property that both held string values in order to pass the type checker.</p>

<h2 id="using-template-strings-types">Using Template Strings Types</h2>

<p>Starting with TypeScript 4.1, it is possible to create types using <a href="https://www.digitalocean.com/community/tutorials/understanding-template-literals-in-javascript">template string</a> types. This will allow you to create types that check specific string formats and add more customization to your TypeScript project.</p>

<p>To create template string types, you use a syntax that is almost the same as what you would use when creating template string literals. But instead of values, you will use other types inside the string template.</p>

<p>Imagine you wanted to create a type that passes all strings that begin with <code>get</code>. You would be able to do that using template string types:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type StringThatStartsWithGet = `get${string}`;

const myString: StringThatStartsWithGet = 'getAbc';
</code></pre>
<p><code>myString</code> will pass the type checker here because the string starts with <code>get</code> then is followed by an additional string.</p>

<p>If you passed an invalid value to your type, like the following <code>invalidStringValue</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type StringThatStartsWithGet = `get${string}`;

const invalidStringValue: StringThatStartsWithGet = 'something';
</code></pre>
<p>The TypeScript Compiler would give you the error <code>2322</code>:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Type '"something"' is not assignable to type '`get${string}`'. (2322)
</code></pre>
<p>Making types with template strings helps you to customize your type to the specific needs of your project. In the next section, you will try out type assertions, which add a type to otherwise untyped data.</p>

<h2 id="using-type-assertions">Using Type Assertions</h2>

<p>The <a href="https://www.digitalocean.com/community/tutorials/how-to-use-basic-types-in-typescript#basic-types-used-in-typescript"><code>any</code> type</a> can be used as the type of any value, which often does not provide the strong typing needed to get the full benefit out of TypeScript. But sometimes you may end up with some variables bound to <code>any</code> that are outside of your control. This will happen if you are using external dependencies that were not written in TypeScript or that do not have <a href="https://www.typescriptlang.org/docs/handbook/declaration-files/publishing.html">type declaration available</a>.</p>

<p>In case you want to make your code type-safe in those scenarios, you can use type assertions, which is a way to change the type of a variable to another type. Type assertions are made possible by adding <code>as <span class="highlight">NewType</span></code> after your variable. This will change the type of the variable to that specified after the <code>as</code> keyword.</p>

<p>Take the following example:</p>
<pre class="code-pre "><code class="code-highlight language-ts">const valueA: any = 'something';

const valueB = valueA as string;
</code></pre>
<p><code>valueA</code> has the type <code>any</code>, but, using the <code>as</code> keyword, this code coerces the <code>valueB</code> to have the type <code>string</code>.</p>

<p><span class='note'><strong>Note:</strong> To assert a variable of <code>TypeA</code> to have the type <code>TypeB</code>, <code>TypeB</code> must be a subtype of <code>TypeA</code>. Almost all TypeScript types, besides <code>never</code>, are a subtype of <code>any</code>, including <code>unknown</code>.<br></span></p>

<h2 id="utility-types">Utility Types</h2>

<p>In the previous sections, you reviewed multiple ways to create custom types out of basic types. But sometimes you do not want to create a completely new type from scratch. There are times when it might be best to use a few properties of an existing type, or even create a new type that has the same shape as another type, but with all the properties set to be optional.</p>

<p>All of this is possible using existing utility types available with TypeScript. This section will cover a few of those utility types; for a full list of all available ones, take a look at the <a href="https://www.typescriptlang.org/docs/handbook/utility-types.html">Utility Types</a> part of the TypeScript handbook.</p>

<p>All utility types are <em>Generic Types</em>, which you can think of as a type that accepts other types as parameters. A Generic type can be identified by being able to pass type parameters to it using the <code>&lt;TypeA, TypeB, ...&gt;</code> syntax.</p>

<h3 id="record-lt-key-value-gt"><code>Record&lt;Key, Value&gt;</code></h3>

<p>The <code>Record</code> utility type can be used to create an indexable type in a cleaner way than using the index signature covered previously.</p>

<p>In your indexable types example, you had the following type:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = {
  [key: string]: any;
};
</code></pre>
<p>You can use the <code>Record</code> utility type instead of an indexable type like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = Record&lt;string, any&gt;;
</code></pre>
<p>The first type parameter of the <code>Record</code> generic is the type of each <code>key</code>. In the following example, all the keys must be strings:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = Record&lt;<span class="highlight">string</span>, any&gt;
</code></pre>
<p>The second type parameter is the type of each <code>value</code> of those keys. The following would allow the values to be <code>any</code>:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type Data = Record&lt;string, <span class="highlight">any</span>&gt;
</code></pre>
<h3 id="omit-lt-type-fields-gt"><code>Omit&lt;Type, Fields&gt;</code></h3>

<p>The <code>Omit</code> utility type is useful to create a new type based on another one, while excluding some properties you do not want in the resulting type.</p>

<p>Imagine you have the following type to represent the type of a user row in a database:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};
</code></pre>
<p>If in your code you are retrieving all the fields but the <code>addressId</code> one, you can use <code>Omit</code> to create a new type without that field:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowWithoutAddressId = <span class="highlight">Omit&lt;UserRow, 'addressId'&gt;</span>;
</code></pre>
<p>The first argument to <code>Omit</code> is the type that you are basing the new type on. The second is the field that you&rsquo;d like to omit.</p>

<p>If you hover over <code>UserRowWithoutAddressId</code> in your code editor, you will find that it has all the properties of the <code>UserRow</code> type but the ones you omitted.</p>

<p>You can pass multiple fields to the second type parameter using a union of strings. Say you also wanted to omit the <code>id</code> field, you could do this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowWithoutIds = Omit&lt;UserRow, <span class="highlight">'id' | 'addressId'</span>&gt;;
</code></pre>
<h3 id="pick-lt-type-fields-gt"><code>Pick&lt;Type, Fields&gt;</code></h3>

<p>The <code>Pick</code> utility type is the exact opposite of the <code>Omit</code> type. Instead of saying the fields you want to omit, you specify the fields you want to use from another type.</p>

<p>Using the same <code>UserRow</code> you used before:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};
</code></pre>
<p>Imagine you need to select only the <code>email</code> key from the database row. You could create such a type using <code>Pick</code> like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowWithEmailOnly = <span class="highlight">Pick&lt;UserRow, 'email'&gt;</span>;
</code></pre>
<p>The first argument to <code>Pick</code> here specifies the type you are basing the new type on. The second is the key that you would like to include.</p>

<p>This would be equivalent to the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRowWithEmailOnly = {
    email: string;
}
</code></pre>
<p>You are also able to pick multiple fields using an union of strings:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowWithEmailOnly = Pick&lt;UserRow, <span class="highlight">'name' | 'email'</span>&gt;;
</code></pre>
<h3 id="partial-lt-type-gt"><code>Partial&lt;Type&gt;</code></h3>

<p>Using the same <code>UserRow</code> example, imagine you want to create a new type that matches the object your database client can use to insert new data into your user table, but with one small detail: Your database has default values for all fields, so you are not required to pass any of them. To do this, you can use a <code>Partial</code> utility type to optionally include all fields of the base type.</p>

<p>Your existing type, <code>UserRow</code>, has all the properties as required:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};
</code></pre>
<p>To create a new type where all properties are optional, you can use the <code>Partial&lt;Type&gt;</code> utility type like the following:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowInsert = <span class="highlight">Partial&lt;UserRow&gt;</span>;
</code></pre>
<p>This is exactly the same as having your <code>UserRowInsert</code> like this:</p>
<pre class="code-pre "><code class="code-highlight language-ts">type UserRow = {
  id: number;
  name: string;
  email: string;
  addressId: string;
};

type UserRowInsert = {
  id?: number | undefined;
  name?: string | undefined;
  email?: string | undefined;
  addressId?: string | undefined;
};
</code></pre>
<p>Utility types are a great resource to have, because they provide a faster way to build up types than creating them from the basic types in TypeScript.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Creating your own custom types to represent the data structures used in your own code can provide a flexible and useful TypeScript solution for your project. In addition to increasing the type-safety of your own code as a whole, having your own business objects typed as data structures in the code will increase the overall documentation of the code-base and improve your own developer experience when working with teammates on the same code-base.</p>

<p>For more tutorials on TypeScript, check out our <a href="https://www.digitalocean.com/community/tags/typescript">TypeScript Topic page</a>.</p>
