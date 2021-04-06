---
title : "How To Lay Out Text with CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-lay-out-text-with-css
image: "https://sergio.afanou.com/assets/images/image-midres-31.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/diversity-in-tech">Diversity in Tech Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>The way text is presented on a web page determines how legible the content is and how willing the reader is to read the content. Web <em>typesetting</em>, the art of laying out text, is about controlling the content to present the reader with a pleasant and efficient reading experience.</p>

<p>This tutorial will teach you how to use the CSS properties that are most effective at laying out text content. You will use properties like <code>line-height</code>, <code>letter-spacing</code>, and <code>text-transform</code> with demo content to optimize for reading and define a content hierarchy by giving headings prominence. These concepts and approaches will help you better present content on your websites.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li>An understanding of CSS’s cascade and specificity features, which you can get by reading <a href="https://www.digitalocean.com/community/tutorials/how-to-apply-css-styles-to-html-with-cascade-and-specificity">How To Apply CSS Styles to HTML with Cascade and Specificity</a>.</li>
<li>Knowledge of type selectors, combinator selectors, and selector groups, which you can find in <a href="https://www.digitalocean.com/community/tutorials/how-to-select-html-elements-to-style-with-css">How To Select HTML Elements to Style with CSS</a>.</li>
<li>An empty HTML file saved on your local machine as <code>index.html</code> that you can access from your text editor and web browser of choice. To get started, check out our <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-html-project">How To Set Up Your HTML Project</a> tutorial, and follow <a href="https://www.digitalocean.com/community/tutorials/how-to-use-and-understand-html-elements#how-to-view-an-offline-html-file-in-your-browser">How To Use and Understand HTML Elements</a> for instructions on how to view your HTML in your browser. If you’re new to HTML, try out the whole <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">How To Build a Website in HTML</a> series.</li>
</ul>

<h2 id="setting-up-the-html-and-css-files">Setting Up the HTML and CSS Files</h2>

<p>In this section you will set up the HTML content that you will style throughout the tutorial. The purpose of the HTML in this tutorial is to provide various elements and situations to style. You will also create your CSS file and set a base style.</p>

<p>To begin, open <code>index.html</code> in your text editor and add the following HTML to the file:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
    &lt;/head&gt;
    &lt;body&gt;
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>Next, in the <code>&lt;head&gt;</code> tag, you will add a <code>meta</code> tag for the character set for this page, a <code>title</code> element for the title of the page, a <code>meta</code> tag defining how the page should be handled on mobile devices, and the CSS files to load:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
        <span class="highlight">&lt;meta charset="UTF-8" /&gt;</span>
        <span class="highlight">&lt;title&gt;Text Layout&lt;/title&gt;</span>
        <span class="highlight">&lt;meta name="viewport" content="width=device-width" /&gt;</span>
        <span class="highlight">&lt;link rel="preconnect" href="https://fonts.gstatic.com" /&gt;</span>
        <span class="highlight">&lt;link href="https://fonts.googleapis.com/css2?family=Public+Sans:ital,wght@0,400;0,700;1,400;1,700&amp;amp;family=Quicksand:wght@700&amp;amp;display=swap" rel="stylesheet" /&gt;</span>
        <span class="highlight">&lt;link href="styles.css" rel="stylesheet" /&gt;</span>
    &lt;/head&gt;
    &lt;body&gt;
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>Notice that the CSS files include a couple of fonts from <a href="https://fonts.google.com">Google Fonts</a> and the <code>styles.css</code> file you will create later in this section. If you&rsquo;d like to learn more about how to use Google Fonts, check out <a href="https://www.digitalocean.com/community/tutorials/how-to-style-text-elements-with-font-size-and-color-in-css#loading-custom-fonts-with-google-fonts">How To Style Text Elements with Font, Size, and Color in CSS</a>. </p>

<p>Now you will set up the HTML content contained within the page’s <code>&lt;body&gt;</code> tag. This content will be contained in an <code>&lt;article&gt;</code>, which will have an <code>&lt;h1&gt;</code> element, a couple each of <code>&lt;h2&gt;</code> and <code>&lt;h3&gt;</code> elements, and several <code>&lt;p&gt;</code> elements throughout. You will fill these tags with text content from the <a href="http://www.cupcakeipsum.com">Cupcake Ipsum</a> filler text generator. This filler content will provide all that is needed to apply visual styles for this tutorial.</p>

