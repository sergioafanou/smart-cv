---
title : "How To Install PHP 7.4 and Set Up a Local Development Environment on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-php-7-4-and-set-up-a-local-development-environment-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-54.jpg"
---

<p><em>The author selected <a href="https://www.brightfunds.org/organizations/open-sourcing-mental-illness-ltd">Open Sourcing Mental Illness Ltd</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>PHP is a popular server scripting language known for creating dynamic and interactive web pages. Getting up and running with your language of choice is the first step in learning to program. </p>

<p>This tutorial will guide you through installing PHP 7.4 on Ubuntu and setting up a local programming environment via the command line. You will also install a dependency manager, <a href="https://getcomposer.org/">Composer</a>, and test your installation by running a script.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To complete this tutorial, you will need a local or virtual machine with Ubuntu 20.04 installed and have administrative access and an internet connection to that machine. You can download this operating system via the <a href="http://releases.ubuntu.com/releases">Ubuntu releases page</a>.</p>

<h2 id="step-1-—-setting-up-php-7-4">Step 1 — Setting Up PHP 7.4</h2>

<p>You&rsquo;ll be completing your installation and setup on the command line, which is a non-graphical way to interact with your computer. That is, instead of clicking on buttons, you’ll be typing in text and receiving feedback from your computer through text as well.</p>

<p>The command line, also known as a shell or terminal, can help you modify and automate many of the tasks you do on a computer every day and is an essential tool for software developers. There are many terminal commands to learn that can enable you to do more powerful things. The article <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal">An Introduction to the Linux Terminal</a> can get you better oriented with the terminal.</p>

<p>On Ubuntu, you can find the Terminal application by clicking on the Ubuntu icon in the upper-left-hand corner of your screen and typing <code>terminal</code> into the search bar. Click on the Terminal application icon to open it. Alternatively, you can hit the <code>CTRL</code>, <code>ALT</code>, and <code>T</code> keys on your keyboard at the same time to open the Terminal application automatically.</p>

<p><img src="https://assets.digitalocean.com/articles/eng_python/UbuntuDebianSetUp/UbuntuSetUp.png" alt="Ubuntu terminal"></p>

<p><span class='note'><strong>Note:</strong> Ubuntu 20.04 ships with PHP 7.4 in its upstream repositories. This means that if you attempt to install PHP without a specified version, it will use 7.4. <br></span></p>

<p>You will want to avoid relying on the default version of PHP because that default version could change depending on where you are running your code. You may also wish to install a different version to match an application you are using or to upgrade to a newer version, such as PHP 8. </p>

<p>Run the following command to update <code>apt-get</code> itself, which ensures that you have access to the latest versions of anything you want to install:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt-get update
</li></ul></code></pre>
<p>Next, install <code>software-properties-common</code>, which adds management for additional software sources:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt -y install software-properties-common
</li></ul></code></pre>
<p>The <code>-y</code> flag will automatically agree to the installation. Without that, you would receive a prompt in your terminal window for each installation.</p>

<p>Next, install the repository <code>ppa:ondrej/php</code>, which will give you all your versions of PHP:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo add-apt-repository ppa:ondrej/php
</li></ul></code></pre>
<p>Finally, you update <code>apt-get</code> again so your package manager can see the newly listed packages:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt-get update
</li></ul></code></pre>
<p>Now you&rsquo;re ready to install PHP 7.4 using the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt -y install php7.4
</li></ul></code></pre>
<p>Check the version installed:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php -v
</li></ul></code></pre>
<p>You will receive something similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>PHP 7.4.0beta4 (cli) (built: Aug 28 2019 11:41:49) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0-dev, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.0beta4, Copyright (c), by Zend Technologies
</code></pre>
<p>Besides PHP itself, you will likely want to install some additional PHP modules. You can use this command to install additional modules, replacing <code><span class="highlight">PACKAGE_NAME</span></code> with the package you wish to install:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt-get install php7.4-<span class="highlight">PACKAGE_NAME</span>
</li></ul></code></pre>
<p>You can also install more than one package at a time. Here are a few suggestions of the most common modules you will most likely want to install:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt-get install -y php7.4-cli php7.4-json php7.4-common php7.4-mysql php7.4-zip php7.4-gd php7.4-mbstring php7.4-curl php7.4-xml php7.4-bcmath
</li></ul></code></pre>
<p>This command will install the following modules:</p>

<ul>
<li><code>php7.4-cli</code> - command interpreter, useful for testing PHP scripts from a shell or performing general shell scripting tasks</li>
<li><code>php7.4-json</code> - for working with JSON data</li>
<li><code>php7.4-common</code> - documentation, examples, and common modules for PHP</li>
<li><code>php7.4-mysql</code> - for working with MySQL databases</li>
<li><code>php7.4-zip</code> - for working with compressed files</li>
<li><code>php7.4-gd</code> - for working with images</li>
<li><code>php7.4-mbstring</code> - used to manage non-ASCII strings</li>
<li><code>php7.4-curl</code> - lets you make HTTP requests in PHP</li>
<li><code>php7.4-xml</code> - for working with XML data</li>
<li><code>php7.4-bcmath</code> - used when working with precision floats</li>
</ul>

