---
title : "Cómo usar ps, kill y nice para administrar procesos en Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux-es
image: "https://sergio.afanou.com/assets/images/image-midres-18.jpg"
---

<h3 id="introducción">Introducción</h3>

<hr>

<p>Un servidor Linux, como cualquier otro equipo con el que pueda estar familiarizado, ejecuta aplicaciones. Para el equipo, estos se consideran &ldquo;procesos&rdquo;.</p>

<p>Mientras que Linux se encargará de la administración de bajo nivel, entre bastidores, en el ciclo de vida de un proceso, se necesitará una forma de interactuar con el sistema operativo para administrarlo desde un nivel superior.</p>

<p>En esta guía, explicaremos algunos aspectos sencillos de la administración de procesos. Linux proporciona una amplia colección de herramientas para este propósito.</p>

<p>Exploraremos estas ideas en un VPS de Ubuntu 12.04, pero cualquier distribución moderna de Linux funcionará de manera similar.</p>

<h2 id="cómo-ver-los-procesos-en-ejecución-en-linux">Cómo ver los procesos en ejecución en Linux</h2>

<hr>

<h3 id="top">top</h3>

<hr>

<p>La forma más sencilla de averiguar qué procesos se están ejecutando en su servidor es ejecutar el comando <code>top</code>:</p>
<pre class="code-pre "><code>top***

top - 15:14:40 up 46 min,  1 user,  load average: 0.00, 0.01, 0.05 Tasks:  56 total,   1 running,  55 sleeping,   0 stopped,   0 zombie Cpu(s):  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k total,   316576k used,   703024k free,     7652k buffers Swap:        0k total,        0k used,        0k free,   258976k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND               1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.07 ksoftirqd/0            6 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0            7 root      RT   0     0    0    0 S  0.0  0.0   0:00.03 watchdog/0             8 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 cpuset                 9 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 khelper               10 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kdevtmpfs
</code></pre>
<p>La parte superior de la información ofrece estadísticas del sistema, como la carga del sistema y el número total de tareas.</p>

<p>Se puede ver fácilmente que hay 1 proceso en ejecución y 55 procesos en reposo (es decir, inactivos/que no usan los recursos de la CPU).</p>

<p>La parte inferior tiene los procesos en ejecución y las estadísticas de uso.</p>

<h3 id="htop">htop</h3>

<hr>

<p>Una versión mejorada de <code>top</code>, llamada <code>htop</code>, está disponible en los repositorios. Instálelo con este comando:</p>
<pre class="code-pre "><code>sudo apt-get install htop
</code></pre>
<p>Si ejecutamos el comando <code>htop</code>, veremos que hay una pantalla más fácil de usar:</p>
<pre class="code-pre "><code>htop***

  Mem[|||||||||||           49/995MB]     Load average: 0.00 0.03 0.05   CPU[                          0.0%]     Tasks: 21, 3 thr; 1 running   Swp[                         0/0MB]     Uptime: 00:58:11   PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command  1259 root       20   0 25660  1880  1368 R  0.0  0.2  0:00.06 htop     1 root       20   0 24188  2120  1300 S  0.0  0.2  0:00.56 /sbin/init   311 root       20   0 17224   636   440 S  0.0  0.1  0:00.07 upstart-udev-brid   314 root       20   0 21592  1280   760 S  0.0  0.1  0:00.06 /sbin/udevd --dae   389 messagebu  20   0 23808   688   444 S  0.0  0.1  0:00.01 dbus-daemon --sys   407 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.02 rsyslogd -c5   408 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   409 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   406 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.04 rsyslogd -c5   553 root       20   0 15180   400   204 S  0.0  0.0  0:00.01 upstart-socket-br
</code></pre>
<p>Puede <a href="https://www.digitalocean.com/community/articles/how-to-use-top-netstat-du-other-tools-to-monitor-server-resources#process">aprender más sobre cómo usar top y htop</a> aquí.</p>

