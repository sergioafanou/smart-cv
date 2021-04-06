---
title : "Cómo usar Journalctl para ver y manipular los registros de Systemd"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs-es
image: "https://sergio.afanou.com/assets/images/image-midres-39.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Algunas de las ventajas más convincentes de <code>systemd</code> son las que se relacionan con el proceso y registro del sistema. Al usar otros sistemas, los registros suelen estar dispersos por todo el sistema, son manejados por diferentes demonios y herramientas, y puede ser bastante difícil de interpretar cuando abarcan varias aplicaciones. <code>Systemd</code> intenta resolver estos problemas al proporcionar una solución de administración centralizada para registrar todos los procesos del kernel y del usuario. El sistema que recopila y administra estos registros se conoce como diario.</p>

<p>El diario se implementa con el demonio <code>journald</code>, que gestiona todos los mensajes producidos por el kernel, initrd, servicios, etc. En esta guía, veremos cómo usar la utilidad <code>journalctl</code>, que puede usarse para acceder y manipular los datos que se encuentran dentro del diario.</p>

<h2 id="idea-general">Idea general</h2>

<p>Uno de los impulsos detrás del diario <code>systemd</code> es centralizar la administración de los registros, independientemente de dónde se originen los mensajes. Dado que el proceso <code>systemd</code> gestiona gran parte del proceso de arranque y administración de servicios, tiene sentido estandarizar la forma en que se recopila y acceden a estos registros. El demonio <code>journald</code> recopila datos de todas las fuentes disponibles y los almacena en un formato binario para una manipulación fácil y dinámica.</p>

<p>Eso nos proporciona varias ventajas significativas. Al interactuar con los datos usando una sola utilidad, los administradores pueden mostrar datos de registro de forma dinámica según sus necesidades. Esto puede ser tan simple como ver los datos del arranque de hace tres arranques o combinar las entradas de registro de forma secuencial de dos servicios relacionados para depurar un problema de comunicación.</p>

<p>Almacenar los datos de registro en formato binario también significa que los datos pueden mostrarse en formatos de salida arbitrarios dependiendo de lo que se necesite en el momento. Por ejemplo, para la gestión de los registros diarios puede que esté acostumbrado a ver los registros en el formato estándar de <code>syslog</code>, pero si decide graficar las interrupciones del servicio más adelante, puede mostrar cada entrada como un objeto JSON para que sea consumible en su servicio gráfico. Dado que los datos no se escriben en el disco en texto plano, no es necesario convertirlos cuando se necesita un distinto formato bajo demanda.</p>

<p>El diario <code>systemd</code> puede usarse con una implementación de <code>syslog</code> existente o puede sustituir la funcionalidad <code>syslog</code>, según sus necesidades. Aunque el diario <code>systemd</code> cubrirá la mayoría de las necesidades de registro del administrador, también puede complementar los mecanismos de registro existentes. Por ejemplo, es posible que tenga un servidor <code>syslog</code> centralizado que utilice para compilar datos de varios servidores, pero también es posible que quiera interconectar los registros de varios servicios en un solo sistema con el diario <code>systemd</code>. Puede hacer ambas cosas combinando estas tecnologías.</p>

<h2 id="configurar-la-hora-del-sistema">Configurar la hora del sistema</h2>

<p>Una de las ventajas de usar un diario binario para el registro es la posibilidad de ver los registros en hora UTC o local libremente. <code>systemd</code> mostrará los resultados en hora local de manera predeterminada.</p>

<p>Debido a esto, antes de comenzar con el diario, nos aseguraremos de que la zona horaria esté configurada correctamente. El paquete de <code>systemd</code> realmente viene con una herramienta llamada <code>timedatectl</code> que puede ayudar con esto.</p>

