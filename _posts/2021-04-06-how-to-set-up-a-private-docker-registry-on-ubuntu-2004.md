---
title : "How To Set Up a Private Docker Registry on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-30.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/foss-nonprofits">Free and Open Source Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://docs.docker.com/registry/#what-it-is">Docker Registry</a> is an application that manages storing and delivering Docker container images. Registries centralize container images and reduce build times for developers. Docker images guarantee the same runtime environment through virtualization, but building an image can involve a significant time investment. For example, rather than installing dependencies and packages separately to use Docker, developers can download a compressed image from a registry that contains all of the necessary components. Furthermore, developers can automate pushing images to a registry using continuous integration tools, such as <a href="https://travis-ci.com/">TravisCI</a>, to seamlessly update images during production and development.</p>

<p>Docker also has a free public registry, <a href="https://hub.docker.com/">Docker Hub</a>, that can host your custom Docker images, but there are situations where you will not want your image to be publicly available. Images typically contain all the code necessary to run an application, so using a private registry is preferable when using proprietary software.</p>

<p>In this tutorial, you will set up and secure your own private Docker Registry. You will use <a href="https://docs.docker.com/compose/">Docker Compose</a> to define configurations to run your Docker containers and Nginx to forward server traffic from the internet to the running Docker container. Once you&rsquo;ve completed this tutorial, you will be able to push a custom Docker image to your private registry and pull the image securely from a remote server.</p>

<h2 id="prerequisites">Prerequisites</h2>

<ul>
<li>Two Ubuntu 20.04 servers set up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">the Ubuntu 20.04 Initial Server Setup Guide</a>, including a <code>sudo</code> non-root user and a firewall. One server will <strong>host</strong> your private Docker Registry and the other will be your <strong>client</strong> server.</li>
<li>Docker installed on both servers by following <strong>Step 1 and 2</strong> of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04">How To Install and Use Docker on Ubuntu 20.04</a>.</li>
<li>Docker Compose installed on the <strong>host</strong> server by following <strong>Step 1</strong> of <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04">How To Install and Use Docker Compose on Ubuntu 20.04</a>.</li>
<li>Nginx installed on your <strong>host</strong> server by following the steps  in <a href="https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04">How To Install Nginx on Ubuntu 20.04</a>.</li>
<li>Nginx secured with Let&rsquo;s Encrypt on your server for the private Docker Registry, by following the <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04">How To Secure Nginx with Let&rsquo;s Encrypt on Ubuntu 20.04</a> tutorial. Make sure to redirect all traffic from HTTP to HTTPS in <strong>Step 4</strong>.</li>
<li>A domain name that resolves to the server you&rsquo;re using for the private Docker Registry. You will set this up as part of the Let&rsquo;s Encrypt prerequisite. In this tutorial, we&rsquo;ll refer to it as <code><span class="highlight">your_domain</span></code>.</li>
</ul>

<h2 id="step-1-—-installing-and-configuring-the-docker-registry">Step 1 — Installing and Configuring the Docker Registry</h2>

<p>Docker on the command line is useful when starting out and testing containers, but proves to be unwieldy for bigger deployments involving multiple containers running in parallel.</p>

<p>With Docker Compose, you can write one <code>.yml</code> file to set up each container&rsquo;s configuration and information the containers need to communicate with each other. You can use the <code>docker-compose</code> command-line tool to issue commands to all the components that make up your application, and control them as a group.</p>

<p>Docker Registry is itself an application with multiple components, so you will use Docker Compose to manage it. To start an instance of the registry, you&rsquo;ll set up a <code>docker-compose.yml</code> file to define it and the location on disk where your registry will be storing its data.</p>

<p>You&rsquo;ll store the configuration in a directory called <code>docker-registry</code> on the main server. Create it by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir ~/docker-registry
</li></ul></code></pre>
<p>Navigate to it:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd ~/docker-registry
</li></ul></code></pre>
<p>Then, create a subdirectory called <code>data</code>, where your registry will store its images:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir data
</li></ul></code></pre>
<p>Create and open a file called <code>docker-compose.yml</code> by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano docker-compose.yml
</li></ul></code></pre>
<p>Add the following lines, which define a basic instance of a Docker Registry:</p>
<div class="code-label " title="~/docker-registry/docker-compose.yml">~/docker-registry/docker-compose.yml</div><pre class="code-pre docker-compose"><code class="code-highlight language-bash">version: '3'

