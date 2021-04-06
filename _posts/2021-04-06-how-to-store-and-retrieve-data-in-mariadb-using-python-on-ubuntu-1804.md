---
title : "How To Store and Retrieve Data in MariaDB Using Python on Ubuntu 18.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-store-and-retrieve-data-in-mariadb-using-python-on-ubuntu-18-04
image: "https://sergio.afanou.com/assets/images/image-midres-12.jpg"
---

<p><em>The author selected the <a href="https://www.brightfunds.org/funds/tech-education">Tech Education Fund</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p><a href="https://mariadb.org/">MariaDB</a> is an open source version of the popular MySQL relational database management system (DBMS) with a SQL interface for accessing and managing data. It is highly reliable and easy to administer, which are essential qualities of a DBMS capable of serving modern applications. With Python’s growing popularity in technologies like artificial intelligence and machine learning, MariaDB makes a good option for a database server for Python.</p>

<p>In this tutorial, you will connect a Python application to a database server using the MySQL connector. This module allows you to make queries on the database server from within your application. You&rsquo;ll set up MariaDB for a Python environment on Ubuntu 18.04 and write a Python script that connects to and executes queries on MariaDB.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>Before you begin this guide, you will need the following:</p>

<ul>
<li>One Ubuntu 18.04 server with at least 1GB of RAM set up by following <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04">the Ubuntu 18.04 Initial Server Setup Guide</a>, including a sudo non-root user and a firewall.</li>
<li>Python installed on your server. Follow our <a href="https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-ubuntu-18-04">How To Install Python and Set Up a Programming Environment</a> tutorial.</li>
<li>MariaDB installed on your server. Follow our <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-18-04">How To Install MariaDB</a> tutorial. Make sure to note your authorization credentials (username and password), because you will use that later in the tutorial. </li>
</ul>

<h2 id="step-1-—-preparing-and-installing">Step 1 — Preparing and Installing</h2>

<p>In this step, you&rsquo;ll create a database and a table in MariaDB. </p>

<p>First, open your terminal and enter the MariaDB shell from the terminal with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mysql
</li></ul></code></pre>
<p>Once you&rsquo;re in the MariaDB shell, your terminal prompt will change. In this tutorial, you&rsquo;ll write Python to connect to an example employee database named <code>workplace</code> and a table named <code>employees</code>.</p>

<p>Start by creating the <code>workplace</code> database:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [(none)]&gt;">CREATE DATABASE workplace;
</li></ul></code></pre>
<p>Next, tell MariaDB to use <code>workplace</code> as your current database:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [(none)]&gt;">USE workplace;
</li></ul></code></pre>
<p>You will receive the following output, which means that every query you run after this will take effect in the <code>workplace</code> database:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Database changed
</code></pre>
<p>Next, create the <code>employees</code> table:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [workplace]&gt;">CREATE TABLE employees (first_name CHAR(35), last_name CHAR(35));
</li></ul></code></pre>
<p>In the table schema, the parameters <code>first_name</code> and a <code>last_name</code> are specified as character strings (<code>CHAR</code>) with a maximum length of <code>35</code>.</p>

<p>Following this, exit the MariaDB shell:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [workplace]&gt;">exit;
</li></ul></code></pre>
<p>Back in the terminal, export your MariaDB authorization credentials as environment variables:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">export username="<span class="highlight">username</span>"
</li><li class="line" data-prefix="$">export password="<span class="highlight">password</span>"
</li></ul></code></pre>
<p>This technique allows you to avoid adding credentials in plain text within your script. </p>

<p>You&rsquo;ve set up your environment for the project. Next, you&rsquo;ll begin writing your script and connect to your database.</p>

<h2 id="step-2-—-connecting-to-your-database">Step 2 — Connecting to Your Database</h2>

<p>In this step, you will install the <a href="https://dev.mysql.com/doc/connector-python/en/">MySQL Connector</a> and set up the database.</p>

<p>In your terminal, run the following command to install the Connector:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">pip3 install mysql-connector-python
</li></ul></code></pre>
<p><code>pip</code> is the standard package manager for Python. <code>mysql-connector-python</code> is the database connector Python module.</p>

<p>Once you&rsquo;ve successfully installed the connector, create and open a new file Python file:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano database.py
</li></ul></code></pre>
<p>In the opened file, import <a href="https://docs.python.org/3/library/os.html">the <code>os</code> module</a> and the <code>mysql.connector</code> module using the <code>import</code> keyword:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">import os
import mysql.connector as database
</code></pre>
<p>The <code>as</code> keyword here means that <code>mysql.connector</code> will be referenced as <code>database</code> in the rest of the code.</p>

