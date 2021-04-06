---
title : "How To Use WP-CLI v2 to Manage Your WordPress Site from the Command Line"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-wp-cli-v2-to-manage-your-wordpress-site-from-the-command-line
image: "https://sergio.afanou.com/assets/images/image-midres-35.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/organizations/free-software-foundation-inc">Free Software Foundation</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://wp-cli.org/">WP-CLI</a> is a command-line tool for <a href="https://wordpress.org/">WordPress</a> development and administrative tasks. It provides several commands that you can use to manage your WordPress website without needing to log in to the dashboard and navigate through the pages.</p>

<p>Using WP-CLI to manage your WordPress installation over the conventional interface process helps to speed up your workflow. For many aspects of your website, you can also use WP-CLI in Bash scripts to automate tasks that are tedious or take a long time to perform.</p>

<p>In this tutorial, you&rsquo;ll use many of the features of WP-CLI and discover how it can fit into your workflow. You&rsquo;ll cover common operations such as managing plugins and themes, creating content, working with the database, and updating WordPress. The capabilities of WP-CLI go beyond this tutorial; however, you&rsquo;ll be able to transfer the skills from this tutorial to work with the more common options of other WP-CLI features.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To follow this tutorial, you&rsquo;ll need a secure WordPress installation. If you need to set up WordPress, you can follow these tutorials for your chosen server distribution:</p>

<ul>
<li>A server that is configured with a non-root <code>sudo</code> user. You can follow one of our <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Initial Server Setup Guides</a> for this.</li>
<li>Linux, Apache, MySQL, PHP (LAMP stack) installed on your server. Follow <a href="https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04">How To Install Linux, Apache, MySQL, PHP (LAMP) stack</a> for your server&rsquo;s distribution.</li>
<li>A secure WordPress installation. You can set this up by following <a href="https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-ubuntu-20-04-with-a-lamp-stack">How To Install WordPress with a LAMP Stack</a>.</li>
</ul>

<p><span class='note'><strong>Note:</strong> It&rsquo;s also possible <a href="https://make.wordpress.org/cli/">to install WordPress with WP-CLI</a> if you don&rsquo;t have an existing setup, but we won&rsquo;t be covering that aspect in this article.<br></span></p>

<h2 id="step-1-—-installing-wp-cli">Step 1 — Installing WP-CLI</h2>

<p>In this step, you&rsquo;ll install the latest version of the WP-CLI tool on your server. The tool is packaged in a <a href="https://make.wordpress.org/cli/handbook/guides/installing/">Phar file</a>, which is a packaging format for PHP applications that makes app deployment and distribution convenient. </p>

<p>You can download the Phar file for WP-CLI through <code>curl</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
</li></ul></code></pre>
<p>Once you have downloaded the file, run the following command to verify that it is working:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">php wp-cli.phar --info
</li></ul></code></pre>
<p>You will receive the following output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>OS:     Linux 5.4.0-51-generic #56-Ubuntu SMP Mon Oct 5 14:28:49 UTC 2020 x86_64
Shell:  /bin/bash
PHP binary:     /usr/bin/php7.4
PHP version:    7.4.3
php.ini used:   /etc/php/7.4/cli/php.ini
WP-CLI root dir:        phar://wp-cli.phar/vendor/wp-cli/wp-cli
WP-CLI vendor dir:      phar://wp-cli.phar/vendor
WP_CLI phar path:       /home/ayo
WP-CLI packages dir:
WP-CLI global config:
WP-CLI project config:
WP-CLI version: 2.4.0
</code></pre>
<p>Next, make the file executable with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">chmod +x wp-cli.phar
</li></ul></code></pre>
<p>At this point, you can execute the <code>wp-cli.phar</code> file directly to access the WP-CLI tool. To make it available globally on the system, move it to your <code>/usr/local/bin/</code> directory and rename it to <code>wp</code>. This ensures that you can access WP-CLI from any directory by entering the <code>wp</code> command at the start of a prompt:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mv wp-cli.phar /usr/local/bin/wp
</li></ul></code></pre>
<p>Now, you will be able to issue the following command to check the installed version of WP-CLI:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp cli version
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>WP-CLI 2.4.0
</code></pre>
<p>In this step, you installed WP-CLI on your server. You can check out <a href="https://make.wordpress.org/cli/handbook/guides/installing/#alternative-installation-methods">alternative installation methods</a> in the documentation. In subsequent sections, you&rsquo;ll explore the tasks you can accomplish through the WP-CLI interface.</p>

