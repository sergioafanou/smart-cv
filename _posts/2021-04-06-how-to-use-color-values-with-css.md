---
title : "How To Use Color Values with CSS"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-color-values-with-css
image: "https://sergio.afanou.com/assets/images/image-midres-41.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/diversity-in-tech">Diversity in Tech Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>Color is a useful part of design and development when creating websites. It helps set a mood and communicate an aesthetic. Color also helps readers scan and identify content quickly. </p>

<p>With CSS, there are four ways to generate colors, and each has its own unique strength. This tutorial will show you how to use color keywords, hexadecimal color values, the <code>rgb()</code> color format, and lastly the <code>hsl()</code> color format. You will use all four approaches with the same set of HTML to experience how each color format works with the same content. Throughout the tutorial, you will use the <code>color</code>, <code>border</code>, and <code>background-color</code> properties to apply these color formats to the HTML.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li>An understanding of CSS’s cascade and specificity features, which you can get by reading <a href="https://www.digitalocean.com/community/tutorials/how-to-apply-css-styles-to-html-with-cascade-and-specificity">How To Apply CSS Styles to HTML with Cascade and Specificity</a>.</li>
<li>Knowledge of type selectors, combinator selectors, and selector groups, which you can find in <a href="https://www.digitalocean.com/community/tutorials/how-to-select-html-elements-to-style-with-css">How To Select HTML Elements to Style with CSS</a>.</li>
<li>An empty HTML file saved on your local machine as <code>index.html</code> that you can access from your text editor and web browser of choice. To get started, check out our <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-html-project">How To Set Up Your HTML Project</a> tutorial, and follow <a href="https://www.digitalocean.com/community/tutorials/how-to-use-and-understand-html-elements#how-to-view-an-offline-html-file-in-your-browser">How To Use and Understand HTML Elements</a> for instructions on how to view your HTML in your browser. If you’re new to HTML, try out the whole <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">How To Build a Website in HTML</a> series.</li>
</ul>

<h2 id="setting-up-the-example-html-and-css">Setting Up the Example HTML and CSS</h2>

<p>In this section, you will set up the HTML base for all the visual styles you will write throughout the tutorial. You will also create your <code>styles.css</code> file and add styles that set the layout of the content.</p>

<p>Start by opening <code>index.html</code> in your text editor. Then, add the following HTML to the file:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
    &lt;/head&gt;
    &lt;body&gt;
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>Next, go to the <code>&lt;head&gt;</code> tag and add a <code>&lt;meta&gt;</code> tag to define the <a href="https://developer.mozilla.org/en-US/docs/Glossary/character_encoding">character set</a> for the HTML file, then the title of the page, the <code>&lt;meta&gt;</code> tag defining how mobile devices should render the page, and finally the <code>&lt;link&gt;</code> element that loads the CSS file you will make later. </p>

<p>These additions are highlighted in the following code block:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
        <span class="highlight">&lt;meta charset="UTF-8" /&gt;</span>
    <span class="highlight">&lt;title&gt;Colors With CSS&lt;/title&gt;</span>
    <span class="highlight">&lt;meta name="viewport" content="width=device-width" /&gt;</span>
    <span class="highlight">&lt;link href="styles.css" rel="stylesheet" /&gt;</span>
    &lt;/head&gt;
    &lt;body&gt;
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>After adding the <code>&lt;head&gt;</code> content, next move to the <code>&lt;body&gt;</code> element, where content will be added from the <a href="https://en.wikipedia.org/wiki/Color">Wikipedia article on color</a>. Add the highlighted section from this code block to your <code>index.html</code> file in your text editor:</p>
<div class="code-label " title="index.html">index.html</div><pre class="code-pre "><code class="code-highlight language-html">&lt;!doctype html&gt;
&lt;html&gt;
    &lt;head&gt;
        &lt;meta charset="UTF-8" /&gt;
        &lt;title&gt;Colors With CSS&lt;/title&gt;
        &lt;meta name="viewport" content="width=device-width" /&gt;
        &lt;link href="styles.css" rel="stylesheet" /&gt;
    &lt;/head&gt;
    &lt;body&gt;
        <span class="highlight">&lt;article&gt;</span>
            <span class="highlight">&lt;header&gt;</span>
                <span class="highlight">&lt;h1&gt;About Color&lt;/h1&gt;</span>
            <span class="highlight">&lt;/header&gt;</span>
            <span class="highlight">&lt;p&gt;Color is the characteristic of visual perception described through color categories, with names such as red, orange, yellow, green, blue, or purple. This perception of color derives from the stimulation of photoreceptor cells by electromagnetic radiation. Color categories and physical specifications of color are associated with objects through the wavelengths of the light that is reflected from them and their intensities. This reflection is governed by the object's physical properties such as light absorption, emission spectra, etc.&lt;/p&gt;</span>
            <span class="highlight">&lt;hr /&gt;</span>
            <span class="highlight">&lt;p&gt;By defining a color space, colors can be identified numerically by coordinates, which in 1931 were also named in global agreement with internationally agreed color names like mentioned above (red, orange, etc.) by the International Commission on Illumination. The RGB color space for instance is a color space corresponding to human trichromacy and to the three cone cell types that respond to three bands of light: long wavelengths, peaking near 564–580 nm (red); medium-wavelength, peaking near 534–545 nm (green); and short-wavelength light, near 420–440 nm (blue). There may also be more than three color dimensions in other color spaces, such as in the CMYK color model, wherein one of the dimensions relates to a color's colorfulness.&lt;/p&gt;</span>
            <span class="highlight">&lt;footer&gt;</span>
                <span class="highlight">Adapted from Wikipedia’s article on &lt;a href="https://en.wikipedia.org/wiki/Color"&gt;Color&lt;/a&gt;</span>
            <span class="highlight">&lt;/footer&gt;</span>
        <span class="highlight">&lt;/article&gt;</span>
    &lt;/body&gt;
