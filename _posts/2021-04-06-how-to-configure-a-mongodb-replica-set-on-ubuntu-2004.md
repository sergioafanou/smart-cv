---
title : "How To Configure a MongoDB Replica Set on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-a-mongodb-replica-set-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-40.jpg"
---

<p><em>An earlier version of this tutorial was written by <a href="https://www.digitalocean.com/community/users/jellingwood">Justin Ellingwood</a>.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a>, also known as <em>Mongo</em>, is an open-source document database used in many modern web applications. It is classified as a <a href="https://www.digitalocean.com/community/tutorials/what-is-nosql">NoSQL database</a> because it does not rely on a traditional table-based relational database structure. Instead, it uses JSON-like documents with dynamic schemas. This means that, unlike <a href="https://www.digitalocean.com/community/tutorials/what-is-the-relational-model">relational databases</a>, MongoDB does not require a predefined schema before you add data to a database.</p>

<p>When working with databases, it’s often useful to have multiple copies of your data. This provides redundancy in case one of the database servers fails and can improve a database&rsquo;s availability and scalability, as well as reduce read latencies. The practice of synchronizing data across multiple separate databases is called <em>replication</em>. In MongoDB, a group of servers that maintain the same data set through replication are referred to as a <em>replica set</em>.</p>

<p>This tutorial provides a brief overview of how replication works in MongoDB and outlines how to configure and initiate a replica set with three members. In this example configuration, each member of the replica set will be a distinct MongoDB instance running on separate Ubuntu 20.04 servers.</p>

<span class='note'><p>
<strong>Note</strong>: Be aware that the procedure outlined in this guide is intended to demonstrate how to get a replica set up and running quickly. Upon completing this tutorial you&rsquo;ll have a functioning replica set, but it will not have any security features enabled. This setup is <strong>not recommended for production environments</strong>.</p>

<p>The Community version of MongoDB comes with two authentication methods that can help keep your database secure, <em>keyfile authentication</em> and <em>x.509 authentication</em>. For production deployments that employ replication, <a href="https://docs.mongodb.com/manual/tutorial/deploy-sharded-cluster-with-keyfile-access-control/index.html#keyfile-security">the MongoDB documentation recommends</a> using x.509 authentication, and it describes keyfiles as &ldquo;bare-minimum forms of security&rdquo; that are &ldquo;best suited for testing or development environments.&rdquo; However, the process of obtaining and configuring x.509 certificates comes with a number of caveats and decisions that must be made on a case-by-case basis, which is beyond the scope of a DigitalOcean tutorial. </p>

<p>If you plan on using your replica set for testing or development, we <strong>strongly encourage</strong> you to follow our tutorial on <a href="https://www.digitalocean.com/community/tutorials/how-to-configure-keyfile-authentication-for-mongodb-replica-sets-on-ubuntu-20-04">How To Configure Keyfile Authentication for MongoDB Replica Sets on Ubuntu 20.04</a>. <br></p></span>

<h2 id="prerequisites">Prerequisites</h2>

<p>To complete this guide, you will need:</p>

<ul>
<li>Three servers, each running Ubuntu 20.04. All three of these servers should have a non-root administrative user and a firewall configured with UFW. To set this up, follow our <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">initial server setup guide for Ubuntu 20.04</a>.</li>
<li>MongoDB installed on each of your Ubuntu servers. To this end, follow our tutorial on <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">How To Install MongoDB on Ubuntu 20.04</a>, making sure to complete each step on all three of your servers.</li>
</ul>

<p>Please note that, for clarity, this guide will refer to the three servers as <strong>mongo0</strong>, <strong>mongo1</strong>, and <strong>mongo2</strong>. Any examples showing commands or file changes performed on <strong>mongo0</strong> will have a blue background, like this:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">
</li></ul></code></pre>
<p>Commands and file changes performed on <strong>mongo1</strong> will have a pink background:</p>
<pre class="code-pre custom_prefix prefixed third-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo1:~$">
</li></ul></code></pre>
<p>Example actions on <strong>mongo2</strong> will have a green background:</p>
<pre class="code-pre custom_prefix prefixed fourth-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo2:~$">
</li></ul></code></pre>
<p>Lastly, commands that must be run or file changes that must be made on <strong>every</strong> server will have a standard gray background, like this:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">
</li></ul></code></pre>
<h2 id="understanding-mongodb-replica-sets">Understanding MongoDB Replica Sets</h2>