<h2 id="step-2-—-configuring-wordpress-plugins">Step 2 — Configuring WordPress Plugins</h2>

<p>It can be tedious to install and manage WordPress plugins through the admin user interface. It&rsquo;s possible to offload such tasks to WP-CLI to make the process much faster. In this section you will learn to install, update, and delete plugins on a WordPress site through the command line.</p>

<p>Before you proceed, make sure you are in the directory of your WordPress installation:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd /var/www/<span class="highlight">wordpress</span>
</li></ul></code></pre>
<p>Remember to change the highlighted directory name to the directory that contains your WordPress installation. This might be your domain name, if you followed the prerequisite tutorials.</p>

<h3 id="listing-current-plugins">Listing Current Plugins</h3>

<p>You can list the currently installed plugins on a WordPress site with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin list
</li></ul></code></pre>
<p>It displays a list of plugin names along with their status, version, and an indication of an available update.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+---------+----------+-----------+---------+
| name    | status   | update    | version |
+---------+----------+-----------+---------+
| akismet | inactive | available | 4.1.7   |
| hello   | inactive | none      | 1.7.2   |
+---------+----------+-----------+---------+
</code></pre>
<h3 id="searching-for-plugins">Searching for Plugins</h3>

<p>You can search for plugins through the search bar on the <a href="https://wordpress.org/plugins/">WordPress plugin repository page</a> or you can use the following command for quicker access:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin search <span class="highlight">seo</span>
</li></ul></code></pre>
<p>Once you run this command, you will receive a list of the top 10 plugins that match the search term (as of early 2021). The expected output for the <code>seo</code> query is:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: Showing 10 of 4278 plugins.
+------------------------------------------------------------+---------------------+--------+
| name                                                       | slug                | rating |
+------------------------------------------------------------+---------------------+--------+
| Yoast SEO                                                  | wordpress-seo       | 98     |
| All in One SEO                                             | all-in-one-seo-pack | 92     |
| Rank Math &amp;#8211; SEO Plugin for WordPress                 | seo-by-rank-math    | 98     |
| The SEO Framework                                          | autodescription     | 98     |
| SEOPress, on-site SEO                                      | wp-seopress         | 98     |
| Slim SEO &amp;#8211; Fast &amp;amp; Automated WordPress SEO Plugin | slim-seo            | 92     |
| W3 Total Cache                                             | w3-total-cache      | 88     |
| LiteSpeed Cache                                            | litespeed-cache     | 98     |
| SEO 2021 by Squirrly (Smart Strategy)                      | squirrly-seo        | 92     |
| WP-Optimize &amp;#8211; Clean, Compress, Cache.                | wp-optimize         | 96     |
+------------------------------------------------------------+---------------------+--------+
</code></pre>
<p>You can go to the next page by using the <code>--page</code> flag:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin search <span class="highlight">seo</span> --page=2
</li></ul></code></pre>
<p>Take note of the value in the <code>slug</code> column. You&rsquo;ll use this value to install or update the plugin on the command line.</p>

<h3 id="installing-plugins">Installing Plugins</h3>

<p>You can install one or more plugins by using the <code>wp plugin install</code> command. You find the name of the plugin you want to install (in the <code>slug</code> column) and pass it as an argument to <code>wp plugin install</code>. You can also find the name of the plugin in the URL of the plugin page.</p>