<p>PHP configurations related to Apache are stored in <code>/etc/php/7.4/apache2/php.ini</code>. You can list all loaded PHP modules with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php -m
</li></ul></code></pre>
<p>You have installed PHP and verified the version you have running. You also installed any required PHP modules and were able to list the modules that you have loaded. </p>

<p>You could start using PHP right now, but you will likely want to use various libraries to build PHP applications quickly. Before you test your PHP environment, first set up a dependency manager for your projects.</p>

<h2 id="step-2-—-setting-up-composer-for-dependency-management-optional">Step 2 — Setting Up Composer for Dependency Management (Optional)</h2>

<p>Libraries are a collection of code that can help you solve common problems without needing to write everything yourself. Since there are many libraries available, using a dependency manager will help you manage multiple libraries as you become more experienced in writing PHP.</p>

<p><a href="https://getcomposer.org/">Composer</a> is a tool for dependency management in PHP. It allows you to declare the libraries your project depends on and will manage installing and updating these packages. </p>

<p>Although similar, Composer is not a package manager in the same sense as <code>yum</code> or <code>apt</code>. It deals with &ldquo;packages&rdquo; or libraries, but it manages them on a per-project basis, installing them in a directory (e.g. <code>vendor</code>) inside your project. By default, it does not install anything globally. Thus, it is a <em>dependency manager</em>. It does, however, support a global project for convenience via the <code>global</code> command.</p>

<p>This idea is not new, and Composer is strongly inspired by Node&rsquo;s <code>npm</code> and Ruby&rsquo;s <code>bundler</code>.</p>

<p>Suppose:</p>

<ul>
<li>You have a project that depends on several libraries.</li>
<li>Some of those libraries depend on other libraries.</li>
</ul>

<p>Composer:</p>

<ul>
<li>Enables you to declare the libraries you depend on.</li>
<li>Finds out which versions of which packages can and need to be installed and installs them by downloading them into your project.</li>
<li>Enables you to update all your dependencies in one command.</li>
<li>Enables you to see the Basic Usage chapter for more details on declaring dependencies.</li>
</ul>

<p>There are, in short, two ways to install Composer: locally as part of your project or globally as a system-wide executable. Either way, you will start with the local install.</p>

<h3 id="locally">Locally</h3>

<p>To quickly install Composer in the current directory, run this script in your terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
</li><li class="line" data-prefix="$">php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
</li><li class="line" data-prefix="$">php composer-setup.php
</li><li class="line" data-prefix="$">php -r "unlink('composer-setup.php');"
</li></ul></code></pre>
<p>This installer script will check some <code>php.ini</code> settings, warn you if they are set incorrectly, and then download the latest <code>composer.phar</code> in the current directory. The four lines will, in order:</p>

<ul>
<li>Download the installer to the current directory</li>
<li>Verify the installer SHA-384, which you can also <a href="https://composer.github.io/pubkeys.html">cross-check here</a></li>
<li>Run the installer</li>
<li>Remove the installer</li>
</ul>

<p>The installer will check a few PHP settings and then download <code>composer.phar</code> to your working directory. This file is the Composer binary. It is a PHAR (PHP archive), which is an archive format for PHP that can be run on the command line, amongst other things.</p>

<p>In order to run Composer, you use <code>php composer.phar</code>. As an example, run this command to see the version of Composer you have installed:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php composer.phar --version
</li></ul></code></pre>
<p>To use Composer locally, you will want your <code>composer.phar</code> file to be in your project&rsquo;s root directory. You can start in your project directory before installing Composer. You can also move the file after installation. You can also install Composer to a specific directory by using the <code>--install-dir</code> option and additionally (re)name it using the <code>--filename</code> option.</p>

<p>Since Composer is something used across projects, it&rsquo;s recommended that you continue to the next portion and set Composer to run globally. </p>

<h3 id="globally">Globally</h3>

<p>You can place the Composer PHAR anywhere you wish. If you put it in a directory that is part of your <code>$PATH</code>, you can access it globally. You can even make it executable on Ubuntu (and other Unix systems) and invoke it without directly using the PHP interpreter.</p>

