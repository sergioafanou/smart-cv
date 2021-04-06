---
title : "How To Implement Browser Caching with Nginx's header Module on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-implement-browser-caching-with-nginx-s-header-module-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-17.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>The faster a website loads, the more likely a visitor is to stay. When websites are full of images and interactive content run by scripts loaded in the background, opening a website is not a simple task. It consists of requesting many different files from the server one by one. Minimizing the quantity of these requests is one way to speed up your website.</p>

<p>One method for improving website performance is <em>browser caching</em>. Browser caching tells the browser that it can reuse local versions of downloaded files instead of requesting the server for them again and again. To do this, you must introduce new HTTP response headers that tell the browser how to behave.</p>

<p>Nginx&rsquo;s header module can help you accomplish browser caching. You can use this module to add any arbitrary headers to the response, but its major role is to properly set caching headers. In this tutorial, we will use Nginx&rsquo;s header module to implement browser caching.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To follow this tutorial, you will need:</p>

<ul>
<li><p>One Ubuntu 20.04 server with a regular, non-root user with sudo privileges. You can learn how to prepare your server by following this <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Initial Server Setup tutorial</a>.</p></li>
<li><p>Nginx installed on your server by following the <a href="https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04">How To Install Nginx on Ubuntu 20.04 tutorial</a>.</p></li>
</ul>

<h2 id="step-1-—-creating-test-files">Step 1 — Creating Test Files</h2>

<p>In this step, we will create several test files in the default Nginx directory. We&rsquo;ll use these files later to check Nginx&rsquo;s default behavior and then to test that browser caching is working.</p>

<p>To infer what kind of file is served over the network, Nginx does not analyze the file contents; that would be prohibitively slow. Instead, it looks up the file extension to determine the file&rsquo;s <em>MIME type</em>, which denotes its purpose.</p>

<p>Because of this behavior, the content of our test files is irrelevant. By naming the files appropriately, we can trick Nginx into thinking that, for example, one entirely empty file is an image and another is a stylesheet.</p>

<p>Create a file named <code>test.html</code> in the default Nginx directory using <code>truncate</code>. This extension denotes that it&rsquo;s an HTML page:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo truncate -s 1k /var/www/html/test.html
</li></ul></code></pre>
<p>Let&rsquo;s create a few more test files in the same manner: one <code>jpg</code> image file, one <code>css</code> stylesheet, and one <code>js</code> JavaScript file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo truncate -s 1k /var/www/html/test.jpg
</li><li class="line" data-prefix="$">sudo truncate -s 1k /var/www/html/test.css
</li><li class="line" data-prefix="$">sudo truncate -s 1k /var/www/html/test.js
</li></ul></code></pre>
<p>The next step is to check how Nginx behaves with respect to sending caching control headers on a fresh installation with the files we have just created.</p>

<h2 id="step-2-—-checking-the-default-behavior">Step 2 — Checking the Default Behavior</h2>

<p>By default, all files will have the same default caching behavior. To explore this, we&rsquo;ll use the HTML file we created in step 1, but you can run these tests with any example files.</p>

<p>So, let&rsquo;s check if <code>test.html</code> is served with any information regarding how long the browser should cache the response. The following command requests a file from our local Nginx server and shows the response headers:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -I http://localhost/test.html
</li></ul></code></pre>
<p>You will see several HTTP response headers:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output: Nginx response headers">Output: Nginx response headers</div>HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Tue, 02 Feb 2021 19:03:21 GMT
Content-Type: text/html
Content-Length: 1024
Last-Modified: Tue, 02 Feb 2021 19:02:58 GMT
Connection: keep-alive
<span class="highlight">ETag: "6019a1e2-400"</span>
Accept-Ranges: bytes
</code></pre>
<p>In the second to last line you will find the <code>ETag</code> header, which contains a unique identifier for this particular revision of the requested file. If you execute the previous <code>curl</code> command repeatedly, you will find the exact same <code>ETag</code> value.</p>

<p>When using a web browser, the <code>ETag</code> value is stored and sent back to the server with the <code>If-None-Match</code> request header when the browser wants to request the same file again — for example, when refreshing the page.</p>