&lt;/html&gt;
</code></pre>
<p>The contents are in an <code>&lt;article&gt;</code> tag to define a designated area known as a <em>landmark</em>, which is a part of your design that you are drawing attention to. This element can have its own <code>&lt;header&gt;</code> and <code>&lt;footer&gt;</code> tags. The <code>&lt;header&gt;</code> contains an <code>&lt;h1&gt;</code> tag with the content title, and the <code>&lt;footer&gt;</code> element contains content for citing the source. Between the two <code>&lt;p&gt;</code> tags of the primary content is an <code>&lt;hr /&gt;</code> tag, which is a self-closing tag that creates a horizontal rule line. As a piece of content, the horizontal rule can be used to break up content or to indicate a shift in the content.</p>

<p>That completes the work needed for <code>index.html</code>. Save your changes to the file, then open <code>index.html</code> in your web browser. The webpage will appear like the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/1.png" alt="Black text with large, bold title text and two paragraphs with a rule line between the paragraphs, all in a serif font."></p>

<p>Now you will begin working on the base styles needed for this tutorial. Create a file called <code>styles.css</code> and open it in your text editor.</p>

<p>To begin, create a <code>body</code> type selector and add <code>font-famiy: sans-serif</code> to use the browser’s default sans serif font. Then add a <code>line-height: 1.5</code> to give a default line spacing between text of one and half the <code>font-size</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">body {
    font-family: sans-serif;
    line-height: 1.5;
}
</code></pre>
<p>Next, add styles for the <code>&lt;article&gt;</code> element by creating a type selector for it with a <code>margin</code>, <code>padding</code>, <code>width</code>, and <code>max-width</code>, along with a <code>box-sizing: border-box</code> to redefine the <a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-the-box-model-in-css">Box Model</a> for the <code>&lt;article&gt;</code>. This will make sure the <code>padding</code> value is not added to the full width of the container.</p>

<p>Add the highlighted sections of the following code block for the values for each of the properties:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">article {</span>
    <span class="highlight">margin: 2rem auto;</span>
    <span class="highlight">padding: 2rem;</span>
    <span class="highlight">width: 90%;</span>
    <span class="highlight">max-width: 40rem;</span>
    <span class="highlight">box-sizing: border-box;</span>
<span class="highlight">}</span>
</code></pre>
<p>First, the <code>margin</code> with a value of <code>2rem auto</code> will center the <code>article</code> container on the page. The <code>padding</code> of <code>2rem</code> will give ample space around the container as you add color properties later in the tutorial. The <code>width</code> allows the container to be fluid in width as screen size changes, until it reaches the <code>max-width</code> value of <code>40rem</code> (which is equivalent to <code>640px</code>).</p>

<p>Next, write an <code>h1</code> type selector and give the element a <code>font-size</code> of <code>2rem</code>. Then, add a <code>margin</code> of <code>0 0 1rem</code> to remove the default <code>margin-top</code> and provide a new value for the <code>margin-bottom</code>. Lastly, center the text by adding a <code>text-align: center;</code> to the selector block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">h1 {</span>
    <span class="highlight">font-size: 2rem;</span>
    <span class="highlight">margin: 0 0 1rem;</span>
    <span class="highlight">text-align: center;</span>
<span class="highlight">}</span>
</code></pre>
<p>Next, you will apply some base styles to the <code>&lt;hr&gt;</code> element. The browser defaults for the <code>&lt;hr&gt;</code> element create a box with no height, full width, and a border. Although this container is self-closing and does not contain content, it can accept a lot of styles. </p>