<p><img src="https://assets.digitalocean.com/articles/wp-cli2021/step2a.png" alt="Plugin name in URL"></p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin install jetpack wordpress-seo gutenberg
</li></ul></code></pre>
<p>The output indicates the progress and completion of the installation of each of the plugins:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Installing Jetpack – WP Security, Backup, Speed, &amp; Growth (9.3.1)
Downloading installation package from https://downloads.wordpress.org/plugin/jetpack.9.3.1.zip...
Unpacking the package...
Installing the plugin...
Plugin installed successfully.
Installing Yoast SEO (15.6.2)
Downloading installation package from https://downloads.wordpress.org/plugin/wordpress-seo.15.6.2.zip...
Unpacking the package...
Installing the plugin...
Plugin installed successfully.
Installing Gutenberg (9.8.1)
Downloading installation package from https://downloads.wordpress.org/plugin/gutenberg.9.8.1.zip...
Unpacking the package...
Installing the plugin...
Plugin installed successfully.
Success: Installed 3 of 3 plugins.
</code></pre>
<p>You can run the <code>wp plugin list</code> command again to confirm that you&rsquo;ve installed the plugins successfully:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+---------------+----------+-----------+---------+
| name          | status   | update    | version |
+---------------+----------+-----------+---------+
| akismet       | inactive | available | 4.1.7   |
| gutenberg     | inactive | none      | 9.8.1   |
| hello         | inactive | none      | 1.7.2   |
| jetpack       | inactive | none      | 9.3.1   |
| wordpress-seo | inactive | none      | 15.6.2  |
+---------------+----------+-----------+---------+
</code></pre>
<p>If you want to install a plugin from a remote source other than the WordPress plugin repository, you can pass the zip file&rsquo;s URL as an argument to <code>wp plugin install</code>. This can be helpful for installing custom or premium plugins. For example, the following command  will install the <code>myplugin.zip</code> file hosted on <code>example.com</code>. Make sure to replace the highlighted URL with a link to the plugin zip file before running the command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin install <span class="highlight">https://example.com/wp-content/uploads/myplugin.zip</span>
</li></ul></code></pre>
<p>To install an older version of a plugin in the WordPress repository, specify the desired plugin version through the <code>--version</code> flag:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin install jetpack --version=8.0
</li></ul></code></pre>
<h3 id="activating-and-deactivating-plugins">Activating and Deactivating Plugins</h3>

<p>You can install and activate plugins in one go by appending the <code>--activate</code> flag to <code>wp plugin install</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin install redirection --activate
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Installing Redirection (5.0)
Downloading installation package from https://downloads.wordpress.org/plugin/redirection.zip...
Using cached file '/home/ayo/.wp-cli/cache/plugin/redirection-5.0.zip'...
Unpacking the package...
Installing the plugin...
Plugin installed successfully.
Activating 'redirection'...
Warning: Plugin 'redirection' is already active.
Success: Installed 1 of 1 plugins.
</code></pre>
<p>To activate or deactivate one or more plugins, use the <code>wp plugin activate</code> and <code>wp plugin deactivate</code> commands respectively:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin activate jetpack gutenberg
</li><li class="line" data-prefix="$">wp plugin deactivate jetpack gutenberg
</li></ul></code></pre>
<p>Or you can use the <code>--all</code> flag to activate or deactivate all plugins at once. This is useful if you want to debug a problem in your WordPress installation:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin activate --all
</li><li class="line" data-prefix="$">wp plugin deactivate --all
</li></ul></code></pre>
<h3 id="updating-plugins">Updating Plugins</h3>

<p>You can update plugins through the <code>wp plugin update</code> command. You can choose to update a set of plugins or all of them at once by appending the <code>--all</code> flag. For example, to update the <code>akismet</code> plugin, you can run the following command: </p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin update akismet
</li></ul></code></pre>
<p>You would receive output similar to:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Enabling Maintenance mode...
Downloading update from https://downloads.wordpress.org/plugin/akismet.4.1.8.zip...
Unpacking the update...
Installing the latest version...
Removing the old version of the plugin...
Plugin updated successfully.
Disabling Maintenance mode...
+---------+-------------+-------------+---------+
| name    | old_version | new_version | status  |
+---------+-------------+-------------+---------+
| akismet | 4.1.7       | 4.1.8       | Updated |
+---------+-------------+-------------+---------+
Success: Updated 1 of 1 plugins.
</code></pre>
<h3 id="deleting-plugins">Deleting plugins</h3>

