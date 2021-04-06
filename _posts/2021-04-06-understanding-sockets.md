---
title : "Understanding Sockets"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-sockets
image: "https://sergio.afanou.com/assets/images/image-midres-47.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Sockets are a way to enable inter-process communication between programs running on a server, or between programs running on separate servers. Communication between servers relies on <em>network sockets</em>, which use the Internet Protocol (IP) to encapsulate and handle sending and receiving data.</p>

<p>Network sockets on both clients and servers are referred to by their <em>socket address</em>. An address is a unique combination of a transport protocol like the Transmission Control Protocol (TCP) or User Datagram Protocol (UDP), an IP address, and a port number.</p>

<p>In this tutorial you will learn about the following different types of sockets that are used for inter-process communication:</p>

<ul>
<li><em>Stream sockets</em>, which use TCP as their underlying transport protocol</li>
<li><em>Datagram sockets</em>, which use UDP as their underlying transport protocol</li>
<li><em>Unix Domain Sockets</em>, which use local files to send and receive data instead of network interfaces and IP packets.</li>
</ul>

<p>In each section of this tutorial you will also learn how to enumerate the respective socket types on a Linux system. You’ll examine each type of socket using a variety of command line tools.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>The examples in this tutorial were validated on an Ubuntu 20.04 server. You can follow this tutorial using most modern Linux distributions on a local computer or remote server, as long as you have the equivalent version of each of the required tools for your distribution installed.</p>

<p>To get started using Ubuntu 20.04, you will need one server that has been configured by following our <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Initial Server Setup for Ubuntu 20.04</a> guide. </p>

<p>You will also need a few other packages in order to examine sockets on your system. Ensure that your local package cache is up to date using the <code>apt update</code> command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update 
</li></ul></code></pre>
<p>Then install the required packages using this command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install iproute2 netcat-openbsd socat
</li></ul></code></pre>
<p>The <code>iproute2</code> package contains the <code>ss</code> utility, which is what we’ll use to inspect sockets. We’ll use the <code>netcat-openbsd</code> package to install netcat. Note that netcat is abbreviated to <code>nc</code> when it is invoked on the command line. Finally, we’ll use the <code>socat</code> package to create example sockets.</p>

<h2 id="what-is-a-stream-socket">What is a Stream Socket?</h2>

<p>Stream sockets are connection oriented, which means that packets sent to and received from a network socket are delivered by the host operating system in order for processing by an application. Network based stream sockets typically use the Transmission Control Protocol (TCP) to encapsulate and transmit data over a network interface.</p>

<p>TCP is designed to be a reliable network protocol that relies on a stateful connection. Data that is sent by a program using a TCP-based stream socket will be successfully received by a remote system (assuming there are no routing, firewall, or other connectivity issues). TCP packets can arrive on a physical network interface in any order. In the event that packets arrive out of order, the network adapter and host operating system will ensure that they are reassembled in the correct sequence for processing by an application.</p>

<p>A typical use for a TCP-based stream socket would be for a web server like Apache or Nginx handling HTTP requests on port <code>80</code>, or HTTPS on port <code>443</code>. For HTTP, a socket address would be similar to <code>203.0.113.1:80</code>, and for HTTPS it would be something like <code>203.0.113.1:443</code>.</p>

<h3 id="creating-tcp-based-stream-sockets">Creating TCP-based Stream Sockets</h3>

<p>In the following example you’ll use the <code>socat</code> (short for <code><span class="highlight">SO</span>cket <span class="highlight">CAT</span></code>) command to emulate a web server listening for HTTP requests on port 8080 (the alternative HTTP port). Then you’ll examine the socket using the <code>ss</code>, and <code>nc</code> commands. </p>

