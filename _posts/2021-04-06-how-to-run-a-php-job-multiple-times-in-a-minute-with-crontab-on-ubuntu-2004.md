---
title : "How To Run a PHP Job Multiple Times in a Minute with Crontab on Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-run-a-php-job-multiple-times-in-a-minute-with-crontab-on-ubuntu-20-04
image: "https://sergio.afanou.com/assets/images/image-midres-44.jpg"
---

<p><em>The author selected <a href="https://www.brightfunds.org/organizations/girls-who-code">Girls Who Code</a> to receive a donation as part of the <a href="https://do.co/w4do-cta">Write for DOnations</a> program.</em></p>

<h3 id="introduction">Introduction</h3>

<p>In Linux, you can use the versatile <a href="https://man7.org/linux/man-pages/man5/crontab.5.html">crontab tool</a> to process long-running tasks in the background at specific times. While the daemon is great for running repetitive tasks, it has got one limitation: you can only execute tasks at a minimum time interval of 1 minute.</p>

<p>However, in many applications, to avoid a poor user experience it&rsquo;s preferable for jobs to execute more frequently. For instance, if you&rsquo;re using the <a href="https://en.wikipedia.org/wiki/Job_queue">job-queue model</a> to schedule file-processing tasks on your website, a significant wait will have a negative impact on end users.</p>

<p>Another scenario is an application that uses the job-queue model either to send text messages or emails to clients once they&rsquo;ve completed a certain task in an application (for example, sending money to a recipient). If users have to wait a minute before the delivery of a confirmation message, they might think that the transaction failed and try to repeat the same transaction. </p>

<p>To overcome these challenges, you can program a PHP script that loops and processes tasks repetitively for 60 seconds as it awaits for the crontab daemon to call it again after the minute. Once the PHP script is called for the first time by the crontab daemon, it can execute tasks in a time period that matches the logic of your application without keeping the user waiting.</p>

<p>In this guide, you will create a sample <code>cron_jobs</code> database on an Ubuntu 20.04 server. Then, you&rsquo;ll set up a <code>tasks</code> table and a script that executes the jobs in your table in intervals of 5 seconds using the PHP <code>while(...){...}</code> loop and <code>sleep()</code> functions.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To complete this tutorial, you require the following:</p>

<ul>
<li><p>An Ubuntu 20.04 server set up with a non-root user. Follow our <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Initial Server Setup with Ubuntu 20.04</a> guide.</p></li>
<li><p>A LAMP stack set up on your server. Refer to the <a href="https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-20-04">How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 20.04</a> guide. For this tutorial, you may skip <strong>Step 4 — Creating a Virtual Host for your Website</strong>.</p></li>
</ul>

<h2 id="step-1-—-setting-up-a-database">Step 1 — Setting Up a Database</h2>

<p>In this step, you&rsquo;ll create a sample database and table. First, <code>SSH</code> to your server and log in to MySQL as root:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mysql -u root -p
</li></ul></code></pre>
<p>Enter your root password for the MySQL server and press <code>ENTER</code> to proceed. Then, run the following command to create a <code>cron_jobs</code> database.</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">CREATE DATABASE <span class="highlight">cron_jobs</span>;
</li></ul></code></pre>
<p>Create a non-root user for the database. You&rsquo;ll need the credentials of this user to connect to the <code>cron_jobs</code> database from PHP. Remember to replace <code>EXAMPLE_PASSWORD</code> with a strong value:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">CREATE USER '<span class="highlight">cron_jobs_user</span>'@'localhost' IDENTIFIED WITH mysql_native_password BY '<span class="highlight">EXAMPLE_PASSWORD</span>';
</li><li class="line" data-prefix="mysql&gt;">GRANT ALL PRIVILEGES ON <span class="highlight">cron_jobs</span>.* TO '<span class="highlight">cron_jobs_user</span>'@'localhost';           
</li><li class="line" data-prefix="mysql&gt;">FLUSH PRIVILEGES;
</li></ul></code></pre>
<p>Next, switch to the <code>cron_jobs</code> database:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">USE <span class="highlight">cron_jobs</span>;
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Database changed
</code></pre>
<p>Once you&rsquo;ve selected the database, create a <code>tasks</code> table. In this table, you&rsquo;ll insert some tasks that will be automatically executed by a cron job. Since the minimum time interval for running a cron job is <code>1</code> minute, you&rsquo;ll later code a PHP script that overrides this setting and instead, execute the jobs in intervals of 5 seconds.</p>