<p>Primero, consulte qué zonas horarias están disponibles con la opción <code>list-timezones</code>:</p>
<pre class="code-pre "><code>timedatectl list-timezones
</code></pre>
<p>Esto mostrará la lista de zonas horarias disponibles en su sistema. Cuando encuentre la que coincida con la ubicación de su servidor, puede configurarla usando la opción <code>set-timezone</code>:</p>
<pre class="code-pre "><code>sudo timedatectl set-timezone <span class="highlight">zone</span>
</code></pre>
<p>Para asegurarse de que su máquina esté usando la hora correcta actual, utilice el comando <code>timedatectl</code> solo o con la opción <code>status</code>. En la pantalla, se mostrará lo mismo:</p>
<pre class="code-pre "><code>timedatectl status
</code></pre><pre class="code-pre "><code>      <span class="highlight">Local time: Thu 2015-02-05 14:08:06 EST</span>
  Universal time: Thu 2015-02-05 19:08:06 UTC
        RTC time: Thu 2015-02-05 19:08:06
       Time zone: America/New_York (EST, -0500)
     NTP enabled: no
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
</code></pre>
<p>La primera línea debe mostrar la hora correcta.</p>

<h2 id="visualización-básica-de-registros">Visualización básica de registros</h2>

<p>Para ver los registros que el demonio <code>journald</code> ha recopila el comando <code>journalctl</code>.</p>

<p>Cuando se utilice solo, cada entrada de diario que se encuentre en el sistema se mostrará dentro de un localizador (por lo general, <code>less</code>) para que pueda navegar en él Las entradas más antiguas estarán arriba:</p>
<pre class="code-pre "><code>journalctl
</code></pre><pre class="code-pre "><code>-- Logs begin at Tue 2015-02-03 21:48:52 UTC, end at Tue 2015-02-03 22:29:38 UTC. --
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journald[139]: Received SIGTERM from PID 1 (systemd).
Feb 03 21:48:52 localhost.localdomain kernel: audit: type=1404 audit(1423000132.274:2): enforcing=1 old_en
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: input: ImExPS/2 Generic Explorer Mouse as /devices/platform/
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  8 users, 102 roles, 4976 types, 294 bools, 1 sens,
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  83 classes, 104131 rules

. . .
</code></pre>
<p>Es probable que tenga que desplazarse por páginas y páginas de datos, que pueden ser decenas o cientos de miles de líneas si <code>systemd</code> ha estado en su sistema por mucho tiempo. Esto demuestra la cantidad de datos disponibles en la base de datos del diario.</p>

<p>El formato será familiar para aquellos que se utilizan para el registro <code>syslog</code> estándar. Sin embargo, esto realmente recopila datos de más fuentes de las que las implementaciones <code>syslog</code> tradicionales son capaces de recopilar. Incluye registros del proceso de arranque inicial, el kernel, el initrd, el error de estándar de la aplicación y la salida. Todos ellos están disponibles en el diario.</p>

<p>Es posible que observe que todas las marcas de tiempo que se muestran son de hora local. Esto está disponible para cada entrada de registro ahora que tenemos nuestro tiempo local correctamente configurado en el sistema. Todos los registros se muestran usando esta nueva información.</p>

<p>Si desea mostrar las marcas de tiempo en hora UTC, puede usar el indicador <code>--utc</code>:</p>
<pre class="code-pre "><code>journalctl --utc
</code></pre>
<h2 id="filtrado-del-diario-por-hora">Filtrado del diario por hora</h2>

<p>A pesar de que tener acceso a una colección tan grande de datos es definitivamente útil, es difícil o imposible inspeccionar y procesar mentalmente tal gran cantidad de datos. Debido a esto, una de las características más importantes de <code>journalctl</code> son sus opciones de filtrado.</p>

<h3 id="mostrar-los-registros-del-arranque-actual">Mostrar los registros del arranque actual</h3>

<p>La más básica de estas que puede usar diariamente, es el indicador <code>-b</code>. Este indicador mostrará todas las entradas del diario que se recopilaron desde el reinicio más reciente.</p>
<pre class="code-pre "><code>journalctl -b
</code></pre>
<p>Esto le ayudará a identificar y administrar la información que sea pertinente para su entorno actual.</p>

<p>En los casos en que no se usa esta función y se muestran más de un día de arranques, verá que <code>journalctl</code> insertó una línea parecida a la siguiente cada vez que el sistema se cae:</p>
<pre class="code-pre "><code>. . .

