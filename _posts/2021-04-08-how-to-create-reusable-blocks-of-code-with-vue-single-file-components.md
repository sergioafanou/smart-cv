---
title : "How To Create Reusable Blocks of Code with Vue Single-File Components"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-create-reusable-blocks-of-code-with-vue-single-file-components
image: "https://sergio.afanou.com/assets/images/image-midres-20.jpg"
---

<p><em>The author selected <a href="https://osmihelp.org/">Open Sourcing Mental Illness</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>When creating a web application using <a href="https://vuejs.org/">Vue.js</a>, it&rsquo;s a best practice to construct your application in small, modular blocks of code. Not only does this keep the parts of your application focused, but it also makes the application easier to update as it grows in complexity. Since an app generated from the <a href="https://cli.vuejs.org/">Vue CLI</a> requires a build step, you have access to a <em>Single-File Components</em> (SFC) to introduce modularity into your app. SFCs have the <code>.vue</code> extension and contain an HTML <code>&lt;template&gt;</code>, <code>&lt;script&gt;</code>, and <code>&lt;style&gt;</code> tags and can be implemented in other components.</p>

<p>SFCs give the developer a way to create their own <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">HTML</a> tags for each of their components and then use them in their application. In the same way that the <code>&lt;p&gt;</code> HTML tag will render a paragraph in the browser, and hold non-rendered functionality as well, the component tags will render the SFC wherever it is placed in the Vue template.</p>

<p>In this tutorial, you are going to create a SFC and use <code>props</code> to pass data down and <code>slots</code> to inject content between tags. By the end of this tutorial, you will have a general understanding of what SFCs are and how to approach code re-usability. </p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li><a href="https://nodejs.org/en/">Node.js</a> version <code>14.16.0</code> or greater installed on your computer. To install this on macOS or Ubuntu 20.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How To Install Node.js and Create a Local Development Environment on macOS</a> or the <strong>Installing Using a PPA</strong> section of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04">How To Install Node.js on Ubuntu 20.04</a></li>
<li><a href="https://www.digitalocean.com/community/tutorials/how-to-generate-a-vue-js-single-page-app-with-vue-create">Vue CLI installed</a> on your machine and a fresh project generated. Since this tutorial uses the Vue 3 Composition API, make sure that you select the <code>3.x (Preview)</code> option when generating the app. The name of this project will be <code>sfc-project</code>, which will act as the root directory.</li>
<li>You will also need a basic knowledge of JavaScript, HTML, and CSS, which you can find in our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">How To Build a Website With HTML</a> series, <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-css">How To Build a Website With CSS</a> series, and in <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript</a>.</li>
</ul>

<h2 id="step-1-—-setting-up-the-project">Step 1 — Setting Up the Project</h2>

<p>In this tutorial, you are going to be creating an airport card component that displays a number of airports and their codes in a series of cards. After following the Prerequisites section, you will have a new Vue project named <code>sfc-project</code>. In this section, you will import data into this generated application. This data will be an array of objects consisting of a few properties that you will use to display information in the browser.</p>

<p>Once the project is generated, open your terminal and <code>cd</code> or change directory into the root <code>src</code> folder:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd sfc-project/src
</li></ul></code></pre>
<p>From there, create a new directory named <code>data</code> with the <code>mkdir</code> command, then create a new file with the name <code>us-airports.js</code> using the <code>touch</code> command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir data
</li><li class="line" data-prefix="$">touch data/us-airports.js
</li></ul></code></pre>
<p>In your text editor of choice, open this new JavaScript file and add in the following local data:</p>
<div class="code-label " title="sfc-project/data/us-airports.js">sfc-project/data/us-airports.js</div><pre class="code-pre "><code class="code-highlight language-javascript">export default [
  {
    name: 'Cincinnati/Northern Kentucky International Airport',
    abbreviation: 'CVG',
    city: 'Hebron',
    state: 'KY'
  },
  {
    name: 'Seattle-Tacoma International Airport',
    abbreviation: 'SEA',
    city: 'Seattle',
    state: 'WA'
  },
  {
    name: 'Minneapolis-Saint Paul International Airport',
    abbreviation: 'MSP',
    city: 'Bloomington',
    state: 'MN'
  }
]
</code></pre>
<p>This data is an <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">array</a> of <a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">objects</a> consisting of a few airports in the United States. This will be rendered in a Single-File Component later in the tutorial.</p>

<p>Save and exit the file.</p>