<p>To delete WordPress plugins, you can use the <code>wp plugin delete</code> command. You can specify one or more plugins to delete like the following: </p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin delete redirection
</li></ul></code></pre>
<p>Your output will confirm the deletion:</p>
<pre class="code-pre plaintext"><code><div class="secondary-code-label " title="Output">Output</div>Deleted 'redirection' plugin.
Success: Deleted 1 of 1 plugins.
</code></pre>
<p>You can also delete all the installed plugins in one go by appending the <code>--all</code> flag instead of specifying the plugin names one after the other:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp plugin delete --all
</li></ul></code></pre>
<p>In this step, you&rsquo;ve used WP-CLI to manage the plugins on your WordPress website. It&rsquo;s much faster to perform actions compared to clicking through the admin dashboard. In the next section, you&rsquo;ll leverage WP-CLI for installing and managing WordPress themes.</p>

<h2 id="step-3-—-configuring-themes">Step 3 — Configuring Themes</h2>

<p>The process of managing themes through WP-CLI is almost identical to the way you can use it to manage plugins. In this section, you&rsquo;ll source and apply new themes to a WordPress website through the <code>wp theme</code> subcommand.</p>

<p>First, check what themes you currently have installed on the website:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp theme list
</li></ul></code></pre>
<p>You&rsquo;ll receive a list of the installed themes:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+-----------------+----------+-----------+---------+
| name            | status   | update    | version |
+-----------------+----------+-----------+---------+
| twentynineteen  | inactive | available | 1.8     |
| twentytwenty    | inactive | none      | 1.6     |
| twentytwentyone | active   | available | 1.0     |
+-----------------+----------+-----------+---------+
</code></pre>
<p>There are three themes currently installed and the active one is <code>twentytwentyone</code>. If you want to find something with more features, you can try a search like the following:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp theme search color
</li></ul></code></pre>
<p>The output shows there are 832 themes that match the <code>color</code> search term: </p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: Showing 10 of 832 themes.
+---------------------+---------------------+--------+
| name                | slug                | rating |
+---------------------+---------------------+--------+
| Color               | color               | 0      |
| All Colors          | all-colors          | 100    |
| Color Blog          | color-blog          | 98     |
| Color Block         | color-block         | 0      |
| X Blog color        | x-blog-color        | 0      |
| Multicolor Business | multicolor-business | 0      |
| ColorNews           | colornews           | 100    |
| Colorist            | colorist            | 100    |
| ColorMag            | colormag            | 98     |
| MultiColors         | multicolors         | 74     |
+---------------------+---------------------+--------+
</code></pre>
<p>You can page through the results using the <code>--page</code> flag. For this example, just go ahead and install the <code>ColorMag</code> theme since it has a pretty good rating. The <code>--activate</code> flag activates the theme immediately:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp theme install colormag --activate
</li></ul></code></pre>
<p>The output will confirm the installation:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Installing ColorMag (2.0.4)
Downloading installation package from https://downloads.wordpress.org/theme/colormag.2.0.4.zip...
Unpacking the package...
Installing the theme...
Theme installed successfully.
Activating 'colormag'...
Success: Switched to 'ColorMag' theme.
Success: Installed 1 of 1 themes.
</code></pre>
<p>If you visit your website, you&rsquo;ll find that the ColorMag theme was applied successfully.</p>

<p><img src="https://assets.digitalocean.com/articles/wp-cli2021/step3a.png" alt="ColorMag theme"></p>