-- Reboot --

. . .
</code></pre>
<p>Lo puede usar para separar la información de manera lógica en sesiones de arranque.</p>

<h3 id="arranques-anteriores">Arranques anteriores</h3>

<p>Aunque por lo general querrá mostrar la información del arranque actual, hay ocasiones en que los arranques anteriores también son útiles. El diario puede guardar la información de varios arranques anteriores, por lo que <code>journalctl</code> puede usarse para mostrar la información más fácilmente.</p>

<p>Algunas distribuciones permiten guardar la información de los arranques anteriores de manera predeterminada, mientras que otras deshabilitan esta función. Para habilitar la información de arranque persistente, puede crear el directorio para almacenar el diario escribiendo lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir -p /var/log/journal
</li></ul></code></pre>
<p>O puede editar el archivo de configuración del diario:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/systemd/journald.conf
</li></ul></code></pre>
<p>En la sección <code>[Journal]</code>, establezca la opción <code>Storage=</code> en &ldquo;persistent&rdquo; (persistente) para habilitar el registro persistente:</p>
<div class="code-label " title="/etc/systemd/journald.conf">/etc/systemd/journald.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
[Journal]
Storage=<span class="highlight">persistent</span>
</code></pre>
<p>Cuando la opción de guardar los arranques anteriores está habilitada en su servidor, <code>journalctl</code> proporciona algunos comandos para ayudarlo a trabajar con los arranques como unidad de división. Para ver los arranques que <code>journald</code> conoce, utilice la opción <code>--list-boots</code> con <code>journalctl</code>:</p>
<pre class="code-pre "><code>journalctl --list-boots
</code></pre><pre class="code-pre "><code>-2 caf0524a1d394ce0bdbcff75b94444fe Tue 2015-02-03 21:48:52 UTC—Tue 2015-02-03 22:17:00 UTC
-1 13883d180dc0420db0abcb5fa26d6198 Tue 2015-02-03 22:17:03 UTC—Tue 2015-02-03 22:19:08 UTC
 0 bed718b17a73415fade0e4e7f4bea609 Tue 2015-02-03 22:19:12 UTC—Tue 2015-02-03 23:01:01 UTC
</code></pre>
<p>Esto mostrará una línea para cada arranque. La primera columna es la compensación del arranque que puede usarse para hacer referencia fácilmente al arranque con <code>journalctl</code>. Si necesita una referencia absoluta, el ID del arranque está en la segunda columna. Puede saber la hora a la que se refiere la sesión de arranque con las dos especificaciones de hora que aparecen al final.</p>

<p>Para mostrar la información de estos arranques, puede usar la información de la primera o la segunda columna.</p>

<p>Por ejemplo, para ver el diario del arranque anterior, utilice el puntero relativo <code>-1</code> con el indicador <code>-b</code>:</p>
<pre class="code-pre "><code>journalctl -b -1
</code></pre>
<p>También puede usar el ID de arranque invocar los datos de un arranque:</p>
<pre class="code-pre "><code>journalctl -b caf0524a1d394ce0bdbcff75b94444fe
</code></pre>
<h3 id="ventanas-de-tiempo">Ventanas de tiempo</h3>

<p>Aunque ver las entradas de registro por arranque es increíblemente útil, en ocasiones es posible que quiera solicitar ventanas de tiempo que no se alinean bien con el arranque del sistema. Este puede ser el caso especialmente cuando se trata de servidores de larga duración con un tiempo de actividad significativo.</p>

<p>Puede filtrar por límites de tiempo arbitrarios usando las opciones <code>--since</code> y <code>--until</code>, que restringen las entradas mostradas a aquellas posteriores o anteriores al tiempo indicado, respectivamente.</p>

