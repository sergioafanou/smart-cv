---
title : "How To Navigate Between Views with Vue Router"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-navigate-between-views-with-vue-router
image: "https://sergio.afanou.com/assets/images/image-midres-38.jpg"
---

<p><em>The author selected <a href="https://osmihelp.org/">Open Sourcing Mental Illness</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>Most websites or applications have multiple <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">HTML</a> pages that are either static or dynamically generated. With more traditional websites, there is one <a href="https://www.digitalocean.com/community/tutorials/introduction-to-the-dom">Document Object Model (DOM)</a> and one page per URL route. With <em>single-page applications</em> however, every &ldquo;page&rdquo; or view is rendered within one HTML page. User interface (UI) frameworks like Vue.js render these pages and components when needed through the use of a Virtual DOM. This <em>Virtual DOM</em> is a <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">JavaScript</a> representation of the original DOM and is much easier for the client to update.</p>

<p>When the user visits a single-page application, that application will compare itself to the Virtual DOM and re-render only the parts of the web page that are changed. This technique prevents the page from flashing white when clicking through links. Let&rsquo;s say you have three sections on a web page: a header, content, and a footer. When you navigate to another URL, Vue will only render the content area of the application that has changed between pages.</p>

<p>In Vue.js, you can create several views using the first-party library <a href="https://router.vuejs.org/">Vue Router</a>. This router makes an association with a view to a URL. In this tutorial, you are going to learn how to add the Vue Router library, integrate it into your project, and create dynamically generated routes. You will also learn the different types of routes available to you as a developer. When completed, you will have an application that you can navigate using a variety of methods. </p>

<p>To illustrate these concepts, you&rsquo;ll create a small application that displays airport information. When the user clicks on an airport card, the application will navigate to a dynamic view of the airport&rsquo;s details. To do this, the program will read a URL parameter and filter out data based on that parameter.</p>

<h3 id="prerequisites">Prerequisites</h3>

<p>To complete this tutorial, you will need:</p>

<ul>
<li>Node.js version <code>10.6.0</code> or greater installed on your computer. To install this on macOS or Ubuntu 18.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How To Install Node.js and Create a Local Development Environment</a> on macOS or the <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04"><strong>Installing Using a PPA</strong> section of How To Install Node.js on Ubuntu 18.04</a>.</li>
<li><a href="https://www.digitalocean.com/community/tutorials/how-to-generate-a-vue-js-single-page-app-with-vue-create">Vue CLI installed</a> on your machine and a fresh project generated. For the purpose of this tutorial, do not select Vue Router when creating your app. You will be installing this manually in the first step of this tutorial. The name of this project will be <code>airport-codes</code>, which will act as the root directory.</li>
<li>You will also need a basic knowledge of JavaScript, HTML, and CSS, which you can find in our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-html">How To Build a Website With HTML</a> series, <a href="https://www.digitalocean.com/community/tutorial_series/how-to-build-a-website-with-css">How To Build a Website With CSS</a> series, and in <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript</a>.</li>
</ul>

<h2 id="step-1-—-setting-up-the-sample-application">Step 1 — Setting Up the Sample Application</h2>

<p>The application that you are going to build to learn Vue Router will require some initial data. In this step, you will create and structure this data to be accessible to your Vue app later in the tutorial.</p>

<p>To add this dataset, you need to create a <code>data</code> directory and create a JavaScript file with the name <code>airports.js</code>. If you are not in the <code>src</code> directory, <code>cd</code> into it first:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd src
</li></ul></code></pre>
<p>Then make a <code>data</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir data
</li></ul></code></pre>
<p>Now create a <code>data/airports.js</code> file and open it in your text editor.</p>

<p>Add the following to create data for your application:</p>
<div class="code-label " title="airport-codes/src/data/airports.js">airport-codes/src/data/airports.js</div><pre class="code-pre "><code class="code-highlight language-javascript">export default [
  {
    name: 'Cincinnati/Northern Kentucky International Airport',
    abbreviation: 'CVG',
    city: 'Hebron',
    state: 'KY',
      destinations: {
      passenger: [ 'Toronto', 'Seattle/Tacoma', 'Austin', 'Charleston', 'Denver', 'Fort Lauderdale', 'Jacksonville', 'Las Vegas', 'Los Angeles', 'Baltimore', 'Chicago', 'Detroit', 'Dallas', 'Tampa' ],
        cargo: [ 'Anchorage', 'Baltimore', ' Chicago' , 'Indianapolis', 'Phoenix', 'San Francisco', 'Seattle', 'Louisville', 'Memphis' ]
      }
  },
  {
    name: 'Seattle-Tacoma International Airport',
    abbreviation: 'SEA',
    city: 'Seattle',
    state: 'WA',
      destinations: {
      passenger: [ 'Dublin', 'Mexico City', 'Vancouver', 'Albuquerque', 'Atlanta', 'Frankfurt', 'Amsterdam', 'Salt Lake City', 'Tokyo', 'Honolulu' ],
        cargo: [ 'Spokane', 'Chicago', 'Dallas', ' Shanghai', 'Cincinnati', 'Luxenbourg', 'Anchorage', 'Juneau', 'Calgary', 'Ontario' ]
      }
  },
  {
    name: 'Minneapolis-Saint Paul International Airport',
    abbreviation: 'MSP',
    city: 'Bloomington',
    state: 'MN',
      destinations: {
      passenger: [ 'Dublin', 'Paris', 'Punta Cana', 'Winnipeg', 'Tokyo', 'Denver', 'Tulsa', 'Washington DC', 'Orlando', 'Mexico City' ],
        cargo: [ 'Cincinnati', 'Omaha', 'Winnipeg', 'Chicago', 'St. Louis', 'Portland', 'Philadelphia', 'Milwaukee', 'Ontario' ]
      }
  }
]
</code></pre>
<p>This data is an <a href="https://www.digitalocean.com/community/tutorials/understanding-arrays-in-javascript">array</a> of <a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">objects</a> consisting of a few airports in the United States. In this application, you are going to iterate through this data to generate cards consisting of the <code>name</code>, <code>abbreviation</code>, <code>city</code>, and <code>state</code> properties. When the user clicks on a card, you will route them to a dynamic view with Vue Router and read from one of these properties. From there, you will create a nested route to display information stored in the <code>destination</code> property.</p>