<p>We can simulate this on the command line with the following command. Make sure you change the <code>ETag</code> value in this command to match the <code>ETag</code> value in your previous output:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -I -H 'If-None-Match: "<span class="highlight">6019a1e2-400</span>"' http://localhost/test.html
</li></ul></code></pre>
<p>The response will now be different:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output: Nginx response headers">Output: Nginx response headers</div>HTTP/1.1 <span class="highlight">304 Not Modified</span>
Server: nginx/1.18.0 (Ubuntu)
Date: Tue, 02 Feb 2021 19:04:09 GMT
Last-Modified: Tue, 02 Feb 2021 19:02:58 GMT
Connection: keep-alive
<span class="highlight">ETag: "6019a1e2-400"</span>
</code></pre>
<p>This time, Nginx will respond with <strong>304 Not Modified</strong>. It won&rsquo;t send the file over the network again; instead, it will tell the browser that it can reuse the file it already has downloaded locally.</p>

<p>This is useful because it reduces network traffic, but it’s not good enough to achieve good caching performance. The problem with <code>ETag</code> is that browsers always send a request to the server asking if it can reuse its cached file. Even though the server responds with a 304 instead of sending the file again, it still takes time to make the request and receive the response.</p>

<p>In the next step, we will use the headers module to append caching control information. This will make the browser cache some files locally without explicitly asking the server if its fine to do so.</p>

<h2 id="step-3-—-configuring-cache-control-and-expires-headers">Step 3 — Configuring Cache-Control and Expires Headers</h2>

<p>In addition to the <code>ETag</code> file validation header, there are two caching control response headers: <code>Cache-Control</code> and <code>Expires</code>. <code>Cache-Control</code> is the newer version, with more options than <code>Expires</code> and is generally more useful if you want finer control over your caching behavior.</p>

<p>If these headers are set, they can tell the browser that the requested file can be kept locally for a certain amount of time (including forever) without requesting it again. If the headers are not set, browsers will always request the file from the server, expecting either <strong>200 OK</strong> or <strong>304 Not Modified</strong> responses.</p>

<p>We can use the header module to set these HTTP headers. The header module is a core Nginx module, which means it doesn&rsquo;t need to be installed separately to be used.</p>

<p>To add the header module, open the default Nginx configuration file in <code>nano</code> or your favorite text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/nginx/sites-available/default
</li></ul></code></pre>
<p>Find the <code>server</code> configuration block:</p>
<div class="code-label " title="/etc/nginx/sites-available/default">/etc/nginx/sites-available/default</div><pre class="code-pre "><code class="code-highlight language-nginx">. . .
# Default server configuration
#