<p>Los valores de tiempo pueden venir en varios formatos. Para los valores absolutos de tiempo, debe usar el siguiente formato:</p>
<pre class="code-pre "><code>YYYY-MM-DD HH:MM:SS
</code></pre>
<p>Por ejemplo, podemos ver todas las entradas desde el 10 de enero de 2015 a las 5:15 p. m. escribiendo:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10 17:15:00"
</code></pre>
<p>Si se omiten los componentes del formato anterior, se aplicarán algunos valores predeterminados. Por ejemplo, si se omite la fecha, se asumirá la fecha actual. Si falta el componente que hace referencia a la hora, se sustituirá por “00:00:00” (medianoche). El campo de los segundos también se puede omitir para que aparezca &ldquo;00&rdquo; de manera predeterminada:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10" --until "2015-01-11 03:00"
</code></pre>
<p>El diario también comprende algunos valores relativos y los accesos directos con nombre. Por ejemplo, puede usar las palabras &ldquo;yesterday&rdquo;, &ldquo;today&rdquo;, &ldquo;tomorrow&rdquo;, o &ldquo;now&rdquo;. Puede hacer referencia a tiempos relativos anteponiendo &ldquo;-&rdquo; o &ldquo;+&rdquo; a un valor numérico o utilizando palabras como &ldquo;ago&rdquo; en la construcción de las instrucciones.</p>

<p>Para obtener los datos del día de ayer, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl --since yesterday
</code></pre>
<p>Si recibió informes de una interrupción del servicio que comenzó a las 9:00 a.m. y continuó hasta hace una hora, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl --since 09:00 --until "1 hour ago"
</code></pre>
<p>Como puede ver, es relativamente fácil definir ventanas de tiempo flexibles para filtrar las entradas que desea ver.</p>

<h2 id="filtrado-por-interés-de-mensaje">Filtrado por interés de mensaje</h2>

<p>En la sección anterior hemos aprendido algunas formas de filtrar los datos del diario utilizando restricciones de tiempo. En esta sección, veremos cómo filtrar según el servicio o componente que le interese. El diario <code>systemd</code> proporciona varias formas de hacer esto.</p>

<h3 id="por-unidad">Por unidad</h3>

<p>Probablemente la forma más útil de filtrar es por la unidad que le interesa. Podemos usar la opción <code>-u</code> para filtrar de esta manera.</p>

<p>Por ejemplo, para ver todos los registros de una unidad de Nginx en nuestro sistema, podemos escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl -u nginx.service
</code></pre>
<p>Por lo general, también querrá filtrar por tiempo para mostrar las líneas que le interesa. Por ejemplo, para comprobar cómo se está ejecutando el servicio hoy, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl -u nginx.service --since today
</code></pre>
<p>Este tipo de enfoque resulta extremadamente útil cuando se aprovecha la capacidad del diario para intercalar registros de varias unidades. Por ejemplo, si su proceso de Nginx está conectado a una unidad PHP-FPM para procesar contenido dinámico, puede combinar las entradas de ambos en orden cronológico especificando ambas unidades:</p>
<pre class="code-pre "><code>journalctl -u nginx.service -u php-fpm.service --since today
</code></pre>
<p>Eso puede hacer que sea mucho más fácil detectar las interacciones entre diferentes programas y sistemas de depuración en vez de procesos individuales.</p>

<h3 id="por-proceso-usuario-o-id-de-grupo">Por proceso, usuario o ID de grupo</h3>

<p>Algunos servicios generan varios procesos secundarios para realizar el trabajo. Si ha localizado el PID exacto del proceso que le interesa, también se puede hacer un filtrado basándonos en eso.</p>

<p>Para hacer esto, podemos filtrar especificando el campo <code>_PID</code>. Por ejemplo, si el PID que nos interesa es 8088, podemos escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl _PID=8088
</code></pre>
<p>En otras ocasiones, es posible que quiera mostrar todas las entradas registradas de un usuario o de un grupo específico. Esto puede hacerse con los filtros <code>_UID</code> o <code>_GID</code>. Por ejemplo, si su servidor web se ejecuta bajo el usuario <code>www-data</code>, puede encontrar el ID de usuario escribiendo lo siguiente:</p>
<pre class="code-pre "><code>id -u www-data
</code></pre><pre class="code-pre "><code>33
</code></pre>
<p>Luego, puede usar el ID que salió en la búsqueda para filtrar los resultados del diario:</p>
<pre class="code-pre "><code>journalctl _UID=<span class="highlight">33</span> --since today
</code></pre>
<p>El diario <code>systemd</code> tiene muchos campos que pueden usarse para filtrar. Algunos de estos son pasados desde el proceso que se está registrando y otros son aplicados por <code>journald</code> usando la información que recopila del sistema en el momento del registro.</p>