services:
  registry:
    image: registry:2
    ports:
    - "5000:5000"
    environment:
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      - ./data:/data
</code></pre>
<p>First, you name the first service <code>registry</code>, and set its image to <code>registry</code>, version <code>2</code>. Then, under <code>ports</code>, you map the port <code>5000</code> on the host to the port <code>5000</code> of the container. This allows you to send a request to port <code>5000</code> on the server, and have the request forwarded to the registry.</p>

<p>In the <code>environment</code> section, you set the <code>REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY</code> variable to <code>/data</code>, specifying in which volume it should store its data. Then, in the <code>volumes</code> section, you map the <code>/data</code> directory on the host file system to <code>/data</code> in the container, which acts as a passthrough. The data will actually be stored on the host&rsquo;s file system.</p>

<p>Save and close the file.</p>

<p>You can now start the configuration by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker-compose up
</li></ul></code></pre>
<p>The registry container and its dependencies will be downloaded and started.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Creating network "docker-registry_default" with the default driver
Pulling registry (registry:2)...
2: Pulling from library/registry
e95f33c60a64: Pull complete
4d7f2300f040: Pull complete
35a7b7da3905: Pull complete
d656466e1fe8: Pull complete
b6cb731e4f93: Pull complete
Digest: sha256:da946ca03fca0aade04a73aa94b54ff0dc614216bdd1d47585f97b4c1bdaa0e2
Status: Downloaded newer image for registry:2
Creating docker-registry_registry_1 ... done
Attaching to docker-registry_registry_1
registry_1  | time="2021-03-18T12:32:59.587157744Z" level=warning msg="No HTTP secret provided - generated random secret. This may cause problems with uploads if multiple registries are behind a load-balancer. To provide a shared secret, fill in http.secret in the configuration file or set the REGISTRY_HTTP_SECRET environment variable." go.version=go1.11.2 instance.id=119fe50b-2bb6-4a8d-902d-dfa2db63fc2f service=registry version=v2.7.1
registry_1  | time="2021-03-18T12:32:59.587912733Z" level=info msg="redis not configured" go.version=go1.11.2 instance.id=119fe50b-2bb6-4a8d-902d-dfa2db63fc2f service=registry version=v2.7.1
registry_1  | time="2021-03-18T12:32:59.598496488Z" level=info msg="using inmemory blob descriptor cache" go.version=go1.11.2 instance.id=119fe50b-2bb6-4a8d-902d-dfa2db63fc2f service=registry version=v2.7.1
registry_1  | time="2021-03-18T12:32:59.601503005Z" level=info msg="listening on [::]:5000" go.version=go1.11.2 instance.id=119fe50b-2bb6-4a8d-902d-dfa2db63fc2f service=registry version=v2.7.1
...
</code></pre>
<p>You&rsquo;ll address the <code>No HTTP secret provided</code> warning message later in this tutorial. Notice that the last line of the output shows it has successfully started listening on port <code>5000</code>.</p>

<p>You can press <code>CTRL+C</code> to stop its execution.</p>

<p>In this step, you have created a Docker Compose configuration that starts a Docker Registry listening on port <code>5000</code>. In the next steps, you&rsquo;ll expose it at your domain and set up authentication.</p>

<h2 id="step-2-—-setting-up-nginx-port-forwarding">Step 2 — Setting Up Nginx Port Forwarding</h2>

<p>As part of the prerequisites, you&rsquo;ve enabled HTTPS at your domain. To expose your secured Docker Registry there, you&rsquo;ll only need to configure Nginx to forward traffic from your domain to the registry container.</p>

<p>You have already set up the <code>/etc/nginx/sites-available/<span class="highlight">your_domain</span></code> file, containing your server configuration. Open it for editing by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/nginx/sites-available/<span class="highlight">your_domain</span>
</li></ul></code></pre>
<p>Find the existing <code>location</code> block:</p>
<div class="code-label " title="/etc/nginx/sites-available/your_domain">/etc/nginx/sites-available/your_domain</div><pre class="code-pre "><code class="code-highlight language-bash">...
location / {
  ...
}
...
</code></pre>
<p>You need to forward traffic to port <code>5000</code>, where your registry will be listening for traffic. You also want to append headers to the request forwarded to the registry, which provides additional information from the server about the request itself. Replace the existing contents of the <code>location</code> block with the following lines:</p>
<div class="code-label " title="/etc/nginx/sites-available/your_domain">/etc/nginx/sites-available/your_domain</div><pre class="code-pre "><code class="code-highlight language-bash">...
location / {
    # Do not allow connections from docker 1.5 and earlier
    # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
    if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
      return 404;
    }

    proxy_pass                          http://localhost:5000;
    proxy_set_header  Host              $http_host;   # required for docker client's sake
    proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
    proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_read_timeout                  900;
}
...
</code></pre>
<p>The <code>if</code> block checks the user agent of the request and verifies that the version of the Docker client is above 1.5, as well as that it&rsquo;s not a <code>Go</code> application that&rsquo;s trying to access. For more explanation on this, you can find the <code>nginx</code> header configuration in <a href="https://docs.docker.com/registry/recipes/nginx/#setting-things-up">Docker&rsquo;s registry Nginx guide</a>.</p>