<p>Next, you will create another set of airport data. This data will consist of European airports. Using the <code>touch</code> command, create a new JavaScript file named <code>eu-airports.js</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch data/eu-airports.js
</li></ul></code></pre>
<p>Then open the file and add the following data:</p>
<div class="code-label " title="sfc-project/data/eu-airports.js">sfc-project/data/eu-airports.js</div><pre class="code-pre "><code class="code-highlight language-javascript">export default [
  {
    name: 'Paris-Charles de Gaulle Airport',
    abbreviation: 'CDG',
    city: 'Paris',
    state: 'Ile de France'
  },
  {
    name: 'Flughafen München',
    abbreviation: 'MUC',
    city: 'Munich',
    state: 'Bavaria'
  },
  {
    name: 'Fiumicino "Leonardo da Vinci" International Airport',
    abbreviation: 'FCO',
    city: 'Rome',
    state: 'Lazio'
  }
]
</code></pre>
<p>This set of data is for European airports in France, Germany, and Italy, respectively.</p>

<p>Save and exit the file.</p>

<p>Next, in the root directory, run the following command in your terminal to start your Vue CLI application running on a local development server:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">npm run serve
</li></ul></code></pre>
<p>This will open the application in your browser on <code>localhost:8080</code>. The port number may be different on your machine. </p>

<p>Visit the address in your browser. You will find the following start up screen:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/1.png" alt="Vue default template page"></p>

<p>Next, start a new terminal and open your <code>App.vue</code> file in your <code>src</code> folder. In this file, delete the <code>img</code> and <code>HelloWorld</code> tags in the <code>&lt;template&gt;</code> and  the <code>components</code> section and <code>import</code> statement in the <code>&lt;script&gt;</code>. Your <code>App.vue</code> will resemble the following:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;

&lt;/template&gt;

&lt;script&gt;
export default {
  name: 'App',
}
&lt;/script&gt;

&lt;style&gt;
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
&lt;/style&gt;
</code></pre>
<p>After this, import the <code>us-airports.js</code> file that you created earlier. In order to make this data reactive so you can use it in the <code>&lt;template&gt;</code>, you need to import the <a href="https://v3.vuejs.org/api/refs-api.html"><code>ref</code> function</a> from <code>vue</code>. You will need to return the <code>airport</code> reference so the data can be used in the HTML template.</p>

<p>Add the following highlighted lines:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  <span class="highlight">&lt;div class="wrapper"&gt;</span>
    <span class="highlight">&lt;div v-for="airport in airports" :key="airport.abbreviation" class="card"&gt;</span>
      <span class="highlight">&lt;p&gt;{{ airport.abbreviation }}&lt;/p&gt;</span>
      <span class="highlight">&lt;p&gt;{{ airport.name }}&lt;/p&gt;</span>
      <span class="highlight">&lt;p&gt;{{ airport.city }}, {{ airport.state }}&lt;/p&gt;</span>
    <span class="highlight">&lt;/div&gt;</span>
  <span class="highlight">&lt;/div&gt;</span>
&lt;/template&gt;

&lt;script&gt;
<span class="highlight">import { ref } from 'vue'</span>
<span class="highlight">import data from '@/data/us-airports.js'</span>

export default {
  name: 'App',
  <span class="highlight">setup() {</span>
    <span class="highlight">const airports = ref(data)</span>

    <span class="highlight">return { airports }</span>
  <span class="highlight">}</span>
}
&lt;/script&gt;
...
</code></pre>
<p>In this snippet, you imported the data and rendered it using <code>&lt;div&gt;</code> elements and the <a href="https://v3.vuejs.org/api/directives.html#v-for"><code>v-for</code> directive</a> in the template.</p>

<p>At this point, the data is imported and ready to be used in the <code>App.vue</code> component. But first, add some styling to make the data easier for users to read. In this same file, add the following CSS in the <code>&lt;style&gt;</code> tag:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;style&gt;
#app { ... }

<span class="highlight">.wrapper {</span>
  <span class="highlight">display: grid;</span>
  <span class="highlight">grid-template-columns: 1fr 1fr 1fr;</span>
  <span class="highlight">grid-column-gap: 1rem;</span>
  <span class="highlight">max-width: 960px;</span>
  <span class="highlight">margin: 0 auto;</span>
<span class="highlight">}</span>

<span class="highlight">.card {</span>
  <span class="highlight">border: 3px solid;</span>
  <span class="highlight">border-radius: .5rem;</span>
  <span class="highlight">padding: 1rem;</span>
  <span class="highlight">margin-bottom: 1rem;</span>
<span class="highlight">}</span>

