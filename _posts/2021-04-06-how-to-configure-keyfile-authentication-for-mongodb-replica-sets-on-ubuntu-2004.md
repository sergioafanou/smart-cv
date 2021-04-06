---
title : "How To Configure Keyfile Authentication for MongoDB Replica Sets on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-keyfile-authentication-for-mongodb-replica-sets-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-41.jpg"
---

<h3 id="introduction">Introduction</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a>, also known as <em>Mongo</em>, is an open-source document database used in many modern web applications. It is classified as a <a href="https://www.digitalocean.com/community/tutorials/what-is-nosql">NoSQL database</a> because it does not rely on the relational database model. Instead, it uses JSON-like documents with dynamic schemas. This means that, unlike <a href="https://www.digitalocean.com/community/tutorials/what-is-the-relational-model">relational databases</a>, MongoDB does not require a predefined schema before you add data to a database.</p>

<p>When you&rsquo;re working with multiple distributed MongoDB instances, as in the case of a <a href="https://docs.mongodb.com/manual/replication/">replica set</a> or a <a href="https://docs.mongodb.com/manual/sharding/index.html">sharded database architecture</a>, it&rsquo;s important to ensure that the communications between them are secure. One way to do this is through <a href="https://docs.mongodb.com/manual/core/security-internal-authentication/#internal-auth-keyfile"><em>keyfile authentication</em></a>. This involves creating a special file that essentially functions as a shared password for each member in the cluster.</p>

<p>This tutorial outlines how to update an existing replica set to use keyfile authentication. The procedure involved in this guide will also ensure that the replica set doesn&rsquo;t go through any downtime, so the data within the replica set will remain available for any clients or applications that need access to it.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To complete this tutorial, you will need:</p>

<ul>
<li>Three servers, each running Ubuntu 20.04. All three of these servers should have an administrative non-root user and a firewall configured with UFW. To set this up, follow our <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">initial server setup guide for Ubuntu 20.04</a>.</li>
<li>MongoDB installed on each of your Ubuntu servers. Follow our tutorial on <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">How To Install MongoDB on Ubuntu 20.04</a>, making sure to complete each step on each of your servers.</li>
<li>All three of your MongoDB installations configured as a replica set. Follow this tutorial on <a href="https://www.digitalocean.com/community/tutorials/how-to-configure-a-mongodb-replica-set-on-ubuntu-20-04">How To Configure a MongoDB Replica Set on Ubuntu 20.04</a> to set this up.</li>
<li>SSH keys generated for each server. In addition, you should ensure that each server has the other two servers&rsquo; public keys added to its <code>authorized_keys</code> file. This is to ensure that each machine can communicate with one another over SSH, which will make it easier to distribute the keyfile to each of them in Step 2. To set these up, follow our guide on <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04">How To Set Up SSH Keys on Ubuntu 20.04</a>.</li>
</ul>

<p>Please note that, for clarity, this guide will follow the conventions established in the prerequisite replica set tutorial and refer to the three servers as <strong>mongo0</strong>, <strong>mongo1</strong>, and <strong>mongo2</strong>. It will also assume that you&rsquo;ve completed <a href="https://www.digitalocean.com/community/tutorials/how-to-configure-a-mongodb-replica-set-on-ubuntu-20-04#step-1-%E2%80%94-configuring-dns-resolution">Step 1 of that guide</a> and configured each server&rsquo;s <code>hosts</code> file so that the following hostnames will resolve to given server&rsquo;s IP address:</p>

<table><thead>
<tr>
<th>Hostname</th>
<th>Resolves to</th>
</tr>
</thead><tbody>
<tr>
<td><strong>mongo0.replset.member</strong></td>
<td><strong>mongo0</strong></td>
</tr>
<tr>
<td><strong>mongo1.replset.member</strong></td>
<td><strong>mongo1</strong></td>
</tr>
<tr>
<td><strong>mongo2.replset.member</strong></td>
<td><strong>mongo2</strong></td>
</tr>
</tbody></table>