<p>El símbolo inicial de guion bajo indica que el campo <code>_PID</code> es de este último tipo. El diario registra e indexa automáticamente el PID del proceso que está registrando para filtrarlo después. Puede obtener información sobre todos los campos disponibles del diario escribiendo lo siguiente:</p>
<pre class="code-pre "><code>man systemd.journal-fields
</code></pre>
<p>En esta guía, veremos algunas de estas opciones. Por ahora, sin embargo, vamos a repasar una opción más útil que tiene que ver con el filtrado mediante estos campos. La opción <code>-F</code> puede usarse para mostrar todos los valores disponibles para un determinado campo del diario.</p>

<p>Por ejemplo, para ver qué ID de grupo tiene entradas el diario <code>systemd</code>, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl -F _GID
</code></pre><pre class="code-pre "><code>32
99
102
133
81
84
100
0
124
87
</code></pre>
<p>Esto mostrará todos los valores que el diario almacenó para el campo del ID de grupo. Esto puede ayudarlo a crear sus filtros.</p>

<h3 id="por-ruta-de-componente">Por ruta de componente</h3>

<p>También podemos filtrar proporcionando una ubicación de ruta.</p>

<p>Si la ruta conduce a un ejecutable, <code>journalctl</code> mostrará todas las entradas que implican el ejecutable en cuestión. Por ejemplo, para encontrar aquellas entradas que implican el ejecutable <code>bash</code>, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl /usr/bin/bash
</code></pre>
<p>Por lo general, si una unidad está disponible para el ejecutable, ese método es más limpio y proporciona mejor información (entradas de procesos secundarios asociados, etc.). A veces, sin embargo, eso no es posible.</p>

<h3 id="mostrar-mensajes-de-kernel">Mostrar mensajes de kernel</h3>

<p>Los mensajes de kernel, que suelen encontrarse en el resultado de <code>dmesg</code>, también se pueden recuperar del diario.</p>

<p>Para mostrar solo estos mensajes, podemos añadir los indicadores <code>-k</code> o <code>--dmesg</code> a nuestro comando:</p>
<pre class="code-pre "><code>journalctl -k
</code></pre>
<p>De manera predeterminada, esto mostrará los mensajes del kernel del arranque actual. Puede especificar un arranque alternativo usando los indicadores normales de selección de arranque que se mostraron anteriormente. Por ejemplo, para obtener los mensajes de hace cinco arranques, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl -k -b -5
</code></pre>
<h3 id="por-prioridad">Por prioridad</h3>

<p>Un filtro que suele interesar a los administradores de sistemas es el de prioridad de mensaje. Aunque a menudo es útil registrar información a un nivel muy verboso, cuando realmente se asimila la información disponible, los registros de baja prioridad pueden distraer y confundir.</p>

<p>Puede usar <code>journalctl</code> para mostrar solo mensajes de una prioridad especifica o superior usando la opción <code>-p</code>. Esto le permite filtrar mensajes de menor prioridad.</p>

<p>Por ejemplo, para mostrar solo las entradas registradas en el nivel de error o superior, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>journalctl -p err -b
</code></pre>
<p>Esto le mostrará todos los mensajes marcados como error, crítico, alerta o emergencia. El diario implementa los niveles de mensaje <code>syslog</code> estándar. Puede usar el nombre de prioridad o su valor numérico correspondiente. En orden de mayor a menor prioridad, estos son:</p>

<ul>
<li>0: emergencia</li>
<li>1: alerta</li>
<li>2: crítico</li>
<li>3: error</li>
<li>4: advertencia</li>
<li>5: aviso</li>
<li>6: información</li>
<li>7: depuración</li>
</ul>