<p>Start by giving it a <code>height</code> value of <code>0.25rem</code>. Then, add a <code>margin</code> property of <code>2rem auto</code> to create space above and below the <code>&lt;hr&gt;</code> and to center the element. Add a <code>width: 90%;</code> with a <code>max-width: 18rem;</code> so the <code>&lt;hr&gt;</code> element is always smaller than the article container and never larger than <code>18rem</code>. Finally, use the <code>border-radius</code> property to round the ends of the <code>&lt;hr&gt;</code> element with a value of <code>0.5rem</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">hr {</span>
    <span class="highlight">height: 0.25rem;</span>
    <span class="highlight">margin: 2rem auto;</span>
    <span class="highlight">width: 90%;</span>
    <span class="highlight">max-width: 18rem;</span>
    <span class="highlight">border-radius: 0.5rem;</span>
<span class="highlight">}</span>
</code></pre>
<p>Finally, create a <code>footer</code> selector block and add a <code>margin-top</code> property with a value of <code>2rem</code>, followed by a <code>font-size</code> of <code>0.875rem</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">footer {</span>
    <span class="highlight">margin-top: 2rem;</span>
    <span class="highlight">font-size: 0.875rem;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save your changes to <code>styles.css</code>. Then, return to <code>index.html</code> in your web browser and reload the page. The content is now ready to have colors applied to the base styles. The following image shows how it will render in your browser.</p>

<p><img src="https://assets.digitalocean.com/articles/67679/2.png" alt="Black text with large, bold title text and two paragraphs, with a narrow rounded rule line between the paragraphs, all in a sans serif font."></p>

<p>In this section, you set up your HTML in the <code>index.html</code> file and created the base styles in the <code>styles.css</code> file. The next section will introduce you to color keywords, which you will use to apply colors to the content.</p>

<h2 id="applying-colors-with-keyword-values">Applying Colors With Keyword Values</h2>

<p>Starting off with colors on the web, it is helpful to work with the predefined color keywords. In this section, you will use some of the color keywords to apply color to the content of your HTML.</p>

<p>There are well over one hundred color keyword values that have been added to the list over time. The <a href="https://www.w3.org/">World Wide Web Consortium</a> has an exhaustive <a href="https://www.w3.org/wiki/CSS/Properties/color/keywords">list of color keyword values</a> on their Wiki. Color keyword values are useful for throwing together a quick design or identifying and debugging CSS problems, such as by setting the <code>color</code> property to <code>magenta</code> so the element stands out against a muted color palette. The keywords cover all hues with variations of shades and tints of each, including grays.</p>

<p>Begin by going to the <code>article</code> type selector and adding <code>background-color</code>, <code>border</code>, and <code>color</code> properties. For the <code>background-color</code>, use the faint light brown <code>seashell</code> keyword. Give the <code>border</code> a thickness of <code>0.25rem</code>, a <code>solid</code> style, and for the color use the <code>sandybrown</code> keyword. Lastly, for the <code>color</code> property use the <code>maroon</code> keyword:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    margin: 2rem auto;
    padding: 2rem;
    width: 90%;
    max-width: 40rem;
    box-sizing: border-box;
    <span class="highlight">background-color: seashell;</span>
    <span class="highlight">border: 0.25rem solid sandybrown;</span>
    <span class="highlight">color: maroon;</span>
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to you browser to refresh the <code>index.html</code> page. The page will now have a visually defined <code>article</code> area with a light brown background color and a slightly darker brown thick border. The text content will be a dark red, and may appear more as a dark brown in the context of the other brown colors. The following image shows how the page will appear in your browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/3.png" alt="Brown text in a sans serif font, with a lighter tan background and border."></p>

<p>Next, return to <code>styles.css</code> in your text editor. Go to the <code>h1</code> selector and add a <code>color</code> property. Using a complementary color of brown hues, add the <code>teal</code> keyword for the <code>color</code> property value:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    font-size: 2rem;
    margin: 0 0 1rem;
    text-align: center;
    <span class="highlight">color: teal;</span>
}
...
</code></pre>
<p>Now, carrying on with the concept of complementary colors, go to the <code>hr</code> element and add a <code>border</code> property of <code>0.25rem solid teal</code>. After that, add a <code>background-color</code> property with the keyword <code>darkturquoise</code> as the value:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
hr {
    height: 0.25rem;
    margin: 2rem auto;
    width: 90%;
    max-width: 18rem;
    border-radius: 0.5rem;
    <span class="highlight">border: 0.25rem solid teal;</span>
    <span class="highlight">background-color: darkturquoise;</span>
}
...
</code></pre>
<p>Save these changes to your <code>styles.css</code> file and refresh <code>index.html</code> in your browser. The <code>&lt;hr&gt;</code> element between the two paragraphs will have a thick <code>teal</code> border with a lighter teal color inside, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/4.png" alt="Brown text in a sans serif font, with a lighter tan background and a border with a title text in teal and a rule line between paragraphs."></p>