<p>Save and exit from the file.</p>

<p>Next, create a <code>Home.vue</code> component inside of a directory called <code>views</code>. You can create this directory and component by opening your terminal and using the <code>mkdir</code> and <code>touch</code> commands.</p>

<p>Run the following command to make the directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir views
</li></ul></code></pre>
<p>Then create the <code>Home.vue</code> file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch views/Home.vue
</li></ul></code></pre>
<p>This <code>Home.vue</code> component will act as this application&rsquo;s homepage. In it, you will leverage the <code>v-for</code> directive to iterate through the <code>airports.js</code> dataset and show each airport in a card.</p>

<p>Add the following code to <code>Home.vue</code>:</p>
<div class="code-label " title="airport-codes/src/views/Home.vue">airport-codes/src/views/Home.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div class="wrapper"&gt;
    &lt;div v-for="airport in airports" :key="airport.abbreviation" class="airport"&gt;
      &lt;p&gt;{{ airport.abbreviation }}&lt;/p&gt;
      &lt;p&gt;{{ airport.name }}&lt;/p&gt;
      &lt;p&gt;{{ airport.city }}, {{ airport.state }}&lt;/p&gt;
    &lt;/div&gt;
  &lt;/div&gt;
&lt;/template&gt;

&lt;script&gt;
import { ref } from 'vue'
import allAirports from '@/data/airports.js'

export default {
  setup() {
    const airports = ref(allAirports)
        return { airports }
    }
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
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-column-gap: 1rem;
  max-width: 960px;
  margin: 0 auto;
}
.airport {
  border: 3px solid;
  border-radius: .5rem;
  padding: 1rem;
}
.airport p:first-child {
  font-weight: bold;
  font-size: 2.5rem;
  margin: 1rem 0;
}
.airport p:last-child {
  font-style: italic;
  font-size: .8rem;
}
&lt;/style&gt;
</code></pre>
<p>You may notice that there is some <a href="https://www.digitalocean.com/community/tutorial_series/how-to-style-html-with-css">CSS</a> included in this code snippet. In the <code>Home.vue</code> component, you are iterating through a collection of airports, each of which is assigned a CSS class of <code>airport</code>. This CSS adds some styling to the generated HTML by making borders to give each airport the apperance of a card. <code>:first-child</code> and <code>:last-child</code> are <em>pseudo</em> selectors that apply different styling to the first and last <code>p</code> tags in the HTML inside of the <code>div</code> with the class of <code>airport</code>.</p>

<p>Save and close the file.</p>

<p>Now that you have this initial view created along with the local dataset, you will install Vue Router in the next step.</p>

<h2 id="step-2-—-installing-vue-router">Step 2 — Installing Vue Router</h2>

<p>There are a few ways you can install Vue Router. If you are creating a new project from scratch with the <a href="https://www.digitalocean.com/community/tutorials/how-to-generate-a-vue-js-single-page-app-with-vue-create">Vue CLI</a>, you can select <strong>Vue Router</strong> in the prompt; Vue CLI will then install and configure it for you. For the sake of this tutorial, however, it is assumed that you did not select the <strong>Vue Router</strong> option in the CLI setup. You will instead install Vue Router via <a href="https://docs.npmjs.com/about-npm">npm</a>.</p>

<p>To install Vue Router, first move from the <code>src</code> directory back to the root of your project directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd ..
</li></ul></code></pre>
<p>Then run the following in your terminal window in the root directory of your project:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">npm i vue-router@next
</li></ul></code></pre>
<p>You may notice the <code>@next</code> in this command. Since this project is using Vue 3 and the Composition API, you are telling npm to download the latest experimental version of this library. If you would like more information on current releases, check out the <a href="https://github.com/vuejs/vue-router/releases">Vue Router release page on GitHub</a>.</p>

<p>This will download the <code>vue-router</code> library from npm and add it to your <code>package.json</code> file, so that it automatically downloads the next time you run <code>npm install</code>.</p>