<p>For now, create your <code>tasks</code> table:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">CREATE TABLE tasks (
</li><li class="line" data-prefix="mysql&gt;">    task_id BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
</li><li class="line" data-prefix="mysql&gt;">    task_name VARCHAR(50),          
</li><li class="line" data-prefix="mysql&gt;">    queued_at DATETIME,     
</li><li class="line" data-prefix="mysql&gt;">    completed_at DATETIME,
</li><li class="line" data-prefix="mysql&gt;">    is_processed CHAR(1)    
</li><li class="line" data-prefix="mysql&gt;">) ENGINE = InnoDB;
</li></ul></code></pre>
<p>Insert three records to the tasks table. Use the MySQL <code>NOW()</code> function in the <code>queued_at</code> column to record the current date and time when the tasks are queued. Also for the <code>completed_at</code> column, use the MySQL <code>CURDATE()</code> function to set a default time of <code>00:00:00</code>. Later, as tasks complete, your script will update this column:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">INSERT INTO tasks (task_name, queued_at, completed_at, is_processed) VALUES ('TASK 1', NOW(), CURDATE(), 'N');
</li><li class="line" data-prefix="mysql&gt;">INSERT INTO tasks (task_name, queued_at, completed_at, is_processed) VALUES ('TASK 2', NOW(), CURDATE(), 'N');
</li><li class="line" data-prefix="mysql&gt;">INSERT INTO tasks (task_name, queued_at, completed_at, is_processed) VALUES ('TASK 3', NOW(), CURDATE(), 'N');
</li></ul></code></pre>
<p>Confirm the output after running each <code>INSERT</code> command:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Query OK, 1 row affected (0.00 sec)
...
</code></pre>
<p>Make sure the data is in place by running a <code>SELECT</code> statement against the <code>tasks</code> table:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">SELECT task_id, task_name, queued_at, completed_at, is_processed FROM tasks;
</li></ul></code></pre>
<p>You will find a list of all tasks:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>+---------+-----------+---------------------+---------------------+--------------+
| task_id | task_name | queued_at           | completed_at        | is_processed |
+---------+-----------+---------------------+---------------------+--------------+
|       1 | TASK 1    | 2021-03-06 06:27:19 | 2021-03-06 00:00:00 | N            |
|       2 | TASK 2    | 2021-03-06 06:27:28 | 2021-03-06 00:00:00 | N            |
|       3 | TASK 3    | 2021-03-06 06:27:36 | 2021-03-06 00:00:00 | N            |
+---------+-----------+---------------------+---------------------+--------------+
3 rows in set (0.00 sec)
</code></pre>
<p>The time for the <code>completed_at</code> column is set to <code>00:00:00</code>, this column will update once the tasks are processed by a PHP script that you will create next. </p>

<p>Exit from the MySQL command-line interface:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">QUIT;
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Bye
</code></pre>
<p>Your <code>cron_jobs</code> database and <code>tasks</code> table are now in place and you can now create a PHP script that processes the jobs.</p>

<h2 id="step-2-—-creating-a-php-script-that-runs-tasks-after-5-seconds">Step 2 — Creating a PHP Script that Runs Tasks After 5 Seconds</h2>

<p>In this step, you&rsquo;ll create a script that uses a combination of the PHP <code>while(...){...}</code> loop and <code>sleep</code> functions to run tasks after every 5 seconds.</p>