<p>Next, return to your text editor and go to the <code>footer</code> selector in your <code>styles.css</code> file. Here, add a <code>color</code> property and use <code>chocolate</code> as the value:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
footer {
    margin-top: 2rem;
    font-size: 0.875rem;
    <span class="highlight">color: chocolate;</span>
}
</code></pre>
<p>Save your changes to <code>styles.css</code> in your text editor, then return to your browser and refresh <code>index.html</code>. Your browser will render <code>chocolate</code> as a lighter brown hue than <code>maroon</code>, but not as light at the <code>saddlebrown</code> border:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/5.png" alt="Sans serif text in a light brown color with a link in blue with an underline."></p>

<p>Before returning to your code editor, take note of the color of the link in the <code>&lt;footer&gt;</code> element. It is using the default browser color for a link, which is blue, unless you have visited the link, in which case the color is purple. There is a special value for <code>color</code> you will now use called <code>inherit</code>, which instead of defining a specific color uses the color of its parent container, in this case the <code>&lt;footer&gt;</code>.</p>

<p>To use the <code>inherit</code> value on a <code>color</code> property, return to <code>styles.css</code> in your text editor, and after the closing tag for your <code>footer</code> selector, add a new combinator of <code>footer a</code> to scope the styles to <code>&lt;a&gt;</code> elements inside of <code>&lt;footer&gt;</code> elements. Then, add a <code>color</code> property with a value of <code>inherit</code>, so the <code>&lt;a&gt;</code> will inherit the <code>color</code> value set on the <code>footer</code>, as shown in the highlighted section of the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
<span class="highlight">footer a {</span>
    <span class="highlight">color: inherit;</span>
<span class="highlight">}</span>
</code></pre>
<p>Save your changes to the <code>styles.css</code> file and refresh <code>index.html</code> in your web browser. The <code>&lt;a&gt;</code> is now the same color as the other <code>footer</code> text and retains the underline, which distinguishes the text as a link. The following image shows how this will appear in your browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/6.png" alt="Sans serif text in a light brown color with a link underlined."></p>

<p><span class='note'><strong>Note:</strong> The <code>inherit</code> value only works on colors that can accept the foreground <code>color</code> value, such as <code>border-color</code> and <code>color</code>. Other properties, such as <code>background-color</code> or <code>box-shadow</code>, can access the defined <code>color</code> value with a special value called <code>currentColor</code>. It works the same way as <code>inherit</code>, but for instances where <code>inherit</code> is not a valid value.<br></span></p>

<p>You used color keywords in this section to set several colors of varying contrasts to the content. You also used the <code>inherit</code> value to reuse a color value without explicitly defining a value. In the next section, you will use the hexadecimal color code system to adjust the colors of the content.</p>

<h2 id="applying-colors-with-hexadecimal">Applying Colors With Hexadecimal</h2>

<p>The hexadecimal, or hex, color value is the most common method of applying colors to elements on the web. In this section, you will use hex colors to redefine and adjust the visual style of the content.</p>

<p>It is important to have an understanding of what the hexadecimal system is and how it works. <a href="https://en.wikipedia.org/wiki/Hexadecimal">Hexadecimal</a> is a base 16 valuation system that uses <strong>0-9</strong> as their numerical values and the letters <strong>a-f</strong> as values ranging from <strong>10-15</strong>. The hex values are used to control each of the primary colors (red, green, and blue) in intensity from <strong>0</strong>, represented as <code>00</code>, up to <strong>255</strong>, represented as <code>ff</code>. The hex value follows a <code>#</code> to indicate that the color type is a hex color code, then two digits for each color channel, starting with red, then green, then blue.</p>

<p>Hexadecimal color codes can be written in two ways. The first is the long form, which is more common and more detailed, containing six digits, two for each color channel. An example of this is yellow created with <code>#ffff00</code>. The second way of writing a hex color code is a short form, which can be written with only three digits that the browser will interpret as a doubling of each single value. In the short form, yellow can be created with <code>#ff0</code>. The short form hex color value allows for a quicker, but more limited palette if used exclusively.</p>

