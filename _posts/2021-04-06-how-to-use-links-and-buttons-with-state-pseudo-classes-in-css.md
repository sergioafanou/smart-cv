---
title : "How To Use Links and Buttons with State Pseudo-Classes in CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-links-and-buttons-with-state-pseudo-classes-in-css
image: "https://sergio.afanou.com/assets/images/image-midres-14.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/diversity-in-tech">Diversity in Tech Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>An important part of web development is providing feedback when a user interacts with an element. This interaction is accomplished through changing <em>state</em>, which indicates how the user is using or has used a given element on the page. In CSS, there are special variations on selectors called a <em>pseudo-class</em>, which allow state changes to initiate style changes.</p>

<p>In this tutorial, you will use the <code>:hover</code>, <code>:active</code>, and <code>:focus</code> user actions and the <code>:visited</code> location pseudo-classes. You will use <code>&lt;a&gt;</code> and <code>&lt;button&gt;</code> as the interactive elements in the tutorial. Both of these elements have similar interactive states and are functionally identical to the user. From a development standpoint, <code>&lt;a&gt;</code> elements are specifically for interacting with URLs, whereas the <code>&lt;button&gt;</code> element is used to trigger forms or <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">JavaScript</a> functions. In addition to working with these four distinct states, you will use the <code>transition</code> property to animate the styles between these states of interactivity.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li>An understanding of CSS’s <a href="https://www.digitalocean.com/community/tutorials/how-to-apply-css-styles-to-html-with-cascade-and-specificity">cascade and specificity</a> features and the <a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-the-box-model-in-css">box model</a>.</li>
<li>Knowledge of <a href="https://www.digitalocean.com/community/tutorials/how-to-select-html-elements-to-style-with-css">type selectors, combinator selectors, and selector groups</a>.</li>
<li>Familiarity with <a href="https://www.digitalocean.com/community/tutorials/how-to-lay-out-text-with-css">text layout</a> and <a href="https://www.digitalocean.com/community/tutorials/how-to-use-color-values-with-css">color properties</a>.</li>
<li>An empty HTML file saved on your local machine as <code>index.html</code> that you can access from your text editor and web browser of choice. To get started, check out our <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-html-project">How To Set Up Your HTML Project</a> tutorial, and follow <a href="https://www.digitalocean.com/community/tutorials/how-to-use-and-understand-html-elements#how-to-view-an-offline-html-file-in-your-browser">How To Use and Understand HTML Elements</a> for instructions on how to view your HTML in your browser. If you’re new to HTML, try out the whole <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">How To Build a Website in HTML</a> series.</li>
<li>An empty CSS file called <code>styles.css</code> and an empty HTML file called <code>index.html</code>, both saved on your local machine in the same directory. </li>
</ul>

<h2 id="setting-up-the-initial-html-and-css">Setting Up the Initial HTML and CSS</h2>

<p>To begin working with links and buttons, you will first set up the HTML and CSS needed as a foundation for the tutorial. In this section, you will write out all the necessary HTML and some initial CSS styles that will handle layout and start the visual aesthetic. </p>

<p>To begin, open <code>index.html</code> in your text editor. Then, add the following highlighted HTML to the file:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html"><span class="highlight">&lt;!doctype html&gt;</span>
<span class="highlight">&lt;html&gt;</span>
  <span class="highlight">&lt;head&gt;</span>
  <span class="highlight">&lt;/head&gt;</span>
  <span class="highlight">&lt;body&gt;</span>
  <span class="highlight">&lt;/body&gt;</span>