<span class="highlight">.card p:first-child {</span>
  <span class="highlight">font-weight: bold;</span>
  <span class="highlight">font-size: 2.5rem;</span>
  <span class="highlight">margin: 1rem 0;</span>
<span class="highlight">}</span>

<span class="highlight">.card p:last-child {</span>
  <span class="highlight">font-style: italic;</span>
  <span class="highlight">font-size: .8rem;</span>
<span class="highlight">}</span>
&lt;/style&gt;
</code></pre>
<p>In this case, you are using <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout">CSS Grid</a> to compose these cards of airport codes into a grid of three. Notice how this grid is set up in the <code>.wrapper</code> class. The <code>.card</code> class is the card or section that contains each airport code, name, and location. If you would like to learn more about CSS, check out our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-style-html-with-css">How To Style HTML with CSS</a>.</p>

<p>Open your browser and navigate to <code>localhost:8080</code>. You will find a number of cards with airport codes and information:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/2.png" alt="Three airport cards rendering the data for the US airports dataset"></p>

<p>Now that you have set up your initial app, you can refactor the data into a Single-File Component in the next step.</p>

<h2 id="step-2-—-creating-a-single-file-component">Step 2 — Creating a Single-File Component</h2>

<p>Since Vue CLI uses <a href="https://webpack.js.org/">Webpack</a> to build your app into something the browser can read, your app can use SFCs or <code>.vue</code> files instead of plain JavaScript. These files are a way for you to create small blocks of scalable and reusable code. If you were to change one component, it would be updated everywhere.</p>

<p>These <code>.vue</code> components usually consist of these three things: <code>&lt;template&gt;</code>, <code>&lt;script&gt;</code>, and <code>&lt;style&gt;</code> elements. SFC components can either have <em>scoped</em> or <em>unscoped</em> styles. When a component has scoped styles, that means the CSS between the <code>&lt;style&gt;</code> tags will only affect the HTML in the <code>&lt;template&gt;</code> in the same file. If a component has un-scoped styles, the CSS will affect the parent component as well as its children.</p>

<p>With your project successfully set up, you are now going to break these airport cards into a component called <code>AirportCards.vue</code>. As it stands now, the HTML in the <code>App.vue</code> is not very reusable. You will break this off into its own component so you can import it anywhere else into this app while preserving the functionality and visuals.</p>

<p>In your terminal, create this <code>.vue</code> file in the <code>components</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch src/components/AirportCards.vue
</li></ul></code></pre>
<p>Open the <code>AiportCards.vue</code> component in your text editor. To illustrate how you can re-use blocks of code using components, move most of the code from the <code>App.vue</code> file to the <code>AirportCards.vue</code> component:</p>
<div class="code-label " title="sfc-project/src/components/AirportCards.vue">sfc-project/src/components/AirportCards.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div class="wrapper"&gt;
    &lt;div v-for="airport in airports" :key="airport.abbreviation" class="card"&gt;
      &lt;p&gt;{{ airport.abbreviation }}&lt;/p&gt;
      &lt;p&gt;{{ airport.name }}&lt;/p&gt;
      &lt;p&gt;{{ airport.city }}, {{ airport.state }}&lt;/p&gt;
    &lt;/div&gt;
  &lt;/div&gt;
&lt;/template&gt;

&lt;script&gt;
import { ref } from 'vue'
import data from '@/data/us-airports.js'

export default {
  name: 'Airports',
  setup() {
    const airports = ref(data)

    return { airports }
  }
}
&lt;/script&gt;

&lt;style scoped&gt;
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-column-gap: 1rem;
  max-width: 960px;
  margin: 0 auto;
}

.card {
  border: 3px solid;
  border-radius: .5rem;
  padding: 1rem;
  margin-bottom: 1rem;
}

.card p:first-child {
  font-weight: bold;
  font-size: 2.5rem;
  margin: 1rem 0;
}

.card p:last-child {
  font-style: italic;
  font-size: .8rem;
}
&lt;/style&gt;
</code></pre>
<p>Save and close the file.</p>

<p>Next, open your <code>App.vue</code> file. Now you can clean up the <code>App.vue</code> component and import <code>AirportCards.vue</code> into it:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  <span class="highlight">&lt;AirportCards /&gt;</span>
&lt;/template&gt;

&lt;script&gt;
<span class="highlight">import AirportCards from '@/components/Airports.vue'</span>