<p>Los números o nombres anteriores pueden usarse indistintamente con la opción <code>-p</code>. Al seleccionar una prioridad, se mostrarán los mensajes marcados en el nivel especificado y aquellos que se encuentran por encima.</p>

<h2 id="modificar-la-pantalla-del-diario">Modificar la pantalla del diario</h2>

<p>Anteriormente, hemos demostrado la selección de entradas mediante el filtrado. Sin embargo, hay otras formas de modificar el resultado. Podemos ajustar la pantalla <code>journalctl</code> para que se adapte a varias necesidades.</p>

<h3 id="truncar-o-ampliar-el-resultado">Truncar o ampliar el resultado</h3>

<p>Podemos ajustar la forma en que <code>journalctl</code> muestra datos indicándole que reduzca o amplíe el resultado.</p>

<p>Por defecto, <code>journalctl</code> mostrará toda la entrada en el localizador, permitiendo que las entradas se desplacen a la derecha de la pantalla. Se puede acceder a esta información presionando la tecla de flecha derecha.</p>

<p>Si prefiere truncar el resultado, insertar un elipses donde se eliminó la información, puede usar la opción <code>--no-full</code>:</p>
<pre class="code-pre "><code>journalctl --no-full
</code></pre><pre class="code-pre "><code>. . .

Feb 04 20:54:13 journalme sshd[937]: Failed password for root from 83.234.207.60...h2
Feb 04 20:54:13 journalme sshd[937]: Connection closed by 83.234.207.60 [preauth]
Feb 04 20:54:13 journalme sshd[937]: PAM 2 more authentication failures; logname...ot
</code></pre>
<p>También puede hacer lo opuesto e indicarle a <code>journalctl</code> que muestre toda la información, independientemente de si incluye caracteres no imprimibles. Podemos hacerlo con el indicador <code>-a</code>:</p>
<pre class="code-pre "><code>journalctl -a
</code></pre>
<h3 id="resultado-a-la-salida-estándar">Resultado a la salida estándar</h3>

<p>Por defecto, <code>journalctl</code> muestra el resultado en un localizador para un uso más sencillo. Sin embargo, si está planeando procesar los datos con herramientas de manipulación de texto, es probable que quiera poder mostrar un resultado estándar.</p>

<p>Puede hacer esto con la opción <code>--no-pager</code>:</p>
<pre class="code-pre "><code>journalctl --no-pager
</code></pre>
<p>Se puede canalizar inmediatamente a una utilidad de procesamiento o redireccionar a un archivo en el disco, dependiendo de sus necesidades.</p>

<h3 id="formatos-de-resultado">Formatos de resultado</h3>

<p>Si está procesando las entradas del diario, como se mencionó anteriormente, es probable que le resulte más fácil analizar los datos si están en un formato más fácil de usar. Afortunadamente, el diario puede mostrarse en varios formatos según sea necesario. Puede hacerlo usando la opción <code>-o</code> con un especificador de formato.</p>

<p>Por ejemplo, puede mostrar las entradas del diario en JSON escribiendo lo siguiente:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json
</code></pre><pre class="code-pre "><code>{ "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635", "__REALTIME_TIMESTAMP" : "1422990364739502", "__MONOTONIC_TIMESTAMP" : "27200938", "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d", "PRIORITY" : "6", "_UID" : "0", "_GID" : "0", "_CAP_EFFECTIVE" : "3fffffffff", "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee", "_HOSTNAME" : "desktop", "SYSLOG_FACILITY" : "3", "CODE_FILE" : "src/core/unit.c", "CODE_LINE" : "1402", "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading", "SYSLOG_IDENTIFIER" : "systemd", "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5", "_TRANSPORT" : "journal", "_PID" : "1", "_COMM" : "systemd", "_EXE" : "/usr/lib/systemd/systemd", "_CMDLINE" : "/usr/lib/systemd/systemd", "_SYSTEMD_CGROUP" : "/", "UNIT" : "nginx.service", "MESSAGE" : "Starting A high performance web server and a reverse proxy server...", "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973" }

