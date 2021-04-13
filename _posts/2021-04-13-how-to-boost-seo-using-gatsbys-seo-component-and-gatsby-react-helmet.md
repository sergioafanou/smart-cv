---
title : "How To Boost SEO Using Gatsby's SEO Component and Gatsby React Helmet"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-boost-seo-using-gatsby-s-seo-component-and-gatsby-react-helmet
image: "https://sergio.afanou.com/assets/images/image-midres-44.jpg"
---

<p><em>The author selected <a href="https://www.devcolor.org/">/dev/color</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>When a user codes and deploys a website, they often want an online audience to find and read the website they’ve created. <em>Search engine optimization</em> (SEO) is the practice of making a website discoverable by this online audience. Optimizing search involves making changes to your Gatsby app so that it will show up in the results for search engines like <a href="https://www.google.com">Google</a>, <a href="https://www.bing.com/">Bing</a>, and <a href="https://duckduckgo.com/">DuckDuckGo</a>. This is often done by fine-tuning the metadata that ends up in the HTML for your site.</p>

<p>In this tutorial, you will configure Gatsby&rsquo;s <a href="https://www.gatsbyjs.com/tutorial/seo-and-social-sharing-cards-tutorial/">SEO component</a> that comes with SEO tooling right out of the box. You will add meta tags to your site using <a href="https://www.gatsbyjs.com/plugins/gatsby-plugin-react-helmet/">Gatsby React Helmet</a>. Meta tags are important because they give search engines information about your site. Usually the better understanding Google has about your site, the more accurately it can index your webpage. You will also create social media cards for <a href="https://twitter.com/">Twitter</a>, and <a href="https://ogp.me/">Open Graph</a> meta tags in <a href="https://www.facebook.com/">Facebook</a>. There are over one billion people using some form of social media, so optimizing for social media is an efficient way to get your website in front of many internet users.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li><p><a href="https://nodejs.org">Node.js</a> version 14.16.0 installed on your computer. To install this on macOS or Ubuntu 20.04, follow the steps in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-and-create-a-local-development-environment-on-macos">How to Install Node.js and Create a Local Development Environment on macOS</a> or the <strong>Installing Using a PPA</strong> section of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04">How To Install Node.js on Ubuntu 20.04</a>.</p></li>
<li><p><a href="https://www.gatsbyjs.com/">Gatsby.js</a> and the Gatsby CLI tool installed. You can find out how to install this in the <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-your-first-gatsby-website">How To Set Up Your First Gatsby Website</a> tutorial.</p></li>
<li><p>An understanding of JavaScript will be useful. You can learn more about JavaScript at our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-javascript">How To Code in JavaScript series</a>. Although Gatsby uses React, you don’t need to know React in order to get started. However, if you would like to learn the basic concepts, check out our <a href="https://www.digitalocean.com/community/tutorial_series/how-to-code-in-react-js">How To Code in React series</a>.</p></li>
</ul>

<h2 id="step-1-—-creating-an-empty-project">Step 1 — Creating an Empty Project</h2>

<p>In this section, you are going to create a new project based on the <a href="https://github.com/gatsbyjs/gatsby-starter-blog">Gatsby starter default template</a>. You are going to create a whale-watching website, and in the following steps you will improve its SEO. This will give you a solid project that you can optimize with meta tags and social media assets.</p>

<p>First, use the CLI tool to start a new project named <code>gatsby-seo-project</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">gatsby new <span class="highlight">gatsby-seo-project</span> https://github.com/gatsbyjs/gatsby-starter-default
</li></ul></code></pre>
<p>This creates a new website from the starter template in the <a href="https://github.com/gatsbyjs/gatsby-starter-default"><code>gatsby-starter-default</code></a> GitHub repository from Gatsby.</p>

<p>Once the project is created, move into the <code>src/images</code> folder of the project:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd <span class="highlight">gatsby-seo-project</span>/src/images
</li></ul></code></pre>
<p>Once you are in the <code>images</code> folder, download a picture of a whale from the stock photography website <a href="https://unsplash.com/">Unsplash</a>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget 'https://unsplash.com/photos/JRsl_wfC-9A/download?force=true&amp;w=640'
</li></ul></code></pre>
<p><a href="https://www.gnu.org/software/wget/">Wget</a> is a Gnu command that downloads files from the internet.</p>