<p>The next step is to create your routes file. This file will contain all the possible routes that the user can navigate to. When a certain route is visited in the URL bar, the component that is associated with a URL route will mount.</p>

<p>In your terminal, in the <code>src</code> directory, move into the <code>src</code> directory and create a <code>router</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd src
</li><li class="line" data-prefix="$">mkdir router
</li></ul></code></pre>
<p>Next, create an <code>index.js</code> file inside the <code>router</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch router/index.js
</li></ul></code></pre>
<p>Open the file you just created into an editor of your choice. The first thing to do is <code>import</code> the Vue Router library. You actually don&rsquo;t need to access everything in this library in order to create routes. You can opt to <a href="https://www.digitalocean.com/community/tutorials/understanding-destructuring-rest-parameters-and-spread-syntax-in-javascript">destructure</a> or import only what you need to minimize the bundle size. In this case, you need two functions from <code>vue-router</code>: <code>createWebHistory</code> and <code>createRouter</code>. These functions create a history that a user can go back to and construct a router object for Vue, respectively.</p>

<p>Add the following code to <code>router/index.js</code>:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"
</code></pre>
<p>Next, add the following highlighted lines to create and export your router:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"

<span class="highlight">const router = createRouter({</span>
  <span class="highlight">history: createWebHistory(),</span>
<span class="highlight">})</span>

<span class="highlight">export default router</span>
</code></pre>
<p>This file will export a router object that is returned from the <code>createRouter</code> function. The object you pass in has two properties: <code>history</code> and <code>routes</code>. The <code>history</code> property contains the generated history from <code>createWebHistory</code> and <code>routes</code> is an array of objects. You will add <code>routes</code> later in this tutorial.</p>

<p>Next, import the <code>Home</code> view and create an array of objects, storing them into a <code>const</code> called <code>routes</code>:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"
<span class="highlight">import Home from "@/views/Home.vue"</span>

<span class="highlight">const routes = [</span>
  <span class="highlight">{</span>
    <span class="highlight">path: "/",</span>
    <span class="highlight">name: "Home",</span>
    <span class="highlight">component: Home,</span>
  <span class="highlight">},</span>
<span class="highlight">]</span>

const router = createRouter({ ... })

export default router
</code></pre>
<p>Each route is an object with three properties:</p>

<ul>
<li><code>path</code>: The URL address</li>
<li><code>name</code>: An assigned name to reference a route in your project</li>
<li><code>component</code>: The component that gets mounted when the <code>path</code> is entered in the URL bar of the browser</li>
</ul>

<p>Now that your <code>routes</code> array as been created, you will need to add it to the exported <code>router</code> object. </p>

<p>After the <code>history</code> key/value pair, add <code>routes</code>. This is shorthand for <code>routes: routes</code> in JavaScript:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"

const routes = [ ... ]

const router = createRouter({
  history: createWebHistory(),
  <span class="highlight">routes</span>
})

export default router
</code></pre>
<p>At this point, Vue Router is integrated into your project, and you have a route registered. Save and exit the file.</p>

<p>In another terminal, run the following command to start a development server on your local machine:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">npm run serve
</li></ul></code></pre>
<p>If you visit <code>localhost:8080/</code> in your browser window, you will not see the <code>Home.vue</code> component just yet. The last step in this integration process is to tell Vue to listen to this <code>router/index.js</code> file and inject the mounted component where <code>&lt;router-view /&gt;</code> is referenced. To do this, you need to reference it in the <code>src/main.js</code> file of your application. </p>

<p>First, open <code>src/main.js</code>. Then add the following highlighted lines:</p>
<div class="code-label " title="airport-codes/src/main.js">airport-codes/src/main.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createApp } from 'vue'
import App from './App.vue'
<span class="highlight">import router from './router'</span>

createApp(App)<span class="highlight">.use(router)</span>.mount('#app')
</code></pre>
<p>With this <code>.use()</code> function that is chained to <code>createApp</code>, Vue.js is now listening to route changes and leveraging your <code>src/router/index.js</code> file. However, Vue Router has no way to display the mounted <code>Home.vue</code> component. To do this, you need to add the <code>router-view</code> component inside your <code>App.vue</code> file. This component tells Vue Router to mount any component associated with a route where <code>&lt;router-view /&gt;</code> is. </p>

<p>Save and exit the <code>main.js</code> file.</p>

<p>Next, open the <code>App.vue</code> file. Delete the default contents and replace it with the following:</p>
<div class="code-label " title="airport-codes/src/App.vue">airport-codes/src/App.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;router-view /&gt;
&lt;/template&gt;
</code></pre>
<p>Save and exit the file.</p>

<p>Now visit <code>localhost:8080/</code> in your browser. You will find the <code>Home.vue</code> component rendered, as shown in the following screenshot:</p>

<p><img src="https://assets.digitalocean.com/articles/67678/1.png" alt="Three cards displaying information about airports, retrieved from the data/airports.js file."></p>

<p>Vue Router has now been downloaded and integrated with a registered route. In the next section, you are going to create additional routes, including two internal pages and a default <strong>404</strong> page if no route was detected.</p>

<h2 id="step-3-—-creating-internal-pages">Step 3 — Creating Internal Pages</h2>