<p>To begin using hex color codes, open up <code>styles.css</code> in your text editor and go the <code>article</code> element selector. For the <code>background-color</code> property value, use <code>#f0f0f0</code>, which is a very light gray. Next, for the <code>border</code> properties color value, use the short form hex code of <code>#ccc</code>, which is a middle gray. Finally, for the main text <code>color</code> properties, use the dark gray short form hex code <code>#444</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: <span class="highlight">#f0f0f0</span>;
    border: 0.25rem solid <span class="highlight">#ccc</span>;
    color: <span class="highlight">#444</span>;
}
...
</code></pre>
<p><span class='note'><strong>Note:</strong> When working with text content, it is important to maintain a good color contrast between a <code>background-color</code> and the text <code>color</code> value. This helps the readability of the text for a broad range of readers and website users. To learn more about the importance of accessible color contrast ratios, watch this <a href="https://sparkbox.com/foundry/web_design_color_contrast_accessible_web_colors"><strong>Designing with Accessible Color Contrast on the Web</strong> video series</a>. Also, <a href="https://webaim.org/resources/contrastchecker/">this contrast calculator provided by WebAIM</a> is a great tool to test if your colors provide enough contrast to make text readable for an inclusive audience.<br></span></p>

<p>Next, you will set the <code>h1</code> color value to a dark red. Go to the <code>h1</code> selector in your <code>styles.css</code> file and update the <code>color</code> property to have a value of <code>#900</code>, which turns on the red channel to about the midpoint and leaves the green and blue channels off:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    ...
    color: <span class="highlight">#900</span>;
}
...
</code></pre>
<p>Carrying on with the red values, update the <code>hr</code> color properties to match the <code>h1</code> element. Set the <code>border</code> property color to <code>#bd0000</code>, which requires the long form hex code, since the color is a value between <code>#b00</code> and <code>#a00</code>. Then, set the <code>background-color</code> to a full red value with <code>#f00</code>. This is the hex value equivalent of the <code>red</code> keyword:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
hr {
    ...
    border: .25rem solid <span class="highlight">#bd0000</span>;
    background-color: <span class="highlight">#f00</span>;
}
...
</code></pre>
<p>Lastly, to carry on with the <code>footer</code> text being a lighter version of the main text, go to the <code>footer</code> property and change the <code>color</code> property to be a middle gray of <code>#888</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
footer {
    ...
    color: <span class="highlight">#888</span>;
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to your browser to refresh <code>index.html</code> and review your changes. As is shown in the following image, the <code>article</code> container is now comprised of several gray colors with the heading and rule line variations of red:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/7.png" alt="Dark gray text in a sans serif font with a lighter gray background and border. The title text is red and there is a rule line between paragraphs that is two shades of red."></p>

<p>In this section, you used several hexadecimal color code values to define colors throughout the <code>styles.css</code> styles document. In the next section, you will translate these hexadecimal values to a more legible numeric value with the <code>rgb()</code> color code.</p>

<h2 id="applying-colors-with-rgb">Applying Colors With <code>rgb()</code></h2>

<p>Where the hexadecimal color values are defined with a limited number of alphanumeric values, <code>rgb()</code> uses numeric only values ranging from 0 to 255 for each primary color channel. Using only numerical values for these allows for a much quicker comprehension of the colors created by the <code>rgb()</code> format than by the hexadecimal.</p>

<p>Like the hexadecimal format and the structure of the <code>rgb()</code> format indicate, the colors are represented within the parenthesis as the red channel value, green channel value, and then blue channel value. A black color formatted in <code>rgb()</code> is <code>rgb(0, 0, 0)</code>, while a white is formatted as <code>rgb(255, 255, 255)</code>. This also means a full red color is <code>rgb(255, 0, 0)</code>, while a full green is <code>rgb(0, 255, 0)</code>, and so on.</p>

<p>To begin refactoring your code to use <code>rgb()</code> values instead of hexadecimal values, it can be helpful to use a HEX to RGB converter, such as <a href="https://duckduckgo.com/?q=hex+to+rgb">this HEX to RGB converter embedded in the Duck Duck Go search engine</a>. Using a mathematical approach, the numerical value can be decoded using the hexadecimal value for each channel. In the case of a hex code for the red channel being <strong>fe</strong>, this means that the <strong>f</strong> indicates 15 iterations of 16 counts from 0-15, making the <strong>f</strong> equal to <strong>16×15</strong>, or 240. The <strong>e</strong> of the hexadecimal <strong>fe</strong> is equal to <strong>14</strong> in the base-16 sequence. Add these two values together and the red channel for the <code>rgb()</code> value is <strong>254</strong>.</p>

<p>This chart will help you identify the values of each hexadecimal value to convert them to their numerical value needed for <code>rgb()</code>. The first row is the value for the first character in the hexadecimal sequence, which is the hex value multiplied by 16. The second row is the value for the second character in a hexadecimal sequence, which is the hex value multiplied by 1:</p>

<table><thead>
<tr>
<th></th>
<th>Calculation</th>
<th>0</th>
<th>1</th>
<th>2</th>
<th>3</th>
<th>4</th>
<th>5</th>
<th>6</th>
<th>7</th>
<th>8</th>
<th>9</th>
<th>a</th>
<th>b</th>
<th>c</th>
<th>d</th>
<th>e</th>
<th>f</th>
</tr>
</thead><tbody>
<tr>
<td>First Hex Digit</td>
<td>x*16</td>
<td>0</td>
<td>16</td>
<td>32</td>
<td>48</td>
<td>64</td>
<td>80</td>
<td>96</td>
<td>112</td>
<td>128</td>
<td>144</td>
<td>160</td>
<td>176</td>
<td>192</td>
<td>208</td>
<td>224</td>
<td>240</td>
</tr>
<tr>
<td>Second Hex Digit</td>
<td>x*1</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
<td>5</td>
<td>6</td>
<td>7</td>
<td>8</td>
<td>9</td>
<td>10</td>
<td>11</td>
<td>12</td>
<td>13</td>
<td>14</td>
<td>15</td>
</tr>
</tbody></table>

<p>Open your <code>styles.css</code> file and go to the <code>article</code> selector block in your text editor. Identify the <code>background-color</code> property with the value of <code>#f0f0f0</code>. Using either a conversion tool or the mathematical formula with the chart previous, this will come to be <code>rgb(240, 240, 240)</code>, since the <strong>f</strong> in the sequence is in the second position and is equal to <strong>240</strong>. Adding these two values together is <strong>240</strong>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: <span class="highlight">rgb(240,240,240)</span>;
    border: 0.25rem solid #ccc;
    color: #444;
}
...
</code></pre>
<p>Next, for both the <code>border</code> and <code>color</code> values since these are written as the short form, it’s helpful to expand these out to the long form when converting to <code>rgb()</code>. The <code>#ccc</code> short form becomes <code>#cccccc</code> in the long form. That creates a formula of <strong>(12 * 16) + 12</strong>, which results in <strong>204</strong>. Applying that value to each channel, <code>#ccc</code> becomes <code>rgb(204, 204, 204)</code>. After applying this new value, as shown in the following highlighted section, the same approach can be applied to the <code>color</code> value of <code>#444</code>, which becomes <code>rgb(68, 68, 68)</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: rgb(240,240,240);
    border: 0.25rem solid <span class="highlight">rgb(204, 204, 204)</span>;
    color: <span class="highlight">rgb(68, 68, 68)</span>;
}
</code></pre>
<p>Next, move on to the <code>h1</code> and <code>hr</code> color properties. For all three of these properties, the colors only use the red channel, meaning the <code>rgb()</code> will be <strong>0</strong> for the green and blue channels:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    ...
    color: <span class="highlight">rgb(153, 0, 0)</span>;
}