<p>Save and close the file when you&rsquo;re done. Apply the changes by restarting Nginx:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart nginx
</li></ul></code></pre>
<p>If you get an error, double-check the configuration you&rsquo;ve added.</p>

<p>To confirm that Nginx is properly forwarding traffic to your registry container on port <code>5000</code>,  run it:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker-compose up
</li></ul></code></pre>
<p>Then, in a browser window, navigate to your domain and access the <code>v2</code> endpoint, like so:</p>
<pre class="code-pre "><code>https://<span class="highlight">your_domain</span>/v2
</code></pre>
<p>You will see an empty JSON object:</p>
<pre class="code-pre "><code class="code-highlight language-json">{}
</code></pre>
<p>In your terminal, you&rsquo;ll receive output similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>registry_1  | time="2018-11-07T17:57:42Z" level=info msg="response completed" go.version=go1.7.6 http.request.host=cornellappdev.com http.request.id=a8f5984e-15e3-4946-9c40-d71f8557652f http.request.method=GET http.request.remoteaddr=128.84.125.58 http.request.uri="/v2/" http.request.useragent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/604.4.7 (KHTML, like Gecko) Version/11.0.2 Safari/604.4.7" http.response.contenttype="application/json; charset=utf-8" http.response.duration=2.125995ms http.response.status=200 http.response.written=2 instance.id=3093e5ab-5715-42bc-808e-73f310848860 version=v2.6.2
registry_1  | 172.18.0.1 - - [07/Nov/2018:17:57:42 +0000] "GET /v2/ HTTP/1.0" 200 2 "" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/604.4.7 (KHTML, like Gecko) Version/11.0.2 Safari/604.4.7"
</code></pre>
<p>You can see from the last line that a <code>GET</code> request was made to <code>/v2/</code>, which is the endpoint you sent a request to, from your browser. The container received the request you made, from the port forwarding, and returned a response of <code>{}</code>. The code <code>200</code> in the last line of the output means that the container handled the request successfully.</p>

<p>Press <code>CTRL+C</code> to stop its execution.</p>

<p>Now that you have set up port forwarding, you&rsquo;ll move on to improving the security of your registry.</p>

<h2 id="step-3-—-setting-up-authentication">Step 3 — Setting Up Authentication</h2>

<p>Nginx allows you to set up HTTP authentication for the sites it manages, which you can use to limit access to your Docker Registry. To achieve this, you&rsquo;ll create an authentication file with <code>htpasswd</code> and add username and password combinations to it that will be accepted.</p>

<p>You can obtain the <code>htpasswd</code> utility by installing the <code>apache2-utils</code> package. Do so by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install apache2-utils -y
</li></ul></code></pre>
<p>You&rsquo;ll store the authentication file with credentials under <code>~/docker-registry/auth</code>. Create it by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir ~/docker-registry/auth
</li></ul></code></pre>
<p>Navigate to it:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd ~/docker-registry/auth
</li></ul></code></pre>
<p>Create the first user, replacing <code><span class="highlight">username</span></code> with the username you want to use. The <code>-B</code> flag orders the use of the <code>bcrypt</code> algorithm, which Docker requires:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">htpasswd -Bc registry.password <span class="highlight">username</span>
</li></ul></code></pre>
<p>Enter the password when prompted, and the combination of credentials will be appended to <code>registry.password</code>.</p>

<span class='note'><p>
<strong>Note:</strong> To add more users, re-run the previous command without <code>-c</code>, which creates a new file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">htpasswd -B registry.password <span class="highlight">username</span>
</li></ul></code></pre>
<p></p></span>