<p>As mentioned in the introduction, MongoDB handles replication through an implementation called <em>replica sets</em>. Each running instance of MongoDB that&rsquo;s part of a given replica set is referred to as one of its <em>members</em>. Every replica set must have one <em>primary</em> member and at least one <em>secondary</em> member.</p>

<p>The primary member is the main access point for transactions with the replica set and is the only member that can accept write operations. Each replica set can have only one primary member at a time, as replication happens by copying the primary&rsquo;s <em>oplog</em> (short for &ldquo;operations log&rdquo;) and repeating the logged changes on the secondaries&rsquo; respective data sets. Multiple primaries accepting write operations would lead to data conflicts.</p>

<p>By default, applications will only query the primary member for both read and write operations. You can configure your setup to read from one or more of the secondary members, but since data is transferred asynchronously, reads from secondary nodes can result in old data being served. Thus, such a configuration isn&rsquo;t ideal for every use case.</p>

<p>One feature that distinguishes MongoDB&rsquo;s replica sets from other replication implementations is their automatic failover mechanism. In the event that the primary member becomes unavailable, an automated election process happens among the secondary nodes to choose a new primary. A replica set can have up to 50 members, but a maximum of 7 can vote in an election.</p>

<p>If the secondary member pool contains an even number of nodes, however, it could result in an inability to elect a new primary due to a voting impasse. This would necessitate the inclusion of a third type of member in the replica set: an <em>arbiter</em>. An arbiter is an optional member of a replica set that votes in situations like this to ensure that the set is able to reach a decision. Be aware, though, that arbiters do not have a copy of the data set and they&rsquo;re barred from becoming the replica set&rsquo;s primary. If a replica set has only one secondary member, then an arbiter is required.</p>

<p>There may be times when you don&rsquo;t want all of your secondaries to follow the standard rules for secondary members of a replica set. MongoDB allows you to configure secondary members of a replica set to take on the following nonstandard roles:</p>

<ul>
<li><strong>Priority 0 Replication Members</strong>: There are some situations where the election of certain set members to the primary position could have a negative impact on your application&rsquo;s performance. For instance, if you are replicating data to a remote datacenter or a certain secondary member&rsquo;s hardware is inadequate for it to function as the main access point for the set, setting its priority to <code>0</code> can ensure that this member will not become a primary but can continue copying data.</li>
<li><strong>Hidden Replication Members</strong>: Some situations require you to keep one set of members accessible and visible to your clients while hiding background members which have separate purposes and shouldn&rsquo;t be used for read operations. As an example, you may need a secondary member to be the base for analytics work, which would benefit from an up-to-date dataset but would cause a strain on working members. By setting this member to hidden, it will not interfere with the general operations of the replica set. Hidden members must be set to a priority of <code>0</code> to avoid becoming the primary member, but they can vote in elections.</li>
<li><strong>Delayed Replication Members</strong>: By setting the delay option for a secondary member, you can control how long the secondary waits to perform each action it copies from the primary&rsquo;s oplog. This is useful if you would like to safeguard against accidental deletions or recover from destructive operations. For instance, if you delay a secondary by a half-day, it would not immediately perform accidental operations on its own set of data and could be used to revert changes. Delayed members cannot become primary members, but can vote in elections. In most situations, they should also be hidden to prevent application processes from reading data that is out-of-date.</li>
</ul>

<h2 id="step-1-—-configuring-dns-resolution">Step 1 — Configuring DNS Resolution</h2>