. . .
</code></pre>
<p>Eso es útil para el análisis sintáctico con utilidades. Puede usar el formato <code>json-pretty</code> para obtener un mejor manejo de la estructura de datos antes de transmitirla al consumidor JSON:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json-pretty
</code></pre><pre class="code-pre "><code>{
    "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635",
    "__REALTIME_TIMESTAMP" : "1422990364739502",
    "__MONOTONIC_TIMESTAMP" : "27200938",
    "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d",
    "PRIORITY" : "6",
    "_UID" : "0",
    "_GID" : "0",
    "_CAP_EFFECTIVE" : "3fffffffff",
    "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee",
    "_HOSTNAME" : "desktop",
    "SYSLOG_FACILITY" : "3",
    "CODE_FILE" : "src/core/unit.c",
    "CODE_LINE" : "1402",
    "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading",
    "SYSLOG_IDENTIFIER" : "systemd",
    "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5",
    "_TRANSPORT" : "journal",
    "_PID" : "1",
    "_COMM" : "systemd",
    "_EXE" : "/usr/lib/systemd/systemd",
    "_CMDLINE" : "/usr/lib/systemd/systemd",
    "_SYSTEMD_CGROUP" : "/",
    "UNIT" : "nginx.service",
    "MESSAGE" : "Starting A high performance web server and a reverse proxy server...",
    "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973"
}

. . .
</code></pre>
<p>Se pueden usar los siguientes formatos para la visualización:</p>

<ul>
<li><strong>cat</strong>: Muestra solo el campo de mensaje.</li>
<li><strong>export</strong>: Formato binario adecuado para transferir o hacer copias de seguridad.</li>
<li><strong>json</strong>: JSON estándar con una entrada por línea.</li>
<li><strong>json-pretty</strong>: JSON formateado para una mejor legibilidad humana</li>
<li><strong>json-sse</strong>: Resultado formateado JSON envuelto para hacer que el evento enviado por servidor sea compatible</li>
<li><strong>short</strong>: El resultado de estilo <code>syslog</code> predeterminado</li>
<li><strong>short-iso</strong>: El formato predeterminado aumentado para mostrar las marcas de tiempo del reloj de pared ISO 8601.</li>
<li><strong>short-monotonic</strong>: El formato predeterminado con marcas de tiempo monotónicas.</li>
<li><strong>short-precise</strong>: El formato predeterminado con precisión de microsegundos.</li>
<li><strong>verbose</strong>: Muestra todos los campos del diario disponibles para la entrada, incluidos los que suelen estar ocultos internamente.</li>
</ul>

<p>Estas opciones le permiten mostrar las entradas del diario en el formato que mejor se adapte a sus necesidades actuales.</p>

<h2 id="supervisión-activa-de-procesos">Supervisión activa de procesos</h2>

<p>El comando <code>journalctl</code> imita la cantidad de administradores que utilizan <code>tail</code> para supervisar la actividad activa o reciente. Esta funcionalidad está incorporada en <code>journalctl</code>, lo que le permite acceder a estas funciones sin necesidad de recurrir a otra herramienta.</p>

<h3 id="cómo-mostrar-los-registros-recientes">Cómo mostrar los registros recientes</h3>

<p>Para mostrar una cantidad determinada de registros, puede usar la opción <code>-n</code>, que funciona exactamente igual que <code>tail -n</code>.</p>

<p>Por defecto, se mostrarán las 10 entradas más recientes:</p>
<pre class="code-pre "><code>journalctl -n
</code></pre>
<p>Puede especificar el número de entradas que desea ver agregando un número después de <code>-n</code>:</p>
<pre class="code-pre "><code>journalctl -n 20
</code></pre>
<h3 id="cómo-hacer-un-seguimiento-de-los-registros">Cómo hacer un seguimiento de los registros</h3>

<p>Para seguir activamente los registros a medida que se escriben, puede usar el indicador <code>-f</code>. Una vez más, esto funciona de la manera prevista si tiene experiencia usando <code>tail -f</code>:</p>
<pre class="code-pre "><code>journalctl -f
</code></pre>
<h2 id="mantenimiento-del-diario">Mantenimiento del diario</h2>