<p>Next, initialize the authorization credentials you exported as Python variables:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">. . .
username = os.environ.get("username")
password = os.environ.get("password")
</code></pre>
<p>Follow up and establish a database connection using the <code>connect()</code> method provided by <code>database</code>. The method takes a series of named arguments specifying your client credentials:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">. . .
connection = database.connect(
    user=<span class="highlight">username</span>,
    password=<span class="highlight">password</span>,
    host=localhost,
    database="<span class="highlight">workplace</span>")
</code></pre>
<p>You declare a variable named <code>connection</code> that holds the call to the <code>database.connect()</code> method. Inside the method, you assign values to the <code>user</code>, <code>password</code>, <code>host</code>, and <code>database</code> arguments. For user and password, you will reference your MariaDB authorization credentials. The host will be <code>localhost</code> by default if you are running the database on the same system.</p>

<p>Lastly, call the <code>cursor()</code> method on the connection to obtain the database cursor:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">. . .
cursor = connection.cursor()
</code></pre>
<p>A <em>cursor</em> is a database object that retrieves and also updates data, one row at a time, from a set of data.</p>

<p>Leave your file open for the next step.</p>

<p>Now you can connect to MariaDB with your credentials; next, you will add entries to your database using your script.</p>

<h2 id="step-3-—-adding-data">Step 3 — Adding Data</h2>

<p>Using the <code>execute()</code> method on the database cursor, you will add entries to your database in this step.</p>

<p>Define a function <code>add_data()</code> to accept the first and last names of an employee as arguments. Inside the function, create a try/except block. Add the following code following your cursor object:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">. . .
def add_data(<span class="highlight">first_name</span>, <span class="highlight">last_name</span>):
    try:
        statement = "INSERT INTO employees (first_name,last_name) VALUES (%s, %s)"
        data = (first_name, last_name)
        cursor.execute(statement, data)
        connection.commit()
        print("Successfully added entry to database")
    except database.Error as e:
        print(f"Error adding entry to database: {e}")
</code></pre>
<p>You use the <code>try</code> and <code>except</code> block to catch and handle exceptions (events or errors) that disrupt the normal flow of program execution.</p>

<p>Under the <code>try</code> block, you declare <code>statement</code> as a variable holding your <code>INSERT</code> SQL statement. The statement tells MariaDB to add to the columns <code>first_name</code> and <code>last_name</code>. </p>

<p>The code syntax accepts data as parameters that reduce the chances of SQL injection. Prepared statements with parameters ensure that only given parameters are securely passed to the database as intended. Parameters are generally not injectable. </p>

<p>Next you declare <code>data</code> as a <a href="https://www.digitalocean.com/community/tutorials/understanding-data-types-in-python-3#tuples">tuple</a> with the arguments received from the <code>add_data</code> function. Proceed to run the <code>execute()</code> method on your <code>cursor</code> object by passing the SQL statement and the data. After calling the <code>execute()</code> method, you call the <code>commit()</code> method on the connection to permanently save the inserted data.</p>

<p>Finally, you print out a success message if this succeeds.</p>

<p>In the <code>except</code> block, which only executes when there&rsquo;s an exception, you declare <code>database.Error</code> as <code>e</code>. This variable will hold information about the type of exception or what event happened when the script breaks. You then proceed to print out an error message formatted with <code>e</code> to end the block using an <a href="https://www.digitalocean.com/community/tutorials/how-to-use-f-strings-to-create-strings-in-python-3">f-string</a>.</p>

<p>After adding data to the database, you&rsquo;ll next want to retrieve it. The next step will take you through the process of retrieving data.</p>

<h2 id="step-4-—-retrieving-data">Step 4 — Retrieving Data</h2>

<p>In this step, you will write a SQL query within your Python code to retrieve data from your database.</p>

<p>Using the same <code>execute()</code> method on the database cursor, you can retrieve a database entry. </p>

<p>Define a function <code>get_data()</code> to accept the last name of an employee as an argument, which you will call with the <code>execute()</code> method with the <code>SELECT</code> SQL query to locate the exact row:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">. . .
def get_data(<span class="highlight">last_name</span>):
    try:
      statement = "SELECT first_name, last_name FROM employees WHERE last_name=%s"
      data = (last_name,)
      cursor.execute(statement, data)
      for (first_name, last_name) in cursor:
        print(f"Successfully retrieved {first_name}, {last_name}")
    except database.Error as e:
      print(f"Error retrieving entry from database: {e}")
</code></pre>
<p>Under the <code>try</code> block, you declare <code>statement</code> as a variable holding your <code>SELECT</code> SQL statement. The statement tells MariaDB to retrieve the columns <code>first_name</code> and <code>last_name</code> from the <code>employees</code> table when a specific last name is matched. </p>

<p>Again, you use parameters to reduce the chances of SQL injection. </p>