<p>Open a new <code>/var/www/html/tasks.php</code> file in the root directory of your web server using nano:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /var/www/html/tasks.php
</li></ul></code></pre>
<p>Next, create a new <code>try {</code> block after a <code>&lt;?php</code> tag and declare the database variables that you created in Step 1. Remember to replace <code>EXAMPLE_PASSWORD</code> with the actual password for your database user:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">&lt;?php
try {
    $db_name     = '<span class="highlight">cron_jobs</span>';
    $db_user     = '<span class="highlight">cron_jobs_user</span>';
    $db_password = '<span class="highlight">EXAMPLE_PASSWORD</span>';
    $db_host     = 'localhost';
</code></pre>
<p>Next, declare a new PDO (PHP Data Object) class and set the attribute <code>ERRMODE_EXCEPTION</code> to catch any PDO errors. Also, switch <code>ATTR_EMULATE_PREPARES</code> to <code>false</code> to let the native MySQL database engine handle emulation. Prepared statements allow you to send the SQL queries and data separately to enhance security and reduce chances of an SQL injection attack:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">
    $pdo = new PDO('mysql:host=' . $db_host . '; dbname=' . $db_name, $db_user, $db_password);
    $pdo-&gt;setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);  
    $pdo-&gt;setAttribute(PDO::ATTR_EMULATE_PREPARES, false);       
</code></pre>
<p>Then, create a new variable named <code>$loop_expiry_time</code> and set it to the current time plus 60 seconds. Then open a new PHP <code>while(time() &lt; $loop_expiry_time) {</code> statement. The idea here is to create a loop that runs until the current time (<code>time()</code>) matches the variable <code>$loop_expiry_time</code>:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">       
    $loop_expiry_time = time() + 60;

    while (time() &lt; $loop_expiry_time) { 
</code></pre>
<p>Next, declare a prepared SQL statement that retrieves unprocessed jobs from the <code>tasks</code> table:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">   
        $data = [];
        $sql  = "select 
                 task_id
                 from tasks
                 where is_processed = :is_processed
                 ";
</code></pre>
<p>Execute the SQL command and fetch all rows from the <code>tasks</code> table that have the column <code>is_processed</code> set to <code>N</code>. This means the rows are not processed:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">  
        $data['is_processed'] = 'N';  

        $stmt = $pdo-&gt;prepare($sql);
        $stmt-&gt;execute($data);
</code></pre>
<p>Next, loop through the retrieved rows using a PHP <code>while ($row = $stmt-&gt;fetch(PDO::FETCH_ASSOC)) {...}</code> statement and create another SQL statement. This time around, the SQL command updates the <code>is_processed</code> and <code>completed_at</code> columns for each task processed. This ensures that you don&rsquo;t process tasks more than one time:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">  
        while ($row = $stmt-&gt;fetch(PDO::FETCH_ASSOC)) { 
            $data_update   = [];         
            $sql_update    = "update tasks set 
                              is_processed  = :is_processed,
                              completed_at  = :completed_at
                              where task_id = :task_id                                 
                              ";

            $data_update   = [                
                             'is_processed' =&gt; 'Y',                          
                             'completed_at' =&gt; date("Y-m-d H:i:s"),
                             'task_id'      =&gt; $row['task_id']                         
                             ];
            $stmt = $pdo-&gt;prepare($sql_update);
            $stmt-&gt;execute($data_update);
        }
</code></pre>
<p><span class='note'><strong>Note:</strong> If you have a large queue to be processed (for example, 100,000 records per second), you might consider queueing jobs in a <a href="https://www.digitalocean.com/community/tags/redis">Redis Server</a> since it is faster than MySQL when it comes to implementing the job-queue model. Nevertheless, this guide will process a smaller dataset.<br></span></p>

<p>Before you close the first PHP <code>while (time() &lt; $loop_expiry_time) {</code> statement, include a <code>sleep(5);</code> statement to pause the jobs execution for 5 seconds and free up your server resources. </p>

<p>You may change the 5 seconds period depending on your business logic and how fast you want tasks to execute. For instance, if you would like the tasks to be processed 3 times in a minute, set this value to 20 seconds.</p>

<p>Remember to <code>catch</code> any PDO error messages inside a <code>} catch (PDOException $ex) { echo $ex-&gt;getMessage(); }</code> block:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">                 sleep(5); 

        }       

} catch (PDOException $ex) {
    echo $ex-&gt;getMessage(); 
}
</code></pre>
<p>Your complete <code>tasks.php</code> file will be as follows:</p>
<div class="code-label " title="/var/www/html/tasks.php">/var/www/html/tasks.php</div><pre class="code-pre "><code class="code-highlight language-php">&lt;?php
try {
    $db_name     = 'cron_jobs';
    $db_user     = 'cron_jobs_user';
    $db_password = '<span class="highlight">EXAMPLE_PASSWORD</span>';
    $db_host     = 'localhost';

    $pdo = new PDO('mysql:host=' . $db_host . '; dbname=' . $db_name, $db_user, $db_password);
    $pdo-&gt;setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);  
    $pdo-&gt;setAttribute(PDO::ATTR_EMULATE_PREPARES, false);               

    $loop_expiry_time = time() + 60;

    while (time() &lt; $loop_expiry_time) { 
        $data = [];
        $sql  = "select 
                 task_id
                 from tasks
                 where is_processed = :is_processed
                 ";

        $data['is_processed'] = 'N';             

        $stmt = $pdo-&gt;prepare($sql);
        $stmt-&gt;execute($data);

        while ($row = $stmt-&gt;fetch(PDO::FETCH_ASSOC)) { 
            $data_update   = [];         
            $sql_update    = "update tasks set 
                              is_processed  = :is_processed,
                              completed_at  = :completed_at
                              where task_id = :task_id                                 
                              ";

            $data_update   = [                
                             'is_processed' =&gt; 'Y',                          
                             'completed_at' =&gt; date("Y-m-d H:i:s"),
                             'task_id'      =&gt; $row['task_id']                         
                             ];
            $stmt = $pdo-&gt;prepare($sql_update);
            $stmt-&gt;execute($data_update);
        }   

        sleep(5); 

        }       

} catch (PDOException $ex) {
    echo $ex-&gt;getMessage(); 
}
</code></pre>
<p>Save the file by pressing <code>CTRL</code> + <code>X</code>, <code>Y</code> then <code>ENTER</code>. </p>