<p>When it comes time to initialize your replica set in Step 4, you&rsquo;ll need to provide an address where each replica set member can be reached by the other two in the set. The MongoDB documentation recommends against using IP addresses when configuring a replica set, since IP addresses can change unexpectedly. Instead, MongoDB <a href="https://docs.mongodb.com/manual/tutorial/deploy-geographically-distributed-replica-set/#hostnames">recommends</a> using logical DNS hostnames when configuring replica sets.</p>

<p>One way to do this is to <a href="https://www.digitalocean.com/docs/networking/dns/how-to/add-subdomain/">configure subdomains for each replication member</a>. Although configuring subdomains would be ideal for a production environment or another long-term solution, this tutorial will outline how to configure DNS resolution by editing each server&rsquo;s respective <code>hosts</code> files.</p>

<p><code>hosts</code> is a special file that allows you to assign human-readable hostnames to numerical IP addresses. This means that if the IP address of any of your servers ever changes, you&rsquo;ll only have to update the <code>hosts</code> file on the three servers instead of reconfiguring the replica set.</p>

<p>On Linux and other Unix-like systems, <code>hosts</code> is stored in the <code>/etc/</code> directory. On <strong>each of your three servers</strong>, edit the file with your preferred text editor. Here, we&rsquo;ll use <code>nano</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/hosts
</li></ul></code></pre>
<p>After the first few lines which configure the <strong>localhost</strong>, add an entry for each member of the replica set. These entries take the form of an IP address followed by the human-readable name of your choice, as in this example:</p>
<div class="code-label " title="/etc/hosts">/etc/hosts</div><pre class="code-pre "><code class="code-highlight language-bash"><span class="highlight">IP_address</span>   <span class="highlight">any_hostname</span>
</code></pre>
<p>You can configure your servers to use whatever hostname you&rsquo;d like, but it can be helpful to make each hostname descriptive. In examples throughout this guide, the three servers will use these hostnames:</p>

<ul>
<li><strong>mongo0.replset.member</strong></li>
<li><strong>mongo1.replset.member</strong></li>
<li><strong>mongo2.replset.member</strong></li>
</ul>

<p>Using these hostnames, your <code>/etc/hosts</code> files would look similar to the following highlighted lines:</p>
<div class="code-label " title="/etc/hosts">/etc/hosts</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
127.0.0.1 localhost

<span class="highlight">203.0.113.0 mongo0.replset.member</span>
<span class="highlight">203.0.113.1 mongo1.replset.member</span>
<span class="highlight">203.0.113.2 mongo2.replset.member</span>
. . .
</code></pre>
<span class='info'><p>
If you don&rsquo;t know your servers&rsquo; IP addresses offhand, you can run the following <code>curl</code> command on each server to retrieve them. <code>icanhazip.com</code> is a website that shows the IP address of whatever computer is used to access it. By providing its URL as an argument to the <code>curl</code> command, the command will print the IP address of the server from which you run it to standard output:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">curl -4 icanhazip.com
</li></ul></code></pre>
<p>If you&rsquo;re using DigitalOcean Droplets, you can also find your servers&rsquo; IP addresses in the <a href="https://cloud.digitalocean.com/">Control Panel</a>.<br></p></span>

<p>The new lines you add here should be identical on each of the three hosts in your set. Save and close the file on each of your servers. If you used <code>nano</code> to edit these files, do so by pressing <code>CTRL + X</code>, <code>Y</code>, and then <code>ENTER</code>.</p>

<p>After editing, saving, and closing the <code>hosts</code> file on each of your servers, you&rsquo;ll have finished configuring DNS resolution for your replica set. You can now move on to updating each server&rsquo;s firewall rules to allow them to communicate with one another.</p>

<h2 id="step-2-—-updating-each-server-39-s-firewall-configurations-with-ufw">Step 2 — Updating Each Server&rsquo;s Firewall Configurations with UFW</h2>

<p>Assuming you followed the prerequisite <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">initial server setup guide</a> you will have set up a firewall on each of the servers on which you&rsquo;ve installed MongoDB and enabled access for the <code>OpenSSH</code> UFW profile. This is an important security measure, as these firewalls currently block connections to any port on your servers, save for <code>ssh</code> connections that present keys which align with those in each server&rsquo;s respective <code>authorized_keys</code> file. </p>