<p>Now that the list of credentials is made, you&rsquo;ll edit <code>docker-compose.yml</code> to order Docker to use the file you created to authenticate users. Open it for editing by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano ~/docker-registry/docker-compose.yml
</li></ul></code></pre>
<p>Add the highlighted lines:</p>
<div class="code-label " title="~/docker-registry/docker-compose.yml">~/docker-registry/docker-compose.yml</div><pre class="code-pre docker-compose"><code class="code-highlight language-bash">version: '3'

services:
  registry:
    image: registry:2
    ports:
    - "5000:5000"
    environment:
      <span class="highlight">REGISTRY_AUTH: htpasswd</span>
      <span class="highlight">REGISTRY_AUTH_HTPASSWD_REALM: Registry</span>
      <span class="highlight">REGISTRY_AUTH_HTPASSWD_PATH: /auth/registry.password</span>
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      <span class="highlight">- ./auth:/auth</span>
      - ./data:/data
</code></pre>
<p>You&rsquo;ve added environment variables specifying the use of HTTP authentication and provided the path to the file <code>htpasswd</code> created. For <code>REGISTRY_AUTH</code>, you have specified <code>htpasswd</code> as its value, which is the authentication scheme you are using, and set <code>REGISTRY_AUTH_HTPASSWD_PATH</code> to the path of the authentication file. <code>REGISTRY_AUTH_HTPASSWD_REALM</code> signifies the name of <code>htpasswd</code> realm.</p>

<p>You&rsquo;ve also mounted the <code>./auth</code> directory to make the file available inside the registry container. Save and close the file.</p>

<p>You can now verify that your authentication works correctly. First, navigate to the main directory:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd ~/docker-registry
</li></ul></code></pre>
<p>Then, run the registry by executing:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker-compose up
</li></ul></code></pre>
<p>In your browser, refresh the page of your domain. You&rsquo;ll be asked for a username and password.</p>

<p>After providing a valid combination of credentials, you&rsquo;ll see an empty JSON object:</p>
<pre class="code-pre "><code class="code-highlight language-json">{}
</code></pre>
<p>This means that you&rsquo;ve successfully authenticated and gained access to the registry. Exit by pressing <code>CTRL+C</code>.</p>

<p>Your registry is now secured and can be accessed only after authentication. You&rsquo;ll now configure it to run as a background process while being resilient to reboots by starting automatically.</p>

<h2 id="step-4-—-starting-docker-registry-as-a-service">Step 4 — Starting Docker Registry as a Service</h2>

<p>You can ensure that the registry container starts every time the system boots up, or after it crashes, by instructing Docker Compose to always keep it running. Open <code>docker-compose.yml</code> for editing:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano docker-compose.yml
</li></ul></code></pre>
<p>Add the following line under the <code>registry</code> block:</p>
<div class="code-label " title="docker-compose.yml">docker-compose.yml</div><pre class="code-pre docker-compose"><code class="code-highlight language-bash">...
  registry:
    <span class="highlight">restart: always</span>
...
</code></pre>
<p>Setting <code>restart</code> to always ensures that the container will survive reboots. When you&rsquo;re done, save and close the file.</p>

<p>You can now start your registry as a background process by passing in <code>-d</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker-compose up -d
</li></ul></code></pre>
<p>With your registry running in the background, you can freely close the SSH session, and the registry won&rsquo;t be affected.</p>

<p>Because Docker images may be very large in size, you&rsquo;ll now increase the maximum file size that Nginx will accept for uploads.</p>

<h2 id="step-5-—-increasing-file-upload-size-for-nginx">Step 5 — Increasing File Upload Size for Nginx</h2>

<p>Before you can push an image to the registry, you need to ensure that your registry will be able to handle large file uploads.</p>

<p>The default size limit of file uploads in Nginx is <code>1m</code>, which is not nearly enough for Docker images. To raise it, you&rsquo;ll modify the main Nginx config file, located at <code>/etc/nginx/nginx.conf</code>. Open it for editing by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/nginx/nginx.conf
</li></ul></code></pre>
<p>Find the <code>http</code> section, and add the following line:</p>
<div class="code-label " title="/etc/nginx/nginx.conf">/etc/nginx/nginx.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
http {
        <span class="highlight">client_max_body_size 16384m;</span>
        ...
}
...
</code></pre>
<p>The <code>client_max_body_size</code> parameter is now set to <code>16384m</code>, making the maximum upload size equal to 16GB.</p>

<p>Save and close the file when you&rsquo;re done.</p>

<p>Restart Nginx to apply the configuration changes:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart nginx
</li></ul></code></pre>
<p>You can now upload large images to your Docker Registry without Nginx blocking the transfer or erroring out.</p>