<p>Next, list all of the images in the same <code>images</code> directory with the <code>ls</code> command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ls
</li></ul></code></pre>
<p>You will receive the following output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>'download?force=true&amp;w=640' gatsby-astronaut.png      gatsby-icon.png
</code></pre>
<p><code>'download?force=true&amp;w=640'</code> is a hard name to remember, so rename it to <code>whale-watching.png</code>: </p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mv 'download?force=true&amp;w=640' whale-watching.png
</li></ul></code></pre>
<p>Now that you have your whale image, go to the root of your project and open <code>src/pages/index.js</code>. Make the highlighted change in the following code to customize your website: </p>
<div class="code-label " title="gatsby-seo-project/src/pages/index.js">gatsby-seo-project/src/pages/index.js</div><pre class="code-pre "><code class="code-highlight language-javascript">import * as React from "react"
import { Link } from "gatsby"
import { StaticImage } from "gatsby-plugin-image"

import Layout from "../components/layout"
import SEO from "../components/seo"

const IndexPage = () =&gt; (
  &lt;Layout&gt;
    &lt;SEO title="Home" /&gt;
    &lt;h1&gt;<span class="highlight">Whale watching for all</span>&lt;/h1&gt;
    &lt;p&gt;<span class="highlight">Come see extraordinary whales!</span>&lt;/p&gt;
    &lt;StaticImage
      src="../images/<span class="highlight">whale-watching.png</span>"
      width={300}
      quality={95}
      formats={["AUTO", "WEBP", "AVIF"]}
      alt="<span class="highlight">A surfacing whale</span>"
      style={{ marginBottom: `1.45rem` }}
    /&gt;
    &lt;p&gt;
      &lt;Link to="/page-2/"&gt;Go to page 2&lt;/Link&gt; &lt;br /&gt;
      &lt;Link to="/using-typescript/"&gt;Go to "Using TypeScript"&lt;/Link&gt;
    &lt;/p&gt;
  &lt;/Layout&gt;
)

export default IndexPage
</code></pre>
<p>Save the file. To try out the code, start a local development server with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">gatsby develop
</li></ul></code></pre>
<p>Once the server is running, check <code>localhost:8000</code> in your browser. You will find your new site rendered in the browser:</p>

<p><img src="https://assets.digitalocean.com/articles/67637/1.png" alt="Gatsby site with whale image and text."></p>

<p>You are now finished setting up your project. Next, you will add meta tags to your site header with React Helmet.</p>

<h2 id="step-2-—-creating-an-seo-component-with-react-helmet">Step 2 — Creating an SEO Component with React Helmet</h2>

<p>In this section, you are going to learn how to control the technical SEO aspects of your site with the help of <a href="https://www.gatsbyjs.com/plugins/gatsby-plugin-react-helmet/">Gatsby&rsquo;s React Helmet plugin</a> and an SEO component. The Helmet plugin provides <a href="https://www.digitalocean.com/community/tutorials/react-server-side-rendering">server side rendering</a> to all of the metadata found in the head of the Gatsby site. This is important because, without server side rendering, there is a chance that server engine bots might not be able to scrape and record metadata before the site is rendered, making it more difficult to index the site for search.</p>

<p>When you use <a href="https://github.com/gatsbyjs/gatsby-starter-default"><code>gatsby-starter-default</code></a> as a base for your website, it already comes with everything you need to start tweaking SEO. To do this, you will be working with the following files:</p>

<ul>
<li><p><code>gatsby-config.js</code>: Gatsby config includes metadata values that <a href="https://graphql.org/">GraphQL</a> will query and place in the SEO file.</p></li>
<li><p><code>src/components/seo.js</code>: This file contains the Helmet and the SEO component.</p></li>
</ul>