<p>There are a few instances in this guide in which you must run a command or update a file on only one of these servers. In such cases, this guide will default to using <strong>mongo0</strong> in examples and will signify this by showing commands or file changes in a blue background, like this:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">
</li></ul></code></pre>
<p>Any commands that must be run or file changes that must be made on <strong>multiple</strong> servers will have a standard gray background, like this:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">
</li></ul></code></pre>
<h2 id="about-keyfile-authentication">About Keyfile Authentication</h2>

<p>In MongoDB, keyfile authentication relies on <a href="https://docs.mongodb.com/manual/core/security-scram/">Salted Challenge Response Authentication Mechanism (SCRAM)</a>, the database system&rsquo;s default authentication mechanism. SCRAM involves MongoDB reading and verifying credentials presented by a user against a combination of their username, password, and authentication database, all of which are known by the given MongoDB instance. This is the same mechanism used to authenticate users who supply a password when connecting to the database.</p>

<p>In keyfile authentication, the keyfile acts as a shared password for each member in the cluster. A keyfile must contain between 6 and 1024 characters. Keyfiles can only contain characters from the base64 set, and note that MongoDB strips whitespace characters when reading keys. Beginning in version <span class="highlight">4.2</span> of Mongo, keyfiles use YAML format, allowing you to share multiple keys in a single keyfile.</p>

<span class='warning'><p>
<strong>Warning</strong>: The Community version of MongoDB comes with two authentication methods that can help keep your database secure, <em>keyfile authentication</em> and <em>x.509 authentication</em>. For production deployments that employ replication, <a href="https://docs.mongodb.com/manual/tutorial/deploy-sharded-cluster-with-keyfile-access-control/index.html#keyfile-security">the MongoDB documentation recommends</a> using x.509 authentication, and it describes keyfiles as &ldquo;bare-minimum forms of security&rdquo; that are &ldquo;best suited for testing or development environments.&rdquo;</p>

<p>The process of obtaining and configuring x.509 certificates comes with a number of caveats and decisions that must be made on a case-by-case basis, meaning that this procedure is beyond the scope of a DigitalOcean tutorial. If you plan on using a replica set in a production environment, we <strong>strongly encourage</strong> you to review the <a href="https://docs.mongodb.com/manual/tutorial/configure-x509-member-authentication/">official MongoDB documentation on x.509 authentication</a>. </p>

<p>If you plan on using your replica set for testing or development, you can proceed with following this tutorial to add a layer of security to your cluster.<br></p></span>

<h2 id="step-1-—-creating-a-user-administrator">Step 1 — Creating a User Administrator</h2>

<p>When you enable authentication in MongoDB, it will also enable <em>role-based access control</em> for the replica set. Per the MongoDB documentation: </p>

<blockquote>
<p>MongoDB uses Role-Based Access Control (RBAC) to govern access to a MongoDB system. A user is granted one or more roles that determine the user’s access to database resources and operations.</p>
</blockquote>

<p>When access control is enabled on a MongoDB instance, it means that you won&rsquo;t be able to access any of the resources on the system unless you&rsquo;ve authenticated as a valid MongoDB user. Even then, you must authenticate as a user with the appropriate privileges to access a given resource.</p>

<p>If you don&rsquo;t create a user for your MongoDB system before enabling keyfile authentication (and, consequently, access control), you will not be locked out of your replica set. You can create a MongoDB user which you can use to authenticate to the set and, if necessary, create other users through Mongo&rsquo;s <a href="https://docs.mongodb.com/manual/core/security-users/#localhost-exception"><em>localhost exception</em></a>. This is a special exception MongoDB makes for configurations that have enabled access control but lack users. This exception only allows you to connect to the database on the <strong>localhost</strong> and then create a user in the <code>admin</code> database.</p>