<p>Once you&rsquo;ve completed coding the logic in the <code>/var/www/html/tasks.php</code> file, you&rsquo;ll schedule the crontab daemon to execute the file after every 1 minute in the next step.</p>

<h2 id="step-3-—-scheduling-the-php-script-to-run-after-1-minute">Step 3 — Scheduling the PHP Script to Run After 1 Minute</h2>

<p>In Linux, you can schedule jobs to run automatically after a stipulated time by entering a command into the crontab file. In this step, you will instruct the crontab daemon to run your <code>/var/www/html/tasks.php</code> script after every minute. So, open the <code>/etc/crontab</code> file using nano:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/crontab
</li></ul></code></pre>
<p>Then add the following toward the end of the file to execute the <code>http://localhost/tasks.php</code> after every <code>1</code> minute:</p>
<div class="code-label " title="/etc/crontab">/etc/crontab</div><pre class="code-pre "><code class="code-highlight language-bash">...
* * * * * root /usr/bin/wget -O - http://localhost/tasks.php
</code></pre>
<p>Save and close the file. </p>

<p>This guide assumes that you have a basic knowledge of how cron jobs work. Consider reading our guide on <a href="https://www.digitalocean.com/community/tutorials/how-to-use-cron-to-automate-tasks-ubuntu-1804#understanding-how-cron-works">How to Use Cron to Automate Tasks on Ubuntu</a>. </p>

<p>As earlier indicated, although the cron daemon runs the <code>tasks.php</code> file after every 1 minute, once the file is executed for the first time, it will loop through the open tasks for another 60 seconds. By the time the loop time expires, the cron daemon will execute the file again and the process will continue.</p>

<p>After updating and closing the <code>/etc/crontab</code> file, the crontab daemon should begin executing the MySQL tasks that you inserted in the <code>tasks</code> table immediately. To confirm whether everything is working as expected, you&rsquo;ll query your <code>cron_jobs</code> database next.</p>

<h2 id="step-4-—-confirming-job-execution">Step 4 — Confirming Job Execution</h2>

<p>In this step, you will open your database one more time to check whether the <code>tasks.php</code> file is processing queued jobs when executed automatically by the crontab.</p>

<p>Log back in to your MySQL server as root:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mysql -u root -p
</li></ul></code></pre>
<p>Then, enter your MySQL server&rsquo;s root password and hit <code>ENTER</code> to proceed. Then, switch to the database:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">USE <span class="highlight">cron_jobs</span>;
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Database changed
</code></pre>
<p>Run a <code>SELECT</code> statement against the <code>tasks</code> table:</p>
<pre class="code-pre custom_prefix prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="mysql&gt;">SELECT task_id, task_name, queued_at, completed_at, is_processed FROM tasks;
</li></ul></code></pre>
<p>You will receive output similar to the following. In the <code>completed_at</code> column, tasks have been processed at intervals of <code>5</code> seconds. Also, the tasks have been marked as completed since the <code>is_processed</code> column is now set to <code>Y</code>, which means <code>YES</code>. </p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>
+---------+-----------+---------------------+---------------------+--------------+
| task_id | task_name | queued_at           | completed_at        | is_processed |
+---------+-----------+---------------------+---------------------+--------------+
|       1 | TASK 1    | 2021-03-06 06:27:19 | 2021-03-06 06:30:01 | Y            |
|       2 | TASK 2    | 2021-03-06 06:27:28 | 2021-03-06 06:30:06 | Y            |
|       3 | TASK 3    | 2021-03-06 06:27:36 | 2021-03-06 06:30:11 | Y            |
+---------+-----------+---------------------+---------------------+--------------+
3 rows in set (0.00 sec)
</code></pre>
<p>This confirms that your PHP script is working as expected; you have run tasks in a shorter time interval by overriding the limitation of the 1 minute time period set by the crontab daemon.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this guide, you&rsquo;ve set up a sample database on an Ubuntu 20.04 server. Then, you have created jobs in a table and run them at intervals of 5 seconds using the PHP <code>while(...){...}</code> loop and <code>sleep()</code> functions. Use the logic in this tutorial when you&rsquo;re next implementing a job-queue-based application where tasks need to be run multiple times within a 1 minute time period.</p>

<p>For more PHP tutorials, check out our <a href="https://www.digitalocean.com/community/tags/php">PHP topic page</a>.</p>