<p>The output from the <code>wp theme list</code> command notes that there is an update available for both the <code>twentynineteen</code> and <code>twentytwentyone</code> themes. You can update them both using the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp theme update --all
</li></ul></code></pre>
<p>You&rsquo;ll receive output similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Downloading update from https://downloads.wordpress.org/theme/twentynineteen.1.9.zip...
Unpacking the update...
Installing the latest version...
Removing the old version of the theme...
Theme updated successfully.
Downloading update from https://downloads.wordpress.org/theme/twentytwentyone.1.1.zip...
Unpacking the update...
Installing the latest version...
Removing the old version of the theme...
Theme updated successfully.
+-----------------+-------------+-------------+---------+
| name            | old_version | new_version | status  |
+-----------------+-------------+-------------+---------+
| twentynineteen  | 1.8         | 1.9         | Updated |
| twentytwentyone | 1.0         | 1.1         | Updated |
+-----------------+-------------+-------------+---------+
Success: Updated 2 of 2 themes.
</code></pre>
<p>The <code>wp theme</code> command offers many subcommands that can help you achieve tasks like getting the details of a theme, checking if a particular theme is installed, or even deleting one or more themes. You can explore all of the options by prepending <code>help</code> before the subcommand, as in <code>wp help theme</code> or <code>wp help theme install</code>.</p>

<p>Now that you can manage themes through WP-CLI, you&rsquo;ll review the options that the tool provides for managing WordPress content.</p>

<h2 id="step-4-—-creating-posts-and-pages">Step 4 — Creating Posts and Pages</h2>

<p>WP-CLI provides several ways to manage content through the command line. It can be more comfortable to write posts in the terminal if you&rsquo;re familiar with a command-line editor like <a href="https://www.nano-editor.org/">nano</a> or <a href="https://www.vim.org/download.php">vim</a>.</p>

<p>You can browse the list of posts on the site with:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post list
</li></ul></code></pre>
<p>You&rsquo;ll receive a list of posts:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+----+--------------+-------------+---------------------+-------------+
| ID | post_title   | post_name   | post_date           | post_status |
+----+--------------+-------------+---------------------+-------------+
| 1  | Hello world! | hello-world | 2021-01-24 12:32:06 | publish     |
+----+--------------+-------------+---------------------+-------------+
</code></pre>
<p>The output shows one published post with the title of <code>Hello world!</code> and an ID of <code>1</code>. To delete this post, use the <code>wp post delete</code> command and pass it the post ID:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post delete 1
</li></ul></code></pre>
<p>Your output will confirm the post&rsquo;s deletion:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: Trashed post 1.
</code></pre>
<p>To create a new post, run the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post create --post_status=publish --post_title="Sample post created with WP-CLI" --edit
</li></ul></code></pre>
<p>This command uses the <code>--post_status</code> flag to set the status of the post. Setting it to <code>publish</code> ensures that the post is published immediately after running the command. If you want to create a draft instead, set the <code>--post_status</code> flag to <code>draft</code>. The <code>--post_title</code> flag is how you can specify the title of the post, while <code>--edit</code> causes the post body to be opened in the default system editor (vim). You can find out the other flags that you can use in conjunction with the <code>create</code> subcommand by typing <code>wp help post create</code> in your terminal.</p>

<p>Once the vim editor is open, press the <code>i</code> key to enter INSERT mode then enter the content of the post into the editor. Once you&rsquo;re done editing the post, exit the vim editor by pressing the <code>ESC</code> button then type <code>:wq</code> and press <code>ENTER</code>. You will receive the following output after exiting vim:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: Created post 6.
</code></pre>
<p>If you enter the <code>wp post list</code> command again, you will find the post you just created. You can also check the frontend of the website.</p>

<p><img src="https://assets.digitalocean.com/articles/wp-cli2021/step4a.png" alt="WP-CLI post"></p>