<p>However, relying on the localhost exception to create a MongoDB user after enabling authentication means that your replica set will go through a period of downtime, since the replicas will not be able to authenticate their connection until after you create a user. This step outlines how to create a user <em>before</em> enabling authentication to ensure that your replica set remains available. This user will have permissions to create other users on the database, giving you the freedom to create other users with whatever permissions they need in the future. In MongoDB, a user with such permissions is known as a <em>user administrator</em>.</p>

<p>To begin, connect to the <strong>primary member</strong> of your replica set. If you aren&rsquo;t sure which of your members is the primary, you can run the <code>rs.status()</code> method to identify it.</p>

<p>Run the following <code>mongo</code> command from the bash prompt of any of the Ubuntu servers hosting a MongoDB instance in your replica set. This command&rsquo;s <code>--eval</code> option instructs the <code>mongo</code> operation to not open up the shell interface environment that appears when you run <code>mongo</code> by itself and instead run the command or method, wrapped in single quotes, that follows the <code>--eval</code> argument:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo --eval 'rs.status()'
</li></ul></code></pre>
<p><code>rs.status()</code>  returns a lot of information, but the relevant portion of the output is the <code>"members" :</code> array. In the context of MongoDB, an array is a collection of documents held between a pair of square brackets (<code>[</code> and <code>]</code>).</p>