export default {
  name: 'App',
  <span class="highlight">components: {</span>
    <span class="highlight">AirportCards</span>
  <span class="highlight">}</span>
}
&lt;/script&gt;

&lt;style scoped&gt;
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
&lt;/style&gt;
</code></pre>
<p>Now that <code>AirportCards</code> is a standalone component, you have put it in the <code>&lt;template&gt;</code> HTML as you would a <code>&lt;p&gt;</code> tag.</p>

<p>When you open up <code>localhost:8080</code> in your browser, nothing will change. The same three airport cards will still display, because you are rendering the new SFC in the <code>&lt;AirportCards /&gt;</code> element.</p>

<p>Next, add this same component in the template again to illustrate the re-usability of components:</p>
<div class="code-label " title="/src/App.vue">/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;AirportCards /&gt;
  <span class="highlight">&lt;airport-cards /&gt;</span>
&lt;/template&gt;
...
</code></pre>
<p>You may notice that this new instance of <code>AirportCards.vue</code> is using <em>kebab-case</em> over <em>PascalCase</em>. When referencing components, Vue does not care which one you use. All capitalized words and letters will be separated by a hyphen and will be lower case. The same applies to props as well, which will be explained in the next section.</p>

<p><span class='note'><strong>Note:</strong> The case that you use is up to personal preference, but consistency is important. Vue.js recommends using kebab-case as it follows the HTML standard.<br></span></p>

<p>Open the browser and visit <code>localhost:8080</code>. You will find the cards duplicated:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/3.png" alt="The US airport cards rendered twice in the browser."></p>

<p>This adds modularity to your app, but the data is still static. The row of cards is useful if you want to show the same three airports, but changing the data source would require changing the hard-coded data. In the next step, you are going to expand this component further by registering props and passing data from the parent down to the child component.</p>

<h2 id="step-3-—-leveraging-props-to-pass-down-data">Step 3 — Leveraging Props to Pass Down Data</h2>

<p>In the previous step, you created an <code>AirportCards.vue</code> component that rendered a number of cards from the data in the <code>us-airports.js</code> file. In addition to that, you also doubled the same component reference to illustrate how you can easily duplicate code by adding another instance of that component in the <code>&lt;template&gt;</code>.</p>

<p>However, leaving the data static will make it difficult to change the data in the future. When working with SFCs, it can help if you think of components as functions. These functions are components that can take in arguments (props) and return something (HTML). For this case, you will pass data into the airports parameter to return dynamic HTML.</p>

<p>Open the <code>AirportCards.vue</code> component in your text editor. You are currently importing <code>data</code> from the <code>us-airports.js</code> file. Remove this import statement, as well as the <code>setup</code> function in the <code>&lt;script&gt;</code> tag:</p>
<div class="code-label " title="sfc-project/src/components/AirportCards.vue">sfc-project/src/components/AirportCards.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;

export default {
  name: 'Airports',
}
&lt;/script&gt;
...
</code></pre>
<p>Save the file. At this point, nothing will render in the browser. </p>

<p>Next, move forward by defining a prop. This prop can be named anything; it just describes the data coming in and associates it with a name.</p>

<p>To create a prop, add the <code>props</code> property in the component. The value of this is a series of key/value pairs. The key is the name of the prop, and the value is the description of the data. It&rsquo;s best to provide as much description as you can:</p>
<div class="code-label " title="sfc-project/src/components/AirportCards.vue">sfc-project/src/components/AirportCards.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;

export default {
  name: 'Airports',
  <span class="highlight">props: {</span>
    <span class="highlight">airports: {</span>
      <span class="highlight">type: Array,</span>
      <span class="highlight">required: true</span>
    <span class="highlight">}</span>
  <span class="highlight">}</span>
}
&lt;/script&gt;
...
</code></pre>
<p>Now, in <code>AirportCard.vue</code>, the property <code>airports</code> refers to the data that is passed in. Save and exit from the file.</p>

<p>Next, open the <code>App.vue</code> component in your text editor. Like before, you will need to <code>import</code> data from the <code>us-airports.js</code> file and <code>import</code> the <code>ref</code> function from Vue to make it reactive for the HTML template:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;AirportCards <span class="highlight">:airports="usAirports"</span> /&gt;
  &lt;airport-cards /&gt;
&lt;/template&gt;

&lt;script&gt;
<span class="highlight">import { ref } from 'vue'</span>
import AirportCards from '@/components/Airports.vue'
<span class="highlight">import usAirportData from '@/data/us-airports.js'</span>