<h2 id="step-6-—-publishing-to-your-private-docker-registry">Step 6 — Publishing to Your Private Docker Registry</h2>

<p>Now that your Docker Registry server is up and running, and accepting large file sizes, you can try pushing an image to it. Since you don&rsquo;t have any images readily available, you&rsquo;ll use the <code>ubuntu</code> image from Docker Hub, a public Docker Registry, to test.</p>

<p>From your second, client server, run the following command to download the <code>ubuntu</code> image, run it, and get access to its shell:</p>
<pre class="code-pre command prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -t -i ubuntu /bin/bash
</li></ul></code></pre>
<p>The <code>-i</code> and <code>-t</code> flags give you interactive shell access into the container.</p>

<p>Once you&rsquo;re in, create a file called <code>SUCCESS</code> by running:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="root@f7e13d5464d1:/#">touch /SUCCESS
</li></ul></code></pre>
<p>By creating this file, you have customized your container. You&rsquo;ll later use it to check that you&rsquo;re using exactly the same container.</p>

<p>Exit the container shell by running:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="root@f7e13d5464d1:/#">exit
</li></ul></code></pre>
<p>Now, create a new image from the container you&rsquo;ve just customized:</p>
<pre class="code-pre command prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker commit $(docker ps -lq) test-image
</li></ul></code></pre>
<p>The new image is now available locally, and you&rsquo;ll push it to your new container registry. First, you have to log in:</p>
<pre class="code-pre command prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login https://<span class="highlight">your_domain</span>
</li></ul></code></pre>
<p>When prompted, enter in a username and password combination that you&rsquo;ve defined in step 3 of this tutorial.</p>

<p>The output will be:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>...
Login Succeeded
</code></pre>
<p>Once you&rsquo;re logged in, rename the created image:</p>
<pre class="code-pre command prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag test-image <span class="highlight">your_domain</span>/test-image
</li></ul></code></pre>
<p>Finally, push the newly tagged image to your registry:</p>
<pre class="code-pre command prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">your_domain</span>/test-image
</li></ul></code></pre>
<p>You&rsquo;ll receive output similar to the following:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>The push refers to a repository [<span class="highlight">your_domain</span>/test-image]
420fa2a9b12e: Pushed
c20d459170d8: Pushed
db978cae6a05: Pushed
aeb3f02e9374: Pushed
latest: digest: sha256:88e782b3a2844a8d9f0819dc33f825dde45846b1c5f9eb4870016f2944fe6717 size: 1150
</code></pre>
<p>You&rsquo;ve verified that your registry handles user authentication by logging in, and allows authenticated users to push images to the registry. You&rsquo;ll now try pulling the image from your registry.</p>

<h2 id="step-7-—-pulling-from-your-private-docker-registry">Step 7 — Pulling From Your Private Docker Registry</h2>

<p>Now that you&rsquo;ve pushed an image to your private registry, you&rsquo;ll try pulling from it.</p>

<p>On the main server, log in with the username and password you set up previously:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login https://<span class="highlight">your_domain</span>
</li></ul></code></pre>
<p>Try pulling the <code>test-image</code> by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker pull <span class="highlight">your_domain</span>/test-image
</li></ul></code></pre>
<p>Docker should download the image. Run the container with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -it <span class="highlight">your_domain</span>/test-image /bin/bash
</li></ul></code></pre>
<p>List the files present by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ls
</li></ul></code></pre>
<p>You will see the <code>SUCCESS</code> file you&rsquo;ve created earlier, confirming that its the same image you&rsquo;ve created:</p>
<pre class="code-pre "><code>SUCCESS  bin  boot  dev  etc  home  lib  lib64  media   mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
</code></pre>
<p>Exit the container shell by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">exit
</li></ul></code></pre>
<p>Now that you&rsquo;ve tested pushing and pulling images, you&rsquo;ve finished setting up a secure registry that you can use to store custom images.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial you set up your own private Docker Registry, and published a Docker image to it. As mentioned in the introduction, you can also use <a href="https://docs.travis-ci.com/user/docker/">TravisCI</a> or a similar CI tool to automate pushing to a private registry directly. By leveraging Docker containers in your workflow, you can ensure that the image containing the code will result in the same behavior on any machine, whether in production or in development. For more information on writing Docker files, you can visit the <a href="https://docs.docker.com/develop/develop-images/dockerfile_best-practices/">official docs</a> on best practices.</p>