<p>At this point, your <code>App.vue</code> can render any component configured in your <code>src/router/index.js</code> file. When working with a Vue CLI-generated project, one of the directories that is created for you is <code>views</code>. This directory contains any <code>.vue</code> component that is directly mapped to a route in the <code>router/index.js</code> file. It&rsquo;s important to note that this isn&rsquo;t done automatically. You will need to create a <code>.vue</code> and <code>import</code> it into your router file to register it, as detailed earlier. </p>

<p>Before you have all of your other routes defined, you can create a <code>default</code> route. In this tutorial, this default route will act as a <strong>404 - Not Found</strong> page—a fallback in the case no route is found.</p>

<p>First, create the view that will act as the <strong>404</strong> page. Change into the <code>views</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd views
</li></ul></code></pre>
<p>Then create a file called <code>PageNotFound.vue</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch PageNotFound.vue
</li></ul></code></pre>
<p>In your text editor, open this <code>PageNotFound.vue</code> file that you just created. Add the following HTML code to give the view something to render:</p>
<div class="code-label " title="airport-codes/src/views/PageNotFound.vue">airport-codes/src/views/PageNotFound.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div&gt;
    &lt;h1&gt;404 - Page Not Found&lt;/h1&gt;
    &lt;p&gt;This page no longer exists or was moved to another location.&lt;/p&gt;
  &lt;/div&gt;
&lt;/template&gt;
</code></pre>
<p>Save and close the file.</p>

<p>Now that the <code>PageNotFound.vue</code> component has been created, it&rsquo;s time to create a catch-all route for your application. Open up the <code>src/router/index.js</code> file in your text editor and add the following highlighted code:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"
import Home from "@/views/Home.vue"
<span class="highlight">import PageNotFound from '@/views/PageNotFound.vue'</span>

const routes = [
  {
    path: "/",
    name: "Home",
    component: Home,
  },
  <span class="highlight">{</span>
    <span class="highlight">path: '/:catchAll(.*)*',</span>
    <span class="highlight">name: "PageNotFound",</span>
    <span class="highlight">component: PageNotFound,</span>
  <span class="highlight">},</span>
]
...
</code></pre>
<p>Vue Router for Vue 3 uses a custom <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-regular-expressions">RegEx</a>. The value of <code>path</code> contains this new RegEx, which is telling Vue to render <code>PageNotFound.vue</code> for every route, unless the route is already defined. The <code>catchAll</code> in this route refers to a dynamic segment within Vue Router, and <code>(.*)</code> is a regular expression that captures any string.</p>

<p>Save this file and visit your application in your browser window at <code>localhost:8080/not-found</code>. You will find the <code>PageNotFound.vue</code> component rendered in your browser, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67678/2.png" alt="A 404 page that tells the user that the page they are looking for has not been found."></p>

<p>Feel free to change this URL to anything else; you will get the same result.</p>

<p>Before moving on, create another route for your application. This route will be an <strong>about</strong> page.</p>

<p>Open your terminal of choice and create the file in the <code>views</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch About.vue
</li></ul></code></pre>
<p>In your text editor, open this <code>About.vue</code> file that you just created. Add the following HTML to create more information about your site:</p>
<div class="code-label " title="airport-codes/src/views/About.vue">airport-codes/src/views/About.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div&gt;
    &lt;h1&gt;About&lt;/h1&gt;
    &lt;p&gt;This is an about page used to illustrate mapping a view to a router with Vue Router.&lt;/p&gt;
  &lt;/div&gt;
&lt;/template&gt;
</code></pre>
<p>Save and close the file.</p>

<p>With that view created, open the <code>src/router/index.js</code> file in your text editor, import the <code>About.vue</code> component, and register a new route with a path of <code>/about</code>:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import { createWebHistory, createRouter } from "vue-router"
import Home from '@/views/Home.vue'
<span class="highlight">import About from '@/views/About.vue'</span>
import PageNotFound from '@/views/PageNotFound.vue'

...
const routes = [
  {
    path: "/",
    name: "Home",
    component: Home,
  },
  <span class="highlight">{</span>
    <span class="highlight">path: '/about',</span>
    <span class="highlight">name: "About",</span>
    <span class="highlight">component: About,</span>
  <span class="highlight">},</span>
  {
    path: '/:catchAll(.*)*',
    name: "PageNotFound",
    component: PageNotFound,
  },
]
...
</code></pre>
<p>Save and close the file.</p>

<p>At this point, you have three different routes:</p>

<ul>
<li><code>localhost:8080/</code>, which routes to <code>Home.vue</code></li>
<li><code>localhost:8080/about</code>, which routes to <code>About.vue</code></li>
<li>Any other route, which by default goes to <code>PageNotFound.vue</code></li>
</ul>

<p>Once you save this file, open your browser and first visit <code>localhost:8080/</code>. Once the application loads, you will find the contents of <code>Home.vue</code>: the collection of airport cards. </p>

<p>Continue testing these routes by visiting <code>localhost:8080/about</code>. This is a static route, so you will find the contents of the <code>About.vue</code> component, which at this point contains a heading and a paragraph. </p>

<p><img src="https://assets.digitalocean.com/articles/67678/3.PNG" alt='A placehold about page for the sample app that reads "This is an about page"'></p>