<p>Apply the HTML to your <code>index.html</code>, as shown in the highlighted sections of the following code block:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
        ...
    &lt;/head&gt;
    &lt;body&gt;
        <span class="highlight">&lt;article&gt;</span>
            <span class="highlight">&lt;h1&gt;Sugar plum chupa chups chocolate bar cupcake donut&lt;/h1&gt;</span>
            <span class="highlight">&lt;h2&gt;Tootsie roll oat cake macaroon&lt;/h2&gt;</span>
            <span class="highlight">&lt;p&gt;Jujubes brownie candy. Dessert tootsie roll pie gummi bears danish cotton candy. Sugar plum &lt;strong&gt;I love fruitcake pastry&lt;/strong&gt;. Jelly-o gummi bears muffin gummi bears marzipan cheesecake donut gingerbread I love. Cupcake wafer cake.&lt;/p&gt;</span>
            <span class="highlight">&lt;p&gt;Cupcake donut topping &lt;em&gt;&lt;strong&gt;chupa chups halvah&lt;/strong&gt;&lt;/em&gt; chupa chups. Macaroon tootsie roll cupcake caramels chocolate fruitcake gingerbread jelly-o. Tiramisu I love marshmallow jelly-o I love jelly beans candy gummi bears.&lt;/p&gt;</span>
            <span class="highlight">&lt;h2&gt;Jelly beans tiramisu pastry danish donut&lt;/h2&gt;</span>
            <span class="highlight">&lt;h3&gt;Lemon drops pastry marshmallow&lt;/h3&gt;</span>
            <span class="highlight">&lt;p&gt;I love marshmallow candy. &lt;em&gt;Sesame snaps&lt;/em&gt; muffin danish. Chocolate cake cookie jelly-o tiramisu halvah brownie halvah chocolate chocolate cake. Jelly-o caramels jujubes bonbon cupcake danish tootsie roll chocolate bar. Macaroon I love muffin candy canes sweet roll I love. I love bonbon marshmallow croissant ice cream I love gummi bears.&lt;/p&gt;</span>
            <span class="highlight">&lt;p&gt;Pie apple pie I love jujubes biscuit I love. Chocolate cake pastry tiramisu &lt;strong&gt;soufflé powder caramels&lt;/strong&gt; I love ice cream. Dragée liquorice toffee jelly jelly beans. Sesame snaps candy canes soufflé. Biscuit donut bear claw jujubes halvah pastry macaroon lemon drops. Tootsie roll dragée cookie candy soufflé dragée cupcake liquorice.&lt;/p&gt;</span>
            <span class="highlight">&lt;h3&gt;Apple pie pudding topping&lt;/h3&gt;</span>
            <span class="highlight">&lt;p&gt;Powder dragée sesame snaps candy canes jelly-o. Halvah gingerbread cheesecake wafer. &lt;strong&gt;&lt;em&gt;Wafer tootsie roll&lt;/em&gt;&lt;/strong&gt; I love I love. Cake toffee I love. Cotton candy cotton candy jelly beans I love bonbon toffee. Chupa chups chupa chups caramels ice cream halvah candy chocolate cake. Marshmallow carrot cake jelly beans.&lt;/p&gt;</span>
            <span class="highlight">&lt;p&gt;Chocolate cake sweet roll pudding chocolate cake fruitcake bear claw.&lt;/p&gt;</span>
        <span class="highlight">&lt;/article&gt;</span>
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>Save all these additions to your <code>index.html</code> file and then open the file in your web browser. As you write your styles, have the <code>index.html</code> file loaded in your browser to check how your styles are applied to the content:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/1.png" alt="Content in a serif font with black text on a white background."></p>

<p>Next, create a file in the same directory as <code>index.html</code> called <code>styles.css</code> and open the new file in your text editor.</p>

<p>There are two font families that you will load from Google fonts. The first font will be the default font for the page, as it will be used by all content on the page.</p>