<p>First, run the following <code>socat</code> commands to create two TCP-based sockets that are listening for connections on port <code>8080</code>, using IPv4 and IPv6 interfaces:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">socat TCP4-LISTEN:8080,fork /dev/null&amp;
</li><li class="line" data-prefix="$">socat TCP6-LISTEN:8080,ipv6only=1,fork /dev/null&amp;
</li></ul></code></pre>
<ul>
<li>The <code>TCP4-LISTEN:8080</code> and <code>TCP6-LISTEN:8080</code> arguments are the protocol type and port number to use. They tell <code>socat</code> to create TCP sockets on port <code>8080</code> on all IPv4 and IPv6 interfaces, and to listen to each socket for incoming connections. <code>socat</code> can listen on any available port on a system, so any port from <code>0</code> to <code>65535</code> is a valid parameter for the socket option.</li>
<li>The <code>fork</code> option is used to ensure that <code>socat</code> continues to run after it handles a connection, otherwise it would exit automatically.</li>
<li>The <code>/dev/null</code> path is used in place of a remote socket address. In this case it tells <code>socat</code> to print any incoming input to the <code>/dev/null</code> file, which discards it silently.</li>
<li>The <code>ipv6only=1</code> flag is used for the IPv6 socket to tell the operating system that the socket is not configured to send packets to <a href="https://en.wikipedia.org/wiki/IPv6#IPv4-mapped_IPv6_addresses">IPv4-mapped IPv6 addresses</a>. Without this flag, <code>socat</code> will bind to both IPv4 and IPv6 addresses.</li>
<li>The <code>&amp;</code> character instructs the shell to run the command in the background. This flag will ensure that <code>socat</code> continues to run while you invoke other commands to examine the socket.</li>
</ul>

<p>You will receive output like the following, which indicates the two <code>socat</code> process IDs that are running in the background of your shell session. Your process IDs will be different than the ones highlighted here:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[1] <span class="highlight">434223</span>
[2] <span class="highlight">434224</span>
</code></pre>
<p>Now that you have two <code>socat</code> processes listening on TCP port <code>8080</code> in the background, you can examine the sockets using the <code>ss</code> and <code>nc</code> utilities.</p>

<h3 id="examining-tcp-based-stream-sockets">Examining TCP-Based Stream Sockets</h3>

<p>To examine TCP sockets on a modern Linux system using the <code>ss</code> command, run it with the following flags to restrict the output:</p>

<ul>
<li>The <code>-4</code> and <code>-6</code> flags tell <code>ss</code> to only examine IPv4 or IPv6 sockets respectively. Omitting this option will display both sets of sockets.</li>
<li>The <code>t</code> flag limits the output to TCP sockets. By default the <code>ss</code> tool will display all types of sockets in use on a Linux system.</li>
<li>The <code>l</code> flag limits the output to listening sockets. Without this flag, all TCP connections would be displayed, which would include things like SSH, clients that may be connected to a web-server, or connections that your system may have to other servers.</li>
<li>The <code>n</code> flag ensures that port numbers are displayed instead of service names. </li>
</ul>

<p>First run the <code>ss -4  -tln</code> command to examine the IPv4 TCP-based sockets that are listening for connections on your system:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ss -4 -tln
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>State      Recv-Q     Send-Q         Local Address:Port           Peer Address:Port     Process     
. . .
LISTEN     0          1                    <span class="highlight">0.0.0.0:8080</span>                  0.0.0.0:*
. . .
</code></pre>
<p>There may be other lines with other ports in your output depending on which services are running on your system. The highlighted <code>0.0.0.0:8080</code> portion of the output indicates the IPv4 TCP socket is listening on all available IPv4 interfaces on port <code>8080</code>. A service that is only listening on a specific IPv4 address will show only that IP in the highlighted field, for example <code>203.0.113.1:8080</code>.</p>

<p>Now run the same <code>ss</code> command again but with the <code>-6</code> flag:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ss -6 -tln
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process  
. . .
LISTEN   0        5                   <span class="highlight">[::]:8080</span>               [::]:*
. . .
</code></pre>
<p>There may be other lines with other ports in your output depending on which services are running on your system. The highlighted <code>[::]:8080</code> portion of the output indicates the IPv6 TCP socket is listening on all available IPv6 interfaces on port <code>8080</code> (as indicated by the <code>::</code> characters, which are IPv6 notation for an address composed of all zeros). A service that is only listening on a specific IPv6 address will show only that IP in the highlighted field, for example <code>[2604:a880:400:d1::3d3:6001]:8080</code>.</p>

<h3 id="connecting-to-tcp-based-stream-sockets">Connecting to TCP-Based Stream Sockets</h3>

<p>So far you have learned how to create and enumerate TCP sockets on both IPv4 and IPv6 interfaces. Now that you have two sockets listening for connections, you can experiment with connecting to sockets using the netcat utility. </p>

<p>Using netcat to test TCP connections to local and remote sockets is a very useful troubleshooting technique that can help isolate connectivity and firewall issues between systems.</p>