export default {
  name: 'App',
  components: {
    AirportCards
  },
  <span class="highlight">setup() {</span>
    <span class="highlight">const usAirports = ref(usAirportData)</span>

    <span class="highlight">return { usAirports }</span>
  <span class="highlight">}</span>
}
&lt;/script&gt;
</code></pre>
<p>If you open your browser and visit <code>localhost:8080</code>, you will find the same US airports as before:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/2.png" alt="US airports rendered on cards in the browser"></p>

<p>There is another <code>AirportCards.vue</code> instance in your template. Since you defined props within that component, you can pass any data with the same structure to render a number of cards from different airports. This is where the <code>eu-airports.js</code> file from the initial setup comes in.</p>

<p>In <code>App.vue</code>, import the <code>eu-airports.js</code> file, wrap it in the <code>ref</code> function to make it reactive, and return it:</p>
<div class="code-label " title="/src/App.vue">/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;AirportCards :airports="usAirports" /&gt;
  &lt;airport-cards <span class="highlight">:airports="euAirports"</span> /&gt;
&lt;/template&gt;

&lt;script&gt;
...
import usAirportData from '@/data/us-airports.js'
<span class="highlight">import euAirportData from '@/data/eu-airports.js'</span>

export default {
  ...
  setup() {
    const usAirports = ref(usAirportData)
    <span class="highlight">const euAirports = ref(euAirportData)</span>

    return { usAirports<span class="highlight">, euAirports</span> }
  }
}
&lt;/script&gt;
...
</code></pre>
<p>Open your browser and visit <code>localhost:8080</code>. You will find the European airport data rendered below the US airport data: </p>

<p><img src="https://assets.digitalocean.com/articles/67735/4.png" alt="US airport data rendered as cards, followed by European airport data rendered in the same way."></p>

<p>You have now successfully passed in a different dataset into the same component. With <code>props</code>, you are essentially re-assigning data to a new name and using that new name to reference data in the child component.</p>

<p>At this point, this application is starting to become more dynamic. But there is still something else that you can do to make this even more focused and re-usable. In Vue.js, you can use something called <em>slots</em>. In the next step, you are going to create a <code>Card.vue</code> component with a default <code>slot</code> that injects the HTML into a placeholder.</p>

<h2 id="step-4-—-creating-a-general-card-component-using-slots">Step 4 — Creating a General Card Component Using Slots</h2>

<p>Slots are a great way to create re-usable components, especially if you do not know if the HTML in that component will be similar. In the previous step, you created another <code>AirportCards.vue</code> instance with different data. In that example, the HTML is the same for each. It&rsquo;s a <code>&lt;div&gt;</code> in a <code>v-for</code> loop with paragraph tags.</p>

<p>Open your terminal and create a new file using the <code>touch</code> command. This file will be named <code>Card.vue</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch src/components/Card.vue
</li></ul></code></pre>
<p>In your text editor, open the new <code>Card.vue</code> component. You are going to take some of the CSS from <code>AirportCards.vue</code> and add it into this new component.</p>

<p>Create a <code>&lt;style&gt;</code> tag and add in the following CSS:</p>
<div class="code-label " title="sfc-project/src/components/Card.vue">sfc-project/src/components/Card.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;style&gt;
.card {
  border: 3px solid;
  border-radius: .5rem;
  padding: 1rem;
  margin-bottom: 1rem;
}
&lt;/style&gt;
</code></pre>
<p>Next, create the HTML template for this component. Before the <code>&lt;style&gt;</code> tag, add a <code>&lt;template&gt;</code> tag with the following:</p>
<div class="code-label " title="sfc-project/src/components/Card.vue">sfc-project/src/components/Card.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div class="card"&gt;

  &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>In between the <code>&lt;div class="card"&gt;</code>, add the <code>&lt;slot /&gt;</code> component. This is a component that is provided to you by Vue. There is no need to import this component; it is globally imported by Vue.js:</p>
<div class="code-label " title="sfc-project/src/components/Card.vue">sfc-project/src/components/Card.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div class="card"&gt;
    <span class="highlight">&lt;slot /&gt;</span>
  &lt;/div&gt;
&lt;/template&gt;
</code></pre>
<p>This <code>slot</code> is a placeholder for the HTML that is between the <code>Card.vue</code> component&rsquo;s tags when it is referenced elsewhere.</p>

<p>Save and exit the file.</p>