<p>Create a <code>body</code> type selector and add a <code>font-family</code> property with the font stack <code>'Public Sans', sans-serif</code> to set this as the new default font:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css"><span class="highlight">body {</span>
    <span class="highlight">font-family: 'Public Sans', sans-serif;</span>
<span class="highlight">}</span>
</code></pre>
<p>This applies the font to the <code>body</code> element. All the content in this example will inherit that font, without needing to be declared individually. The font name is <strong>Public Sans</strong>, and it will have a fallback font that uses the browser’s default <code>sans-serif</code> font. Fonts should always have a fallback font using a comma separated list called a <em>font stack</em>. Fallback fonts provide a readable option if the custom font doesn’t load or lacks a special character.</p>

<p>Next, the heading elements, <code>h1</code>, <code>h2</code>, and <code>h3</code>, will get a special font for the rest of the page called <strong>Quicksand</strong>. Create a group selector consisting of the three headers <code>h1, h2, h3</code> and apply the same font stack setup as the <code>body</code> with <strong>Quicksand</strong>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">body {
    font-family: 'Public Sans', sans-serif;
}

<span class="highlight">h1, h2, h3 {</span>
    <span class="highlight">font-family: 'Quicksand', sans-serif;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to your browser to reload <code>index.html</code>. The custom font will now load, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/2.png" alt="Content with black text on a white background with heading text in a rounded sans serif font and the paragraphs in sans serif font."></p>

<p>For the last part of setting up your files, return to your <code>styles.css</code> file in the text editor. Create singular type selectors for the <code>h1</code>, <code>h2</code>, <code>h3</code>, and <code>p</code> elements to define a <code>font-size</code> for each. Use the <code>rem</code> unit for value, setting the <code>h1</code> to <code>2.5rem</code>, the <code>h2</code> to <code>1.875rem</code>, the <code>h3</code> to <code>1.5</code>, and the <code>p</code> to <code>1.25rem</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1, h2, h3 {
    font-family: 'Quicksand', sans-serif;
}

<span class="highlight">h1 {</span>
    <span class="highlight">font-size: 2.5rem; /* 40px */</span>
}

<span class="highlight">h2 {</span>
    <span class="highlight">font-size: 1.875rem; /* 30px */</span>
<span class="highlight">}</span>

<span class="highlight">h3 {</span>
    <span class="highlight">font-size: 1.5rem; /* 24px */</span>
<span class="highlight">}</span>

<span class="highlight">p {</span>
    <span class="highlight">font-size: 1.25rem; /* 20px */</span>
<span class="highlight">}</span>
</code></pre>
<p>The code block includes a comment that indicates what each <code>rem</code> value will return as a <code>px</code> unit. The <code>rem</code> unit gives the user more control over adjusting the <code>font-size</code> to a preferable ratio than the <code>px</code> value allows. For more on these units, read through <a href="https://www.digitalocean.com/community/tutorials/how-to-use-common-units-in-css">How To Use Common Units in CSS</a>.</p>

<p>Save your changes to <code>styles.css</code> and return to your browser to reload the <code>index.html</code> file. The text font sizes will adjust to look similar to the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/3.png" alt="Content with black text on a white background with heading text in a rounded sans serif font and the paragraphs in sans serif font with various sized text."></p>

<p>In this section you set up your HTML content in <code>index.html</code> and created your <code>styles.css</code> file. You applied the Google Font typefaces to the CSS globally on the <code>body</code> element and specifically to the <code>h1</code>, <code>h2</code>, and <code>h3</code> elements. You also set the <code>font-size</code> values for all the text elements on the page. In the next section you will use the <code>width</code> property to create more readable line lengths.</p>

<h2 id="improve-line-lengths-using-width-and-max-width">Improve Line Lengths Using <code>width</code> and <code>max-width</code></h2>

<p>In this section, you will use the <code>width</code> and <code>max-width</code> properties to set an appropriate line length for the text.</p>

<p>An often overlooked aspect of web typography is how long a line of text is presented. For both users in need of accessibility assistance and those without, the length of a line contributes significantly to the effort needed to read the text. The longer a line of text is the more likely the reader will have difficulty keeping track of the text. Shorter lines of text help the reader navigate and scan the content.</p>

<p>Open <code>styles.css</code> in your text editor and write an <code>article</code> type selector to apply a <code>width</code> of <code>90%</code> and add a <code>margin</code> property with a value of <code>0 auto</code>. This combination will make sure the content is set to be 90% of the screen’s width, and the <code>auto</code> value in the <code>margin</code> will keep the content block in the middle of the page:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">article {</span>
    <span class="highlight">margin: 0 auto;</span>
    <span class="highlight">width: 90%;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save these changes to <code>styles.css</code> and reload <code>index.html</code> in your browser. The text will be centered, but the lines of text will be very long. See the following image for how this will appear in your browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/4.png" alt="Text content in black taking up 90% of the width, with equal spacing on either side."></p>

<p>The ideal line length is between 45 and 75 characters, as explained by <a href="http://clarissapeterson.com">Clarissa Peterson</a> in her <a href="https://www.youtube.com/watch?v=hjXPAtBJd-Y">talk on Responsive Typography</a>. Longer than 75 characters and the reader can begin to lose track of what line they are reading. On the other hand, shorter than 45 characters and the readers eyes can become fatigued from the constant moving from line to line.</p>

<p>Setting a width based on a character count is possible with a unit called the <em>character unit</em>, represented by <code>ch</code> in code. The <code>ch</code> unit is determined by the size of the zero character (<code>0</code>) in the font. Since the ideal line length is between 45–75 characters, you can set a <code>max-width</code> value in that range, and the <code>article</code> element will stop growing once it has reached that size.</p>

<p>Return to the <code>styles.css</code> file in your text editor and, after the <code>width</code> property in the <code>article</code> type selector, add a <code>max-width</code> property and give it a value of <code>70ch</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-bash">...
article {
    margin: 0 auto;
    width: 90%;
    <span class="highlight">max-width: 70ch;</span>
}
</code></pre>
<p>This means that the maximum width the element is allowed to grow is the width of 70 zero characters of the font used in that space, which is the font set on the <code>body</code> element.</p>

<p>Save these changes to your <code>styles.css</code> file and reload <code>index.html</code> in your browser. You will find the content centered in the page at a maximum width of approximately 70 characters long. Try resizing the width of your browser to watch the <code>article</code> container transition from a 90% width to its maximum width, as shown in the following animation:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/5.gif" alt="Animation showing a browser window with content growing in width with the window until the content reaches a maximum width."></p>

<p><span class='note'><strong>Note:</strong> Chris Coyier, of CSS Tricks, created a <a href="https://css-tricks.com/bookmarklet-colorize-text-45-75-characters-line-length-testing/">handy bookmarklet tool</a> that will highlight the range of characters between 45 and 75 to help find the best width to set your content.<br></span></p>

<p>In this section you learned that accessibility and legibility share a common ground with the line length of text content. You used the <code>width</code> property with the <code>max-width</code> property to set a size limiting the text length to 45–75 characters using the <code>ch</code> unit. In the next section, you will use the <code>line-height</code> property to set the appropriate spacing between lines of text.</p>

<h2 id="using-the-line-height-property-to-help-readability">Using the <code>line-height</code> Property to Help Readability</h2>

<p>You will use the <code>line-height</code> property in this section to expand and contract the space between the lines of text. You may find that headings typically have less space between lines and that paragraph text tends to have more space. The goal for this spacing is to make the content easier to read. Similar to the width of lines of text, if the lines are too close together, the reader may be distracted by the line above or below. On the other hand, if the text is too far apart, the reader’s eyes can grow tired of jumping over the space between lines and find the text much harder to scan.</p>

<p>Open your <code>styles.css</code> file in your text editor and go to the <code>body</code> selector. Like the <code>font-family</code>, you will use <code>line-height</code> to set a default distance between lines for the whole document. Add the <code>line-height</code> property and give a value of <code>1.5</code>. This value is a measurement of the distance between <em>baselines</em>, the line on which the bottom of text rests:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">body {
    font-family: 'Public Sans', sans-serif;
    <span class="highlight">line-height: 1.5;</span>
}
...
</code></pre>
<p>The default for <code>line-height</code> is tied to the keyword value of <code>normal</code>, which is equal to 1.2 of the font size. This means if the <code>font-size</code> is 16px, then the <code>line-height</code> when set to <code>normal</code> is approximately 19.2px. This is a good median value; however, paragraph text usually needs a bit more space, while a heading sometimes needs a bit less.</p>