<p>To connect to the IPv4 socket over the local loopback address using netcat, run the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -4 -vz 127.0.0.1 8080
</li></ul></code></pre>
<ul>
<li>The <code>-4</code> flag tells netcat to use IPv4.</li>
<li>The <code>-v</code> flag is used to print verbose output to your terminal.</li>
<li>The -<code>z</code> option ensures that netcat only connects to a socket, without sending any data.</li>
<li>The local loopback <code>127.0.0.1</code> IP address is used since your system will have its own unique IP address. If you know the IP for your system you can test using that as well. For example, if your system’s public or private IP address is 203.0.113.1 you could use that in place of the loopback IP. </li>
</ul>

<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">Connection to 127.0.0.1 (127.0.0.1) 8080 port [tcp/http-alt] succeeded!</span>
</code></pre>
<p>The highlighted line is the output from netcat. It indicates that netcat connected to the TCP socket listening on the loopback <code>127.0.0.1</code> IPv4 address on port <code>8080</code>. You can ignore the second line, it is from the socat process running in the background in your terminal. </p>

<p>Now you can repeat the same connection test but using IPv6. Run the following netcat command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -6 -vz ::1 8080
</li></ul></code></pre>
<p>You should receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">Connection to ::1 8080 port [tcp/http] succeeded!</span>
</code></pre>
<p>The highlighted line is the output from netcat. It indicates that netcat connected to the TCP socket listening on the loopback <code>::1</code> IPv6 address on port <code>8080</code>. Again, you can ignore the second line of output.</p>

<p>To clean up your sockets, you’ll need to run the <code>fg</code> (foreground) command for each socat process that you created. Then you’ll use <code>CTRL+C</code> to close each socat. <code>fg</code> will bring processes to the foreground of your terminal in the reverse order that you ran them, so when you run it, the second <code>socat</code> instance will be the one that you interact with first.</p>

<p>Run <code>fg</code> to bring the second IPv6 socat instance to the foreground of your terminal. Then run <code>CTRL+C</code> to close it.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">fg
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>socat TCP6-LISTEN:8080,ipv6only=1,fork /dev/null
</code></pre>
<p>Press <code>CTRL+C</code> to stop the process.</p>

<p>Now run <code>fg</code> again to clean up the first IPv4 socket. You should have output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>socat TCP4-LISTEN:8080,fork /dev/null
</code></pre>
<p>Press <code>CTRL+C</code> to stop the process.</p>

<p>You have now created, examined, and connected to IPv4 and IPv6 sockets on your system. These techniques and tools will work on local development systems, or remote production servers, so try experimenting with each tool to become more familiar with how they can be used to test and troubleshoot TCP sockets.</p>

<h2 id="what-is-a-datagram-socket">What is a Datagram Socket?</h2>

<p>Datagram sockets are connectionless, which means that packets sent and received from a socket are processed individually by applications. Network-based datagram sockets typically use the User Datagram Protocol (UDP) to encapsulate and transmit data.</p>

<p>UDP does not encode sequence information in packet headers, and there is no error correction built into the protocol. Programs that use datagram-based network sockets must build in their own error handling and data ordering logic to ensure successful data transmission. </p>

<p>UDP sockets are commonly used by Domain Name System (DNS) servers. By default, DNS servers use port <code>53</code> to send and receive queries for domain names. An example UDP socket address for a DNS server would be similar to <code>203.0.113.1:53</code>. </p>

<p><span class='note'><strong>Note</strong>: Although the protocol is not included in the human-readable version of the socket address, operating systems differentiate socket addresses by including TCP and UDP protocols as part of the address. So a human-readable socket address like <code>203.0.113.1:53</code> could be using either protocol. Tools like <code>ss</code>, and the older <code>netstat</code> utility, are used to determine which kind of socket is being used.<br></span></p>

<p>The Network Time Protocol (NTP) uses a UDP socket on port <code>123</code> to synchronize clocks between computers. An example UDP socket for the NTP protocol would be <code>203.0.113.1:123</code>.</p>

<h3 id="creating-datagram-sockets">Creating Datagram Sockets</h3>

<p>As in the previous TCP socket example, in this section you’ll use <code>socat</code> again to emulate an NTP server listening for requests on UDP port <code>123</code>. Then you’ll examine the sockets that you create using the <code>ss</code> and <code>nc</code> commands. </p>