<p>Next, you can test the <code>PageNotFound.vue</code> component by visiting anything else in your browser. For example, if you visit, <code>localhost:8080/some-other-route</code>, Vue Router will default to that <code>catchAll</code> route since that route is not defined.</p>

<p>As this step illustrates, Vue Router is a handy first-party library that renders a component that is associated with a specific route. In this step, this library was downloaded and integrated globally through the <code>main.js</code> file and was configured in your <code>src/router/index.js</code> file.</p>

<p>So far, most of your routes are <em>exact</em> routes. This means a component will only mount if the URL fragment matches the <code>path</code> of the router exactly. However, there are other types of routes that have their own purpose and can dynamically generate content. In the next step, you are going to implement the different types of routes and learn when to use one or the other.</p>

<h2 id="step-4-—-creating-routes-with-parameters">Step 4 — Creating Routes With Parameters</h2>

<p>At this point, you have created two exact routes and a dynamic route to a <strong>404</strong> page. But Vue Router has more than these types of routes. You can use the following routes in Vue Router:</p>

<ul>
<li>Dynamic Routes: Routes with dynamic parameters that your application can reference to load unique data.</li>
<li>Named Routes: Routes that can be accessed using the <code>name</code> property. All the routes created at this point have a <code>name</code> property.</li>
<li>Nested Routes: Routes with children associated with them.</li>
<li>Static or Exact Routes: Routes with a static <code>path</code> value.</li>
</ul>

<p>In this section, you will create a dynamic route displaying individual airport information and a nested route for airport destinations.</p>

<h3 id="dynamic-routes">Dynamic Routes</h3>

<p>A dynamic route is useful when you want to reuse a view to display different data depending on the route. For example, if you wanted to create a view that displays airport information depending on the airport code in the URL bar, you could use a dynamic route. In this example, if you were to visit a route of <code>localhost:8080/airport/cvg</code>, your application would display data from the airport with the code <code>cvg</code>, the Cincinnati/Northern Kentucky International Airport. Next, you will create this view as described. </p>

<p>Open up your terminal and create a new <code>.vue</code> file with the <code>touch</code> command. If <code>src</code> is your current working directory, the command will look like this:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch views/AirportDetail.vue
</li></ul></code></pre>
<p>After that is created, open this file in your text editor of choice. Go ahead and create your <code>template</code> and <code>script</code> tags to set up this component:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div&gt;

  &lt;/div&gt;
&lt;/template&gt;

&lt;script&gt;
export default {
  setup() { }
}
&lt;/script&gt;
</code></pre>
<p>Save and close this file.</p>

<p>Next, you need to register this view in the <code>src/router/index.js</code> file. Open up this file in your text editor, then add the following highlighted lines:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import Home from '@/views/Home.vue'
import About from '@/views/About.vue'
<span class="highlight">import AirportDetail from '@/views/AirportDetail.vue'</span>
import PageNotFound from '@/views/PageNotFound.vue'

...
const routes = [
  {
    path: "/",
    name: "Home",
    component: Home,
  },
  {
    path: '/about',
    name: "About",
    component: About,
  },
  <span class="highlight">{</span>
    <span class="highlight">path: '/airport/:code',</span>
    <span class="highlight">name: "AirportDetail",</span>
    <span class="highlight">component: AirportDetail,</span>
  <span class="highlight">},</span>
  {
   path: '/:catchAll(.*)*',
   name: "PageNotFound",
   component: PageNotFound,
  },
]
...
</code></pre>
<p>The <code>:code</code> in this new route is called a <em>parameter</em>. A parameter is any value that can be accessed in your application via this name. In this case, you have a parameter named <code>code</code>. Next, you will display the information associated with this abbreviation by leveraging this parameter.</p>

<p>Save and close this file.</p>

<p>Now that you have some data, open the <code>AirportDetail.vue</code> component again. Import the airport data in this component:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;
<span class="highlight">import airports from '@/data/airports.js'</span>

export default {
  setup() { }
}
&lt;/script&gt;
</code></pre>
<p>Next, create a computed property that returns one object from the array if the <code>abbreviation</code> property of that object matches the <code>:code</code> parameter in the URL. In Vue 3, you need to destructure <code>computed</code> from the <code>vue</code> library:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">...
&lt;script&gt;
<span class="highlight">import { computed } from 'vue'</span>
<span class="highlight">import { useRoute } from 'vue-router'</span>
import airports from '@/data/airports.js'

export default {
  setup() {
  <span class="highlight">const route = useRoute()</span>
    <span class="highlight">const airport = computed(() =&gt; {</span>
        <span class="highlight">return airports.filter(a =&gt; a.abbreviation === route.params.code.toUpperCase())[0]</span>
    <span class="highlight">})</span>

    <span class="highlight">return { airport }</span>
  }
}
&lt;/script&gt;
</code></pre>
<p>This computed property uses the <a href="https://www.digitalocean.com/community/tutorials/how-to-use-array-methods-in-javascript-iteration-methods#filter()"><code>filter</code> array method</a> in JavaScript to return an array of objects if the condition is met. Since you only want one object, the code will always return the first object, which it will access with the <code>[0]</code> index syntax. The <code>route.params.code</code> is how you access the parameter that was defined in your router file. In order to access the route&rsquo;s properties, you will need to import a function from <code>vue-router</code> named <code>useRoute</code>. Now when visiting a route, you have immediate access to all of the route&rsquo;s properties. You are using dot notation to access this <code>code</code> param and retrieve its value.</p>