<span class="highlight">&lt;/html&gt;</span>
</code></pre>
<p>Next, go to the <code>&lt;head&gt;</code> tag and add a <code>&lt;meta&gt;</code> tag to define the character set for the HTML file. Then set the title of the page, add a <code>&lt;meta&gt;</code> tag defining how mobile devices should render the page, and finally load the CSS file with a <code>&lt;link&gt;</code> tag. These additions are highlighted in the following code block. You will encounter this highlighting method throughout the tutorial as code is added and changed:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
  &lt;head&gt;
    <span class="highlight">&lt;meta charset="utf-8"&gt;</span>
    <span class="highlight">&lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;</span>
    <span class="highlight">&lt;title&gt;Link and Buttons with State&lt;/title&gt;</span>
    <span class="highlight">&lt;link rel="stylesheet" href="styles.css"&gt;</span>
  &lt;/head&gt;
  &lt;body&gt;
  &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>After adding the <code>&lt;head&gt;</code> content, next move to the <code>&lt;body&gt;</code> element where content is added to make an informational block with <code>&lt;a&gt;</code> and <code>&lt;button&gt;</code> tags as interactive elements. Add the highlighted section from this code block to your <code>index.html</code> file in your text editor:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
  &lt;head&gt;
    &lt;meta charset="utf-8"&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;
    &lt;title&gt;Link and Buttons with State&lt;/title&gt;
    &lt;link rel="stylesheet" href="styles.css"&gt;
  &lt;/head&gt;
  &lt;body&gt;
    <span class="highlight">&lt;section&gt;</span>
      <span class="highlight">&lt;header&gt;</span>
        <span class="highlight">&lt;button class="link" type="button"&gt;</span>
          <span class="highlight">Close Window</span>
        <span class="highlight">&lt;/button&gt;</span>
      <span class="highlight">&lt;/header&gt;</span>
      <span class="highlight">&lt;div&gt;</span>
        <span class="highlight">&lt;p&gt;</span>
          <span class="highlight">This is a demo paragraph for some demo content for a tutorial. By reading this text you are now informed that this is text for the demo in &lt;a href="https://do.co/tutorials"&gt;this tutorial&lt;/a&gt;. This means you can agree to use this content for the demo or not. If you wish to continue with this tutorial demo content, please select the appropriately styled interactive element below.</span>
        <span class="highlight">&lt;/p&gt;</span>
        <span class="highlight">&lt;div&gt;</span>
          <span class="highlight">&lt;button class="button" type="button"&gt;</span>
            <span class="highlight">Yes, Please</span>
          <span class="highlight">&lt;/button&gt;</span>
          <span class="highlight">&lt;a class="button" href="#"&gt;</span>
            <span class="highlight">No, Thank you</span>
          <span class="highlight">&lt;/a&gt;</span>
        <span class="highlight">&lt;/div&gt;</span>
      <span class="highlight">&lt;/div&gt;</span>
    <span class="highlight">&lt;/section&gt;</span>
  &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>Save your changes to <code>index.html</code> and open your web browser to open the <code>index.html</code> file there. The content of the page will appear in a black serif font on a white background. The <code>&lt;button&gt;</code> element styles will vary based on your browser and operating system, but the page will look similar to the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/1.png" alt="Black serif text on a white background with two blue underlined links and two interactive butons."></p>

<p>Next, create a file called <code>styles.css</code> in the same directory as the <code>index.html</code> file. The following styles in the code block will set up a base style for the container holding the <code>&lt;button&gt;</code> and <code>&lt;a&gt;</code> elements you will be styling throughout the remainder of the tutorial. Add the following code to your <code>styles.css</code> file:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css"><span class="highlight">body {</span>
  <span class="highlight">background-color: #454545;</span>
  <span class="highlight">color: #333;</span>
  <span class="highlight">font-family: sans-serif;</span>
  <span class="highlight">line-height: 1.5;</span>
<span class="highlight">}</span>

<span class="highlight">section {</span>
  <span class="highlight">margin: 2rem auto;</span>
  <span class="highlight">width: 90%;</span>
  <span class="highlight">max-width: 50rem;</span>
  <span class="highlight">box-sizing: border-box;</span>
  <span class="highlight">padding: 2rem;</span>
  <span class="highlight">border-radius: 2rem;</span>
  <span class="highlight">border: 0.25rem solid #777;</span>
  <span class="highlight">background-color: white;</span>
<span class="highlight">}</span>

<span class="highlight">header {</span>
  <span class="highlight">text-align: right;</span>
<span class="highlight">}</span>
</code></pre>
<p>The CSS in this code block adds styles for three portions of the demo content area. The first is the <code>body</code> selector, which applies a dark gray background and then defines default font properties. The second selector focuses on the <code>&lt;section&gt;</code> element, which is the primary container for the demo content and creates a white block with big, rounded corners and a maximum width so it will only grow to a specified amount. Lastly, the <code>header</code> element selector sets the text alignment to the right so the <strong>Close Window</strong> button is to the far top right corner of the <code>&lt;section&gt;</code> container.</p>

<p>If you’d like to learn more about how to use CSS element selectors, check out <a href="https://www.digitalocean.com/community/tutorials/how-to-select-html-elements-to-style-with-css">How To Select HTML Elements to Style with CSS</a>.</p>

<p>Save your changes to the <code>styles.css</code> file and reload the <code>index.html</code> file in your browser. The page styles will have transformed from the browser defaults to a customized style, as demonstrated in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/2.png" alt="Black sans serif text in a white container with rounded corners with two blue underlined links and two interactive butons."></p>

<p>You have set up your HTML and loaded in the base styles for the contents of the page. Next you will create a custom default link style and provide a way for that default style to be applied to <code>button</code> elements. </p>

