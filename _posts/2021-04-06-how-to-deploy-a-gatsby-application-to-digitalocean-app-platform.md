---
title : "How To Deploy a Gatsby Application to DigitalOcean App Platform"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-gatsby-application-to-digitalocean-app-platform
image: "https://sergio.afanou.com/assets/images/image-midres-5.jpg"
---

<p><em>The author selected <a href="https://www.devcolor.org/">/dev/color</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>In this tutorial, you will deploy a <a href="https://www.gatsbyjs.com/">Gatsby</a> application to DigitalOcean&rsquo;s <a href="https://www.digitalocean.com/products/app-platform/">App Platform</a>. App Platform is a <a href="https://www.digitalocean.com/community/tutorials/what-is-platform-as-a-service-paas">Platform as a Service</a> that builds, deploys, and manages apps automatically. When combined with the speed of a <a href="https://www.gatsbyjs.com/docs/glossary/static-site-generator/">static site generator</a> like Gatsby, this provides a scalable <a href="https://www.gatsbyjs.com/docs/glossary/jamstack/">JAMStack</a> solution that doesn&rsquo;t require server-side programming.</p>

<p>In this tutorial, you will create a sample Gatsby app on your local machine, push your code to <a href="https://github.com/">GitHub</a>, then deploy to App Platform.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li><p>On your local machine, you will need a development environment running <a href="https://nodejs.org/en/about/">Node.js</a>; this tutorial was tested on Node.js version 14.16.0 and npm version 6.14.11. To install this on macOS or Ubuntu 20.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How to Install Node.js and Create a Local Development Environment on macOS</a> or the <strong>Installing Using a PPA</strong> section of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04">How To Install Node.js on Ubuntu 20.04</a>.</p></li>
<li><p><a href="https://git-scm.com/">Git</a> installed onto your local machine. You can follow the tutorial <a href="https://www.digitalocean.com/community/tutorials/contributing-to-open-source-getting-started-with-git">Contributing to Open Source: Getting Started with Git</a> to install and set up Git on your computer. </p></li>
<li><p>An account on GitHub, which you can create by going to the <a href="https://github.com/join"><strong>Create your Account</strong> page</a>. </p></li>
<li><p><a href="https://www.digitalocean.com/">A DigitalOcean account</a>.</p></li>
<li><p>The <a href="https://www.gatsbyjs.com/docs/reference/gatsby-cli/">Gatsby CLI tool</a> downloaded onto your local machine. You can learn how to do this in <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-first-gatsby-website"><strong>Step 1</strong> of the How To Set Up Your First Gatsby Website</a> tutorial.</p></li>
<li><p>An understanding of JavaScript will be useful. You can learn more about JavaScript at our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript series</a>. You don’t need to know React in order to get started, but it would be helpful to be familiar with the basic concepts. You can <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-react-js">learn React with this series</a>.</p></li>
</ul>

<h2 id="step-1-—-creating-a-gatsby-project">Step 1 — Creating a Gatsby Project</h2>

<p>In this section, you are going to create a sample Gatsby application, which you will later deploy to App Platform.</p>

<p>First, clone the default Gatsby starter from GitHub. You can do that with the following command in your terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone https://github.com/gatsbyjs/gatsby-starter-default
</li></ul></code></pre>
<p>The Gatsby starter site provides you with the boilerplate code you need to start coding your application. For more information on creating a Gatsby app, check out <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-first-gatsby-website">How To Set Up Your First Gatsby Website</a>.</p>

<p>When you are finished with cloning the repo, <code>cd</code> into the <code>gatsby-starter-default</code> directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd gatsby-starter-default
</li></ul></code></pre>
<p>Then install the Node dependencies:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">npm install
</li></ul></code></pre>
<p>After you&rsquo;ve downloaded the app and installed the dependencies, open the following file in a text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano gatsby-config.js
</li></ul></code></pre>
<p>You have just opened <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-first-gatsby-website#step-2-%E2%80%94-modifying-the-title,-description,-and-author-metadata-in-your-gatsby-config">Gatsby&rsquo;s config file</a>. Here you can change metadata about your site.</p>