<p>In the <code>"members":</code> array you&rsquo;ll find a number of documents, each of which contains information about one of the members in your replica set. Within each of these member documents, find the <code>"stateStr"</code> field. The member whose <code>"stateStr"</code> value is <code>"PRIMARY"</code> is the primary member of your replica set. The following example shows a situation where <strong>mongo0</strong> is the primary:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
    "members" : [
        {
            "_id" : 0,
            "name" : "<span class="highlight">mongo0.replset.member</span>:27017",
            "health" : 1,
            "state" : 1,
            "stateStr" : "<span class="highlight">PRIMARY</span>",
. . .
        },
. . .
</code></pre>
<p>Once you know which of your replica set members is the primary, SSH into the server hosting that instance. For demonstration purposes, this guide will continue to use examples in which <strong>mongo0</strong> is the primary:</p>
<pre class="code-pre command prefixed local-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ssh <span class="highlight">sammy</span>@<span class="highlight">mongo0_ip_address</span>
</li></ul></code></pre>
<p>After logging into the server, connect to MongoDB by opening up the <code>mongo</code> shell environment:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">mongo
</li></ul></code></pre>
<p>When creating a user in MongoDB, you must create them within a specific database which will be used as their <em>authentication database</em>. The combination of the user&rsquo;s name and their authentication database serve as a unique identifier for that user.</p>

<p>Certain administrative actions are only available to users whose authentication database is the <code>admin</code> database — a special privileged database included in every MongoDB installation — including the ability to create new users. Because the goal of this step is to create an user administrator that can create other users in the replica set, connect to the <code>admin</code> database so you can grant this user the appropriate privileges:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">use admin
</li></ul></code></pre><pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>switched to db admin
</code></pre>
<p>MongoDB comes installed with <a href="https://docs.mongodb.com/manual/reference/method/">a number of JavaScript-based shell methods</a> you can use to manage your database. One of these, the <code>db.createUser</code> method, is used to create new users in the database in which the method is run.</p>

<p>Initiate the <code>db.createUser</code> method:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">db.createUser(
</li></ul></code></pre>
<p><span class='note'><strong>Note</strong>: Mongo won&rsquo;t register the <code>db.createUser</code> method as complete until you enter a closing parenthesis. Until you do, the prompt will change from a greater than sign (<code>&gt;</code>) to an ellipsis (<code>...</code>).<br></span></p>

<p>This method requires you to specify a username and password for the user, as well as any roles you want the user to have. Recall that MongoDB stores its data in JSON-like documents; when you create a new user, all you&rsquo;re doing is creating a document to hold the appropriate user data as individual fields.</p>

<p>As with objects in JSON, documents in MongoDB begin and end with curly braces (<code>{</code> and <code>}</code>). Enter an opening curly brace to begin the user document:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">{
</li></ul></code></pre>
<p>Next, enter a <code>user:</code> field, with your desired username as the value in double quotes followed by a comma. The following example specifies the username <strong>UserAdminSammy</strong>, but you can enter whatever username you like:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">user: "<span class="highlight">UserAdminSammy</span>",
</li></ul></code></pre>
<p>Next, enter a <code>pwd</code> field with the <code>passwordPrompt()</code> method as its value. When you execute the <code>db.createUser</code> method, the <code>passwordPrompt()</code> method will provide a prompt for you to enter your password. This is more secure than the alternative, which is to type out your password in cleartext as you did for your username.</p>

<span class='note'><p>
<strong>Note</strong>: The <code>passwordPrompt()</code> method is only compatible with MongoDB versions <span class="highlight">4.2</span> and newer. If you&rsquo;re using an older version of Mongo, then you will have to write out your password in cleartext, similarly to how you wrote out your username:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">pwd: "<span class="highlight">password</span>",
</li></ul></code></pre>
<p></p></span>

<p>Be sure to follow this field with a comma as well:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">pwd: passwordPrompt(),
</li></ul></code></pre>
<p>Then enter a <code>roles</code> field followed by an array detailing the roles you want your administrative user to have. In MongoDB, <em>roles</em> define what actions the user can perform on the resources that they have access to. You can define custom roles yourself, but Mongo also comes with a number of built-in roles that grant commonly-needed permissions.</p>

<p>Because you&rsquo;re creating a user administrator, at a minimum you should grant them the built-in <code>userAdminAnyDatabase</code> role over the <code>admin</code> database. This will allow the user administrator to create and modify new users and roles. Because the administrative user has this role in the <code>admin</code> database, this will also grant it <a href="https://docs.mongodb.com/manual/reference/built-in-roles/#superuser">superuser access to the entire cluster</a>:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
</li></ul></code></pre>
<p>Following that, enter a closing brace to signify the end of the document:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">}
</li></ul></code></pre>
<p>Then enter a closing parenthesis to close and execute the <code>db.createUser</code> method:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">)
</li></ul></code></pre>
<p>All together, here&rsquo;s what your <code>db.createUser</code> method should look like:</p>
<pre class="code-pre  second-environment"><code>&gt; db.createUser(
... {
... user: "<span class="highlight">UserAdminSammy</span>",
... pwd: passwordPrompt(),
... roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
... }
... )
</code></pre>
<p>If each line&rsquo;s syntax is correct, the method will execute properly and you&rsquo;ll be prompted to enter a password:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>Enter password:
</code></pre>
<p>Enter a strong password of your choosing. Then, you&rsquo;ll receive a confirmation that the user was added:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>Successfully added user: {
    "user" : "<span class="highlight">UserAdminSammy</span>",
    "roles" : [
        {
            "role" : "userAdminAnyDatabase",
            "db" : "admin"
        },
        "readWriteAnyDatabase"
    ]
}
</code></pre>
<p>With that, you&rsquo;ve added a MongoDB user profile which you can use to manage other users and roles on your system. You can test this out by creating another user, as outlined in the remainder of this step.</p>

<p>Begin by authenticating as the user administrator you just created:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">db.auth( "UserAdminSammy", passwordPrompt() )
</li></ul></code></pre>
<p><code>db.auth()</code> will return <code>1</code> if authentication was successful:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>1
</code></pre>
<span class='note'><p>
<strong>Note</strong>: In the future, if you want to authenticate as the user administrator when connecting to the cluster, you can do so directly from your server prompt with a command like the following:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo -u "UserAdminSammy" -p --authenticationDatabase "admin"
</li></ul></code></pre>
<p>In this command, the <code>-u</code> option tells the shell that the following argument is the username which you want to authenticate as. The <code>-p</code> flag tells it to prompt you to enter a password, and the <code>--authenticationDatabase</code> option precedes the name of the user&rsquo;s authentication database. If you enter an incorrect password or the username and authentication database do not match, you won&rsquo;t be able to authenticate and you&rsquo;ll have to try connecting again.</p>

<p>Also, be aware that in order for you to create new users in the replica set as the user administrator, you must be connected to the set&rsquo;s primary member.<br></p></span>

<p>The procedure for adding another user is the same as it was for the user administrator. The following example creates a new user with the <code>clusterAdmin</code> role, which means they will be able to perform a number of operations related to replication and sharding. Within the context of MongoDB, a user with these privileges is known as a <em>cluster administrator</em>.</p>

<p>Having a dedicated user to perform specific functions like this is a good security practice, as it limits the number of privileged users you have on your system. After you enable keyfile authentication later in this tutorial, any client that wants to perform any of the operations allowed by the <code>clusterAdmin</code> role — such as any of the <code>rs.</code> methods, like <code>rs.status()</code> or <code>rs.conf()</code> — must first authenticate as the cluster administrator.</p>

<p>That said, you can provide whatever role you&rsquo;d like to this user, and likewise provide them with a different name and authentication database. However, if you want the new user to function as a cluster administrator, then you <strong>must</strong> grant them the <code>clusterAdmin</code> role within the <code>admin</code> database.</p>

<p>In addition to creating a user to serve as the cluster administrator, the following method names the user <strong>ClusterAdminSammy</strong> and uses the <code>passwordPrompt()</code> method to prompt you to enter a password:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">db.createUser(
</li><li class="line" data-prefix="&gt;">{
</li><li class="line" data-prefix="&gt;">user: "<span class="highlight">ClusterAdminSammy</span>",
</li><li class="line" data-prefix="&gt;">pwd: passwordPrompt(),
</li><li class="line" data-prefix="&gt;">roles: [ { role: "clusterAdmin", db: "admin" } ]
</li><li class="line" data-prefix="&gt;">}
</li><li class="line" data-prefix="&gt;">)
</li></ul></code></pre>
<p>Again, if you&rsquo;re using a version of MongoDB that precedes version <span class="highlight">4.2</span>, then you will have to write out your password in cleartext instead of using the <code>passwordPrompt()</code> method.</p>