<p>Instead of writing the post on the command line, it&rsquo;s also possible to import the post content from a text file. First, you need to create the file. For example:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">touch content.txt
</li></ul></code></pre>
<p>Next, open the file in a command-line editor to add or edit your content:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano content.txt
</li></ul></code></pre>
<p>Once you&rsquo;re through with the edits, save and close the file by pressing <code>CTRL-X</code> followed by <code>Y</code> to save. You can import the contents of that file as a WordPress post by using the following command. All you need to do is specify the path to the file after the <code>create</code> subcommand. For the example file here, you would run:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post create ./content.txt --post_title='Sample Post Created with WP-CLI' --post_status=publish
</li></ul></code></pre>
<p>If you want to create a page instead of a post, append the <code>--post_type</code> flag and set it to <code>page</code>: </p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post create --post_title="A simple page" --post_status=draft --post_type=page
</li></ul></code></pre>
<h3 id="generating-posts-or-pages">Generating Posts or Pages</h3>

<p>WP-CLI also provides an option to cleanly generate posts and pages with dummy data. This is useful if you need custom data to quickly test a theme or plugin that you are developing. The following command  is to generate posts. If you don&rsquo;t include additional flags, it will generate 100 posts by default.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post generate
</li></ul></code></pre>
<p>You can change the number of posts generated by using the <code>--count</code> flag:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post generate --count=20
</li></ul></code></pre>
<p>If you want to generate pages instead of posts, append the <code>--post_type</code> flag and set it to <code>page</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post generate --count=20 --post_type=page
</li></ul></code></pre>
<p>You can also use the <code>wp help post generate</code> to see other available options that can help you get your desired result.</p>

<h3 id="wordpress-revisions">WordPress Revisions</h3>

<p>It is not uncommon for older sites to have tens or hundreds of revisions on their main pages due to years of editing and updating content. Revisions can be helpful in case you need to revert back to a previous version of your content, but they can also hurt the performance if there are too many. You can clean up all the post revisions in the WordPress database by executing the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp post delete $(wp post list --post_type='revision' --format=ids) --force
</li></ul></code></pre>
<p>The command enclosed in the parenthesis is evaluated first and it will produce the <code>ids</code> of all the post revisions that are present passing them to the <code>delete</code> subcommand. The <code>--force</code> flag is necessary because posts of type <code>'revision'</code> do not support being sent to trash.</p>

<h2 id="step-5-—-managing-your-database">Step 5 — Managing Your Database</h2>

<p>One of the most useful features of WP-CLI is its ability to interact with the MySQL database. For example, if you need an interactive session, you can enter a MySQL prompt with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp db cli
</li></ul></code></pre>
<p>You can then use the MySQL shell as you normally would and, once you are through with your tasks, exit the shell by typing <code>exit</code>.</p>

<p>For one-off queries, you can use the <code>wp db query</code> command by passing a valid SQL query as an argument to the command. For example, to list all the registered users in the WordPress database, you could run:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp db query "SELECT user_login,ID FROM wp_users;"
</li></ul></code></pre>
<p>You will be presented with an output similar to the following:</p>
<pre class="code-pre plaintext"><code><div class="secondary-code-label " title="Output">Output</div>+------------+----+
| user_login | ID |
+------------+----+
| admin      |  1 |
+------------+----+
</code></pre>
<p>With <code>wp db query</code> you can run any one-off SQL query for the WordPress database.</p>

<h3 id="backing-up-and-restoring">Backing Up and Restoring</h3>

<p>WP-CLI also allows you to back up your WordPress database. Running this following command will place a SQL dump file in the current directory. This file contains your entire WordPress database including your posts, pages, user accounts, menus, and so on:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp db export
</li></ul></code></pre>
<p>Once the file is produced, you can move it to a different location for safekeeping:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: Exported to 'wordpress-2021-01-25-25618e7.sql'.
</code></pre>
<p>You can also import a SQL dump file into your database through the <code>wp db import</code> command. This is useful when you are migrating a WordPress website from one location to another.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp db import file.sql
</li></ul></code></pre>
<h3 id="searching-and-replacing">Searching and Replacing</h3>