<p>Now go back to the <code>AirportCards.vue</code> component. First, import the new <code>Card.vue</code> SFC you just created:</p>
<div class="code-label " title="sfc-project/src/components/AirportCards.vue">sfc-project/src/components/AirportCards.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;
<span class="highlight">import Card from '@/components/Card.vue'</span>

export default {
  name: 'Airports',
  props: { ... },
  <span class="highlight">components: {</span>
    <span class="highlight">Card</span>
  <span class="highlight">}</span>
}
&lt;/script&gt;
...
</code></pre>
<p>Now all that is left is to replace the <code>&lt;div&gt;</code> with <code>&lt;card&gt;</code>:</p>
<div class="code-label " title="/src/components/AirportCards.vue">/src/components/AirportCards.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div class="wrapper"&gt;
    &lt;<span class="highlight">card</span> v-for="airport in airports" :key="airport.abbreviation"&gt;
      &lt;p&gt;{{ airport.abbreviation }}&lt;/p&gt;
      &lt;p&gt;{{ airport.name }}&lt;/p&gt;
      &lt;p&gt;{{ airport.city }}, {{ airport.state }}&lt;/p&gt;
    &lt;/<span class="highlight">card</span>&gt;
  &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>Since you have a <code>&lt;slot /&gt;</code> in your <code>Card.vue</code> component, the HTML between the <code>&lt;card&gt;</code> tags is injected in its place while preserving all styles that have been associated with a card.</p>

<p>Save the file. When you open your browser at <code>localhost:8080</code>, you will find the same cards that you&rsquo;ve had previously. The difference now is that your <code>AirportCards.vue</code> now reference the <code>Card.vue</code> component:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/4.png" alt="US airport data rendered as cards, followed by European airport data rendered in the same way."></p>

<p>To show the power of slots, open the <code>App.vue</code> component in your application and import the <code>Card.vue</code> component:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;
...
<span class="highlight">import Card from '@/components/Card.vue'</span>

export default {
  ...
  components: {
    AirportCards,
    <span class="highlight">Card</span>
  },
  setup() { ... }
}
&lt;/script&gt;
...
</code></pre>
<p>In the <code>&lt;template&gt;</code>, add the following under the <code>&lt;airport-cards /&gt;</code> instances:</p>
<div class="code-label " title="sfc-project/src/App.vue">sfc-project/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;AirportCards :airports="usAirports"/&gt;
  &lt;airport-cards :airports="euAirports" /&gt;
  <span class="highlight">&lt;card&gt;</span>
    <span class="highlight">&lt;p&gt;US Airports&lt;/p&gt;</span>
    <span class="highlight">&lt;p&gt;Total: {{ usAirports.length }}&lt;/p&gt;</span>
  <span class="highlight">&lt;/card&gt;</span>
  <span class="highlight">&lt;card&gt;</span>
    <span class="highlight">&lt;p&gt;EU Airports&lt;/p&gt;</span>
    <span class="highlight">&lt;p&gt;Total: {{ euAirports.length }}&lt;/p&gt;</span>
  <span class="highlight">&lt;/card&gt;</span>
&lt;/template&gt;
...
</code></pre>
<p>Save the file and visit <code>localhost:8080</code> in the browser. Your browser will now render additional elements displaying the number of airports in the datasets:</p>

<p><img src="https://assets.digitalocean.com/articles/67735/5.png" alt="US and European airports displayed, along with two cards that display that there are three airports in each dataset"></p>

<p>The HTML between the <code>&lt;card /&gt;</code> tags is not exactly the same, but it still renders a generic card. When leveraging slots, you can use this functionality to create small, re-usable components that have a number of different uses.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you created single-file components and used <code>props</code> and <code>slots</code> to create reusable blocks of code. In the project, you created an <code>AirportCards.vue</code> component that renders a number of airport cards. You then broke up the <code>AirportCards.vue</code> component further into a <code>Card.vue</code> component with a default slot.</p>

<p>You ended up with a number of components that are dynamic and can be used in a number of different uses, all while keeping code maintainable and in keeping with the <a href="https://en.wikipedia.org/wiki/Don%27t_repeat_yourself">D.R.Y.</a> software principle. </p>

<p>To learn more about Vue components, it is recommended to ready through the <a href="https://vuejs.org/">Vue documentation</a>. For more tutorials on Vue, check out the <a href="https://www.digitalocean.com/community/tutorial_series/how-to-develop-websites-with-vue-js">How To Develop Websites with Vue.js series page</a>.</p>