<p>However, these firewalls will also block the MongoDB instances on each server from communicating with one another, preventing you from initiating the replica set. To correct this, you&rsquo;ll need to add new firewall rules to allow each server access to the port on the other two servers on which MongoDB is listening for connections.</p>

<p>On <strong>mongo0</strong>, run the following <code>ufw</code> command to allow <strong>mongo1</strong> access to port <code>27017</code> on <strong>mongo0</strong>:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">sudo ufw allow from <span class="highlight">mongo1_server_ip</span> to any port 27017
</li></ul></code></pre>
<p>Be sure to change <code><span class="highlight">mogno1_server_ip</span></code> to reflect your <strong>mongo1</strong> server&rsquo;s actual IP address. Note that <code>ufw</code> commands will not work with hostnames configured in the <code>hosts</code> file, so be sure to use your servers&rsquo; actual IP addresses in this command and the following ones. Also, if you&rsquo;ve updated the Mongo instance on this server to use a non-default port, be sure to change <code>27017</code> to reflect the port that your MongoDB instance is actually using.</p>

<p>Then add another firewall rule to give <strong>mongo2</strong> access to the same port:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">sudo ufw allow from <span class="highlight">mongo2_server_ip</span> to any port 27017
</li></ul></code></pre>
<p>Next, update the firewall rules for your other two servers. Run the following commands on <strong>mongo1</strong>, making sure to change the IP addresses to reflect those of <strong>mongo0</strong> and <strong>mongo2</strong>, respectively:</p>
<pre class="code-pre custom_prefix prefixed third-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo1:~$">sudo ufw allow from <span class="highlight">mongo0_server_ip</span> to any port 27017
</li><li class="line" data-prefix="sammy@mongo1:~$">sudo ufw allow from <span class="highlight">mongo2_server_ip</span> to any port 27017
</li></ul></code></pre>
<p>Lastly, run these two commands on <strong>mongo2</strong>. Again, be sure that you enter the correct IP addresses for each server:</p>
<pre class="code-pre custom_prefix prefixed fourth-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo2:~$">sudo ufw allow from <span class="highlight">mongo0_server_ip</span> to any port 27017
</li><li class="line" data-prefix="sammy@mongo2:~$">sudo ufw allow from <span class="highlight">mongo1_server_ip</span> to any port 27017
</li></ul></code></pre>
<p>After adding these UFW rules, each of your three MongoDB servers will be allowed to access the port used by MongoDB on the other two servers. However, you won&rsquo;t be able to test this yet, since the Mongo instance on each server is currently blocking any external connections. After you enable replication by updating each MongoDB instance&rsquo;s configuration file in the next step, you will be able to perform this test.</p>

<h2 id="step-3-—-enabling-replication-in-each-server-39-s-mongodb-configuration-file">Step 3 — Enabling Replication in Each Server&rsquo;s MongoDB Configuration File</h2>

<p>At this point, you&rsquo;ve edited your servers&rsquo; <code>/etc/hosts</code> files to configure hostnames which will resolve to each one&rsquo;s IP address. You&rsquo;ve also opened up each of your servers&rsquo; firewalls to allow the other two servers access to the default MongoDB port, <code>27107</code>. Now you&rsquo;re ready to begin configuring the MongoDB installation on each server to enable replication. </p>

<p>This step outlines how to do this by editing MongoDB&rsquo;s configuration file, <code>/etc/mongod.conf</code>. You must complete every procedure in this step on each server, but for demonstration purposes we will use <strong>mongo0</strong> in examples.</p>

<p>On <strong>mongo0</strong>, open the MongoDB configuration file in your preferred text editor:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">sudo nano /etc/mongod.conf
</li></ul></code></pre>
<p>Even though you&rsquo;ve opened up each server&rsquo;s firewall to allow the other servers access to port <code>27017</code>, MongoDB is currently bound to <code>127.0.0.1</code>, the local loopback network interface. This means that MongoDB is only able to accept connections that originate on the server where it’s installed.</p>