<p>Go to the <code>title</code> key and change <code>Gatsby Default Starter</code> to <code>Save the Whales</code>, as shown in the following highlighted line:</p>
<div class="code-label " title="gatsby-starter-default/gatsby-config.js">gatsby-starter-default/gatsby-config.js</div><pre class="code-pre "><code class="code-highlight language-javascript">module.exports = {
  siteMetadata: {
    title: <span class="highlight">`Save the Whales`</span>,
    description: `Kick off your next, great Gatsby project with this default starter. This barebones starter ships with the main Gatsby configuration files you might need.`,
    author: `@gatsbyjs`,
  },
  plugins: [
    `gatsby-plugin-react-helmet`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `images`,
        path: `${__dirname}/src/images`,
      },
    },
    `gatsby-transformer-sharp`,
    `gatsby-plugin-sharp`,
    {
      resolve: `gatsby-plugin-manifest`,
      options: {
        name: `gatsby-starter-default`,
        short_name: `starter`,
        start_url: `/`,
        background_color: `#663399`,
        theme_color: `#663399`,
        display: `minimal-ui`,
        icon: `src/images/gatsby-icon.png`, // This path is relative to the root of the site.
      },
    },
    // this (optional) plugin enables Progressive Web App + Offline functionality
    // To learn more, visit: https://gatsby.dev/offline
    // `gatsby-plugin-offline`,
  ],
}
</code></pre>
<p>Close and save the file. Now open the index file in your favorite text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano src/pages/index.js
</li></ul></code></pre>
<p>To continue with the &ldquo;Save the Whales&rdquo; theme, replace <code>Hi people</code> with <code>Adopt a whale today</code>, change <code>Welcome to your new Gatsby site.</code> to <code>Whales are our friends.</code>, and delete the last <code>&lt;p&gt;</code> tag: </p>
<div class="code-label " title="gatsby-starter-default/src/pages/index.js">gatsby-starter-default/src/pages/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import React from "react"
import { Link } from "gatsby"
import { StaticImage } from "gatsby-plugin-image"

import Layout from "../components/layout"
import SEO from "../components/seo"

const IndexPage = () =&gt; (
  &lt;Layout&gt;
    &lt;SEO title="Home" /&gt;
    <span class="highlight">&lt;h1&gt;Adopt a whale today&lt;/h1&gt;</span>
    <span class="highlight">&lt;p&gt;Whales are our friends.&lt;/p&gt;</span>
    &lt;StaticImage
      src="../images/gatsby-astronaut.png"
      width={300}
      quality={95}
      formats={["AUTO", "WEBP", "AVIF"]}
      alt="A Gatsby astronaut"
      style={{ marginBottom: `1.45rem` }}
    /&gt;
    &lt;Link to="/page-2/"&gt;Go to page 2&lt;/Link&gt; &lt;br /&gt;
    &lt;Link to="/using-typescript/"&gt;Go to "Using TypeScript"&lt;/Link&gt;
  &lt;/Layout&gt;
)

export default IndexPage
</code></pre>
<p>Save and close the file. You are going to swap out the Gatsby astronaut image with a GIF of a whale. Before you add the GIF, you will first need to create a GIF directory and download it. </p>

<p>Go to the <code>src</code> directory and create a <code>gifs</code> file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd src/
</li><li class="line" data-prefix="$">mkdir gifs
</li></ul></code></pre>
<p>Now navigate into your newly created <code>gifs</code> folder:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd gifs
</li></ul></code></pre>
<p>Download <a href="https://media.giphy.com/media/lqdJsUDvJnHBgM82HB/giphy.gif">a whales GIF from Giphy</a>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget https://media.giphy.com/media/lqdJsUDvJnHBgM82HB/giphy.gif
</li></ul></code></pre>
<p><a href="https://www.gnu.org/software/wget/">Wget</a> is a utilty that allows you to download files from the internet. <a href="https://giphy.com/">Giphy</a> is a website that hosts GIFs. </p>