<p>Es posible que se esté preguntando sobre el costo de almacenar todos los datos que hemos visto hasta ahora. Además, puede que le interese limpiar algunos registros antiguos y liberar espacio.</p>

<h3 id="cómo-encontrar-el-uso-actual-de-disco">Cómo encontrar el uso actual de disco</h3>

<p>Puede averiguar la cantidad de espacio que el diario está ocupando actualmente en el disco usando la opción el indicador <code>--disk-usage</code>:</p>
<pre class="code-pre "><code>journalctl --disk-usage
</code></pre><pre class="code-pre "><code>Journals take up 8.0M on disk.
</code></pre>
<h3 id="cómo-eliminar-registros-antiguos">Cómo eliminar registros antiguos</h3>

<p>Si desea reducir su diario, puede hacerlo de dos distintas formas (disponibles en la versión 218 y posterior de <code>systemd</code>).</p>

<p>Si usa la opción <code>--vacuum-size</code>, puede reducir su diario indicando un tamaño. Esto eliminará las entradas antiguas hasta que el espacio total del diario ocupado en el disco sea del tamaño solicitado:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-size=1G
</code></pre>
<p>Otra forma de reducir el diario es proporcionar un tiempo límite con la opción <code>--vacuum-time</code>. Cualquier entrada que sobrepase ese límite de tiempo será eliminada. Esto le permite mantener las entradas que se han creado después de un tiempo específico.</p>

<p>Por ejemplo, para guardar las entradas del último año, puede escribir lo siguiente:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-time=1years
</code></pre>
<h3 id="cómo-limitar-la-expansión-del-diario">Cómo limitar la expansión del diario</h3>

<p>Puede configurar su servidor para que ponga límites sobre cantidad de espacio que puede ocupar el diario. Puede hacer esto editando el archivo <code>/etc/systemd/journald.conf</code></p>

<p>Puede usar los siguientes elementos para limitar la expansión del diario:</p>

<ul>
<li><strong><code>SystemMaxUse=</code></strong>: especifica el espacio máximo de disco que puede usar el diario en el almacenamiento persistente.</li>
<li><strong><code>SystemKeepFree=</code></strong>: especifica la cantidad de espacio que el diario debe dejar libre al añadir entradas del diario al almacenamiento persistente.</li>
<li><strong><code>SystemMaxFileSize=</code></strong>: controla el tamaño que pueden alcanzar los archivos individuales del diario en el almacenamiento persistente antes de rotar.</li>
<li><strong><code>RuntimeMaxUse=</code></strong>: especifica el espacio máximo de disco que puede usarse en el almacenamiento volátil (dentro del sistema de archivos <code>/run</code>).</li>
<li><strong><code>RuntimeKeepFree=</code></strong>: especifica la cantidad de espacio que debe reservarse para otros usos al escribir datos en el almacenamiento volátil (dentro del sistema de archivos <code>/run</code>).</li>
<li><strong><code>RuntimeMaxFileSize=</code></strong>: especifica la cantidad de espacio que puede ocupar un archivo de diario individual en el almacenamiento volátil (dentro del sistema de archivos <code>/run</code>) antes de rotar.</li>
</ul>

<p>Al establecer estos valores, puede controlar la forma en que <code>journald</code> utiliza y preserva el espacio en su servidor. Tenga en cuenta que <strong><code>SystemMaxFileSize</code></strong> y <strong><code>RuntimeMaxFileSize</code></strong> orientarán los archivos para llegar a los límites indicados. Es importante recordar esto al interpretar el recuento de archivos después de una operación vaciado.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Como puede ver, el diario <code>systemd</code> es increíblemente útil para recopilar y administrar los datos de su sistema y aplicación. Su flexibilidad proviene, en mayor parte, de los amplios metadatos registrados automáticamente y del carácter centralizado del registro. El comando <code>journalctl</code> hace que sea fácil aprovechar las funciones avanzadas del diario, realizar amplios análisis y depurar de forma relacional los diferentes componentes de la aplicación.</p>