hr {
    ...
    border: 2px solid <span class="highlight">rgb(189, 0, 0)</span>;
    background-color: <span class="highlight">rgb(255, 0, 0)</span>;
}
...
</code></pre>
<p>For the <code>h1</code> <code>color</code> property, the <code>#900</code> short form is expanded to <code>#990000</code>, and using the chart the <strong>99</strong> sequence can be calculated as <strong>(16 * 9) + 9</strong>, which equals <strong>153</strong> for a result of <code>rgb(153, 0, 0)</code>. Following the same formula for the <code>hr</code> properties, the <strong>bd</strong> in the <code>border</code> hex value becomes <strong>189</strong>. The <strong>f</strong> in the short form <code>background-color</code> property value is expanded to <strong>ff</strong> with a result of <strong>255</strong>, which is the highest value of a color in both <code>rgb()</code> and hexadecimal color formats.</p>

<p>Continuing to work in your text editor in the <code>styles.css</code> file, identify the <code>footer</code> selector and update the gray used on the <code>color</code> property. Since the value is the short form <code>#888</code>, it can be expanded to <code>#888888</code>, and using the chart and formula, the final value becomes <code>rgb(136, 136, 136)</code>:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
footer {
    ...
    color: <span class="highlight">rgb(136, 136, 136)</span>;
}
...
</code></pre>
<p>Save your changes to the <code>styles.css</code> file then reload the <code>index.html</code> file in your web browser. There will be no difference from the previous step with the hexadecimal values as these <code>rgb()</code> values are equivalent.</p>

<p>The hexadecimal and the <code>rgb()</code> formats equate to the same numerical value, but the <code>rgb()</code> format is more human readable, giving it an advantage over the former format. This allows developers to make informed changes faster and to adjust the intensity of a channel to produce a color, such as adding more red to make a color feel warmer.</p>