server {
    listen 80 default_server;
    listen [::]:80 default_server;

. . .
</code></pre>
<p>Add the following two new sections here: one before the <code>server</code> block, to define how long to cache different file types, and one inside it, to set the caching headers appropriately:</p>
<div class="code-label " title="Modified /etc/nginx/sites-available/default">Modified /etc/nginx/sites-available/default</div><pre class="code-pre "><code class="code-highlight language-nginx">. . .
# Default server configuration
#

<span class="highlight"># Expires map</span>
<span class="highlight">map $sent_http_content_type $expires {</span>
<span class="highlight">    default                    off;</span>
<span class="highlight">    text/html                  epoch;</span>
<span class="highlight">    text/css                   max;</span>
<span class="highlight">    application/javascript     max;</span>
<span class="highlight">    ~image/                    max;</span>
<span class="highlight">    ~font/                     max;</span>
<span class="highlight">}</span>

server {
    listen 80 default_server;
    listen [::]:80 default_server;

    <span class="highlight">expires $expires;</span>
. . .
</code></pre>
<p>The section before the <code>server</code> block is a new <code>map</code> block that defines the mapping between the file type and how long that kind of file should be cached.</p>

<p>We&rsquo;re using several different settings in this map:</p>

<ul>
<li><p>The default value is set to <code>off</code>, which will not add any caching control headers. It&rsquo;s a safe bet for the content, we have no particular requirements on how the cache should work.</p></li>
<li><p>For <code>text/html</code>, we set the value to <code>epoch</code>. This is a special value that results explicitly in no caching, which forces the browser to always ask if the website itself is up to date.</p></li>
<li><p>For <code>text/css</code> and <code>application/javascript</code>, which are stylesheets and JavaScript files, we set the value to <code>max</code>. This means the browser will cache these files for as long as possible, reducing the number of requests considerably given that there are typically many of these files.</p></li>
<li><p>The last two settings are for <code>~image/</code> and <code>~font/</code>, which are regular expressions that will match all file types containing <code>image/</code> or <code>font/</code> in their <em>MIME type</em> name (like <code>image/jpg</code>, <code>image/png</code> or <code>font/woff2</code>). Like stylesheets, both pictures and web fonts on websites can be safely cached to speed up page-loading times, so we set this to <code>max</code> as well.</p></li>
</ul>

<p><span class='note'><strong>Note:</strong> These are just a few examples of the most common <em>MIME types</em> used on websites. You can get acquainted with the more extensive list of such types on <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types">Common MIME types</a> site and add others to the map that you might find useful in your case.<br></span></p>

<p>Inside the server block, the <code>expires</code> directive (a part of the headers module) sets the caching control headers. It uses the value from the <code>$expires</code> variable set in the map. This way, the resulting headers will be different depending on the file type.</p>

<p>Save and close the file to exit.</p>

<p>To enable the new configuration, restart Nginx:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart nginx
</li></ul></code></pre>
<p>Next, let&rsquo;s make sure our new configuration works.</p>

<h2 id="step-4-—-testing-browser-caching">Step 4 — Testing Browser Caching</h2>

<p>Execute the same request as before for the test HTML file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -I http://localhost/test.html
</li></ul></code></pre>
<p>This time the response will be different. You will see two additional HTTP response headers:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Tue, 02 Feb 2021 19:10:13 GMT
Content-Type: text/html
Content-Length: 1024
Last-Modified: Tue, 02 Feb 2021 19:02:58 GMT
Connection: keep-alive
ETag: "6019a1e2-400"
<span class="highlight">Expires: Thu, 01 Jan 1970 00:00:01 GMT</span>
<span class="highlight">Cache-Control: no-cache</span>
Accept-Ranges: bytes
</code></pre>
<p>The <code>Expires</code> header shows a date in the past and <code>Cache-Control</code> is set with <code>no-cache</code>, which tells the browser to always ask the server if there is a newer version of the file (using the <code>ETag</code> header, like before).</p>

<p>You&rsquo;ll find a difference in response with the test image file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -I http://localhost/test.jpg
</li></ul></code></pre>
<p>Note the new output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Tue, 02 Feb 2021 19:10:42 GMT
Content-Type: image/jpeg
Content-Length: 1024
Last-Modified: Tue, 02 Feb 2021 19:03:02 GMT
Connection: keep-alive
ETag: "6019a1e6-400"
<span class="highlight">Expires: Thu, 31 Dec 2037 23:55:55 GMT</span>
<span class="highlight">Cache-Control: max-age=315360000</span>
Accept-Ranges: bytes
</code></pre>
<p>In this case, <code>Expires</code> shows the date in the distant future, and <code>Cache-Control</code> contains <code>max-age</code> information, which tells the browser how long it can cache the file in seconds. This tells the browser to cache the downloaded image for as long as it can, so any subsequent appearances of this image will use local cache and not send a request to the server at all.</p>

<p>The result should be similar for both <code>test.js</code> and <code>test.css</code>, as both JavaScript and stylesheet files are set with caching headers too.</p>

<p>This means the cache control headers have been configured properly and your website will benefit from the performance gain and less server requests due to browser caching. You should customize the caching settings based on your website&rsquo;s content, but the defaults in this article are a reasonable place to start.</p>

<h2 id="conclusion">Conclusion</h2>

<p>The headers module can be used to add any arbitrary headers to the response, but properly setting caching control headers is one of its most useful applications. It increases performance for the website users, especially on networks with higher latency, like mobile carrier networks. It can also lead to better results on search engines that factor speed tests into their results. Setting browser caching headers is a crucial recommendation from <a href="https://developers.google.com/speed/pagespeed/insights/">Google&rsquo;s PageSpeed</a> and similar performance testing tools.</p>

<p>You can find more detailed information about the headers module <a href="http://nginx.org/en/docs/http/ngx_http_headers_module.html">in Nginx&rsquo;s official headers module documentation</a>.</p>