<h2 id="cómo-usar-ps-para-realizar-una-lista-de-procesos">Cómo usar ps para realizar una lista de procesos</h2>

<hr>

<p><code>top</code> y <code>htop</code> proporcionan una buena interfaz para ver los procesos en ejecución, similar a la de un administrador de tareas gráfico.</p>

<p>Sin embargo, estas herramientas no siempre son lo suficientemente flexibles para cubrir adecuadamente todos los escenarios. Un poderoso comando llamado <code>ps</code> generalmente es la respuesta a estos problemas.</p>

<p>Cuando se invoca sin argumentos, el resultado puede ser un poco escaso:</p>
<pre class="code-pre "><code>ps***

  PID TTY          TIME CMD  1017 pts/0    00:00:00 bash  1262 pts/0    00:00:00 ps
</code></pre>
<p>Este resultado muestra todos los procesos asociados con el usuario actual y la sesión de terminal. Eso tiene sentido porque, actualmente, solo estamos ejecutando <code>bash</code> y <code>ps</code> con este terminal.</p>

<p>Para obtener un panorama más completo de los procesos en este sistema, podemos ejecutar lo siguiente:</p>
<pre class="code-pre "><code>ps aux***

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND root         1  0.0  0.2  24188  2120 ?        Ss   14:28   0:00 /sbin/init root         2  0.0  0.0      0     0 ?        S    14:28   0:00 [kthreadd] root         3  0.0  0.0      0     0 ?        S    14:28   0:00 [ksoftirqd/0] root         6  0.0  0.0      0     0 ?        S    14:28   0:00 [migration/0] root         7  0.0  0.0      0     0 ?        S    14:28   0:00 [watchdog/0] root         8  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [cpuset] root         9  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [khelper] . . .
</code></pre>
<p>Estas opciones ordenan a <code>ps</code> que muestre los procesos de propiedad de todos los usuarios (independientemente de su asociación con el terminal) en un formato fácil de usar.</p>

<p>Para ver una vista <em>de estructura jerárquica</em>, en la que se ilustran las relaciones jerárquicas, podemos ejecutar el comando con las siguientes opciones:</p>
<pre class="code-pre "><code>ps axjf***

 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND     0     2     0     0 ?           -1 S        0   0:00 [kthreadd]     2     3     0     0 ?           -1 S        0   0:00  \_ [ksoftirqd/0]     2     6     0     0 ?           -1 S        0   0:00  \_ [migration/0]     2     7     0     0 ?           -1 S        0   0:00  \_ [watchdog/0]     2     8     0     0 ?           -1 S&lt;       0   0:00  \_ [cpuset]     2     9     0     0 ?           -1 S&lt;       0   0:00  \_ [khelper]     2    10     0     0 ?           -1 S        0   0:00  \_ [kdevtmpfs]     2    11     0     0 ?           -1 S&lt;       0   0:00  \_ [netns] . . .
</code></pre>
<p>Como puede ver, el proceso <code>kthreadd</code> se muestra como proceso principal de <code>kstadd/0</code> y los demás.</p>

<h3 id="una-nota-sobre-las-id-de-procesos">Una nota sobre las ID de procesos</h3>

<hr>

<p>En Linux y sistemas tipo Unix, a cada proceso se le asigna un <strong>ID de proceso</strong> o <strong>PID</strong>. Esta es la forma en que el sistema operativo identifica y realiza un seguimiento de los procesos.</p>

<p>Una forma rápida de obtener el PID de un proceso es con el comando <code>pgrep</code>:</p>
<pre class="code-pre "><code>pgrep bash***

1017
</code></pre>
<p>Esto simplemente consultará el ID del proceso y lo mostrará en el resultado.</p>

<p>El primer proceso que se generó en el arranque, llamado <em>init</em>, recibe el PID &ldquo;1&rdquo;.</p>
<pre class="code-pre "><code>pgrep init***