<p>To demonstrate this, add 5 to the value of the red channel for each of the gray colors in the <code>article</code> and <code>footer</code> selectors. For the <code>article</code> properties, change the <code>background-color</code> red channel to <code>245</code>, the <code>border</code> red channel to <code>209</code>, and the <code>color</code> property red channel to <code>73</code>. Then, change the <code>footer</code> selector’s <code>color</code> property red channel to <code>141</code>. These changes are shown in the following code block:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: <span class="highlight">rgb(245,240,240)</span>;
    border: 0.25rem solid <span class="highlight">rgb(209, 204, 204)</span>;
    color: <span class="highlight">rgb(73, 68, 68)</span>;
}
...
footer {
    ...
    color: <span class="highlight">rgb(141, 136, 136)</span>;
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code>, return to your browser, and refresh <code>index.html</code>. The gray colors will become a warmer tint as they have more red than green or blue. The following image shows a comparison between the cooler and warmer gray:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/8.png" alt="Comparison of two styles. The left image has dark gray text in a sans serif font with a lighter gray background and border with a title text in red and a rule line between paragraphs. The right image has the same composition, with the grays in a slighting warmer variant."></p>

<p>In this section, you learned about the <code>rgb()</code> color format and how to convert hexadecimal color values into numerical values, which can be easier to work with. You also added a bit more to the red channel of a <code>rgb()</code> color format to create a warmer variety of gray colors. In the next section, you will use the <code>hsl()</code> color format, which stands for hue, saturation, and lightness.</p>

<h2 id="applying-colors-with-hsl">Applying Colors With <code>hsl()</code></h2>

<p>Where the hexadecimal and <code>rgb()</code> color formats have a closer connection to the combined values of the primary colors, <code>hsl()</code> uses a color from the <a href="https://en.wikipedia.org/wiki/Color_wheel">color wheel</a> with saturation and lightness levels and to generate a final color. </p>

<p>Like the <code>rgb()</code> format, the <code>hsl()</code> format consists of three values in a comma-separated sequence. The first value is the <em>hue</em>, which is a degree marker on the color wheel. The second value is <em>saturation</em>, which is a percentage value where <strong>100%</strong> means full color and <strong>0%</strong> means no color, which results in white, black, or gray depending on the value of the final number in the sequence. <em>Lightness</em> is a percentage value that ranges from white at <strong>100%</strong> to black at <strong>0%</strong>.</p>

<p>The color circle starts at 0 degrees, which is red, and goes around the rainbow to orange, yellow, green, blue, purple, and back to red as the circle completes at 360 degrees. This degree value is the first value of the sequence and can be represented by a number alone or using any of the <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Values_and_Units#Angle_units">angle units</a>. The saturation and lightness are both percentage values and require the percent sign to be present, even if the value is <strong>0</strong>.</p>

<p>The biggest advantage to using the <code>hsl()</code> format is that colors can be more cohesive and complementary when there are similar values. For example, a monochromatic color scheme can be quickly put together by deciding a hue value and a saturation percentage, then setting all the other colors as variations of lightness. Likewise, colors with a similar saturation level will appear better matched despite the difference in hue or lightness levels. </p>

<p>Converting from hexadecimal or <code>rgb()</code> to an <code>hsl()</code> formatted color is not as straightforward. A <a href="https://htmlcolors.com/hex-to-hsl">color conversion tool</a> can help change values from hexadecimal to <code>hsl()</code>.</p>

<p>To begin working with the <code>hsl()</code> color format, open <code>styles.css</code> in your text editor and go to the <code>article</code> and <code>footer</code> selectors. Returning to the gray values from earlier in this tutorial, you can set the hue and lightness values to be <code>0</code> and <code>0%</code>, respectively. Since these are grayscale, only lightness values need to be adjusted:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: <span class="highlight">hsl(0, 0%, 94%)</span>;
    border: 0.25rem solid <span class="highlight">hsl(0, 0%, 80%)</span>;
    color: <span class="highlight">hsl(0, 0%, 27%)</span>;
}
...
footer {
    ...
    color: <span class="highlight">hsl(0, 0%, 45%)</span>;
}
...
</code></pre>
<p>The <code>background-color</code> is a very light gray, and so it is closer to 100% at <code>94%</code>. The <code>border</code> value of the <code>article</code> is a bit darker, but still a light gray with a lightness value at <code>80%</code>. Next, the <code>color</code> value of the <code>article</code> comes in at a much darker <code>27%</code>. Lastly, the <code>color</code> for the <code>footer</code> text uses a lightness value of <code>45%</code>, giving a lighter but still readable gray for the citation reference text. Using only the lightness value, a monochromatic gray palette comes together quickly.</p>

<p>Next, move down to the <code>h1</code> and <code>hr</code> selector blocks. A similar setup will occur as with the <code>article</code> color properties: the hue and saturation values will remain unchanged for the next three properties. The hue value is set to <code>0</code> and the saturation is set to <code>100%</code>, giving a full saturation red. Lightness will once again differentiate these red values:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    ...
    color: <span class="highlight">hsl(0, 100%, 25%)</span>;
}