<h2 id="creating-styles-for-text-links">Creating Styles for Text Links</h2>

<p>In this section, you will create a custom default link style by setting a new color. Then you will remove default button styles in order to make the button look like a default link. <code>a</code> and <code>button</code> have distinct purposes, but website users interact with both in a similar way. Sometimes, these two elements need to appear similar to help explain an interaction to the user or match a design aesthetic.</p>

<p>For the first part of this section, you will set up the default link style that will be used on the generic <code>&lt;a&gt;</code> element and a class of <code>.link</code>, which can then apply the generic link styles to the <code>&lt;button&gt;</code> element. Start by creating a group selector containing an <code>a</code> element selector and a <code>.link</code> class selector with a <code>color</code> property and value of <code>#25a</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">a,</span>
<span class="highlight">.link {</span>
  <span class="highlight">color: #25a;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save your changes to <code>styles.css</code> and open <code>index.html</code> in your browser. The <code>&lt;a&gt;</code> elements on the page are now a deeper blue color than the browser default link blue. Also, the <code>&lt;button&gt;</code> element with the <code>class="link"</code> has the same blue color text in the button. The appearance of change in the browser is demonstrated in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/3.png" alt="Black sans serif text in a white container with rounded corners with two blue underlined links and two interactive buttons."></p>

<p>Next, begin removing the default appearance of a <code>button</code>. By default, browsers add a lot of customization to the look and feel of <code>&lt;button&gt;</code> elements to make sure that they are distinguishable as interactive elements. To remove the extra styling that browsers add to these elements, return to <code>styles.css</code> in your text editor, create a <code>button</code> element selector, and add two similar properties as shown in the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a,
.link {
  color: #25a;
}

<span class="highlight">button {</span>
  <span class="highlight">-webkit-appearance: none;</span>
  <span class="highlight">appearance: none;</span>
<span class="highlight">}</span>
</code></pre>
<p>The first property is <code>-webkit-appearance: none;</code>, which is known as a <em>vendor prefix</em> property. The <code>-webkit-</code> position of the property is only read by browsers built on a derivative of the WebKit browser engine, such as <a href="https://www.apple.com/safari/">Safari</a> or <a href="https://www.google.com/chrome/">Chrome</a>. Some versions of these browsers don’t support the <code>appearance</code> property by themselves and need the <code>-webkit-</code> prefix in order to work.</p>

<p>Vendor prefix usage is on the decline, but still occurs. It is important to put any vendor prefixed properties before the non-prefixed to avoid overriding properties on browsers that support both the prefixed and non-prefixed variants.</p>

<p>Save your changes to <code>styles.css</code> and refresh <code>index.html</code> in your browser. The <code>button</code> element will not lose all its styles; instead, it will become simplified to default styles expected of the web specification. The following image demonstrates how this will appear in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/4.png" alt="Black sans serif text in a white container with rounded corners with two blue underlined links and two interactive buttons."></p>

<p>To remove the remaining default styles of the button, you will need to add several more properties. Return to your <code>styles.css</code> file in your text editor and go to the <code>button</code> selector. Next, you will add the properties to remove the <code>background-color</code>, <code>border</code>, <code>margin</code>, and <code>padding</code>. Then you&rsquo;ll remove the properties for the <code>button</code> element, <code>color</code>, <code>font</code>, and <code>text-align</code>, to <code>inherit</code> the value of the page. </p>

<p>The following code block demonstrates how to set this up:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
button {
  -webkit-appearance: none;
  appearance: none;
  <span class="highlight">background-color: transparent;</span>
  <span class="highlight">border: none;</span>
  <span class="highlight">margin: 0;</span>
  <span class="highlight">padding: 0;</span>
  <span class="highlight">color: inherit;</span>
  <span class="highlight">font: inherit;</span>
  <span class="highlight">text-align: center;</span>
}
</code></pre>
<p>Save these changes to <code>styles.css</code> and refresh <code>index.html</code> in your web browser. Both buttons have now lost their default styles, with the <strong>Close Window</strong> button being closer in styles to the link. The <strong>Yes, Please</strong> button styles will be addressed in the next section. The following image demonstrates how this will appear in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/5.png" alt="Black sans serif text in a white container with rounded corners with two blue underlined links and two interactive buttons that appear as plain text."></p>

<p>To finish making the <strong>Close Window</strong> button look like a text link, open <code>styles.css</code> in your text editor. Below the <code>a, .link</code> group selector, add a new class selector for only the <code>.link</code> class. Add to that a <code>text-decoration</code> property with a value of <code>underline</code>. Then add a property called <code>cursor</code>, which defines how the mouse cursor appears when on that element, and set the value to <code>pointer</code>. The <code>pointer</code> value styles cursor is the hand-style that appears over a link by default:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a,
.link {
...
}

<span class="highlight">.link {</span>
  <span class="highlight">text-decoration: underline;</span>
  <span class="highlight">cursor: pointer;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>Save these changes to your <code>styles.css</code> file and then return to your browser and refresh <code>index.html</code>. The <strong>Close Window</strong> button will now appear and behave in a similar manner to the generic <code>&lt;a&gt;</code> element styles. The following animation demonstrates how the cursor changes when moving over the <strong>Close Window</strong> button:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/6.png" alt="A button styled to look like a defaul link with blue text and underlined."></p>

<p>In this section, you created a custom default style for the <code>&lt;a&gt;</code> element and created a <code>.link</code> class to apply link styling to a <code>&lt;button&gt;</code> element. In the next section, you will create a custom, button-like style that can be applied to both <code>&lt;button&gt;</code> and <code>&lt;a&gt;</code> elements.</p>

<h2 id="creating-styles-for-a-button">Creating Styles for a Button</h2>

<p>Next, you will create a custom, button-like style with a class selector so the styles can be used on either a <code>&lt;button&gt;</code> or an <code>&lt;a&gt;</code> element. Unlike <code>&lt;a&gt;</code> elements, which are used throughout text content, the <code>&lt;button&gt;</code> element has a more intentional purpose. This makes it less necessary to create generic styles for the <code>&lt;button&gt;</code> element, and instead allows you to always add a <code>class</code> attribute.</p>

<p>Start by opening up <code>styles.css</code> in your text editor. Create a new class selector called <code>.button</code>. The styles here will redefine many of the properties that were reset in the previous step on the <code>button</code> element selector. You will add color to the text with the <code>color</code> property, fill the shape with the <code>background-color</code> property, then provide some definition with the <code>border</code> property. Afterward you will give a rounded corner to the button with the <code>border-radius</code> property. Finally, you will use the <code>padding</code> shorthand to give space above and below the text, then double that amount on the left and right.</p>

<p>The following code block contains these values:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">.button {</span>
  <span class="highlight">color: #25a;</span>
  <span class="highlight">background-color: #cef;</span>
  <span class="highlight">border: 1px solid #67c;</span>
  <span class="highlight">border-radius: 0.5rem;</span>
  <span class="highlight">padding: 0.75rem 1.5rem;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to your browser to refresh the <code>index.html</code> file. The look of the <strong>Yes, Please</strong> and <strong>No, Thank you</strong> buttons will change to match the properties. The text is the same blue as the default <code>a</code> style, with a much lighter blue in the background, and another deep blue for the border. The next image demonstrates how this will appear in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/7.png" alt="Text with two buttons below with one button taller than the other. The shorter button has underlined text."></p>

<p>There is a noticeable difference in size between the two buttons. Since the <strong>No, Thank you</strong> button is using an <code>&lt;a&gt;</code> element instead of a <code>&lt;button&gt;</code>, there are a few additional properties that need to be added to the <code>.button</code> selector to equalize the defaults between these two elements.</p>

<p>Return to <code>styles.css</code> in your text editor and go to the <code>.button</code> class selector. To begin, add a <code>display: inline-block</code>, which is the default style on <code>button</code> elements. Next, add a <code>text-decoration: none</code> to remove the underline from the <code>&lt;a&gt;</code> element. As you did with the <code>.link</code> selector, add a <code>cursor: pointer</code> to the selector to get the pointing hand icon when a mouse cursor is over the element. Lastly, add a <code>vertical-align: bottom</code> rule. This last property isn’t necessary for all browsers, but it defines where the bottom of elements are positioned on a line:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
.button {
  color: #25a;
  background-color: #cef;
  border: 1px solid #67c;
  border-radius: 0.5rem;
  padding: 0.75rem 1.5rem;
  <span class="highlight">display: inline-block;</span>
  <span class="highlight">text-decoration: none;</span>
  <span class="highlight">cursor: pointer;</span>
  <span class="highlight">vertical-align: bottom;</span>
}
</code></pre>
<p>Save these additions to <code>styles.css</code> and then refresh <code>index.html</code> in your browser. The two buttons are now equivalent in visual appearance and have borrowed default attributes from one another, so they are perceived to have a similar interaction.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/8.png" alt="Text with two light blue button of equal height with dark blue text and a dark blue thin border."></p>

<p>You created a custom class selector to style both <code>&lt;button&gt;</code> and <code>&lt;a&gt;</code> elements with a button-like style in this section. Next, you will create a conditional style when a mouse cursor is on top of the interactive elements.</p>

<h2 id="applying-the-hover-pseudo-class-to-the-link-and-button">Applying the <code>:hover</code> Pseudo-Class to the Link and Button</h2>

<p>Now you will use the <code>:hover</code> pseudo-class to create an alternative style that displays when the cursor is on the element. Pseudo-classes are a special group of conditions that are defined by a colon (<code>:</code>) and the name of the condition appended to the selector. For example, the <code>a</code> element selector with a hover pseudo-class becomes <code>a:hover</code>.</p>

<p>Open <code>styles.css</code> in your text editor. Below the group selector for <code>a, .link</code>, add a new selector for the hover state by appending each selector with the <code>:hover</code> pseudo-class: <code>a:hover, .link:hover</code>. Then, to make a noticeable change when the cursor hovers over the element, add a  <code>color</code> property with a value of <code>#b1b</code>, which is a dark pink color:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a,
.link {
  ...
}

<span class="highlight">a:hover,</span>
<span class="highlight">.link:hover {</span>
  <span class="highlight">color: #b1b;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>Save the changes to your <code>styles.css</code> file and refresh <code>index.html</code> in your browser. Hover over either the <strong>this tutorial</strong> link or the <strong>Close Window</strong> button to initiate the color change on hover. The following image shows what the hover state looks like when the cursor is over the <strong>this tutorial</strong> link.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/9.png" alt="A paragraph of text with a link with a hand cursor icon over the link. The link text is a dark pink color with an underline."></p>

<p>Next, to add a hover state to the <code>.button</code> elements, return to <code>styles.css</code> in your text editor. Below the <code>.button</code> class selector, add a <code>.button:hover</code> selector to create styles specifically for hover interaction. Next, within the selector, add color properties that will change the button appearance when the cursor is on the buttons. Set a <code>color</code> property to <code>white</code>, then create a <code>background-color</code> and a <code>border-color</code> with both properties set to <code>#25a</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
.button {
  ...
}

<span class="highlight">.button:hover {</span>
  <span class="highlight">color: white;</span>
  <span class="highlight">background-color: #25a;</span>
  <span class="highlight">border-color: #25a;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save these changes to your <code>styles.css</code> file and return to your browser to refresh the <code>index.html</code> file. Now, take your mouse cursor and hover over one of the two buttons on the bottom. The styles change from the light blue background with a deep blue text and border to a deep blue background with white text. The following image shows what the hover style looks like when the mouse cursor is over the <strong>Yes, Please</strong> button.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/10.png" alt="Two buttons below a paragraph of text. One button has a hand pointer cursor over it and is a dark blue with white text. The other button is light blue with dark blue text."></p>

<p>You used the <code>:hover</code> pseudo-class in this section to create style changes to the element based on the cursor being positioned on top of the element. In the next section, you will apply this same concept to a condition when a keyboard is used to navigate through the page.</p>

<h2 id="applying-the-focus-pseudo-class">Applying the <code>:focus</code> Pseudo-Class</h2>

<p>Instead of using a mouse or a touch screen, website visitors will sometimes use their keyboard to navigate and interact with elements of a page. This is most often done by using the <code>TAB</code> key, which will cycle through the interactive elements on the page. The default style uses the <code>outline</code> property to give a visual indicator that the element has focus. This style can be customized by using the <code>:focus</code> pseudo-class to apply property values for this situation.</p>

<p>To begin working with the focus state of the elements on the page, open your <code>styles.css</code> file in your text editor. Start with a new selector below the <code>a:hover, .link:hover</code> group selector with a new group selector for the focus state: <code>a:focus, .link:focus</code>.</p>

<p>The most important part of customizing the focus state is to make sure it is noticeably different from the default state. Here, you will make the <code>:focus</code> pseudo-class styles have black text with a gold background:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a:hover,
.link:hover {
  ...
}

<span class="highlight">a:focus,</span>
<span class="highlight">.link:focus {</span>
  <span class="highlight">color: black;</span>
  <span class="highlight">outline: 2px solid gold;</span>
  <span class="highlight">background-color: gold;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>In this case, you set the <code>color</code> property to <code>black</code> and the <code>background-color</code> property to <code>gold</code>. You also used the <code>outline</code> property, which added some gold color around the edges of the text, outside where the <code>background-color</code> property can reach. </p>

<p>The <code>outline</code> property works similar to the <code>border</code> shorthand property, as it accepts a width, a style, and a color. However, unlike the <code>border</code> properties, <code>outline</code> always goes around the whole element and can not be set to a specific side. Also, unlike <code>border</code>, the <code>outline</code> does not affect the box-model; the shape is only applied visually and does not change the flow of content.</p>

<p>Save your changes to <code>styles.css</code> and refresh <code>index.html</code> in your web browser. Begin pressing the <code>TAB</code> key until the browser brings focus to the <strong>Close Window</strong> and <strong>this tutorial</strong> elements highlight with a gold background. The following image shows what the <strong>this tutorial</strong> link looks like in the browser when it has focus:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/13.png" alt="A paragraph of text with a link. The link text is black color with an underline and a yellow background."></p>

<p>Next, to apply a similar custom focus style to the <code>.button</code> class elements, begin by creating a <code>.button:focus</code> class and pseudo-class selector. Since the <code>.button</code> element is already using a <code>border</code>, you will use that to indicate focus, and so remove the <code>outline</code> default by setting the property to have a value of <code>none</code>. Like the link before, the <code>color</code> property will be set to <code>black</code> and the <code>background-color</code> property will be set to <code>gold</code>. Last, set the <code>border-color</code> property to have a value of <code>black</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
.button:hover {
  ...
}

<span class="highlight">.button:focus {</span>
  <span class="highlight">outline: none;</span>
  <span class="highlight">color: black;</span>
  <span class="highlight">background-color: gold;</span>
  <span class="highlight">border-color: black;</span>
<span class="highlight">}</span>
</code></pre>
<p>Be sure to save your additions to <code>styles.css</code> and then return to you browser to refresh your <code>index.html</code> file. Again, using the <code>TAB</code> key, cycle through the keyboard-focusable elements on the page until you reach the <code>.button</code> elements. They will now light up with a gold background and black text with a black border. The following image demonstrates how the <strong>Yes, Please</strong> button appears in the browser when focused:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/11.png" alt="Two buttons below a paragraph of text. One button is focused with black text and black border with a yellow background. The other button is light blue with dark blue text."></p>

<p>In this section, you used the <code>:focus</code> pseudo-class to create custom styles that appear when the website visitor uses their keyboard to navigate the page. In the next section, you will use the <code>:active</code> pseudo-class to indicate when a user is interacting with an element via a mouse click or a keypress.</p>

<h2 id="applying-the-active-pseudo-class">Applying the <code>:active</code> Pseudo-Class</h2>

<p>The next pseudo-class that you will work with is the <code>:active</code> state of an interactive element. The active state is the state at which an element is interacted with, typically via a mouse down or mouse click action. This provides the opportunity to give the visitor a clear state to indicate a successful mouse click and button press.</p>

<p>To begin working with the <code>:active</code> pseudo-class, open <code>styles.css</code> in your text editor. Following the group selector block for <code>a:focus, .link:focus</code>, add a new selector block with the group selector <code>a:active, .link:active</code>. Give <code>color</code> a value of <code>#808</code>, which will create a darker pink than the <code>:hover</code> state. </p>

<p>Note that some browsers will mix the styles of a <code>:focus</code> pseudo-class and an <code>:active</code> pseudo-class. To prevent this, you will need to remove the <code>outline</code> and <code>background-color</code> properties by setting them to <code>none</code> and <code>transparent</code>, respectively:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a:focus,
.link:focus {
  ...
}

<span class="highlight">a:active,</span>
<span class="highlight">.link:active {</span>
  <span class="highlight">color: #808;</span>
  <span class="highlight">outline: none;</span>
  <span class="highlight">background-color: transparent;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>Save the addition of the <code>:active</code> pseudo-class to your <code>styles.css</code> file, then reload <code>index.html</code> in your web browser. The following animation shows how the <code>:active</code> state changes from the pink to darker pink as the mouse is clicked while over the <strong>this tutorial</strong> link.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/12.gif" alt="An animation involving a paragraph of text with a link with a hand cursor icon over the link. The link text is underlined and is alternating between a dark pink and a darker pink."></p>

<p>Next, to apply an active state to <code>.button</code>, return to <code>styles.css</code> in your text editor. Add a <code>.button:active</code> pseudo-class selector and apply styles that are dark variants of the <code>:hover</code> styles. For the <code>color</code> property, set the value to a light gray with <code>#ddd</code>. For both the <code>background-color</code> and <code>border-color</code> properties, set the value to a dark blue with a value of <code>#127</code>. The highlighted sections of the following code block demonstrate how this is written:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
.button:focus {
  ...
}

<span class="highlight">.button:active {</span>
  <span class="highlight">color: #ddd;</span>
  <span class="highlight">background-color: #127;</span>
  <span class="highlight">border-color: #127;</span>
<span class="highlight">}</span>
</code></pre>
<p>Be sure to save these changes to <code>styles.css</code>, then return to your browser to refresh <code>index.html</code>. Hover your mouse cursor over one of the two buttons in the bottom of the content and then click down. The button will change from a light blue background with blue text and border to a full blue button with white text when hovered, then to a dark blue button with light gray text when clicked. The following animation demonstrates how this change from the <code>:hover</code> to the <code>:active</code> state appears as the mouse button is clicked:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/14.gif" alt="An animation of cursor pressing a button turning the button from blue with white text to a dark blue with light gray text."></p>

<p>You created a visual indicator of a mouse button press event by using the <code>:active</code> pseudo-class to change the styles when that event occurs. In the next section, you will use the <code>:visited</code> pseudo-class to provide an indicator of which <code>&lt;a&gt;</code> elements with a <code>href</code> attribute have visited that link.</p>

<h2 id="applying-the-visited-pseudo-class">Applying the <code>:visited</code> Pseudo-Class</h2>

<p>The <code>:visited</code> pseudo-class is unlike the previous pseudo-classes covered in this tutorial. Where the previous pseudo-classes involve an active interaction of the user with the element, the <code>:visited</code> pseudo-class indicates that an element was previously interacted with. Specifically, the <code>:visited</code> pseudo-class indicates which <code>&lt;a&gt;</code> with an <code>href</code> attribute are present in the browser history, meaning those links have been visited.</p>

<p>To create a customized <code>:visited</code> indicator on the text links, open your <code>styles.css</code> file in your text editor. Below the <code>a:active, .link:active</code> group selector, add a new group selector targeting a <code>a:visited, .link:visted</code> group selector. The default <code>:visited</code> link style is commonly a purple color. For the purposes of the demo, the <code>:visited</code> color will be a dark green.</p>

<p>Add a <code>color</code> property with a value of <code>#080</code>, as shown in the following highlighted code:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a:active,
.link:active {
  ...
}

<span class="highlight">a:visited,</span>
<span class="highlight">.link:visited {</span>
  <span class="highlight">color: #080;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>Save this change to the <code>styles.css</code> file and then open <code>index.html</code> in your web browser. If you haven’t, go ahead and click the <strong>this tutorial</strong> and <strong>No, Thank you</strong> <code>&lt;a&gt;</code> element links. Both of these links will have a text color of dark green, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/15.png" alt="Pragraph of text with a visited link underlined and green and two buttons below. One of the button’s text is green instead of dark blue."></p>

<p>Now, the green text in the button distracts from the purpose of the <strong>No, thank you</strong> button. Additionally, when the visited links are interacted with again with a <code>:hover</code> or <code>:active</code> state, the dark green remains instead of the defined colors for each respective state.</p>

<p>To address these scenarios, return to your <code>styles.css</code> file in your text editor. First, append the <code>a:hover, .link:hover</code> group selector with the added scenario of a <code>:visited</code> element that has an active <code>:hover</code> state by adding <code>a:visited:hover, .link:visited:hover</code>. Likewise, expand the <code>a:active, .link:active</code> selector block with <code>a:visited:active, .link:visited:active</code>. Lastly, the desired visited state for the <code>.button</code> element is to be styled the same as the default state. As such, the <code>.button</code> selector needs to become a group selector of <code>.button, .button:visited</code>, so the visited state appears the same as the default state:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a:hover,
.link:hover,
<span class="highlight">a:visited:hover,</span>
<span class="highlight">.link:visited:hover</span> {
  color: #b1b;
}
...
a:active,
.link:active,
<span class="highlight">a:visited:active,</span>
<span class="highlight">.link:visited:active</span> {
  color: #808;
}

a:visited,
.link:visited {
  color: #080;
}
...
.button,
<span class="highlight">.button:visited</span> {
  ...
}

.button:hover,
<span class="highlight">.button:visited:hover</span> {
  color: white;
  background-color: #25a;
  border-color: #25a;
}
...
</code></pre>
<p>Save your changes to the <code>styles.css</code> file and reload <code>index.html</code> in the web browser. The text default <code>:visited</code> link now appears in the desired dark green color, while the button-style link retains the button look. The following image demonstrates how this will appear in the browser.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/16.png" alt="Paragraph of text with a visited link underlined and green and two similarly-styled buttons below."></p>

<p>You used the <code>:visited</code> pseudo-class to create styles specific to when a link is present in the browser history, indicating to the user links that have been visited. This section concludes the work on pseudo-classes and using them to define specific styles for a given state. In the final section of this tutorial, you will use the <code>transition</code> property to create a seamless animation between these different pseudo-class states.</p>

<h2 id="using-transition-to-animate-between-states">Using <code>transition</code> to Animate Between States</h2>

<p>When working with states of elements, a shift in styles between the states can be abrupt. The <code>transition</code> property is used to blend and animate the styles from one state to the next state to avoid this abruptness. The <code>transition</code> property is a shorthand property that combines the <code>transition-property</code>, <code>transition-duration</code>, and <code>transition-timing-function</code> properties.</p>

<p>The <code>transition-property</code> can be any property that has a value calculated between two given values. Colors are included in this, as they are numerical values, even when a color name is used. The <code>transition-duration</code> property is a numeric value for how long the transition should occur. The value for the duration is often represented in seconds, with the <code>s</code> unit, or milliseconds, with the <code>ms</code> unit. Lastly, the <code>transition-timing-function</code> controls how the animation will play out over time, enabling you to make subtle changes to enhance the animation.</p>

<p>To begin working with the <code>transition</code> property, open your <code>styles.css</code> file and go to the <code>a, .link</code> group selector and the <code>.button, .button:visited</code> group selector. Add a <code>transition</code> property with a value of <code>all 0.5s linear</code>. The <code>all</code> is the value for the <code>transition-property</code>, which tells the browser to animate all the properties that change between the states. The <code>0.5s</code> is the <code>transition-duration</code> value and equates half a second; this can also be represented as <code>500ms</code>. Lastly, the <code>linear</code> position is the <code>transition-timing-function</code> value, which tells the browser to move from one value to the next in a constant increment throughout the duration:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
a,
.link {
  ...
  <span class="highlight">transition: all 0.5s linear;</span>
}
...
.button,
.button:visited {
  ...
  <span class="highlight">transition: all 0.5s linear;</span>
}
</code></pre>
<p>Save your changes to <code>styles.css</code> and then open <code>index.html</code> in your web browser. Once the page loads, begin interacting with the link and button elements and pay attention to how the styles animate between the different states. The following animation shows the button style transitioning from the default state to the <code>:hover</code> pseudo-class state:</p>

<p><img src="https://assets.digitalocean.com/articles/67710/17.gif" alt="Animation of a cursor hovering a button and the button transitions styles from a light blue button with blue text to a blue button with white text."></p>

<p>To make the animations feel more snappy and natural, return to your <code>styles.css</code> file and adjust the <code>transition</code> property values. For the <code>a,.link</code> group selector, change the duration from <code>0.5s</code> to <code>250ms</code>, which is half the duration compared to what it was before. Then change the <code>linear</code> timing function value to <code>ease-in-out</code>. This will create an animation that starts off slow, speeds up in the middle, and slows down to the end. Then, for the <code>.button,.button:visited</code> group selector, change the duration to a quicker <code>180ms</code> and set the timing function to the same <code>ease-in-out</code> value as the link:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">
...
a,
.link {
  ...
  transition: all <span class="highlight">250ms ease-in-out</span>;
}
...
.button,
.button:visited {
  ...
  transition: all <span class="highlight">180ms ease-in-out</span>;
}
</code></pre>
<p>Save these changes to your <code>styles.css</code> file and then refresh the <code>index.html</code> page in your web browser. The transition animations between states will still animate, but are now much quicker and feel faster, too. With the <code>transition</code> property, it is important to play around with the values to find an animation that fits the design. The following animation demonstrates the faster transition of the button from the default state to the <code>:hover</code> state to the <code>:active</code> state.</p>

<p><img src="https://assets.digitalocean.com/articles/67710/18.gif" alt="Animation of a cursor hovering a button and the button transitions styles from a light blue button with blue text to a blue button with white text. Then the cursor moves and hovers a green underlined link and link fades to a pink color."></p>

<p>You have now created an animation between states. The <code>transition</code> property helps make changes between states more engaging and fun.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Providing clear differences between interactive element states is a valuable asset to a website. States help communicate important concepts to the website’s visitor by providing visual feedback to an interaction. </p>

<p>In this tutorial, you have successfully used the primary state pseudo-classes to create multiple styles for different interactive elements. You also learned that there are different purposes behind the <code>&lt;button&gt;</code> and <code>&lt;a&gt;</code> elements, but visually they can communicate similar concepts. Lastly, you used the <code>transition</code> property to provide smooth animations between these states to create more engaging interactive elements. It is important to keep these four states in mind when creating a website so the visitor is given this important interactive feedback.</p>

<p>If you would like to read more CSS tutorials, try out the other tutorials in the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-style-html-with-css">How To Style HTML with CSS series</a>.</p>