<p>Next, change the name from <code>giphy.gif</code> to <code>whales.gif</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mv giphy.gif whales.gif
</li></ul></code></pre>
<p>After you have changed the name of the GIF, move back to the root folder of the project and open up the index file again:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd ../..
</li><li class="line" data-prefix="$">nano src/pages/index.js
</li></ul></code></pre>
<p>Now you will add the GIF to your site&rsquo;s homepage. Delete the <code>StaticImage</code> import and element, then replace with the following highlighted lines:</p>
<div class="code-label " title="gatsby-starter-default/src/pages/index.js">gatsby-starter-default/src/pages/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import React from "react"
import { Link } from "gatsby"

<span class="highlight">import whaleGIF from "../gifs/whales.gif"</span>
import Layout from "../components/layout"
import SEO from "../components/seo"

const IndexPage = () =&gt; (
  &lt;Layout&gt;
    &lt;SEO title="Home" /&gt;
    &lt;h1&gt;Adopt a whale today&lt;/h1&gt;
    &lt;p&gt;Whales are our friends.&lt;/p&gt;
    <span class="highlight">&lt;div style={{ maxWidth: `300px`, marginBottom: `1.45rem` }}&gt;</span>
        <span class="highlight">&lt;img src={whaleGIF} alt="Picture of Whale from BBC America" /&gt;</span>
    <span class="highlight">&lt;/div&gt;</span>
    &lt;Link to="/page-2/"&gt;Go to page 2&lt;/Link&gt; &lt;br /&gt;
    &lt;Link to="/using-typescript/"&gt;Go to "Using TypeScript"&lt;/Link&gt;
  &lt;/Layout&gt;
</code></pre>
<p>Here you imported the whales GIF and included it in an image tag between the <code>&lt;div&gt;</code> element. The <code>alt</code> tag informs the reader where the GIF originated. </p>

<p>Close and save the index file. </p>

<p>Now you will run your site locally to make sure it works. From the root of your project, run the development server: </p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">gatsby develop
</li></ul></code></pre>
<p>After your site has finished building, put <code>localhost:8000</code> into your browser&rsquo;s search bar. You will find the following rendered in your browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/1.png" alt="Front page of a Save the Whales website"></p>

<p>In this section, you created a sample Gatsby app. In the next section, you are going to push your code to GitHub so that it is accessible to App Platform.</p>

<h2 id="step-2-—-pushing-your-code-to-github">Step 2 — Pushing Your Code to GitHub</h2>

<p>In this section of the tutorial, you are going to commit your code to <code>git</code> and push it up to <a href="https://github.com/">GitHub</a>. From there, DigitalOcean&rsquo;s App Platform will be able to access the code for your website.</p>

<p>Go to the root of your project and create a new git repository:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git init
</li></ul></code></pre>
<p>Next, add any modified files to git:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git add .
</li></ul></code></pre>
<p>Finally, commit all of your changes to git with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git commit -m "Initial Commit"
</li></ul></code></pre>
<p>This will commit this version of your app to git version control. The <code>-m</code> takes a string argument and uses it as a message about the commit.</p>

<span class='note'><p>
<strong>Note:</strong> If you have not set up git before on this machine, you may receive the following output:</p>
<pre class="code-pre "><code>*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.
</code></pre>
<p>Run the two <code>git config</code> commands to provide this information before moving on. If you would like to learn more about git, check out our <a href="https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git#setting-up-git">How To Contribute to Open Source: Getting Started with Git</a> tutorial.<br></p></span>

<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[master 1e3317b] Initial Commit
 3 files changed, 7 insertions(+), 13 deletions(-)
 create mode 100644 src/gifs/whales.gif
</code></pre>
<p>Once you have committed the file, go to GitHub and log in. After you log in, <a href="https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo">create a new repository</a> called <strong>gatsby-digital-ocean-app-platform</strong>. You can make the repository either private or public:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/2.png" alt="Creating a new github repo"></p>

<p>After you&rsquo;ve created a new repo, go back to the command line and add the remote repo address:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git remote set-url origin https://github.com/<span class="highlight">your_name</span>/gatsby-digital-ocean-app-platform
</li></ul></code></pre>
<p>Make sure to change <code>your_name</code> to your username on GitHub.</p>

<p>Next, declare that you want to push to the <code>main</code> branch with the following:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git branch -M main
</li></ul></code></pre>
<p>Finally, push your code to your newly created repo:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git push -u origin main
</li></ul></code></pre>
<p>Once you enter your credentials, you will receive output similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Counting objects: 3466, done.
Compressing objects: 100% (1501/1501), done.
Writing objects: 100% (3466/3466), 28.22 MiB | 32.25 MiB/s, done.
Total 3466 (delta 1939), reused 3445 (delta 1926)
remote: Resolving deltas: 100% (1939/1939), done.
To https://github.com/<span class="highlight">your_name</span>/gatsby-digital-ocean-app-platform
 * [new branch]      main -&gt; main
Branch 'main' set up to track remote branch 'main' from 'origin'.
</code></pre>
<p>You will now be able to access your code in your GitHub account.</p>

<p>In this section you pushed your code to a remote GitHub repository. In the next section, you will deploy your Gatsby app from GitHub to App Platform.</p>

<h2 id="step-3-—-deploying-your-gatsby-app-on-digitalocean-app-platform">Step 3 — Deploying your Gatsby App on DigitalOcean App Platform</h2>

<p>In this step, you are going to deploy your app onto DigitalOcean App Platform. If you haven&rsquo;t done so already, create a <a href="https://www.digitalocean.com/products/app-platform/">DigitalOcean account</a>.</p>

<p>Open your <a href="https://cloud.digitalocean.com/">DigitalOcean control panel</a>, select the <strong>Create</strong> button at the top of the screen, then select <strong>Apps</strong> from the dropdown menu:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/3.png" alt="Go to drop down menu and select Apps"></p>

<p>After you have selected <strong>Apps</strong>, you are going to retrieve your repository from GitHub. Click on the GitHub icon and give DigitalOcean permission to access your repositories. It is a best practice to only select the repository that you want deployed.  </p>

<p><img src="https://assets.digitalocean.com/articles/67673/4.png" alt="Choose the repo you want deployed"></p>

<p>You&rsquo;ll be redirected back to DigitalOcean. Go to the <strong>Repository</strong> field and select the project and branch you want to deploy, then click <strong>Next</strong>:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/5.png" alt="Selecting your GitHub repository on the DigitalOcean website"></p>

<p><span class='note'><strong>Note:</strong> Below <strong>Branch</strong> there is a pre-checked box that says <strong>Autodeploy code changes</strong>. This means if you push any changes to your GitHub repository, DigitalOcean will automatically deploy those changes.<br></span></p>

<p>On the next page you&rsquo;ll be asked to configure your app. In your case, all of the presets are correct, so you can click on <strong>Next</strong>: </p>

<p><img src="https://assets.digitalocean.com/articles/67673/6.png" alt="Configuring your app"></p>

<p>When you&rsquo;ve finished configuring your app, give it a name like <strong>save-the-whales</strong>:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/7.png" alt="Name your app"></p>

<p>Once you select your name and click <strong>Next</strong>, you will go to the payment plan page. Since your app is a static site, you can choose the <strong>Starter</strong> plan, which is free:</p>

<p><img src="https://assets.digitalocean.com/articles/67673/8.png" alt="Choose starter plan"></p>

<p>Now click the <strong>Launch Starter App</strong> button. After waiting a couple of minutes, your app will be deployed.</p>

<p>Navigate to the URL listed beneath the title of your app. You will find your Gatsby app successfully deployed.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you created a Gatsby site with GIFs and deployed the site onto <a href="https://www.digitalocean.com/docs/app-platform/">DigitalOcean App Platform</a>. DigitalOcean App Platform is a convenient way to deploy and share your Gatsby projects. If you would like to learn more about this product, check out the <a href="https://www.digitalocean.com/docs/app-platform/how-to/manage-domains/">official documentation for App Platform</a>.</p>