<p>If each line&rsquo;s syntax is correct, the method will execute properly and you&rsquo;ll be prompted to enter a password:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>Enter password:
</code></pre>
<p>Enter a strong password of your choosing. Then, you&rsquo;ll receive a confirmation that the user was added:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>Successfully added user: {
    "user" : "<span class="highlight">ClusterAdminSammy</span>",
    "roles" : [
        {
            "role" : "clusterAdmin",
            "db" : "admin"
        }
    ]
}
</code></pre>
<p>This output confirms that your user administrator is able to create new users and grant them roles. You can now close the MongoDB shell:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">exit
</li></ul></code></pre>
<p>Alternatively, you can close the shell by pressing <code>CTRL + C</code>.</p>

<p>At this point, if you have any clients or applications connected to your MongoDB cluster, it would be a good time to create one or more dedicated users with the appropriate roles which they can use to authenticate to the database. Otherwise, read on to learn how to generate a keyfile, distribute it among the members of your replica set, and then configure each one to require the replica set members to authenticate with the keyfile.</p>

<h2 id="step-2-—-creating-and-distributing-an-authentication-keyfile">Step 2 — Creating and Distributing an Authentication Keyfile</h2>

<p>Before creating a keyfile, it can be helpful to create a directory on each server where you will store the keyfile in order to keep things organized. Run the following command, which creates a directory named <code>mongo-security</code> in the administrative Ubuntu user’s home directory, <strong>on each of your three servers</strong>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir ~/mongo-security
</li></ul></code></pre>
<p>Then generate a keyfile on <strong>one of your servers</strong>. You can do this on any one of your servers but, for illustration purposes, this guide will generate the keyfile on <strong>mongo0</strong>.</p>