<p>Next, go to the group selector targeting <code>h1, h2, h3</code> and set a <code>line-height</code> value to <code>1.15</code>. This will bring the text between lines a bit closer together, and can help the presentation of long heading titles. Add the highlighted line in the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1, h2, h3 {
    font-family: 'Quicksand', sans-serif;
    <span class="highlight">line-height: 1.15;</span>
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to the browser to reload <code>index.html</code>. You will find the length of the content expand as more space is placed between lines of text. See the following image for how this appears in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/6.png" alt="Text content in black on white with headlines closer together vertically and paragraph lines of text further apart."></p>

<p>The value for the <code>line-height</code> property can accept fixed unit values, as well as pixel (<code>px</code>) or <code>rem</code>, but it is better to leave no unit as the default behavior is to multiply the value by the <code>font-size</code>.</p>

<p>You used the <code>line-height</code> property in this section to make the content on the page more legible and easier to scan for the reader. In the next section, you will use the <code>margin</code> properties to set a defined amount of space between types of content as defined in the HTML.</p>

<h2 id="using-the-margin-properties-for-spacing">Using the <code>margin</code> Properties for Spacing</h2>

<p>In this section, you will use the <code>margin</code> property along with the adjacent sibling selector to apply different vertical spacing between the text elements. Where the <code>line-height</code> property gives control between lines of text in an element, <code>margin</code> can be used to adjust the spacing between content elements.</p>

<p>To begin, return to <code>styles.css</code> in your text editor and find the <code>h3</code> selector. In this situation, you will add spacing to the element to have more space above the text and less space beneath. This will cause it to be farther from the content above and closer to the content below.</p>

<p>Add a <code>margin</code> property with a value of <code>2em 0 0.5em</code>. This will apply spacing that is relative to the <code>font-size</code> value, meaning the top margin will be double the <code>font-size</code> at 48px and the bottom will be half the <code>font-size</code> at 12px:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h3 {
    font-size: 1.5rem; /* 24px */
    <span class="highlight">margin: 2em 0 0.5em;</span>
}
...
</code></pre>
<p>Since the <code>margin</code> works on the outside of the element, each element’s <code>margin</code> properties will overlap. This means that even though the <code>h3</code> <code>margin</code> on the bottom side is equivalent to 12px, the <code>&lt;p&gt;</code> element’s margin is larger and therefore defines the space between the <code>&lt;h3&gt;</code> and <code>&lt;p&gt;</code>. You can solve this problem by using the adjacent sibling combinator, which is defined by a plus sign (<code>+</code>) between two selectors, with styles applied to the latter in the sequence.</p>

<p>Create a new selector in <code>styles.css</code> in your text editor using the adjacent sibling combinator as <code>h3 + p</code>, then within the selector block add a <code>margin-top</code> property with a value of <code>0</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h3 {
    font-size: 1.5rem; /* 24px */
    margin: 2em 0 0.5em;
}

<span class="highlight">h3 + p {</span>
    <span class="highlight">margin-top: 0;</span>
<span class="highlight">}</span>
...
</code></pre>
<p>The way the adjacent sibling combinator works, this means that when the browser has an <code>&lt;h3&gt;</code> element and immediately following it is a <code>&lt;p&gt;</code> element, then these styles are applied to only that <code>&lt;p&gt;</code> element. Other <code>&lt;p&gt;</code> elements are unaffected.</p>

<p>Save your changes to <code>styles.css</code> and load <code>index.html</code> in your browser. As shown in the following image, the space above the <code>&lt;h3&gt;</code> elements is now much larger and the space between the <code>&lt;h3&gt;</code> and first <code>&lt;p&gt;</code> after it is much closer.</p>

<p><img src="https://assets.digitalocean.com/articles/67655/7.png" alt="Black text content with a large spacing between two headers."></p>

<p>In this section, you used the <code>margin</code> property to apply different spacing between the elements of the page. With the adjacent sibling selector, you set up conditionals that would apply a greater spacing amount if the <code>&lt;h3&gt;</code> element was preceded by a <code>&lt;p&gt;</code> element. In the next section, you will use the <code>text-align</code> property to adjust the placement of the text on a line.</p>

<h2 id="using-text-align-for-effective-content-presentation">Using <code>text-align</code> for Effective Content Presentation</h2>

<p>You will now use the <code>text-align</code> property to change where the text is placed on a line. The property has four values: <code>left</code>, <code>right</code>, <code>center</code>, and <code>justify</code>. The default value for this property depends on the browser’s language setting. For languages that read from left to right, <code>left</code> is the default, while languages that read right to left have a default of <code>right</code>. The <code>center</code> property places the text in the center of the line of text and leaves an equal amount of blank space on either side of the line of text. Lastly, the <code>justify</code> value spreads the words out to the edges of the container, leaving visually inconsistent spaces between words.</p>

<p>Open <code>styles.css</code> in your text editor and find the <code>h3</code> type selector. Add a <code>text-align</code> property with a value of <code>center</code> as shown in the highlighted portion of the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h3 {
    font-size: 1.5rem; /* 24px */
    margin: 2em 0 0.5em;
    <span class="highlight">text-align: center;</span>
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code> and reload the <code>index.html</code> file in your browser. The content of the two <code>h3</code> level headings are now centered above their respective sections. See the following image for how this appears in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/8.png" alt="Black sans serif text centered to the container."></p>

<p>It is best to keep the <code>text-align</code> property set to the default, as this is best for the user and their settings. However, changing the alignment value can help distinguish some text better or provide a better aesthetic.</p>

<p>You used <code>text-align</code> in this section to center text content within its container. You also learned how and when to use the other values available to the <code>text-align</code> property. In the next section, you will use the <code>text-transform</code> and <code>letter-spacing</code> properties to create visual personality while still keeping the text readable and maintaining efficient hierarchy.</p>

<h2 id="using-letter-spacing-and-text-transform">Using <code>letter-spacing</code> and <code>text-transform</code></h2>

<p>Next, you will use the <code>text-transform</code> property and the <code>letter-spacing</code> property to adjust how the text of a headline appears. The <code>text-transform</code> property controls how text capitalization is formatted, providing options to change the text to <code>uppercase</code>, <code>lowercase</code>, or <code>capitalize</code>, which capitalizes the first letter of each word. The <code>letter-spacing</code> property is the value of space between each character. Together these two properties can create an aesthetic that is legible and stands out.</p>

<p>Begin with the <code>text-transform</code> property by opening <code>styles.css</code> in your text editor and go to the <code>h1</code> type selector. Since this heading is the title of the whole article, it makes sense to have title-case formatting.</p>

<p>Add a <code>text-transform</code> property with a value of <code>capitalize</code>. This value will capitalize the first letter of each word, regardless of if it is capitalized in the HTML:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    font-size: 2.5rem; /* 40px */
    <span class="highlight">text-transform: capitalize;</span>
}
...
</code></pre>
<p>Save this addition to your <code>styles.css</code> file, then open <code>index.html</code> in your browser. The heading copy now has the first letter of each word capitalized, as shown in the following image.</p>

<p><img src="https://assets.digitalocean.com/articles/67655/9.png" alt="Large black text in a rounded sans serif font, with the first letter of each word capitalized."></p>

<p>Next, return to the <code>styles.css</code> file in your text editor and find the <code>h3</code> type selector. This time you will set up the <code>h3</code> style to be all uppercase letters in each word by adding a <code>text-transform</code> property with a value of <code>uppercase</code>. Add the highlighted line for the following code block to your <code>styles.css</code> file:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h3 {
    font-size: 1.5rem; /* 24px */
    margin: 2em 0 0.5em;
    text-align: center;
    <span class="highlight">text-transform: uppercase;</span>
}
...
</code></pre>
<p>After you have made this change, save <code>styles.css</code> and then refresh <code>index.html</code> in your browser. The <code>h3</code> content will now be all uppercase, regardless of how it is written in the HTML. See the following image for how this appears in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/10.png" alt="Horizontally centered content in a black rounded sans serif font, all uppercase."></p>

<p><span class='note'><strong>Note:</strong> When using CSS to apply uppercase styling to content, it can affect how screen readers interpret the content. For example, with <a href="https://www.apple.com/voiceover/info/guide/_1121.html">Voice Over on macOS</a>, a three-letter word that is set to uppercase by CSS will be read as an acronym instead of as a word. When using this styling approach, it is helpful to apply an <code>aria-label</code> attribute to the HTML element with how the content should be read.<br></span></p>

<p>Now that the text is all uppercase, visually the text feels a little bunched together for the aesthetic of the design. Next, use the <code>letter-spacing</code> property to add <code>0.125em</code> spacing between each character. See the highlighted code in the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h3 {
    font-size: 1.5rem; /* 24px */
    margin: 2em 0 0.5em;
    text-align: center;
    text-transform: uppercase;
    <span class="highlight">letter-spacing: 0.125em;</span>
}
...
</code></pre>
<p>Using the <code>em</code> unit on a <code>letter-spacing</code> property allows the spacing between characters to be proportional to the <code>font-size</code>. The <code>0.125em</code> keeps the spacing at one-eighth the height of the font.</p>

<p>Save your changes to <code>styles.css</code> and reload <code>index.html</code> in your browser to render the changes to the <code>h3</code> content. See what this will look like in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67655/11.png" alt="Horizontally centered content in a black rounded sans serif font, all uppercase, with extra space between each characer."></p>

<p>In this section, you used the <code>text-transform</code> property to change the styles for the <code>&lt;h1&gt;</code> element to capitalize the first letter of each word. You also used the <code>text-transform</code> and <code>letter-spacing</code> properties to change the <code>&lt;h3&gt;</code> element to have all uppercase words with the characters spread apart. In the last section, you will use the <code>font</code> property to condense multiple properties into one.</p>

<h2 id="using-the-font-shorthand">Using the <code>font</code> Shorthand</h2>

<p>In this last section, you will use the <code>font</code> property to condense multiple properties into a single property. The <code>font</code> property can be useful when defining several text-related values globally, and placed at a high level in the cascade. Be aware that using the <code>font</code> shorthand will overwrite the individual properties it contains.</p>

<p>To start, return to your <code>styles.css</code> file in your text editor and go to the <code>body</code> selector. Here the <code>font-family</code> and <code>line-height</code> properties can be condensed into the <code>font</code> shorthand property. However, the <code>font</code> shorthand requires that both a <code>font-size</code> and <code>font-family</code> value are present. This means adding a <code>font-size</code> value in addition to the <code>line-height</code> and <code>font-family</code> values. See the highlighted section of the following code block for how to write this out:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">body {
    <span class="highlight">font: 1em/1.5 'Public Sans', sans-serif;</span>
}
...
</code></pre>
<p>Notice that there is a forward slash (<code>/</code>) between the <code>font-size</code> value of <code>1em</code> and the <code>line-height</code> value of <code>1.5</code>. This is the only way to set a <code>line-height</code> value with the <code>font</code> shorthand property, and it must come after the <code>font-size</code> value. The <code>font-size</code> value is set to <code>1em</code> since that is the least disruptive value and carries over the default <code>font-size</code> of the document regardless of its value. Additionally, the <code>font</code> property shorthand accepts values for the <code>font-stretch</code>, <code>font-style</code>,<code>font-variant</code>, and <code>font-weight</code> properties.</p>

<p>Be sure to save your changes to <code>styles.css</code> in your text editor. Reload the <code>index.html</code> in your browser and verify there are no visual changes. Since a shorthand property combines existing properties into a single property, there are no changes to the styles.</p>

<p>You used the <code>font</code> property in this last section of the tutorial to combine several properties into one, condensing the size of your CSS. This property, though very useful, must be used with consideration as it can require redefining properties due to its broad coverage.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Readable text layout can mark the difference between an effective and ineffective design. Laying out text is a subjective balance of providing sufficient space between text lines, words, and blocks to present the content in an unobtrusive manner. When working with text, poor layout can prevent readers from understanding the content. With these properties and their uses in mind, you will make well-read content.</p>

<p>If you would like to read more CSS tutorials, try out the other tutorials in the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-style-html-with-css">How To Style HTML with CSS series</a>.</p>