1
</code></pre>
<p>Entonces, este proceso es responsable de engendrar todos los demás procesos del sistema. Los procesos posteriores reciben números PID mayores.</p>

<p>Un proceso <em>principal</em> es el proceso que se encargó de generarlo. Los procesos principales tienen un <strong>PPID</strong>, que puede ver en los encabezados de las columnas en muchas aplicaciones de administración de procesos, incluidos <code>top</code>, <code>htop</code> y <code>ps</code>.</p>

<p>Cualquier comunicación entre el usuario y el sistema operativo sobre los procesos implica traducir entre los nombres de procesos y los PID en algún momento durante la operación. Este es el motivo por el que las utilidades le indican el PID.</p>

<h3 id="las-relaciones-principal-secundario">Las relaciones principal-secundario</h3>

<hr>

<p>Para crear un proceso secundario se deben seguir dos pasos: fork(), que crea un nuevo espacio de direcciones y copia los recursos propiedad del principal mediante copy-on-write para que estén disponibles para el proceso secundario; y exec(), que carga un ejecutable en el espacio de direcciones y lo ejecuta.</p>

<p>En caso de que un proceso secundario muera antes que su proceso principal, el proceso secundario se convierte en un zombi hasta que el principal haya recopilado información sobre él o haya indicado al núcleo que no necesita esa información. Luego, los recursos del proceso secundario se liberarán. Sin embargo, si el proceso principal muere antes que el secundario, init adoptará el secundario, aunque también puede reasignarse a otro proceso.</p>

<h2 id="cómo-enviar-señales-a-los-procesos-en-linux">Cómo enviar señales a los procesos en Linux</h2>

<hr>

<p>Todos los procesos en Linux responden a <em>señales</em>. Las señales son una forma de decirle a los programas que terminen o modifiquen su comportamiento.</p>

<h3 id="cómo-enviar-señales-a-los-procesos-por-pid">Cómo enviar señales a los procesos por PID</h3>

<hr>

<p>La forma más común de pasar señales a un programa es con el comando <code>kill</code>.</p>

<p>Como es de esperar, la funcionalidad predeterminada de esta utilidad es intentar matar un proceso:</p>

<pre>kill <span class="highlight">PID_of_target_process</span></pre>

<p>Esto envía la señal <strong>TERM</strong> al proceso. La señal TERM indica al proceso debe terminar. Esto permite que el programa realice operaciones de limpieza y cierre sin problemas.</p>

<p>Si el programa tiene un mal comportamiento y no se cierra cuando se le da la señal TERM, podemos escalar la señal pasando la señal <code>KILL</code>:</p>

<pre>kill -KILL <span class="highlight">PID_of_target_process</span></pre>

<p>Esta es una señal especial que no se envía al programa.</p>

<p>En su lugar, se envía al núcleo del sistema operativo, que cierra el proceso. Eso se utiliza para eludir los programas que ignoran las señales que se les envían.</p>

<p>Cada señal tiene un número asociado que puede pasar en vez del nombre. Por ejemplo, puede pasar &ldquo;-15&rdquo; en lugar de &ldquo;-TERM&rdquo; y &ldquo;-9&rdquo; en lugar de &ldquo;-KILL&rdquo;.</p>

<h3 id="cómo-usar-señales-para-otros-fines">Cómo usar señales para otros fines</h3>

<hr>

<p>Las señales no solo se utilizan para cerrar programas. También pueden usarse para realizar otras acciones.</p>

<p>Por ejemplo, muchos demonios se reinician cuando reciben la señal <code>HUP</code> o la señal de colgar. Apache es un programa que funciona así.</p>

<pre>sudo kill -HUP <span class="highlight">pid_of_apache</span></pre>

<p>El comando anterior hará que Apache vuelva a cargar su archivo de configuración y reanude sirviendo contenidos.</p>

<p>Puede enumerar todas las señales que puede enviar con kill escribiendo lo siguiente:</p>
<pre class="code-pre "><code>kill -l***