<p>Navigate to the <code>mongo-security</code> directory you just created:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">cd ~/mongo-security/
</li></ul></code></pre>
<p>Within that directory, create a keyfile with the following <code>openssl</code> command:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">openssl rand -base64 768 &gt; keyfile.txt
</li></ul></code></pre>
<p>Take note of this command&rsquo;s arguments:</p>

<ul>
<li><code>rand</code>: instructs OpenSSL to generate pseudo-random bytes of data</li>
<li><code>-base64</code>: specifies that the command should use base64 encoding to represent the pseudo-random data as printable text. This is important because, as mentioned previously, MongoDB keyfiles can only contain characters in the base64 set</li>
<li><code>768</code>: the number of bytes the command should generate. In base64 encoding, three binary bytes of data are represented as four characters. Because MongoDB keyfiles can have a maximum of 1024 characters, 768 is the maximum number of bytes you can generate for a valid keyfile</li>
</ul>

<p>Following this command&rsquo;s <code>768</code> argument is a greater-than sign (<code>&gt;</code>). This redirects the command&rsquo;s output into a new file named <code>keyfile.txt</code> which will serve as your keyfile. Feel free to name the keyfile something other than <code>keyfile.txt</code> if you&rsquo;d like, but be sure to change the filename whenever it appears in later commands.</p>

<p>Next, modify the keyfile&rsquo;s permissions so that only the owner has read access:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">chmod 400 keyfile.txt
</li></ul></code></pre>
<p>Following this, distribute the keyfile to the other two servers hosting the MongoDB instances in your replica set. Assuming you followed the prerequisite guide on <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04">How To Set Up SSH Keys</a>, you can do so with the <code>scp</code> command:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">scp keyfile.txt <span class="highlight">sammy</span>@mongo1.replset.member:/home/<span class="highlight">sammy</span>/mongo-security
</li><li class="line" data-prefix="sammy@mongo0:~$">scp keyfile.txt <span class="highlight">sammy</span>@mongo2.replset.member:/home/<span class="highlight">sammy</span>/mongo-security
</li></ul></code></pre>
<p>Notice that each of these commands copies the keyfile directly to the <code>~/mongo-security/</code> directories you created previously on <strong>mongo1</strong> and <strong>mongo2</strong>. Be sure to change <code><span class="highlight">sammy</span></code> to the name of the administrative Ubuntu user profile you created on each server. </p>

<p>Next, change the file&rsquo;s owner to the <strong>mongodb</strong> user profile. This is a special user that was created when you installed MongoDB, and it&rsquo;s used to run the <code>mongod</code> service. This user must have access to the keyfile in order for MongoDB to use it for authentication.</p>

<p>Run the following command on <strong>each of your servers</strong> to change the keyfile&rsquo;s owner to the <strong>mongodb</strong> user account:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo chown mongodb:mongodb ~/mongo-security/keyfile.txt
</li></ul></code></pre>
<p>After changing the keyfiles&rsquo; owner on each server, you&rsquo;re ready to reconfigure each of your MongoDB instances to enforce keyfile authentication.</p>

<h2 id="step-3-—-enabling-keyfile-authentication">Step 3 — Enabling Keyfile Authentication</h2>

<p>Now that you&rsquo;ve generated a keyfile and distributed it to each of the servers in your replica set, you can update the MongoDB configuration file on each server to enforce keyfile authentication.</p>

<p>In order to avoid any downtime while configuring the members of your replica set to require authentication, this step involves reconfiguring the secondary members of the set first. Then, you&rsquo;ll direct your primary member to step down and become a secondary member. This will cause the secondary members to hold an election to select a new primary, keeping your cluster available to whatever clients or applications need access to it. You&rsquo;ll then reconfigure the former primary node to enable authentication. </p>