<p>You are first going to open the <code>gatsby-config.js</code> file, which is located at the root of your project:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano gatsby-config.js
</li></ul></code></pre>
<p>Before you make any changes to the file, examine the <code>plugins</code> key in the exported object. The Gatsby default starter already has the Helmet plugin installed, as shown in the following highlighted line:</p>
<div class="code-label " title="gatsby-seo-project/gatsby-config.js">gatsby-seo-project/gatsby-config.js</div><pre class="code-pre "><code class="code-highlight language-javascript">module.exports = {
  siteMetadata: {
    title: `Gatsby Default Starter`,
    description: `Kick off your next, great Gatsby project with this default starter. This barebones starter ships with the main Gatsby configuration files you might need.`,
    author: `@gatsbyjs`,
  },
  plugins: [
    <span class="highlight">`gatsby-plugin-react-helmet`,</span>
    `gatsby-plugin-image`,
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
    `gatsby-plugin-gatsby-cloud`,
    // this (optional) plugin enables Progressive Web App + Offline functionality
    // To learn more, visit: https://gatsby.dev/offline
    // `gatsby-plugin-offline`,
  ],
}
</code></pre>
<p>Next, direct your attention to the <code>siteMetadata</code> key. This contains an <a href="https://www.digitalocean.com/community/tutorials/understanding-objects-in-javascript">object</a> that holds the metadata for your site. You are going to change the <code>title</code>, the <code>description</code>, and the <code>author</code>. You will also add <code>keywords</code> to help users search for your site:</p>
<div class="code-label " title="gatsby-seo-project/gatsby-config.js">gatsby-seo-project/gatsby-config.js</div><pre class="code-pre "><code class="code-highlight language-javascript">module.exports = {
  siteMetadata: {
    title: <span class="highlight">`Wondrous World of Whale Watching`</span>,
    description: <span class="highlight">`Come and enjoy an experience of a lifetime! Watch whales with us!`</span>,
    author: <span class="highlight">`@digitalocean`</span>,
    <span class="highlight">keywords: `whales, marine life, trip, recreation`,</span>
  },
...
</code></pre>
<p>The <code>keywords</code> metadata is instrumental in optimizing for search. While the topic of choosing keywords is beyond the scope of this tutorial, you can learn more about the basics of SEO at <a href="https://developers.google.com/search/docs">Google&rsquo;s search documentation website</a>. Here you have added specific search terms that users might use when searching for a site like the sample whale-watching site.</p>

<p>Save and close this file.</p>

<p>Next, proceed to open the SEO component:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano src/components/seo.js
</li></ul></code></pre>
<p>There is a lot going on in the SEO component. Focus your attention on the <code>SEO</code> function. In this function you are using <a href="https://www.gatsbyjs.com/docs/conceptual/graphql-concepts/">GraphQL</a> to query the <code>siteMetadata</code> object. Remember that you have added <code>keywords</code> to your <code>siteMetadata</code> object, so make the following highlighted change to your query: </p>
<div class="code-label " title="gatsby-seo-project/src/components/seo.js">gatsby-seo-project/src/components/seo.js</div><pre class="code-pre "><code class="code-highlight language-javascript">...
function SEO({ description, lang, meta, title}) {
  const { site } = useStaticQuery(
    graphql`
      query {
        site {
          siteMetadata {
            title
            description
            author
            <span class="highlight">keywords</span>
          }
        }
      }
    `
  )
...
</code></pre>
<p>Below the SEO function, add a reference to this queried data in a <code>keywords</code> constant to make the data easier to work with:</p>
<div class="code-label " title="gatsby-seo-project/src/components/seo.js">gatsby-seo-project/src/components/seo.js</div><pre class="code-pre "><code class="code-highlight language-javascript">...
  <span class="highlight">const keywords = site.siteMetadata.keywords</span>
  const metaDescription = description || site.siteMetadata.description
  const defaultTitle = site.siteMetadata?.title
...
</code></pre>
<p>The variable <code>keywords</code> has all of the keywords you created in the <code>gatsby-config.js</code> file. The variable <code>metaDescription</code> is a description that you can pass as a prop on a page or query from the <code>siteMetadata</code> object in <code>gatsby-config.js</code>. Finally, <code>defaultTitle</code> is set to the value of <code>title</code> in the <code>siteMetadata</code> object. The <code>?</code> in the <code>siteMetadata</code> attribute <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining">checks for a null value</a> and returns <code>undefined</code> for a null or nullish value.</p>

<p>Next, inspect what the SEO component is returning, and add an object for <code>keywords</code>:</p>
<div class="code-label " title="gatsby-seo-project/src/components/seo.js">gatsby-seo-project/src/components/seo.js</div><pre class="code-pre "><code class="code-highlight language-javascript">...
  return (
    &lt;Helmet
      htmlAttributes={{
        lang,
      }}
      title={title}
      titleTemplate={defaultTitle ? `%s | ${defaultTitle}` : null}
      meta={[
        {
          name: `description`,
          content: metaDescription,
        },
        <span class="highlight">{</span>
          <span class="highlight">name: `keywords`,</span>
          <span class="highlight">content: keywords,</span>
        <span class="highlight">},</span>
        {
          property: `og:title`,
          content: title,
        },
        {
          property: `og:description`,
          content: metaDescription,
        },
        {
          property: `og:type`,
          content: `website`,
        },
        {
          name: `twitter:card`,
          content: `summary`,
        },
        {
          name: `twitter:creator`,
          content: site.siteMetadata?.author || ``,
        },
        {
          name: `twitter:title`,
          content: title,
        },
        {
          name: `twitter:description`,
          content: metaDescription,
        },
      ].concat(meta)}
    /&gt;
  )
...
</code></pre>
<p>You are returning a <code>Helmet</code> component. <code>Helmet</code> populates the head of an HTML document using server side rendered data, which makes it easier for Google to crawl and record the metadata. <code>htmlAttributes={{lang,}}</code> specifies the language of the element&rsquo;s content, and <code>title</code> is the title found in the metadata, which comes from <code>siteMetadata</code>. <code>titleTemplate</code> creates the title tag, which is important, since Google penalizes sites that are missing a title tag.</p>

<p>After this section, you&rsquo;ll find the <code>meta</code> object, which contains the metadata. Most of the values here come from <code>siteMetadata</code>. </p>

<p>Finally, examine the <code>SEO.defaultProps</code> and <code>SEO.propTypes</code> objects:</p>
<div class="code-label " title="gatsby-seo-project/src/components/seo.js">gatsby-seo-project/src/components/seo.js</div><pre class="code-pre "><code class="code-highlight language-javascript">...
SEO.defaultProps = {
  lang: `en`,
  meta: [],
  description: ``,
}

SEO.propTypes = {
  description: PropTypes.string,
  lang: PropTypes.string,
  meta: PropTypes.arrayOf(PropTypes.object),
  title: PropTypes.string.isRequired,
}
</code></pre>
<p><code>SEO.defaultProps</code> are the default values of the SEO props. <code>SEO.propTypes</code> passes the correct value type and acts as a <a href="https://www.digitalocean.com/community/tutorials/how-to-customize-react-components-with-props#step-3-%E2%80%94-creating-predictable-props-with-proptypes-and-defaultprops">light typing system</a>.</p>

<p>Save your file with the new <code>keywords</code> entry and start the local server in your terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">gatsby develop
</li></ul></code></pre>
<p>After the server has starter, enter <code>localhost:8000</code> in the browser. Open up the view of the HTML in your browser; for <a href="https://www.google.com/chrome/">Chrome</a>, right click the window and open <a href="https://developer.chrome.com/docs/devtools/">DevTools</a>. Choose <strong>Elements</strong> and open the <code>&lt;head&gt;&lt;/head&gt;</code> tag. In this tag, you will find the following line:</p>
<pre class="code-pre "><code class="code-highlight language-html">...
&lt;meta name="keywords" content="whales, marine life, trip, recreation" data-react-helmet="true"&gt;
...
</code></pre>
<p>You have now successfully set the <code>header</code> data with React Helmet.</p>

<p>In this section, you created metadata to improve the SEO of your whale-watching site. In the next section, you&rsquo;ll add an image and make this site easier to share on social media.</p>

<h2 id="step-3-—-adding-images-to-enhance-social-sharing">Step 3 — Adding Images to Enhance Social Sharing</h2>

<p>Social networks play an important role in attracting attention to your content. In this section, you are going to add an image to two features that optimize sharing your site on social: your <a href="https://twitter.com/">Twitter</a> card and the <a href="https://ogp.me/">Open Graph protocol</a> for <a href="https://www.facebook.com/">Facebook</a>. You will also learn which tools to use to ensure that your metadata is appearing on these two social network platforms.</p>

<p>Open up <code>gatsby-config</code> in a text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano gatsby-config.js
</li></ul></code></pre>
<p>You are going to add <code>images/whale-watching.png</code> into the <code>siteMetadata</code>:</p>
<div class="code-label " title="gatsby-seo-project/gatsby-config.js">gatsby-seo-project/gatsby-config.js</div><pre class="code-pre "><code class="code-highlight language-javascript">module.exports = {
  siteMetadata: {
    title: `Wondrous World of Whale Watching`,
    description: `Come and enjoy an experience of a lifetime! Watch whales with us!`,
    author: `@digitalocean`,
    keywords: `whales, marine life, trip, recreation`,
    <span class="highlight">image: `src/images/whale-watching.png`</span>
  },
  plugins: [
    `gatsby-plugin-react-helmet`,
    `gatsby-plugin-image`,
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
    `gatsby-plugin-gatsby-cloud`,
    // this (optional) plugin enables Progressive Web App + Offline functionality
    // To learn more, visit: https://gatsby.dev/offline
    // `gatsby-plugin-offline`,
  ],
}
</code></pre>
<p>GraphQL will now be able to query the image. Close and save the file.</p>

<p>Next, open up <code>seo.js</code> in the text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano src/components/seo.js
</li></ul></code></pre>
<p>Now that your image is in the site metadata, it&rsquo;s time to add it to the SEO component. Add the following highlighted lines to <code>seo.js</code>:</p>
<div class="code-label " title="gatsby-seo-project/src/components/seo.js">gatsby-seo-project/src/components/seo.js</div><pre class="code-pre "><code class="code-highlight language-javascript">...
function SEO({ description, lang, meta, title}) {
  const { site } = useStaticQuery(
    graphql`
      query {
        site {
          siteMetadata {
            title
            description
            author
            keywords
            <span class="highlight">image</span>
          }
        }
      }
    `
  )
  <span class="highlight">const image = site.siteMetadata.image</span>
  const keywords = site.siteMetadata.keywords
  const metaDescription = description || site.siteMetadata.description
  const defaultTitle = site.siteMetadata?.title

  return (
    &lt;Helmet
      htmlAttributes={{
        lang,
      }}
      title={title}
      titleTemplate={defaultTitle ? `%s | ${defaultTitle}` : null}
      meta={[
        {
          name: `description`,
          content: metaDescription,
        },
        {
          name: `keywords`,
          content: keywords,
        },
        {
          property: `og:title`,
          content: title,
        },
        {
          property: `og:description`,
          content: metaDescription,
        },
        {
          property: `og:type`,
          content: `website`,
        },
        <span class="highlight">{</span>
          <span class="highlight">property: `og:image`,</span>
          <span class="highlight">content: image,</span>
        <span class="highlight">},</span>
        {
          name: `twitter:card`,
          content: `summary`,
        },
        <span class="highlight">{</span>
          <span class="highlight">name: `twitter:image`,</span>
          <span class="highlight">content: image,</span>
        <span class="highlight">},</span>
        {
          name: `twitter:creator`,
          content: site.siteMetadata?.author || ``,
        },
        {
          name: `twitter:title`,
          content: title,
        },
        {
          name: `twitter:description`,
          content: metaDescription,
        },
      ].concat(meta)}
    /&gt;
  )
}

SEO.defaultProps = {
  lang: `en`,
  meta: [],
  description: ``,
}

SEO.propTypes = {
  description: PropTypes.string,
  <span class="highlight">image: PropTypes.string,</span>
  lang: PropTypes.string,
  meta: PropTypes.arrayOf(PropTypes.object),
  title: PropTypes.string.isRequired,
}

export default SEO
</code></pre>
<p>In this code, you:</p>

<ul>
<li>Added the image to the GraphQL query</li>
<li>Created an <code>image</code> variable and set the value to the image found in <code>siteMetadata</code></li>
<li>Added <code>og:image</code> to the <code>meta</code> object</li>
<li>Added <code>twitter:image</code> to the <code>meta</code> object</li>
<li>Added <code>image</code> to <code>SEO.propTypes</code></li>
</ul>

<p>Save your changes and close <code>seo.js</code>.</p>

<p>The final step in this process is to test these changes on Twitter and Facebook. This cannot be done from a local development server; in order to test your site, you must first deploy it. There are many ways to do this, including using DigitalOcean&rsquo;s App Platform, which you can read about in the <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-gatsby-application-to-digitalocean-app-platform">How To Deploy a Gatsby Application to DigitalOcean App Platform tutorial</a>. </p>

<p>This tutorial will use a Gatsby app hosted on App Platform as an example. You can find this app at <code>https://digital-ocean-gatsby-seo-xkmfq.ondigitalocean.app/</code>, and it includes the SEO changes you made to your site in this tutorial.</p>

<p>If you want to test if social media objects show up on Twitter, head over <code>https://cards-dev.twitter.com/validator</code>. This validator is maintained by Twitter, and requires a Twitter account to use. Put the URL for the sample deployed site into the validator:</p>

<p><img src="https://assets.digitalocean.com/articles/67637/2.png" alt="Twitter card validator"></p>

<p>Notice that the custom image will now show when users tweet about your website.</p>

<p>Next, head over to Facebook&rsquo;s Open Graph validator at <code>https://developers.facebook.com/tools/debug/</code>. This is maintained by Facebook, and requires a Facebook account to use. Add the URL for the sample app into the URL field. The debugger will provide you with more detail about which <code>og</code> objects are present and which ones are missing:</p>

<p><img src="https://assets.digitalocean.com/articles/67637/3.png" alt="Facebook open graph validator"></p>

<p>Notice that the image appears with a title and a description in the <strong>Link Preview</strong> section.</p>

<p>You&rsquo;ve now added an image to your metadata, a Twitter card, and a Facebook Open Graph.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial, you boosted the SEO of your site using Gatsby&rsquo;s React Helmet and the SEO component. You&rsquo;ve also learned how to add images to social media cards to make your site more shareable.</p>

<p>With the basics of SEO covered, you can now read more about optimizing search for Gatsby at the <a href="https://www.gatsbyjs.com/docs/how-to/adding-common-features/seo/">official Gatsby documentation</a>.</p>