1) SIGHUP    2) SIGINT   3) SIGQUIT  4) SIGILL   5) SIGTRAP  6) SIGABRT  7) SIGBUS   8) SIGFPE   9) SIGKILL 10) SIGUSR1 11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM . . .
</code></pre>
<h3 id="cómo-enviar-señales-a-los-procesos-por-nombre">Cómo enviar señales a los procesos por nombre</h3>

<hr>

<p>Aunque la forma convencional de enviar señales es mediante el uso de PID, también hay métodos para hacerlo con nombres de procesos regulares.</p>

<p>El comando <code>pkill</code> funciona casi exactamente igual que <code>kill</code>, pero funciona con un nombre de proceso en su lugar:</p>
<pre class="code-pre "><code>pkill -9 ping
</code></pre>
<p>El comando anterior es el equivalente a:</p>
<pre class="code-pre "><code>kill -9 `pgrep ping`
</code></pre>
<p>Si quiere enviar una señal a todas las instancias de un determinado proceso, puede utilizar el comando <code>killall</code>:</p>
<pre class="code-pre "><code>killall firefox
</code></pre>
<p>El comando anterior enviará la señal TERM a todas las instancias de firefox que se estén ejecutando en el equipo.</p>

<h2 id="cómo-ajustar-las-prioridades-de-los-procesos">Cómo ajustar las prioridades de los procesos</h2>

<hr>

<p>A menudo, querrá ajustar qué procesos reciben prioridad en un entorno de servidor.</p>

<p>Algunos procesos pueden considerarse como una misión crítica para su situación, mientras que otros pueden ejecutarse siempre que haya recursos sobrantes.</p>

<p>Linux controla la prioridad a través de un valor llamado <strong>niceness</strong>.</p>

<p>Las tareas de alta prioridad se consideran menos <em>buenas</em> porque no comparten los recursos tan bien. Por otro lado, los procesos de baja prioridad son <em>buenos</em> porque insisten en tomar solo los recursos mínimos.</p>

<p>Cuando ejecutamos <code>top</code> al principio del artículo, había una columna marcada como &ldquo;NI&rdquo;. Este es el valor <em>bueno</em> del proceso:</p>
<pre class="code-pre "><code>top***

Tasks: 56 total, 1 running, 55 sleeping, 0 stopped, 0 zombie Cpu(s):  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k total,   324496k used,   695104k free,     8512k buffers Swap:        0k total,        0k used,        0k free,   264812k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            1635 root      20   0 17300 1200  920 R  0.3  0.1   0:00.01 top                    1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.11 ksoftirqd/0
</code></pre>
<p>Los valores buenos pueden oscilar entre &ldquo;-19/-20&rdquo; (máxima prioridad) y &ldquo;19/20&rdquo; (mínima prioridad) dependiendo del sistema.</p>

<p>Para ejecutar un programa con un determinado valor bueno, podemos usar el comando <code>nice</code>:</p>

<pre>nice -n 15 <span class="highlight">command_to_execute</span></pre>

<p>Esto solo funciona cuando se inicia un nuevo programa.</p>

<p>Para alterar el valor bueno de un programa que ya se está ejecutando, usamos una herramienta llamada <code>renice</code>:</p>

<pre>renice 0 <span class="highlight">PID_to_prioritize</span></pre>

<p><strong>Nota: Mientras que nice funciona necesariamente con un nombre de comando, renice funciona invocando al PID del proceso</strong></p>

<h2 id="conclusión">Conclusión</h2>

<hr>

<p>La administración de procesos es un tema que a veces resulta difícil de entender para los nuevos usuarios, debido a que las herramientas utilizadas son diferentes a sus contrapartes gráficas.</p>

<p>Sin embargo, las ideas son familiares e intuitivas, y, con un poco de práctica, se convertirá en algo natural. Dado que los procesos interviene en todo lo que se hace con un sistema informático, aprender a controlarlos de forma eficaz es una habilidad esencial.</p>

<div class="author">Por Justin Ellingwood</div>