<p><strong>On each of your servers hosting a secondary member of your replica set</strong>, open up MongoDB&rsquo;s configuration file with your preferred text editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongod.conf
</li></ul></code></pre>
<p>Within the file, find the <code>security</code> section. It will look like this by default:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
#security:
. . .
</code></pre>
<p>Uncomment this line by removing the pound sign (<code>#</code>). Then, on the next line, add a <code>keyFile:</code> directive followed by the full path to the keyfile you created in the previous step:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
security:
  <span class="highlight">keyFile: /home/sammy/mongo-security/keyfile.txt</span>
. . .
</code></pre>
<p>Note that there are two spaces at the beginning of this new line. These are necessary for the configuration file to be read correctly. When you enter this line in your own configuration files, make sure that the path you provide reflects the actual path of the keyfile on each server.</p>

<p>Below the <code>keyFile</code> directive, add a <code>transitionToAuth</code> directive with a value of <code>true</code>. When set to <code>true</code>, this configuration option allows the MongoDB instance to accept both authenticated and non-authenticated connections. This is useful when reconfiguring a replica set to enforce authentication, as it will ensure that your data remains available as you restart each member of the set:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
security:
  keyFile: /home/sammy/mongo-security/keyfile.txt
  <span class="highlight">transitionToAuth: true</span>
. . .
</code></pre>
<p>Again, make sure that you include two blank spaces before the <code>transitionToAuth</code> directive.</p>

<p>After making those changes, save and close the file. If you used <code>nano</code> to edit it, you can do so by pressing <code>CTRL + X</code>, <code>Y</code>, and then <code>ENTER</code>. </p>

<p>Then restart the <code>mongod</code> service on <strong>both of the secondary instances&rsquo; servers</strong> to immediately put these changes into effect:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongod
</li></ul></code></pre>
<p>With that, you&rsquo;ve configured keyfile authentication for the secondary members of your replica set. At this point, both authenticated and non-authenticated users can access these members without restriction.</p>

<p>Next, you&rsquo;ll repeat this procedure on the primary member. Before doing so, though, you must step down the member so it&rsquo;s no longer the primary. To do this, open up the MongoDB shell on the server hosting the primary member. For illustration purposes, this guide will again assume this is <strong>mongo0</strong>:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">mongo
</li></ul></code></pre>
<p>From the prompt, run the <code>rs.stepDown()</code> method. This will instruct the primary to become a secondary member, and will cause the current secondary members to hold an election to determine which will serve as the new primary:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">rs.stepDown()
</li></ul></code></pre>
<p>If the method returns <code>"ok" : 1</code> in the output, it means the primary member successfully stepped down to become a secondary:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>{
    <span class="highlight">"ok" : 1,</span>
    "$clusterTime" : {
        "clusterTime" : Timestamp(1614795467, 1),
        "signature" : {
            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
            "keyId" : NumberLong(0)
        }
    },
    "operationTime" : Timestamp(1614795467, 1)
}
</code></pre>
<p>After stepping down the primary, you can close the Mongo shell:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:SECONDARY&gt;">exit
</li></ul></code></pre>
<p>Next, open up the MongoDB configuration file on this server:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">sudo nano /etc/mongod.conf
</li></ul></code></pre>
<p>Find the security section and uncomment the <code>security</code> header by removing the pound sign. Then add the same <code>keyFile</code> and <code>transitionToAuth</code> directives you added to the other MongoDB instances. After making these changes, the <code>security</code> section will look like this:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
security:
  keyFile: /home/<span class="highlight">sammy</span>/mongo-security/keyfile.txt
  transitionToAuth: true
. . .
</code></pre>
<p>Again, make sure that the file path after the <code>keyFile</code> directive reflects the keyfile&rsquo;s actual location on this server.</p>

<p>When finished, save and close the file. Then restart the <code>mongod</code> process:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">sudo systemctl restart mongod
</li></ul></code></pre>
<p>Following that, all of your MongoDB instances are able to accept both authenticated and non-authenticated connections. In the final step of this guide, you&rsquo;ll configure your instances to require users to authenticate before performing privileged actions.</p>

<h2 id="step-4-—-restarting-each-member-without-transitiontoauth-to-enforce-authentication">Step 4 — Restarting Each Member Without <code>transitionToAuth</code> to Enforce Authentication</h2>

<p>At this point, each of your MongoDB instances are configured with the <code>transitionToAuth</code> set to <code>true</code>. This means that even though you&rsquo;ve enabled each server to use the keyfile you created to authenticate connections internally, they&rsquo;re still able to accept non-authenticated connections.</p>

<p>To change this and require each member to enforce authentication, reopen the <code>mongod.conf</code> file <strong>on each server</strong>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongod.conf
</li></ul></code></pre>
<p>Find the <code>security</code> section and disable the <code>transitionToAuth</code> directive. You can do this by commenting the line out by prepending it with a pound sign:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
security:
  keyFile: /home/sammy/mongo-security/keyfile.txt
  <span class="highlight">#</span>transitionToAuth: true
. . .
</code></pre>
<p>After disabling the <code>transitionToAuth</code> directive in each instance&rsquo;s configuration file, save and close each file.</p>

<p>Then, restart the <code>mongod</code> service <strong>on each server</strong>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongod
</li></ul></code></pre>
<p>Following that, each of the MongoDB instances in your replica set will require you to authenticate to perform privileged actions.</p>

<p>To test this, try running a MongoDB method that works when invoked by an authenticated user that has the appropriate privileges. Try running the following command from any of your Ubuntu servers&rsquo; prompts:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo --eval 'rs.status()'
</li></ul></code></pre>
<p>Even though you ran this method successfully in Step 1, the <code>rs.status()</code> method can now only be run by a user that has been granted the <code>clusterAdmin</code> or <code>clusterManager</code> roles since you&rsquo;ve enabled keyfile authentication. Regardless of whether you run this command on a server hosting the primary member or one of the secondary members, it will not work because you have not authenticated:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
MongoDB server version: 4.4.4
{
    "operationTime" : Timestamp(1616184183, 1),
    "ok" : 0,
    "errmsg" : "command replSetGetStatus requires authentication",
    "code" : 13,
    "codeName" : "Unauthorized",
    "$clusterTime" : {
        "clusterTime" : Timestamp(1616184183, 1),
        "signature" : {
            "hash" : BinData(0,"huJUmB/lrrxpx9YfnONM4mayJwo="),
            "keyId" : NumberLong("6941116945081040899")
        }
    }
}
</code></pre>
<p>Recall that, after enabling access control, all of the cluster administration methods (including <code>rs.</code> methods like <code>rs.status()</code>) will only work when invoked by an authenticated user that has been granted the appropriate cluster management roles. If you&rsquo;ve created a cluster administrator — as outlined in Step 1 — and authenticate as that user, then this method will work as expected:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo -u "ClusterAdminSammy" -p --authenticationDatabase "admin" --eval 'rs.status()'
</li></ul></code></pre>
<p>After entering the user&rsquo;s password when prompted, you will see the <code>rs.status()</code> method&rsquo;s output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
MongoDB server version: 4.4.4
{
    "set" : "shard2",
    "date" : ISODate("2021-03-19T20:21:45.528Z"),
    "myState" : 2,
    "term" : NumberLong(4),
    "syncSourceHost" : "mongo1.replset.member:27017",
    "syncSourceId" : 2,
    "heartbeatIntervalMillis" : NumberLong(2000),
    "majorityVoteCount" : 2,
. . .
</code></pre>
<p>This confirms that the replica set is enforcing authentication, and that you&rsquo;re able to authenticate successfully.</p>

<h2 id="conclusion">Conclusion</h2>

<p>By completing this tutorial, you created a keyfile with OpenSSL and then configured a MongoDB replica set to require its members to use it for internal authentication. You also created a user administrator which will allow you to manage users and roles in the future. Throughout all of this, your replica set will not have gone through any downtime and your data will have remained available to your clients and applications.</p>

<p>If you&rsquo;d like to learn more about MongoDB, we encourage you to check out our <a href="https://www.digitalocean.com/community/tags/mongodb">entire library of MongoDB content</a>.</p>