<p>First, run the following <code>socat</code> commands to create two UDP sockets that are listening for connections on port 123, using IPv4 and IPv6 interfaces:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo socat UDP4-LISTEN:123,fork /dev/null&amp;
</li><li class="line" data-prefix="$">sudo socat UDP6-LISTEN:123,ipv6only=1,fork /dev/null&amp;
</li></ul></code></pre>
<p>You will receive output like the following, which indicates the two <code>socat</code> process IDs that are running in the background of your shell session. Your process IDs will be different than the ones highlighted here:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[1] <span class="highlight">465486</span>
[2] <span class="highlight">465487</span>

</code></pre>
<ul>
<li>Each command is prefixed with <code>sudo</code>, because ports <code>0</code> to <code>1024</code> are reserved on most systems. <code>sudo</code> runs a command with administrator permissions, which allows <code>socat</code> to bind to any port in the reserved range.</li>
<li>The <code>UDP4-LISTEN:123</code> and <code>UDP6-LISTEN:123</code> arguments are the protocol type and port to use. They tell socat to create UDP based sockets on port <code>123</code> on both IPv4 and IPv6 interfaces, and to listen for incoming data. Again any port in the entire range of 0-65535 is a valid parameter for UDP sockets.</li>
<li>The <code>fork</code>, <code>ipv6only=1</code>, and <code>/dev/null</code> arguments are used in the same manner as described in the previous TCP example.</li>
</ul>

<p>Now that you have two <code>socat</code> processes listening on UDP port <code>123</code>, you can examine the sockets using the <code>ss</code> and <code>nc</code> utilities.</p>

<h3 id="examining-datagram-sockets">Examining Datagram Sockets</h3>