<p>Another common operation you can perform with WP-CLI is a find-and-replace operation. You can make a dry run first to find out how many instances it would modify. The first string is the search component while the second is the replacement:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp search-replace --dry-run <span class="highlight">'example.com'</span> <span class="highlight">'example.net'</span>
</li></ul></code></pre>
<p>After running this, your output would be similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Success: 10 replacements to be made.
</code></pre>
<p>Once you are sure you want to proceed, remove the <code>--dry-run</code> flag from the previous command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp search-replace <span class="highlight">'example.com'</span> <span class="highlight">'example.net'</span>
</li></ul></code></pre>
<p>In this step, you&rsquo;ve reviewed several database operations that you can perform using WP-CLI. You can also complete other operations, such as optimizing the database, viewing database tables, deleting a database, or resetting one. You can explore the other options under the <code>wp db</code> subcommand by typing <code>wp help db</code> in your terminal.</p>

<h2 id="step-6-—-updating-wordpress">Step 6 — Updating WordPress</h2>

<p>You can update the core WordPress file with WP-CLI. You can examine the current version of WordPress that you have installed by running:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp core version
</li></ul></code></pre><pre class="code-pre plaintext"><code><div class="secondary-code-label " title="Output">Output</div>5.6
</code></pre>
<p>You can check for updates through the <code>wp core check-update</code> command. If your version is not the latest, this will produce an output similar to the following:</p>
<pre class="code-pre plaintext"><code><div class="secondary-code-label " title="Output">Output</div>+---------+-------------+-----------------------------------------------------------------------+
| version | update_type | package_url                                                           |
+---------+-------------+-----------------------------------------------------------------------+
| 5.6.1   | minor       | https://downloads.wordpress.org/release/wordpress-5.6.1-partial-0.zip |
+---------+-------------+-----------------------------------------------------------------------+
</code></pre>
<p>If an update is available, you can install it with:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp core update
</li></ul></code></pre><pre class="code-pre plaintext"><code><div class="secondary-code-label " title="Output">Output</div>Updating to version 5.6.1 (en_US)...
PHP Warning:  Declaration of WP_CLI\Core\CoreUpgrader::download_package($package, $check_signatures = true) should be compatible with WP_Upgrader::download_package($package, $check_signatures = false, $hook_extra = Array) in phar:///usr/local/bin/wp/vendor/wp-cli/core-command/src/WP_CLI/Core/CoreUpgrader.php on line 30
Warning: Declaration of WP_CLI\Core\CoreUpgrader::download_package($package, $check_signatures = true) should be compatible with WP_Upgrader::download_package($package, $check_signatures = false, $hook_extra = Array) in phar:///usr/local/bin/wp/vendor/wp-cli/core-command/src/WP_CLI/Core/CoreUpgrader.php on line 30
Downloading update from https://downloads.wordpress.org/release/wordpress-5.6.1-partial-0.zip...
Unpacking the update...
Success: WordPress updated successfully.
</code></pre>
<p>You can also update to a specific version by setting the <code>--version</code> flag to the version number. If you want to revert to an older version, you also need to add the <code>--force</code> flag, but this isn&rsquo;t recommended:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wp core update --version=5.6
</li><li class="line" data-prefix="$">wp core update --version=5.0 --force 
</li></ul></code></pre>
<p>In this final step, you updated your version of WordPress with WP-CLI.</p>

<h2 id="conclusion">Conclusion</h2>

<p>For WordPress developers and adminstrator&rsquo;s working on the command line, WP-CLI is a great addition to the toolbox. In this tutorial, we covered several of the more common tasks that you can perform through the command line.</p>

<p>WP-CLI has many more commands and options that you can familiarize yourself with to achieve even more on the command line without the web interface. Use <code>wp help &lt;command&gt;</code> to find out all the things you can do with a specific subcommand. There are also many community <a href="https://make.wordpress.org/cli/handbook/references/tools/">tools</a> that extend WP-CLI with even more features. </p>

<p>For more tutorials on WordPress, check out our <a href="https://www.digitalocean.com/community/tags/wordpress">WordPress topic page</a>.</p>