<p>Smilarly to the last function, you declare <code>data</code> as a tuple with <code>last_name</code> followed by a comma. Proceed to run the <code>execute()</code> method on the <code>cursor</code> object by passing the SQL statement and the data. Using a <a href="https://www.digitalocean.com/community/tutorials/how-to-construct-for-loops-in-python-3"><code>for</code> loop</a>, you iterate through the returned elements in the cursor and then print out if there are any successful matches.</p>

<p>In the <code>except</code> block, which only executes when there is an exception, declare <code>database.Error</code> as <code>e</code>. This variable will hold information about the type of exception that occurs. You then proceed to print out an error message formatted with <code>e</code> to end the block.</p>

<p>In the final step, you will execute your script by calling the defined functions.</p>

<h2 id="step-5-—-running-your-script">Step 5 — Running Your Script</h2>

<p>In this step, you will write the final piece of code to make your script executable and run it from your terminal.</p>

<p>Complete your script by calling <code><span class="highlight">add_data()</span></code> and <code><span class="highlight">get_data()</span></code> with sample data (strings) to verify that your code is working as expected.</p>

<p>If you would like to add multiple entries, you can call <code>add_data()</code> with further sample names of your choice.</p>

<p>Once you finish working with the database make sure that you close the connection to avoid wasting resources:<br>
<code><span class="highlight">connection.close()</span></code>:</p>
<div class="code-label " title="database.py">database.py</div><pre class="code-pre "><code class="code-highlight language-python">import os
import mysql.connector as database

username = os.environ.get("username")
password = os.environ.get("password")

connection = database.connect(
    user=username,
    password=password,
    host=localhost,
    database="workplace")

cursor = connection.cursor()

def add_data(first_name, last_name):
    try:
    statement = "INSERT INTO employees (first_name,last_name) VALUES (%s, %s)"
    data = (first_name, last_name)
      cursor.execute(statement, data)
    cursor.commit()
    print("Successfully added entry to database")
    except database.Error as e:
    print(f"Error adding entry to database: {e}")

def get_data(last_name):
    try:
      statement = "SELECT first_name, last_name FROM employees WHERE last_name=%s"
      data = (last_name,)
      cursor.execute(statement, data)
      for (first_name, last_name) in cursor:
        print(f"Successfully retrieved {first_name}, {last_name}")
    except database.Error as e:
      print(f"Error retrieving entry from database: {e}")

<span class="highlight">add_data("Kofi", "Doe")</span>
<span class="highlight">get_data("Doe")</span>

<span class="highlight">connection.close()</span>
</code></pre>
<p>Make sure you have indented your code correctly to avoid errors.</p>

<p>In the same directory, you created the <code>database.py</code> file, run your script with:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python3 ./database.py
</li></ul></code></pre>
<p>You will receive the following output:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Successfully added entry to database
Successfully retrieved <span class="highlight">Kofi, Doe</span>
</code></pre>
<p>Finally, return to MariaDB to confirm you have successfully added your entries. </p>

<p>Open up the MariaDB prompt from your terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mysql
</li></ul></code></pre>
<p>Next, tell MariaDB to switch to and use the <code><span class="highlight">workplace</span></code> database:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [(none)]&gt;">USE workplace;
</li></ul></code></pre>
<p>After you get the success message <code>Database changed</code>, proceed to query for all entries in the <code><span class="highlight">employees</span></code> table:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="MariaDB [workplace]&gt;">SELECT * FROM employees;
</li></ul></code></pre>
<p>You output will be similar to the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+------------+-----------+
| first_name | last_name |
+------------+-----------+
| <span class="highlight">Kofi</span>       | <span class="highlight">Doe</span>       |
+------------+-----------+
1 row in set (0.00 sec)
</code></pre>
<p>Putting it all together, you&rsquo;ve written a script that saves and retrieves information from a MariaDB database. </p>

<p>You started by importing the necessary libraries. You used <code>mysql-connector</code> to connect to the database and <code>os</code> to retrieve authorization credentials from the environment. On the database connection, you retrieved the cursor to carry out queries and structured your code into <code>add_data</code> and <code>get_data</code> functions. With your functions, you inserted data into and retrieved data from the database. </p>

<p>If you wish to implement deletion, you can build a similar function with the necessary declarations, statements, and calls.</p>

<h2 id="conclusion">Conclusion</h2>

<p>You have successfully set up a database connection to MariaDB using a Python script on Ubuntu 18.04. From here, you could use similar code in any of your Python projects in which you need to store data in a database. This guide may also be helpful for other relational databases that were developed out of MySQL.</p>

<p>For more on how to accomplish your projects with Python, check out other community tutorials on <a href="https://www.digitalocean.com/community/tags/python">Python</a>.</p>