<p>After installing locally, run this command to move <code>composer.phar</code> to a directory that is in your path:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mv composer.phar /usr/local/bin/composer
</li></ul></code></pre>
<p>If you&rsquo;d like to install it only for your user and avoid requiring root permissions, you can use <code>~/.local/bin</code> instead, which is available by default on some Linux distributions:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mv composer.phar ~/.local/bin/composer
</li></ul></code></pre>
<p>Now to run Composer, use <code>composer</code> instead of <code>php composer.phar</code>. To check for your Composer version, run:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">composer --version
</li></ul></code></pre>
<p>As a final step, you may optionally initialize your project with <code>composer init</code>. This will create the <code>composer.json</code> file that will manage your project dependencies. Initializing the project will also let you define project details such as Author and License, and use <a href="https://getcomposer.org/doc/01-basic-usage.md#autoloading">Composer&rsquo;s autoload functionality</a>. You can define dependencies now or add them later.</p>

<p>Run this command to initialize a project:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">composer init
</li></ul></code></pre>
<p>Running this command will start the setup wizard. Details that you enter in the wizard can be updated later, so feel free to leave the defaults and just press <code>ENTER</code>. If you aren&rsquo;t ready to install any dependencies, you can choose <code>no</code>. Enter in your details at each prompt:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>This command will guide you through creating your composer.json config.
Package name (sammy/php_install): <span class="highlight">sammy/project1</span>
Description []:
Author [Sammy &lt;sammy@digitalocean.com&gt;, n to skip]:
Minimum Stability []: 
Package Type (e.g. library, project, metapackage, composer-plugin) []: <span class="highlight">project</span>
License []: 

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? <span class="highlight">no</span>
Would you like to define your dev dependencies (require-dev) interactively [yes]? <span class="highlight">no</span>

{
    "name": "sammy/project1",
    "type": "project",
    "authors": [
        {
            "name": "Sammy",
            "email": "sammy@digitalocean.com"
        }
    ],
    "require": {}
}

Do you confirm generation [yes]? <span class="highlight">yes</span>
</code></pre>
<p>Before you confirm the generation, you will see a sample of the <code>composer.json</code> file that the wizard will create. If it all looks good, you can confirm the default of <code>yes</code>. If you need to start over, choose <code>no</code>.</p>

<p>The first time you define any dependency, Composer will create a <code>vendor</code> folder. All dependencies install into this <code>vendor</code> folder. Composer also creates a <code>composer.lock</code> file. This file specifies the <strong>exact</strong> version of each dependency and subdependency used in your project. This assures that any machine on which your program is run, will be using the exact same version of each packages.</p>

<p><span class='note'><strong>Note:</strong> The <code>vendor</code> folder should never be committed to your version control system (VCS). The <code>vendor</code> folder only contains packages you have installed from other vendors. Those individual vendors will maintain their own code in their own version control systems. You should only be tracking the code you write. Instead of committing the <code>vendor</code> folder, you only need to commit your <code>composer.json</code> and <code>composer.lock</code> files. You can learn more about ignoring specific files in <a href="https://www.digitalocean.com/community/cheatsheets/how-to-use-git-a-reference-guide#ignoring-files">How To Use Git: A Reference Guide</a>.<br></span></p>

<p>Now that you have PHP installed and a way to manage your project dependencies using Composer, you&rsquo;re ready to test your environment.</p>

<h2 id="step-3-—-testing-the-php-environment">Step 3 — Testing the PHP Environment</h2>

<p>To test that your system is configured correctly for PHP, you can create and run a basic PHP script. Call this script <code>hello.php</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano hello.php
</li></ul></code></pre>
<p>This will open a blank file. Put the following text, which is valid PHP code, inside the file:</p>
<div class="code-label " title="hello.php">hello.php</div><pre class="code-pre "><code class="code-highlight language-php">&lt;?php
echo 'Hello World!';
?&gt;
</code></pre>
<p>Once you&rsquo;ve added the text, save and close the file. You can do this by holding down the <code>CTRL</code> key and pressing the <code>x</code> key. Then choose <code>y</code> and press <code>ENTER</code>.</p>

<p>Now you can test to make sure that PHP processes your script correctly. Type <code>php</code> to tell PHP to process the file, followed by the name of the file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php hello.php
</li></ul></code></pre>
<p>If the PHP is processed properly, you will see only the characters within the quotes:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Hello World!
</code></pre>
<p>PHP has successfully processed the script, meaning that your PHP environment is successfully installed and you&rsquo;re ready to continue your programming journey.</p>

<h2 id="conclusion">Conclusion</h2>

<p>At this point, you have a PHP 7.4 programming environment set up on your local Ubuntu machine and can begin a coding project.</p>

<p>Before you start coding, you may want to set up an Integrated Development Environment (IDE). While there are many IDEs to choose from, <a href="https://code.visualstudio.com/">VS Code</a> is a popular choice as it offers many powerful features such as a graphical interface, syntax highlighting, and debugging.</p>

<p>With your local machine ready for software development, you can continue to learn more about coding in PHP by following <a href="https://www.digitalocean.com/community/tutorials/how-to-work-with-strings-in-php">How To Work With Strings in PHP</a>.</p>