<p>At this point, this computed property will return a single airport object if the code in the URL matches the <code>abbreviation</code> property. This means that you have access to all of the object properties of an airport and can construct the <code>template</code> of this component.</p>

<p>Continue editing the <code>AirportDetail.vue</code> file in your text editor and build out your template to display the information of the airport:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
 &lt;div&gt;
   <span class="highlight">&lt;p&gt;{{ airport.name }} ({{ airport.abbreviation }})&lt;/p&gt;</span>
   <span class="highlight">&lt;p&gt;Located in {{ airport.city }}, {{ airport.state }}&lt;/p&gt;</span>
 &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>Open your browser and visit <code>localhost:8080/airport/sea</code>. Information related to the Seattle-Tacoma International Airport will render in your browser, as shown in the following image:</p>

<p><img src="https://assets.digitalocean.com/articles/67678/4.PNG" alt="Informational page about the Seattle-Tacoma International Airport, including its abbreviation and location."></p>

<h3 id="nested-routes">Nested Routes</h3>

<p>As your application grows, you may find that you have a number of routes that are related to a parent. A good illustration of this could be a series of routes associated with a user. If there is a user named <code>foo</code>, you could have multiple nested routes for the same user, each starting with <code>/user/foo/</code>. When the user is on the <code>/user/foo/profile</code> route, the user would be viewing a profile page associated with them. A <code>posts</code> page for the user might have a route of <code>/user/foo/posts</code>. This nesting can give you the ability to organize your routes along with your components. For more information on this, check out the <a href="https://next.router.vuejs.org/guide/essentials/nested-routes.html#nested-routes">Vue Router documentation</a>.</p>

<p>You can apply this same pattern to the application you&rsquo;ve built throughout this tutorial. In this section, you&rsquo;re going to add a view to display the destinations that each airport supports. </p>