<p>To examine UDP sockets on a modern Linux system using the <code>ss</code> command, run it with the following <code>-4</code>, -6<code>, and</code>uln` flags to restrict the output:</p>

<p>The <code>u</code> flag limits the output to UDP sockets.<br>
The other flags are the same as the ones used in the previous TCP example.</p>

<p>First run the <code>ss -4  -uln</code> command to examine the IPv4 UDP sockets that are listening for connections on your system:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ss -4 -uln
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>State      Recv-Q     Send-Q         Local Address:Port           Peer Address:Port     Process     
. . .
UNCONN   0        0                 <span class="highlight">0.0.0.0:123</span>            0.0.0.0:*
. . .
</code></pre>
<p>There may be other lines with other ports in your output depending on which services are running on your system. The highlighted <code>0.0.0.0:123</code> portion of the output indicates the IPv4 UDP socket is available on all IPv4 interfaces on port <code>123</code>. A service that is only available on a specific IPv4 address will show only that IP in the highlighted field, for example <code>203.0.113.1:123</code>.</p>

<p>Now run the same <code>ss</code> command again but with the <code>-6</code> flag:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ss -6 -uln
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process  
. . .
UNCONN   0        0                   <span class="highlight">[::]:123</span>              [::]:*
. . .
</code></pre>
<p>There may be other lines with other ports in your output depending on which services are running on your system. The highlighted <code>[::]:123</code> portion of the output indicates the IPv6 TCP socket is available on all IPv6 interfaces on port <code>123</code> (as indicated by the <code>::</code> characters). A service that is only available on a specific IPv6 address will show only that IP in the highlighted field, for example <code>[2604:a880:400:d1::3d3:6001]:123</code>.</p>

<h3 id="testing-datagram-sockets">Testing Datagram Sockets</h3>

<p>Now that you are familiar with how to create and enumerate UDP sockets on both IPv4 and IPv6 interfaces, you can experiment with connecting to them. As with TCP sockets, you can experiment with UDP sockets using the netcat utility. </p>

<p>To connect to the example UDP socket on port <code>123</code> that you created in the previous section of this tutorial, run the following netcat command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -4 -u -vz 127.0.0.1 123
</li></ul></code></pre>
<ul>
<li>The <code>-4</code> flag tells netcat to use IPv4.</li>
<li>The <code>-u</code> option instructs netcat to use UDP instead of TCP.</li>
<li>The <code>-v</code> flag is used to print verbose output to your terminal.</li>
<li>The -<code>z</code> option ensures that netcat only connects to a socket, without sending any data.</li>
<li>The local loopback <code>127.0.0.1</code> IP address is used since your system will have its own unique IP address. If you know the IP for your system you can test using that as well. For example, if your system’s public or private IP address is <code>203.0.113.1</code> you could use that in place of the loopback IP. </li>
</ul>

<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">Connection to 127.0.0.1 123 port [udp/ntp] succeeded!</span>
</code></pre>
<p>The output indicates that netcat <em>did not receive an error</em> from the UDP socket listening on the loopback <code>127.0.0.1</code> IPv4 address on port <code>123</code>. This lack of an error response is used to infer that the socket at <code>127.0.0.1:123</code> is available. This behaviour is different from TCP sockets, which need to exchange packets to confirm if a socket is available.</p>

<span class='note'><p>
<strong>Note:</strong> If the socket in this example was unavailable, the remote system would return an <a href="https://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml#icmp-parameters-codes-3">ICMP type 3 message (Destination Unreachable) with a Code of 3</a>, indicating that the port is unreachable on the remote host.</p>

<p>Inferring that a socket is available based on a lack of error response assumes that there are no firewalls or connectivity issues that are blocking ICMP traffic. Without sending, receiving, and verifying application data over a UDP socket, there is no guarantee that a remote UDP port is open and accepting packets.<br></p></span>

<p>Now you can repeat the same connection test but using IPv6. Run the following netcat command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -6 -u -vz ::1 123
</li></ul></code></pre>
<p>You should receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">Connection to ::1 123 port [udp/ntp] succeeded!!</span>
</code></pre>
<p>The output indicates that netcat <em>did not receive an error</em> from the UDP socket listening on the loopback <code>::1</code> IPv6 address on port <code>123</code>. Again, this lack of an error response is used to infer that the socket at <code>::1:123</code> is available. </p>

<p>To clean up your sockets, you’ll need to run the <code>fg</code> (foreground) command for each socat process that you created. Then you’ll use <code>CTRL+C</code> to close each socat.</p>

<p>Run <code>fg</code> to bring the second IPv6 <code>socat</code> instance to the foreground of your terminal. Then run <code>CTRL+C</code> to close it.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">fg
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>sudo socat UDP6-LISTEN:123,ipv6only=1,fork /dev/null
</code></pre>
<p>Press <code>CTRL+C</code> to stop the process.</p>

<p>Now run <code>fg</code> again to clean up the first IPv4 socket. You will have output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>sudo socat UDP4-LISTEN:123,fork /dev/null
</code></pre>
<p>Press <code>CTRL+C</code> to stop the process.</p>

<p>You have now created, examined, and tested IPv4 and IPv6 UDP sockets on your system. Try experimenting with each tool to become more familiar with how they can be used to test and troubleshoot UDP sockets.</p>

<h2 id="what-is-a-unix-domain-socket">What is a Unix Domain Socket?</h2>

<p>Programs that run on the same server can also communicate with each other using Unix Domain Sockets (UDS). Unix Domain Sockets can be stream-based, or datagram-based. When using domain sockets, data is exchanged between programs directly in the operating system’s kernel via files on the host filesystem. To send or receive data using domain sockets, programs read and write to their shared socket file, bypassing network based sockets and protocols entirely. </p>

<p>Unix Domain Sockets are used widely by database systems that do not need to be connected to a network interface. For example, MySQL on Ubuntu defaults to using a file named <code>/var/run/mysqld/mysql.sock</code> for communication with local clients. Clients read from and write to the socket, as does the MySQL server itself.</p>

<p>PostgreSQL is another database system that uses a socket for local, non-network communication. Typically it defaults to using <code>/run/postgresql/.s.PGSQL.5432</code> as its socket file.</p>

<h3 id="creating-unix-domain-sockets">Creating Unix Domain Sockets</h3>

<p>In the previous sections you explored how TCP is used with stream sockets, and how UDP is used with datagram sockets. In this section you’ll use <code>socat</code> to create both stream-based and datagram-based Unix Domain Sockets without using TCP or UDP to encapsulate data to send over networks. Then you’ll examine the sockets that you create using the <code>ss</code>, and <code>nc</code> commands. Finally, you’ll learn about testing Unix Domain Sockets using netcat.</p>

<p>To get started, run the following <code>socat</code> commands to create two socket files:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">socat unix-listen:/tmp/stream.sock,fork /dev/null&amp;
</li><li class="line" data-prefix="$">socat unix-recvfrom:/tmp/datagram.sock,fork /dev/null&amp;
</li></ul></code></pre>
<ul>
<li>The first command instructs socat to create a socket using the <code>unix-listen</code> address type, which will create a stream-based UDS.</li>
<li>The second command specifies <code>unix-recvfrom</code> as the socket type, which will create a datagram-based UDS</li>
<li>Both commands specify a filename after the <code>:</code> separator. The filename is the address of the socket itself. For the first stream example it is <code>/tmp/stream.sock</code> and for the second datagram example it is <code>/tmp/datagram.sock</code>. Note that the name of a socket is arbitrary but it helps if it is descriptive when you are troubleshooting.</li>
<li>The <code>fork</code>, and <code>/dev/null</code> arguments are used in the same manner as described in the  Stream and Datagram socket example sections.</li>
</ul>

<p>Now that you have created your two UDS sockets, you can examine them using the <code>ss</code> and <code>nc</code> utilities.</p>

<h3 id="examining-unix-domain-sockets">Examining Unix Domain Sockets</h3>

<p>To list all listening Unix Domain Sockets, run the <code>ss -xln</code> command. The <code>x</code> flag ensures that only domain sockets are displayed.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ss -xln
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Netid State  Recv-Q Send-Q      Local Address:Port   Peer Address:Port  Process
. . .
<span class="highlight">u_str</span> LISTEN 0      5        <span class="highlight">/tmp/stream.sock</span> 436470            * 0             
<span class="highlight">u_dgr</span> UNCONN 0      0      <span class="highlight">/tmp/datagram.sock</span> 433843            * 0
 . . .
</code></pre>
<p>Notice the highlighted <code><span class="highlight">u_str</span></code> portion of the <code>/tmp/stream/sock</code> line. This field indicates that the socket type is a stream-based UDS. The second line shows the type is <code><span class="highlight">u_dgr</span></code> which means the socket type is datagram-based.</p>

<p>Since Unix Domain Sockets are files, the usual Linux user and group permissions and access controls can be used to restrict who can connect to the socket. You can also use filesystem tools like <code>ls</code>, <code>mv</code>, <code>chown</code> and <code>chmod</code> to examine and manipulate UDS files. Tools like SELinux can also be used to label UDS files with different security contexts.</p>

<p>To check if a file is a UDS socket, use the <code>ls</code>, <code>file</code> or <code>stat</code> utilities. However, it is important to note that none of these tools can determine if a UDS is stream or datagram-based. Use the <code>ss</code> tool for the most complete information about a Unix Domain Socket.</p>

<p>To examine a socket on the filesystem, the <code>stat</code> utility shows the most relevant information. Run it on the sockets that you created earlier:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">stat /tmp/stream.sock /tmp/datagram.sock
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>  File: <span class="highlight">/tmp/stream.sock</span>
  Size: 0             Blocks: 1          IO Block: 131072 <span class="highlight">socket</span>
Device: 48h/72d    Inode: 1742        Links: 1
Access: (0755/<span class="highlight">s</span>rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-01 18:10:25.025755168 +0000
Modify: 2021-03-01 18:10:25.025755168 +0000
Change: 2021-03-01 18:22:42.678231700 +0000
 Birth: -
  File: <span class="highlight">/tmp/datagram.sock</span>
  Size: 0             Blocks: 1          IO Block: 131072 <span class="highlight">socket</span>
Device: 48h/72d    Inode: 1743        Links: 1
Access: (0755/<span class="highlight">s</span>rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-01 18:10:25.025755168 +0000
Modify: 2021-03-01 18:10:25.025755168 +0000
Change: 2021-03-01 18:10:25.025755168 +0000
 Birth: -
</code></pre>
<p>Notice that for each file, the type is <code>socket</code> (highlighted at the far right of the output) and the access mode has an <code>s</code> character preceding the file’s permissions.</p>

<p>The <code>ls</code> utility will also indicate if a file is a socket. Run <code>ls -l</code> to examine the files:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ls -l /tmp/stream.sock /tmp/datagram.sock
</li></ul></code></pre>
<p>You will receive output like the following. Again note that for sockets, the file mode includes the <code>s</code> character before the file permission fields:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div><span class="highlight">s</span>rwxr-xr-x 1 root root 0 Mar  1 18:10 /tmp/datagram.sock
<span class="highlight">s</span>rwxr-xr-x 1 root root 0 Mar  1 18:10 /tmp/stream.sock
</code></pre>
<p>Now that you have created Unix Domain Sockets, and learned how to examine them using the <code>ss</code> and various filesystem based tools, the next step is to test the sockets using a tool like netcat.</p>

<h3 id="testing-unix-domain-sockets">Testing Unix Domain Sockets</h3>

<p>The netcat utility can be used to connect to Unix Domain Sockets, as well as TCP and UDP sockets that you already learned about earlier in this tutorial. To connect to the example sockets that you created, you will need to specify an extra <code>-U</code> flag when running the netcat command. This flag tells netcat to connect to a UDS, as opposed to a TCP or UDP based network socket.</p>

<p>Additionally, if the socket is datagram-based, you will use the <code>-u</code> flag to instruct netcat to use datagrams as we learned in the Datagram Socket section of this tutorial.</p>

<p>Let’s start examining UDS sockets by connecting to the stream-based socket with the following command:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -U -z /tmp/stream.sock
</li></ul></code></pre>
<p>The <code>-U</code> tells netcat that it is connecting to a Unix Domain Socket.<br>
The -<code>z</code> option ensures that netcat only connects to a socket, without sending any data.<br>
The <code>/tmp/stream.sock</code> is the address of the socket on the filesystem.</p>

<p>You will not receive any output from netcat when you run the command. However, if a socket is <em>not</em> available, netcat will output an error message like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>nc: unix connect failed: No such file or directory
nc: /tmp/stream.sock: No such file or directory
</code></pre>
<p>So the absence of output from netcat when testing a stream-based UDS socket means that the connection was successful.</p>

<p>Repeat the testing process, this time for the datagram-based UDS:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nc -uU -z /tmp/datagram.sock
</li></ul></code></pre>
<p>The additional <code>-u</code> flag is added to tell netcat that the remote socket is a datagram socket. Again, you will not receive any output if the test is successful.</p>

<p>If there is no socket at the address, you will receive an error like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>nc: unix connect failed: No such file or directory
nc: /tmp/datagram.sock: No such file or directory
</code></pre>
<p>To clean up your sockets, you’ll need to run the <code>fg</code> (foreground) command for each socat process that you created. Then you’ll use <code>CTRL+C</code> to close each socat.</p>

<p>Run <code>fg</code> to bring the datagram-based <code>socat</code> instance to the foreground of your terminal:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">fg
</li></ul></code></pre>
<p>You will receive output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>socat unix-recvfrom:/tmp/datagram.sock,fork /dev/null
</code></pre>
<p>Run <code>CTRL+C</code> to close it. You will not receive any output.</p>

<p>Now run <code>fg</code> again to clean up the first stream-based UDS socket. </p>

<p>Again you should have output like the following:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>socat unix-listen:/tmp/stream.sock,fork /dev/null
</code></pre>
<p>Run <code>CTRL+C</code> to end the process. You will not receive any output.</p>

<p>You have now created, examined, and tested Unix Datagram Sockets sockets on your system. Try experimenting with netcat and <code>socat</code> to become more familiar with how you can send and receive data over a UDS, as well as how you can test and troubleshoot Unix Domain sockets.</p>

<h2 id="conclusion">Conclusion</h2>

<p>In this tutorial you explored how different kinds of sockets are used on a Linux system. You learned about stream-based sockets, which typically use TCP for network communication. You also learned about datagram-based sockets, which use UDP to send data over networks. Finally, you explored how Unix Domain Sockets can be either stream or datagram-based on a local server.</p>

<p>In each section you used the <code>ss</code> utility to gather information about sockets on a Linux system. You learned how the different flags that the <code>ss</code> tool provides can help you limit its output to specific types of sockets when you are examining sockets on a system.</p>

<p>Finally, you used the netcat and <code>socat</code> tools to create and connect to each of the three different types of sockets discussed in this tutorial. The netcat utility is widely used to connect to sockets, but it can also create sockets. Its documentation (<code>man nc</code>) contains many examples of how it can be used in either mode. The <code>socat</code> utility is a more advanced tool that can be used to connect to and many different types of sockets that are not covered in this tutorial. Its documentation (<code>man socat</code>) also contains numerous examples of the different ways that it can be used.</p>

<p>Understanding what sockets are and how they work is a core system administration skill. The tools and techniques that you experimented with in this tutorial will help you become more familiar with sockets, and how to troubleshoot them if your servers and applications are not communicating with each other correctly.</p>