<p>To allow remote connections, you must bind MongoDB to your servers&rsquo; publicly-routable IP addresses in addition to <code>127.0.0.1</code>. This way, your MongoDB installation will be able to listen to connections made to your MongoDB server from remote machines.</p>

<p>Find the <code>network interfaces</code> section. It will look like this by default:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1
. . .
</code></pre>
<p>Append a comma to the line beginning with <code>bindIp:</code> followed by <strong>mongo0</strong>&rsquo;s hostname or public IP address. This example uses the hostname configured in Step 1:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1<span class="highlight">,mongo0.replset.member</span>
. . .
</code></pre>
<p>Next, find the line that reads <code>#replication:</code> towards the bottom of the file. It will look like this:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
#replication:
. . . 
</code></pre>
<p>Uncomment this line by removing the pound sign (<code>#</code>). Then add a <code>replSetName</code> directive below this line followed by a name which MongoDB will use to identify the replica set:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
replication:
  <span class="highlight">replSetName: "rs0"</span>
. . . 
</code></pre>
<p>In this example, the <code>replSetName</code> directive&rsquo;s value is <code>"rs0"</code>. You can provide whatever name you&rsquo;d like here, but it can be helpful to use a descriptive name. Keep in mind, though, that each server&rsquo;s <code>mongod.conf</code> file must have the same name after the <code>replSetName</code> directive in order for each of their MongoDB instances to become members of the same replica set.</p>

<p>Note that there are two spaces before the <code>replSetName</code> directive and that the name is wrapped in quotation marks (<code>"</code>), both of which are necessary for this configuration to be read properly.</p>

<p>After updating these two sections of the file, <code>net</code> and <code>replication</code>, they will look like this:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  second-environment"><code class="code-highlight language-bash">. . .
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,<span class="highlight">mongo0.replset.member</span>
. . .
replication:
  replSetName: "rs0"
. . . 
</code></pre>
<p>Save and close the file. Then make these same changes to the <code>/etc/mongod.conf</code> files on <strong>mongo1</strong> and <strong>mongo2</strong>. After doing so, these updated sections will look like this in <strong>mongo1</strong>&rsquo;s configuration file:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  third-environment"><code class="code-highlight language-bash">. . .
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,<span class="highlight">mongo1.replset.member</span>
. . .
replication:
  replSetName: "rs0"
. . . 
</code></pre>
<p>And here&rsquo;s how these sections will look in <strong>mongo2</strong>&rsquo;s configuration file:</p>
<div class="code-label " title="/etc/mongod.conf">/etc/mongod.conf</div><pre class="code-pre  fourth-environment"><code class="code-highlight language-bash">. . .
# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1,<span class="highlight">mongo2.replset.member</span>
. . .
replication:
  replSetName: "rs0"
. . . 
</code></pre>
<p>To reiterate, the IP address or hostname you add to each server&rsquo;s <code>bindIp</code> directive must be that of the server whose <code>mongod.conf</code> file you&rsquo;re editing.</p>

<p>After making these changes to each server&rsquo;s <code>mongod.conf</code> file, save and close each file. Then, restart the <code>mongod</code> service <strong>on each server</strong> by issuing the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongod
</li></ul></code></pre>
<p>With that, you&rsquo;ve enabled replication for each server&rsquo;s MongoDB instance. </p>

<span class='note'><p>
<strong>Note</strong>: At this point, you can use the <code>nc</code> command to test whether the firewall rules you added in Step 2 are correct. <code>nc</code>, short for <em>netcat</em>, is a utility used to establish network connections with TCP or UDP. It’s useful for testing in cases like this because it allows you to specify both an IP address and a port number when making a connection.</p>

<p>The following example <code>nc</code> command includes the <code>-z</code> option, which limits the utility to only scan for a listening daemon on the target server without sending it any data. Recall from the <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">prerequisite installation tutorial</a> that MongoDB is running as a service daemon, making this option useful for testing connectivity. It also includes the <code>v</code> option which increases the command’s verbosity, causing it to return more information than it would otherwise.</p>

<p>This example <code>nc</code> command shows an attempt to reach <strong>mongo1</strong> from <strong>mongo0</strong>:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">nc -zv <span class="highlight">mongo1.replset.member</span> 27017
</li></ul></code></pre>
<p>The following output indicates that <strong>mongo0</strong> is able to reach <strong>mongo1</strong> on the port used by MongoDB:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>Connection to <span class="highlight">mongo1.replset.member</span> 27017 port [tcp/*] succeeded!
</code></pre>
<p>You can test the connection between each pair of servers by repeating this command on each server and specifying the appropriate hostnames or IP addresses.<br></p></span>

<p>After editing each server&rsquo;s <code>mongod.conf</code> file to enable replication and restarting the <code>mongod</code> service, you&rsquo;re ready to initiate the replica set and add each Mongo instance as a member.</p>

<h2 id="step-4-—-starting-the-replica-set-and-adding-members">Step 4 — Starting the Replica Set and Adding Members</h2>

<p>Now that you&rsquo;ve configured each of your three MongoDB installations, you can open up a MongoDB shell to initiate replication and add each as a member.</p>

<p>For demonstration purposes, the examples in this step will use the MongoDB instance on <strong>mongo0</strong> to initiate the replica set. However, you can initiate replication from any server whose <code>mongod.conf</code> file has been appropriately configured. </p>

<p>On <strong>mongo0</strong>, open up the MongoDB shell:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="sammy@mongo0:~$">mongo
</li></ul></code></pre>
<p>From the prompt, you can initiate a replica set from the <code>mongo</code> shell by running the <code>rs.initiate()</code> method. However, running this method by itself would only initiate replication for the machine on which you run the method, and you&rsquo;d then need to add your other Mongo instances by issuing an <code>rs.add()</code> method for each member.</p>

<p>Recall that MongoDB stores its data in JSON-like structures known as <em>documents</em>. Because you&rsquo;ve already edited the <code>mongod.conf</code> file on each of your servers to configure the three Mongo instances for replication, you can instead include a document that holds each member&rsquo;s configuration details within the <code>rs.initiate</code> method. This will allow you to start the replica set and add each member at once, rather than having to run multiple separate methods.</p>

<p>To do this, begin an <code>rs.initiate()</code> method by typing the following and pressing <code>ENTER</code>:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="&gt;">rs.initiate(
</li></ul></code></pre>
<p>Mongo won’t register the <code>rs.initiate</code> method as complete until you enter a closing parenthesis. Until you do, the prompt will change from a greater than sign (<code>&gt;</code>) to an ellipsis (<code>...</code>).</p>

<p>As with objects in JSON, documents in MongoDB begin and end with curly braces (<code>{</code> and <code>}</code>). To begin adding the replica set&rsquo;s configuration document, enter an opening curly brace:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">{
</li></ul></code></pre>
<p>MongoDB documents are composed of any number of <em>field-and-value</em> pairs that take the form of <code><span class="highlight">field</span>: <span class="highlight">value</span></code>. The first field-and-value pair of this particular document must be an <code>_id:</code> field that provides a name to identify the replica set; this field&rsquo;s value must be the same as the <code>replSetName</code> directive you set in your <code>mongod.conf</code> files, which is <code>"rs0"</code> in our examples. </p>

<p>Enter this field-and-value pair, following it with a comma, and then press <code>ENTER</code> to begin a new line:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">_id: "rs0",
</li></ul></code></pre>
<p>Next, add a <code>members:</code> field. Instead of a single value, though, follow this <code>members:</code> field with an array containing multiple documents, each of which represent a replica set member to add. In MongoDB documents, arrays are always placed within a pair of square brackets (<code>[</code> and <code>]</code>).</p>

<p>Add the <code>members:</code> field followed by an opening square bracket to begin the array, and then press <code>ENTER</code> to move to the next line:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">members: [
</li></ul></code></pre>
<p>Now add a document with two field-and-value pairs, separated by a comma, to represent the first member of the replica set. The first of this document&rsquo;s fields is another <code>_id:</code> field which accepts an integer used to identify the member internally. The second is a <code>host:</code> field, which must be followed by a string containing a hostname that will resolve to an address where the member Mongo instance can be reached:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">{ _id: 0, host: "<span class="highlight">mongo0.replset.member</span>" },
</li></ul></code></pre>
<span class='note'><p>
<strong>Note</strong>: If any of your Mongo instances are running on a port other than MongoDB&rsquo;s default — <code>27017</code> — you must follow the hostname with a colon (<code>:</code>) and then the port number, as in this example:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">{ _id: 0, host: "<span class="highlight">mongo0.replset.member</span>:27018" },
</li></ul></code></pre>
<p></p></span>

<p>After entering the first one, enter additional documents for the other members of your replica set. Make sure to separate each document with a comma:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">{ _id: 1, host: "<span class="highlight">mongo1.replset.member</span>" },
</li><li class="line" data-prefix="...">{ _id: 2, host: "<span class="highlight">mongo2.replset.member</span>" }
</li></ul></code></pre>
<p>Next, end the array by entering a closing square bracket:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">]
</li></ul></code></pre>
<p>Lastly, end the configuration document with a closing curly brace, and then close the method with a closing parenthesis:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="...">})
</li></ul></code></pre>
<p>All together, the <code>rs.initiate()</code> method will look like this:</p>
<pre class="code-pre  second-environment"><code>&gt; rs.initiate(
... {
... _id: "rs0",
... members: [
... { _id: 0, host: "<span class="highlight">mongo0.replset.member</span>" },
... { _id: 1, host: "<span class="highlight">mongo1.replset.member</span>" },
... { _id: 2, host: "<span class="highlight">mongo2.replset.member</span>" }
... ]
... })
</code></pre>
<p>Assuming that you entered all the details correctly, once you press <code>ENTER</code> after typing the closing parenthesis the method will run and initiate the replica set. If the method returns <code>"ok" : 1</code> in the output, it means that the replica set was started correctly:</p>
<pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>{
    <span class="highlight">"ok" : 1</span>,
    "$clusterTime" : {
        "clusterTime" : Timestamp(1612389071, 1),
        "signature" : {
            "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
            "keyId" : NumberLong(0)
        }
    },
    "operationTime" : Timestamp(1612389071, 1)
}
</code></pre>
<p>If the replica set was initiated as expected, you&rsquo;ll notice that the MongoDB client&rsquo;s prompt will change from just a greater-than sign (<code>&gt;</code>) to the following:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:SECONDARY&gt;">
</li></ul></code></pre>
<p>MongoDB comes installed with a few built-in methods which you can use to manage and retrieve information about your replica set. Of these, the <code>rs.help()</code> method can be particularly helpful as it returns a list of these replica set methods and descriptions of what they do:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:SECONDARY&gt;">rs.help()
</li></ul></code></pre><pre class="code-pre  second-environment"><code><div class="secondary-code-label " title="Output">Output</div>    rs.status()                                { replSetGetStatus : 1 } checks repl set status
    rs.initiate()                              { replSetInitiate : null } initiates set with default settings
    rs.initiate(cfg)                           { replSetInitiate : cfg } initiates set with configuration cfg
    rs.conf()                                  get the current configuration object from local.system.replset
    rs.reconfig(cfg)                           updates the configuration of a running replica set with cfg (disconnects)
    rs.add(hostportstr)                        add a new member to the set with default attributes (disconnects)
    rs.add(membercfgobj)                       add a new member to the set with extra attributes (disconnects)
    rs.addArb(hostportstr)                     add a new member which is arbiterOnly:true (disconnects)
    rs.stepDown([stepdownSecs, catchUpSecs])   step down as primary (disconnects)
    rs.syncFrom(hostportstr)                   make a secondary sync from the given member
    rs.freeze(secs)                            make a node ineligible to become primary for the time specified
    rs.remove(hostportstr)                     remove a host from the replica set (disconnects)
    rs.secondaryOk()                               allow queries on secondary nodes

    rs.printReplicationInfo()                  check oplog size and time range
    rs.printSecondaryReplicationInfo()             check replica set members and replication lag
    db.isMaster()                              check who is primary
    db.hello()                              check who is primary

    reconfiguration helpers disconnect from the database so the shell will display
    an error, even if the command succeeds.
</code></pre>
<p>After running <code>rs.help()</code> or another one of these methods, you may see the client prompt change again to the following:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">
</li></ul></code></pre>
<p>This means that the MongoDB instance that you&rsquo;re connected to was elected to serve as the primary set member. </p>

<p>Be aware that if you have additional nodes that you&rsquo;d like to add to the replica set in the future, you can do so with the <code>rs.add()</code> method after configuring them as you did the current replica set members in the previous steps:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">rs.add( "<span class="highlight">mongo3.replset.member</span>" )
</li></ul></code></pre>
<p>You can now close the MongoDB client by pressing <code>CTRL + C</code> or by running the <code>exit</code> command:</p>
<pre class="code-pre custom_prefix prefixed second-environment"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="rs0:PRIMARY&gt;">exit
</li></ul></code></pre>
<p>Your replica set is now up and running, and you can begin integrating it with your application.</p>

<span class='warning'><p>
<strong>Warning</strong>: When you opened up the MongoDB prompt to initiate the replica set, you may have noticed a warning message like this:</p>
<pre class="code-pre "><code>. . .
        2021-02-03T21:45:48.379+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
. . .
</code></pre>
<p>This message indicates that you haven&rsquo;t yet enabled access control for your database. Per the MongoDB documentation: </p>

<blockquote>
<p>MongoDB uses Role-Based Access Control (RBAC) to govern access to a MongoDB system. A user is granted one or more roles that determine the user’s access to database resources and operations.</p>
</blockquote>

<p>Because access control hasn&rsquo;t been enabled on any of your MongoDB instances, anyone with access to any of the three servers in the replica set could also gain access to the Mongo instance on that server. This poses an important security risk, since this means they could also gain access to your application data.</p>

<p>One way to remove this warning and add a layer of security to your replica set is by configuring <em>keyfile authentication</em>. As mentioned in the introduction, though, the <a href="https://docs.mongodb.com/manual/tutorial/deploy-replica-set-with-keyfile-access-control/#keyfile-security">MongoDB documentation describes</a> keyfiles as &ldquo;bare-minimum forms of security&rdquo; that are &ldquo;best suited for testing or development environments.&rdquo;</p>

<p>Be aware that, for production deployments, <a href="https://docs.mongodb.com/manual/tutorial/deploy-geographically-distributed-replica-set/#id12">the MongoDB documentation instead recommends</a> using x.509 certificates for internal member authentication. The process of obtaining and configuring x.509 certificates comes with a number of caveats and decisions that must be made on a case-by-case basis, which is beyond the scope of this tutorial. </p>

<p>If you plan on using your replica set for testing or development, we <strong>strongly encourage</strong> you to follow our tutorial on <a href="https://www.digitalocean.com/community/tutorials/how-to-configure-keyfile-authentication-for-mongodb-replica-sets-on-ubuntu-20-04">How To Configure Keyfile Authentication for MongoDB Replica Sets on Ubuntu 20.04</a>. <br></p></span>

<h2 id="conclusion">Conclusion</h2>

<p>Database replication has found wide use as a strategy to improve performance, availability, and data security, to the point where it&rsquo;s recommended that any database used in a production environment has some form of replication enabled. Replicas are also versatile, and can take on many different roles in a data architecture, like reporting or disaster recovery. The automatic failover feature found in MongoDB&rsquo;s replica sets make them particularly valuable for helping to ensure that your data remains highly available in the event of an outage.</p>

<p>If you&rsquo;d like to learn more about MongoDB, we encourage you to check out <a href="https://www.digitalocean.com/community/tags/mongodb">our entire collection of MongoDB tutorials</a>.</p>