<p>Open your terminal and, in the <code>src</code> directory, create a new file with the name <code>AirportDestinations.vue</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch views/AirportDestinations.vue
</li></ul></code></pre>
<p>Next, open your text editor and add the following:</p>
<div class="code-label " title="airport-codes/src/views/AirportDestinations.vue">airport-codes/src/views/AirportDestinations.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;h1&gt;Destinations for {{ airport.name }} ({{ airport.abbreviation }}&lt;/h1&gt;
  &lt;h2&gt;Passenger&lt;/h2&gt;
  &lt;ul&gt;
    &lt;li v-for="(destination, i) in airport.destinations.passenger" :key="i"&gt;
      {{ destination }}
    &lt;/li&gt;
  &lt;/ul&gt;
  &lt;h2&gt;Cargo&lt;/h2&gt;
  &lt;ul&gt;
    &lt;li v-for="(destination, i) in airport.destinations.cargo" :key="i"&gt;
      {{ destination }}
    &lt;/li&gt;
  &lt;/ul&gt;
&lt;/template&gt;

&lt;script&gt;
  import { computed } from 'vue'
  import { useRoute } from 'vue-router'
  import airports from '@/data/airports.js'

  export default {
    setup() {
      const route = useRoute()
      const airport = computed(() =&gt; {
        return airports.filter(a =&gt; a.abbreviation === route.params.code.toUpperCase())[0]
      })
    return { airport }
  }
}
&lt;/script&gt;
</code></pre>
<p>This view will render all of the destinations for each airport from the <code>airports.js</code> file. In this view, you are using the <code>v-for</code> directive to iterate through the destinations. Much like the <code>AirportDetail.vue</code> view, you are creating a computed property called <code>airport</code> to get the airport that matches the <code>:code</code> parameter in the URL bar.</p>

<p>Save and close the file.</p>

<p>To create a nested route, you need to add the <code>children</code> property to your route object in the <code>src/router/index.js</code> file. The child (nested) route object contains the same properties as its parent.</p>

<p>Open up <code>router/index.js</code> and add the following highlighted lines:</p>
<div class="code-label " title="airport-codes/src/router/index.js">airport-codes/src/router/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import Home from '@/views/Home.vue'
import About from '@/views/About.vue'
import AirportDetail from '@/views/AirportDetail.vue'
<span class="highlight">import AirportDestinations from '@/views/AirportDestinations.vue'</span>
import PageNotFound from '@/views/PageNotFound.vue'

...
const routes = [
    ...
  {
    path: '/airport/:code',
    name: "AirportDetail",
    component: AirportDetail,
        children: [
            {
              <span class="highlight">path: 'destinations',</span>
                <span class="highlight">name: 'AirportDestinations',</span>
                <span class="highlight">component: AirportDestinations</span>
            }
        ]
  },
    ...
]
...
</code></pre>
<p>The <code>path</code> for this child route is short compared to its parent. That is because, with nested routes, you do not need to add the entire route. The child inherits its parent&rsquo;s <code>path</code> and will prepend it to the child <code>path</code>.</p>

<p>Open your browser window, and visit <code>localhost:8080/airport/msp/destinations</code>. Nothing appears to be different from the parent, <code>AirportDetails.vue</code>. That is because when using nested routes, you need to include the <code>&lt;router-view /&gt;</code> component in the parent. When visiting the child route, Vue will inject the content from the child view into the parent:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
 &lt;div&gt;
   &lt;p&gt;{{ airport.name }} ({{ airport.abbreviation }})&lt;/p&gt;
   &lt;p&gt;Located in {{ airport.city }}, {{ airport.state }}&lt;/p&gt;
     <span class="highlight">&lt;router-view /&gt;</span>
 &lt;/div&gt;
&lt;/template&gt;
</code></pre>
<p>In this case, when visiting the destinations route in the browser, <code>AirportDestinations.vue</code> will display an unordered list of the destinations that the Minneapolis-Saint Paul International Airport supports, for both passenger and cargo flights.</p>

<p><img src="https://assets.digitalocean.com/articles/67678/5.PNG" alt="A page rendered with a list of passenger and cargo destinations for Minneapolis-Saint Paul International Airport."></p>

<p>In this step, you created dynamic and nested routes and learned how to use a computed property that checks against the <code>:code</code> parameter in the URL bar. Now that all the routes have been created, in the final step, you are going to navigate between the different types of routes by creating links with the <code>&lt;router-link /&gt;</code> component.</p>

<h2 id="step-5-—-navigating-between-routes">Step 5 — Navigating Between Routes</h2>

<p>When working with single-page applications, there are a few caveats you need to be aware off. Since every page is bootstrapped into a single HTML page, navigating between internal routes using the standard anchor (<code>&lt;a /&gt;</code>) will not work. Instead, you will need to use the <code>&lt;router-link /&gt;</code> component provided by Vue Router.</p>

<p>Unlike the standard anchor tag, <code>router-link</code> provides a number of different ways to link to other routes in your application, including using named routes. Named routes are routes that have a <code>name</code> property associated with them. Every link that you have created up to this point has a <code>name</code> already associated with it. Later in this step, you&rsquo;ll learn how to navigate using <code>name</code> rather than the <code>path</code>.</p>

<h3 id="using-the-lt-router-link-gt-component">Using the <code>&lt;router-link /&gt;</code> Component</h3>

<p>The <code>&lt;router-link /&gt;</code> component was globally imported when you initially integrated Vue Router into your application. To use this component, you will add it to your template and provide it with a prop value.</p>

<p>Open the <code>Home.vue</code> component within the <code>views</code> directory in your editor. This view displays every airport that you have in the <code>airports.js</code> file. You can use <code>router-link</code> to replace the containing <code>div</code> tag in order to make each card clickable to their respective detail view. </p>

<p>Add the following highlighted lines to <code>Home.vue</code>:</p>
<div class="code-label " title="airport-codes/src/views/Home.vue">airport-codes/src/views/Home.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
    &lt;div class="wrapper"&gt;
        &lt;<span class="highlight">router-link</span> v-for="airport in airports" :key="airport.abbreviation" class="airport"&gt;
            &lt;p&gt;{{ airport.abbreviation }}&lt;/p&gt;
            &lt;p&gt;{{ airport.name }}&lt;/p&gt;
            &lt;p&gt;{{ airport.city }}, {{ airport.state }}&lt;/p&gt;
        &lt;/<span class="highlight">router-link</span>&gt;
    &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>At the bare minimum, <code>router-link</code> requires one prop called <code>to</code>. This <code>to</code> prop accepts an object with a number of key/value pairs. Since this card uses the <code>v-for</code> directive, these cards are data driven. You&rsquo;ll need to be able to navigate to the <code>AirportDetail.vue</code> component. If you review the path for that component, it is accessible via <code>/airports/:code</code>.</p>

<p>When navigating via the <code>path</code> property, it is a one-to-one match. Add the following highlighted segments:</p>
<div class="code-label " title="airport-codes/src/views/Home.vue">airport-codes/src/views/Home.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
    &lt;div class="wrapper"&gt;
        &lt;router-link <span class="highlight">:to="{ path: `/airports/${airport.abbreviation}` }"</span> v-for="airport in airports" :key="airport.abbreviation" class="airport"&gt;
            ...
        &lt;/router-link&gt;
    &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>In this code, you are using <a href="https://www.digitalocean.com/community/tutorials/understanding-template-literals-in-javascript">JavaScript string interpolation</a> to insert the airport code dynamically as the value for the <code>path</code> property. However, as your applications grows in scale, you may find that navigating to a different route via the <code>path</code> is not the best method. In fact, it is generally considered best practice to navigate using named routes.</p>

<p>To implement a named route, use the <code>name</code> of the route over the <code>path</code>. If you reference <code>src/router/index.js</code> you will find that <code>AirportDetail.vue</code> has a name of <code>AirportDetail</code>:</p>
<div class="code-label " title="airport-codes/src/views/Home.vue">airport-codes/src/views/Home.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
    &lt;div class="wrapper"&gt;
        &lt;router-link :to="<span class="highlight">{ name: 'AirportDetail' }</span>" v-for="airport in airports" :key="airport.abbreviation" class="airport"&gt;
            ...
        &lt;/router-link&gt;
    &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>The benefit of using named routes over exact routes is that named routes are not dependent on the <code>path</code> of the route. URL structures will always change in development, but the name of the route will seldom change.</p>

<p>You might notice that you cannot pass in the airport code as you did earlier with the exact route. If you need to pass in parameters into a named route, you will need to add the <code>params</code> property containing an object that represents your parameter:</p>
<div class="code-label " title="airport-codes/src/views/Home.vue">airport-codes/src/views/Home.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
    &lt;div class="wrapper"&gt;
        &lt;router-link :to="{ name: 'AirportDetail'<span class="highlight">, params: { code: airport.abbreviation }</span> }" v-for="airport in airports" :key="airport.abbreviation" class="airport"&gt;
            ...
        &lt;/router-link&gt;
    &lt;/div&gt;
&lt;/template&gt;
...
</code></pre>
<p>Save this file and view it in your browser at <code>localhost:8080</code>. Clicking on the Seattle-Tacoma airport card will now navigate you to <code>localhost:8080/airport/sea</code>.</p>

<h3 id="programmatic-navigation">Programmatic Navigation</h3>

<p>The <code>&lt;router-link /&gt;</code> component is great to use when you need to navigate to another view within your HTML template. But what about those cases when you need to navigate between routes within a JavaScript function? Vue Router offers a solution to this problem called <em>programmatic navigation</em>.</p>

<p>Earlier in this tutorial, you created a catch-all route called <code>PageNotFound</code>. It would be a good user experience to always navigate to that page if the <code>airport</code> computed property returned <code>undefined</code> in the <code>AirportDetail.vue</code> and <code>AirportDestinations.vue</code> components. In this section, you will implement this feature.</p>

<p>In your text editor, open the <code>AirportDetail.vue</code> component. To achieve this, detect if <code>airport.value</code> is undefined. This function will be called when the component is first mounted, which means you will need to use Vue&rsquo;s lifecycle methods.</p>

<p>Add the following highlighted lines:</p>
<div class="code-label " title="airport-codes/src/views/AirportDetail.vue">airport-codes/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;template&gt;
  &lt;div <span class="highlight">v-if="airport"</span>&gt;
   &lt;p&gt;{{ airport.name }} ({{ airport.abbreviation }})&lt;/p&gt;
   &lt;p&gt;Located in {{ airport.city }}, {{ airport.state }}&lt;/p&gt;
   &lt;router-view /&gt;
  &lt;/div&gt;
&lt;/template&gt;

&lt;script&gt;
    import { computed, <span class="highlight">onMounted</span> } from 'vue'
    import { useRoute } from 'vue-router'
    import airports from '@/data/airports.js'
    <span class="highlight">import router from '@/router'</span>

    export default {
            setup() {
        const route = useRoute()
                const airport = computed(() =&gt; {
                    return airports.filter(a =&gt; a.abbreviation === route.params.code.toUpperCase())[0]
                })

                <span class="highlight">onMounted(() =&gt; {</span>
                    <span class="highlight">if (!airport.value) {</span>
                        <span class="highlight">// Navigate to 404 page here</span>
                    <span class="highlight">}</span>
                <span class="highlight">})</span>

                return { airport }
            }
    }
&lt;/script&gt;
</code></pre>
<p>In this <code>onMounted</code> function, you are checking if <code>airport.value</code> is a falsy value. If it is, then you route to <code>PageNotFound</code>. You can handle programmatic routing similarly to how you handled the <code>router-link</code> component. Since you cannot use a component in JavaScript functions, you need to use <code>router.push({ ... })</code>. This <code>push</code> method of the <code>router</code> object accepts the value of the <code>to</code> prop when using the link component:</p>
<div class="code-label " title="/src/views/AirportDetail.vue">/src/views/AirportDetail.vue</div><pre class="code-pre "><code class="code-highlight language-html">&lt;script&gt;
    ...
  onMounted(() =&gt; {
        if (!airport.value) {
            <span class="highlight">router.push({ name: 'PageNotFound' })</span>
        }
  })
    ...
&lt;/script&gt;
</code></pre>
<p>If the route doesn&rsquo;t exist, or if the data is not returned properly, this will protect the user from the broken web page.</p>

<p>Save the file and navigate to <code>localhost:8080/airport/ms</code>. Since <code>ms</code> is not an airport code in the data, the <code>airport</code> object will be undefined, and the router will redirect you to the <strong>404</strong> page.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you used Vue Router to create a web application that routes between different views. You learned the different types of routes, including exact, named, and nested, as well as created links with parameters using the <code>router-link</code> component. In the final step, you provided programmatic navigation using JavaScript by leveraging the router&rsquo;s <code>push()</code> function.</p>

<p>For more information on <a href="https://router.vuejs.org/">Vue Router</a>, it’s recommended to read through their documentation. The CLI tool specifically has many additional features that weren’t covered in this tutorial. For more tutorials on Vue, check out the <a href="https://www.digitalocean.com/community/tags/vue-js">Vue Topic Page</a>.</p>