hr {
    ...
    border: 2px solid <span class="highlight">hsl(0, 100%, 35%)</span>;
    background-color: <span class="highlight">hsl(0, 100%, 50%)</span>;
}
...
</code></pre>
<p>The <code>h1</code> <code>color</code> gets a dark red by having a lightness value of <code>25%</code>. While the <code>hr</code> will have a darker red <code>border</code> with a <code>35%</code> lightness value, the <code>background-color</code> gets a pure red by having a lightness value of <code>50%</code>. As the value goes up from 50%, a red becomes pink until it is white, and lower than 50% a red becomes more maroon until it is black.</p>

<p>Save your changes to <code>styles.css</code>, return to your browser, and refresh <code>index.html</code> to find how your styles appear when written in the <code>hsl()</code> color format. The following image shows how this will appear:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/9.png" alt="Dark gray text in a sans serif font with a lighter gray background and border with a title text in red and a rule line between paragraphs that is two shades of red."></p>

<p>Now, return to your text editor and go to the <code>article</code> and <code>footer</code> selectors in your <code>styles.css</code> file. You will now adjust only the hue and saturation values, but leave the lightness value as is. Set the hue value to <code>200</code> on all four color properties across the two selectors. Then, for the color properties on the <code>article</code> selector, set the saturation value to <code>50%</code>. Finally, set the saturation level for the <code>footer</code> <code>color</code> property to <code>100%</code>. Reference the highlighted portions of the following code block for how these color values will appear:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
article {
    ...
    background-color: hsl(<span class="highlight">200</span>, <span class="highlight">50%</span>, 94%);
    border: 0.25rem solid hsl(<span class="highlight">200</span>, <span class="highlight">50%</span>, 80%);
    color: hsl(<span class="highlight">200</span>, <span class="highlight">50%</span>, 27%);
}
...
footer {
    ...
    color: hsl(<span class="highlight">200</span>, <span class="highlight">100%</span>, 45%);
}
...
</code></pre>
<p>Save your changes to the <code>styles.css</code> file and reload <code>index.html</code> in your web browser. As in the following image, the gray color is now a cyan color of varying lightness and saturation. The <code>article</code> colors are more muted due to the <code>50%</code> saturation, while the <code>footer</code> color is much more vibrant with the <code>100%</code> saturation.</p>

<p><img src="https://assets.digitalocean.com/articles/67679/10.png" alt="Dark blue text in a sans serif font with a lighter blue background and border, with a title text in red and a rule line between paragraphs that is two shades of red."></p>

<p>One of the greatest advantages of working with the <code>hsl()</code> color format is creating complementary color palettes. A complementary color is always on the opposite side of the color circle. Red’s complementary color is green. With red at the 0 degree mark in the hue value, its complement is at the 180 degree mark. In the case of the <code>article</code> and <code>footer</code> colors having a hue value of <strong>200</strong>, subtracting <strong>180</strong> will give a complementary hue value of <strong>20</strong>, an orange color.</p>

<p>Go to the <code>h1</code> and <code>hr</code> selectors in the <code>styles.css</code> file in your text editor. For the hue values on each of the color properties, change the hue from <code>0</code> to <code>20</code> to set the colors from red to the complementary color of orange:</p>
<div class="code-label " title="styles.css">styles.css</div><pre class="code-pre "><code class="code-highlight language-css">...
h1 {
    ...
    color: hsl(<span class="highlight">20</span>, 100%, 25%);
}

hr {
    ...
    border: 2px solid hsl(<span class="highlight">20</span>, 100%, 35%);
    background-color: hsl(<span class="highlight">20</span>, 100%, 50%);
}
...
</code></pre>
<p>Save your changes to <code>styles.css</code> and return to your web browser to refresh <code>index.html</code>. With a few modifications to the hue values, a pleasant color palette has come together. The following image shows how this will appear in your browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67679/11.png" alt="Dark blue text in a sans serif font with a lighter blue background and border, with a title text in a dark orange and a rule line between paragraphs that is two shades of orange."></p>

<p>In this section, you used the <code>hsl()</code> color format to create colors using the color theory aspects of hue, saturation, and lightness. You also were able to create a complementary color palette using a formula to get the opposite color on the color circle.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you used the four methods of defining color on websites with CSS. The keyword color values allow for quick access to predefined colors as outlined by the CSS specifications. Hexadecimal colors are the most common and widely used format that hold a lot of information in a small number of characters. The <code>rgb()</code> color formation converts the values of the hexadecimal value to a more comprehensive format using numeric values in a comma-separated list. Lastly, the <code>hsl()</code> color format allows for the concepts of color theory to be applied to web site development by using the hues of the color circle, saturation, and lightness to create distinctive and complementary color palettes. All four of these formats can be used together on a single website, each playing to their own strength.</p>

<p>If you would like to read more CSS tutorials, try out the other tutorials in the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-style-html-with-css">How To Style HTML with CSS series</a>.</p>
