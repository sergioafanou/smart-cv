---
title : "Cómo editar el archivo sudoers"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-es
image: "https://sergio.afanou.com/assets/images/image-midres-25.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>La separación de privilegios es uno de los paradigmas de seguridad fundamentales implementados en Linux y en los sistemas operativos tipo Unix. Los usuarios regulares operan con privilegios limitados para reducir el alcance de su influencia en su propio entorno, y no en el sistema operativo en general.</p>

<p>Un usuario especial, llamado <strong>root</strong>, tiene privilegios de <em>superusuario</em>. Esta es una cuenta administrativa sin las restricciones que tienen los usuarios normales. Los usuarios pueden ejecutar comandos con privilegios de superusuario o <strong>root</strong> de varias maneras.</p>

<p>En este artículo, explicaremos cómo obtener privilegios <strong>root</strong> de forma correcta y segura, con un enfoque especial en la edición del archivo <code>/etc/sudoers</code>.</p>

<p>Completaremos estos pasos en un servidor de Ubuntu 20.04, pero la mayoría de las distribuciones de Linux modernas, como Debian y CentOS, deberían funcionar de manera similar.</p>

<p>Esta guía asume que usted ya ha completado la <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">configuración inicial del servidor</a> que se mencionó aquí. Inicie sesión en su servidor como usuario no <strong>root</strong> regular y continúe como se indica abajo.</p>

<p><span class='note'><strong>Nota:</strong> Este tutorial profundiza sobre la escalada de privilegios y el archivo <code>sudoers</code>. Si solo desea añadir privilegios <code>sudo</code> a un usuario, consulte nuestros tutoriales de inicio rápido <em>Cómo crear un nuevo usuario habilitado para sudo</em> en <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> y <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a>.<br></span></p>

<h2 id="cómo-obtener-privilegios-root">Cómo obtener privilegios root</h2>

<p>Existen tres formas básicas de obtener privilegios <strong>root</strong>, que varían en su nivel de sofisticación.</p>

<h3 id="inicio-de-sesión-como-root">Inicio de sesión como root</h3>

<p>El método más sencillo y directo para obtener privilegios <strong>root</strong> es iniciar sesión directamente en su servidor como usuario <strong>root</strong>.</p>

<p>Si está iniciando sesión en una máquina local (o usando una función de consola fuera de banda en un servidor virtual), introduzca <code>root</code> como su nombre de usuario en mensaje de inicio de sesión e ingrese la contraseña <strong>root</strong> cuando se le solicite.</p>

<p>Si está iniciando sesión a través de SSH, especifique el usuario <strong>root</strong> antes de la dirección IP o el nombre de dominio en su cadena de conexión SSH:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ssh root@<span class="highlight">server_domain_or_ip</span>
</li></ul></code></pre>
<p>Si no ha configurado las claves SSH para el usuario <strong>root</strong>, ingrese la contraseña <strong>root</strong> cuando se le solicite.</p>

<h3 id="utilice-su-para-convertirse-en-root">Utilice <code>su</code> para convertirse en root</h3>

<p>Iniciar sesión directamente como <strong>root</strong> normalmente no suele ser recomendable, ya que es fácil comenzar a utilizar el sistema para tareas no administrativas, lo cual es peligroso.</p>

<p>La siguiente forma de obtener privilegios de superusuario le permite convertirse en usuario <strong>root</strong> en cualquier momento, cuando lo necesite.</p>

<p>Podemos hacerlo invocando el comando <code>su</code>, que significa &ldquo;usuario sustituto&rdquo;. Para obtener privilegios <strong>root</strong>, escriba lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">su
</li></ul></code></pre>
<p>Se le solicitará la contraseña del usuario <strong>root</strong>, después de lo cual, iniciará una sesión de shell <strong>root</strong>.</p>

<p>Cuando haya terminado las tareas que requieren privilegios <strong>root</strong>, vuelva a su shell normal escribiendo lo siguiente:</p>
<pre class="code-pre super_user prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="#">exit
</li></ul></code></pre>
<h3 id="utilice-sudo-para-ejecutar-comandos-como-root">Utilice <code>sudo</code> para ejecutar comandos como root</h3>

<p>La última forma de obtener privilegios <strong>root</strong> que explicaremos es con el comando <code>sudo</code>.</p>

<p>El comando <code>sudo</code> permite ejecutar comandos irrepetibles con privilegios <strong>root</strong>, sin necesidad de generar una shell nueva. Se ejecuta así:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo <span class="highlight">command_to_execute</span>
</li></ul></code></pre>
<p>A diferencia de <code>su</code>, el comando <code>sudo</code> solicitará la contraseña del usuario <em>actual</em>, y no la contraseña <strong>root</strong>.</p>

<p>Debido a sus implicaciones de seguridad, no se concede acceso <code>sudo</code> a los usuarios de manera predeterminada, y debe configurarse antes de que funcione correctamente. Consulte nuestros tutoriales <em>de inicio rápido Cómo crear un nuevo usuario habilitado para sudo</em> en <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> y <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a> para aprender a configurar un usuario habilitado para <code>sudo</code>.</p>

<p>En la siguiente sección, explicaremos en mayor detalle cómo modificar la configuración <code>sudo</code>.</p>

<h2 id="¿qué-es-visudo">¿Qué es Visudo?</h2>

<p>El comando <code>sudo</code> se configura a través de un archivo ubicado en <code>/etc/sudoers</code>.</p>

<p><span class='warning'><strong>Advertencia:</strong> Nunca edite este archivo con un editor de texto normal. ¡Siempre utilice el comando <code>visudo</code> en su lugar!<br></span></p>

<p>Dado que una sintaxis inadecuada en el archivo <code>/etc/sudoers</code> puede generar un sistema fallido, en el que es imposible obtener privilegios elevados, es importante utilizar el comando <code>visudo</code> para editar el archivo.</p>

<p>El comando <code>visudo</code> abre un editor de texto igual al normal, pero valida la sintaxis del archivo al guardarlo. Esto evita que los errores de configuración bloqueen las operaciones <code>sudo</code>, que pueden ser su única forma de obtener privilegios <strong>root</strong>.</p>

<p>Tradicionalmente, <code>visudo</code> abre el archivo <code>/etc/sudoers</code> con el editor de texto <code>vi</code>. Sin embargo, Ubuntu, ha configurado <code>visudo</code> para utilizar el editor de texto <code>nano</code> en su lugar.</p>

<p>Si desea cambiarlo de nuevo a <code>vi</code>, emita el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo update-alternatives --config editor
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    10        manual mode

Press &lt;enter&gt; to keep the current choice[*], or type selection number:
</code></pre>
<p>Seleccione el número que coincida con la elección que desea realizar.</p>

<p>En CentOS, puede cambiar este valor añadiendo la siguiente línea a su <code>~/.bashrc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">export EDITOR=`which <span class="highlight">name_of_editor</span>`
</li></ul></code></pre>
<p>Obtenga el archivo para implementar los cambios:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">. ~/.bashrc
</li></ul></code></pre>
<p>Después de configurar <code>visudo</code>, ejecute el comando para acceder al archivo <code>/etc/sudoers</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre>
<h2 id="cómo-modificar-el-archivo-sudoers">Cómo modificar el archivo sudoers</h2>

<p>El archivo <code>/etc/sudoers</code> aparecerá en su editor de texto seleccionado.</p>

<p>He copiado y pegado el archivo de Ubuntu 18.04, con los comentarios eliminados. El archivo <code>/etc/sudoers</code> de CentOS tiene muchas más líneas, algunas de las que no explicaremos en esta guía.</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

root    ALL=(ALL:ALL) ALL

%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

#includedir /etc/sudoers.d
</code></pre>
<p>Veamos qué hacen estas líneas.</p>

<h3 id="líneas-predeterminadas">Líneas predeterminadas</h3>

<p>La primera línea, &ldquo;Defaults env_reset&rdquo;, reinicia el entorno de la terminal para eliminar cualquier variable de usuario. Esta es una medida de seguridad que se utiliza para eliminar las variables de entorno potencialmente dañinas de la sesión <code>sudo</code>.</p>

<p>La segunda línea, <code>Defaults mail_badpass</code>, indica al sistema que envíe notificaciones por correo de intentos de contraseñas erróneas de <code>sudo</code> al usuario <code>mailto</code> configurado. De manera predeterminada, esta es la cuenta <strong>root</strong>.</p>

<p>La tercera línea, que comienza con &ldquo;Defaults secure_path=&hellip;&rdquo;, especifica el <code>PATH</code> (las ubicaciones del sistema de archivos en los que el sistema operativo buscará aplicaciones) que se utilizarán para las operaciones <code>sudo</code>. Esto evita el uso de rutas de usuario que puedan ser perjudiciales.</p>

<h3 id="líneas-de-privilegio-de-usuario">Líneas de privilegio de usuario</h3>

<p>La cuarta línea, que dicta los privilegios <code>sudo</code> del usuario <strong>root</strong>, es diferente de las líneas anteriores. Veamos qué significan los diferentes campos:</p>

<ul>
<li><p><code><span class="highlight">root</span> ALL=(ALL:ALL) ALL</code> El primer campo indica el nombre de usuario al que se aplicará la regla (<strong>root</strong>).</p></li>
<li><p><code>root <span class="highlight">ALL</span>=(ALL:ALL) ALL</code> El primer &ldquo;ALL&rdquo; indica que esta regla se aplica a todos los hosts.</p></li>
<li><p><code>root ALL=(<span class="highlight">ALL</span>:ALL) ALL</code> Este &ldquo;ALL&rdquo; indica que el usuario <strong>root</strong> puede ejecutar comandos como todos los usuarios.</p></li>
<li><p><code>root ALL=(ALL:<span class="highlight">ALL</span>) ALL</code> Este &ldquo;ALL&rdquo; indica que el usuario <strong>root</strong> puede ejecutar comandos como todos los grupos.</p></li>
<li><p><code>root ALL=(ALL:ALL) <span class="highlight">ALL</span></code> El último &ldquo;ALL&rdquo; indica que estas reglas se aplican a todos los comandos.</p></li>
</ul>

<p>Esto significa que nuestro usuario <strong>root</strong> puede ejecutar cualquier comando usando <code>sudo</code>, siempre que proporcione su contraseña.</p>

<h3 id="líneas-de-privilegio-de-grupo">Líneas de privilegio de grupo</h3>

<p>Las siguientes dos líneas son similares a las líneas de privilegios de usuario, pero especifican reglas <code>sudo</code> para los grupos.</p>

<p>Los nombres que comienzan con un <code>%</code> indican los nombres de grupo.</p>

<p>Aquí, vemos que el grupo <strong>admin</strong> puede ejecutar cualquier comando como cualquier usuario en cualquier host. De manera similar, el grupo <strong>sudo</strong> tiene los mismos privilegios, pero también puede ejecutarse como cualquier grupo.</p>

<h3 id="línea-etc-sudoers-d-incluida">Línea /etc/sudoers.d incluida</h3>

<p>La última línea puede parecer un comentario a primera vista:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .

#includedir /etc/sudoers.d
</code></pre>
<p>**Comienza con un <code>#</code>, que normalmente indica un comentario. Sin embargo, esta línea en realidad indica que los archivos dentro del directorio <code>/etc/sudoers.d</code> también se consultarán y aplicarán.</p>

<p>Los archivos dentro de ese directorio siguen las mismas reglas que el archivo <code>/etc/sudoers</code>. Cualquier archivo que no termine en <code>~</code> y que no tenga un <code>.</code> se leerá y anexará a la configuración <code>sudo</code>.</p>

<p>Esto está pensado principalmente para que las aplicaciones alteren los privilegios <code>sudo</code> después de instalarse. Poner todas las reglas asociadas dentro de un solo archivo en el directorio <code>/etc/sudoers.d</code> puede facilitar la tarea de ver qué privilegios están asociados con qué cuentas y revertir las credenciales fácilmente sin tener que manipular el archivo <code>/etc/sudoers</code> directamente.</p>

<p>Al igual que con el archivo <code>/etc/sudoers</code>, siempre debe editar los archivos dentro del directorio <code>/etc/sudoers.d</code> con <code>visudo</code>. La sintaxis para editar estos archivos sería la siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo -f /etc/sudoers.d/<span class="highlight">file_to_edit</span>
</li></ul></code></pre>
<h2 id="cómo-conceder-privilegios-sudo-a-un-usuario">Cómo conceder privilegios sudo a un usuario</h2>

<p>La operación más común que los usuarios desean lograr al administrar los permisos <code>sudo</code> es conceder a un nuevo usuario acceso <code>sudo</code> general. Esto resulta útil si desea dar a una cuenta acceso administrativo completo al sistema.</p>

<p>La forma más sencilla de hacerlo en un sistema configurado con un grupo de administración de uso general, como el sistema Ubuntu en esta guía, es en realidad añadir el usuario en cuestión a ese grupo.</p>

<p>Por ejemplo, en Ubuntu 20.04, el grupo <code>sudo</code> tiene privilegios completos de administrador. Podemos conceder a un usuario estos mismos privilegios añadiéndolo al grupo de la siguiente manera:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG sudo <span class="highlight">username</span>
</li></ul></code></pre>
<p>También se puede usar el comando <code>gpasswd</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> sudo
</li></ul></code></pre>
<p>Ambos lograrán lo mismo.</p>

<p>En CentOS, normalmente es el grupo <code>wheel</code>, en lugar del grupo <code>sudo</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG wheel <span class="highlight">username</span>
</li></ul></code></pre>
<p>O usando <code>gpasswd</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> wheel
</li></ul></code></pre>
<p>En CentOS, si añadir el usuario al grupo no funciona inmediatamente, es posible que tenga que editar el archivo <code>/etc/sudoers</code> para eliminar los comentarios del nombre del grupo:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
%wheel ALL=(ALL) ALL
. . .
</code></pre>
<h2 id="cómo-configurar-reglas-personalizadas">Cómo configurar reglas personalizadas</h2>

<p>Ahora que nos familiarizamos con la sintaxis general del archivo, crearemos algunas reglas nuevas.</p>

<h3 id="cómo-crear-alias">Cómo crear alias</h3>

<p>El archivo <code>sudoers</code> puede organizarse más fácilmente agrupando las cosas con varios tipos de &ldquo;alias&rdquo;.</p>

<p>Por ejemplo, podemos crear tres grupos diferentes de usuarios, con miembros superpuestos:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
User_Alias      GROUPONE = abby, brent, carl
User_Alias      GROUPTWO = brent, doris, eric,
User_Alias      GROUPTHREE = doris, felicia, grant
. . .
</code></pre>
<p>Los nombres de los grupos deben comenzar con una letra mayúscula. Luego, podemos permitir que los miembros de <code>GROUPTWO</code> actualicen la base de datos <code>apt</code> creando una regla como esta:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = /usr/bin/apt-get update
. . .
</code></pre>
<p>Si no especificamos un usuario/grupo para ejecutarse, como en el caso anterior, <code>sudo</code> será el usuario <strong>root</strong> de manera predeterminada.</p>

<p>Podemos permitir que los miembros de <code>GROUPTHREE</code> apaguen y reinicien la máquina creando un &ldquo;alias de comando&rdquo; y usando eso en una regla para <code>GROUPTHREE</code>:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Cmnd_Alias      POWER = /sbin/shutdown, /sbin/halt, /sbin/reboot, /sbin/restart
GROUPTHREE  ALL = POWER
. . .
</code></pre>
<p>Creamos un alias de comando llamado <code>POWER</code> que contiene comandos para apagar y reiniciar la máquina. Luego, permitimos que los miembros de <code>GROUPTHREE</code> ejecuten estos comandos.</p>

<p>También podemos crear los alias &ldquo;Ejecutar como&rdquo;, que pueden sustituir la parte de la regla que especifica el usuario con el que se ejecuta el siguiente comando:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Runas_Alias     WEB = www-data, apache
GROUPONE    ALL = (WEB) ALL
. . .
</code></pre>
<p>Eso permitirá que cualquier miembro del <code>GROUPONE</code> ejecute comandos como usuario <code>www-data</code> o como usuario <code>apache</code>.</p>

<p>Solo tenga en cuenta que las reglas posteriores anularán las reglas anteriores cuando haya un conflicto entre ambas.</p>

<h3 id="cómo-bloquear-reglas">Cómo bloquear reglas</h3>

<p>Existen varias formas en las que se puede obtener más control sobre cómo <code>sudo</code> reacciona a una llamada.</p>

<p>El comando <code>updatedb</code> asociado con el paquete <code>mlocate</code> es relativamente inofensivo en un sistema de un solo usuario. Si queremos que los usuarios lo ejecuten con privilegios <strong>root</strong> <em>sin</em> necesidad de escribir una contraseña, podemos crear una regla como la siguiente:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPONE    ALL = NOPASSWD: /usr/bin/updatedb
. . .
</code></pre>
<p><code>NOPASSWD</code> es un &ldquo;etiqueta&rdquo; que significa que no se solicitará ninguna contraseña. Tiene un comando compañero llamado <code>PASSWD</code>, que es el comportamiento predeterminado. Una etiqueta es importante para el resto de la regla a menos que su etiqueta &ldquo;gemela&rdquo; la anule más adelante en la línea.</p>

<p>Por ejemplo, podemos tener una línea como la siguiente:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = NOPASSWD: /usr/bin/updatedb, PASSWD: /bin/kill
. . .
</code></pre>
<p>Otra etiqueta útil es <code>NOEXEC</code>, que puede usarse para evitar algunos comportamientos peligrosos en ciertos programas.</p>

<p>Por ejemplo, algunos programas, como <code>less</code>, pueden generar otros comandos escribiendo esto desde su interfaz:</p>
<pre class="code-pre "><code>!<span class="highlight">command_to_run</span>
</code></pre>
<p>Esto básicamente ejecuta cualquier comando que el usuario proporcione con los mismos permisos con los que se está ejecutando <code>less</code>, lo que puede ser bastante peligroso.</p>

<p>Para restringir esto, podríamos usar una línea como la siguiente:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
<span class="highlight">username</span>  ALL = NOEXEC: /usr/bin/less
. . .
</code></pre>
<h2 id="otra-información">Otra información</h2>

<p>Existen algunos otros datos que pueden ser útiles cuando se trata de <code>sudo</code>.</p>

<p>Si especificó un usuario o un grupo para &ldquo;ejecutar como&rdquo; en el archivo de configuración, puede ejecutar comandos como esos usuarios usando los indicadores <code>-u</code> y <code>-g</code>, respectivamente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -u <span class="highlight">run_as_user command</span>
</li><li class="line" data-prefix="$">sudo -g <span class="highlight">run_as_group command</span>
</li></ul></code></pre>
<p>Por conveniencia y de manera predeterminada, <code>sudo</code> guardará sus detalles de autenticación durante un tiempo determinado en un solo terminal. Esto significa que no tendrá que escribir su contraseña de nuevo hasta que se agote el tiempo del temporizador.</p>

<p>Para cuestiones de seguridad, si desea eliminar dicho temporizador cuando haya terminado de ejecutar comandos administrativos, puede ejecutar:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li></ul></code></pre>
<p>Si, por otro lado, desea &ldquo;imprimir&rdquo; el comando <code>sudo</code> para que no se le solicite más adelante o para renovar su concesión <code>sudo</code>, siempre puede escribir lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -v
</li></ul></code></pre>
<p>Se le solicitará su contraseña, que se almacenará en caché para usos de <code>sudo</code> posteriores hasta que el plazo de <code>sudo</code> expire.</p>

<p>Si simplemente se pregunta qué tipo de privilegios están definidos para su nombre de usuario, puede escribir lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -l
</li></ul></code></pre>
<p>Esto generará una lista de todas las reglas en el archivo <code>/etc/sudoers</code> que se aplican a su usuario. Eso le dará una buena idea de lo que se podrá hacer o no con <code>sudo</code> como cualquier usuario.</p>

<p>Habrá muchas veces en las que se ejecutará un comando y fallará porque se olvidó precederlo con <code>sudo.</code> Para evitar tener que volver a escribir el comando, puede aprovechar una funcionalidad de bash que significa &ldquo;repetir el último comando&rdquo;:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo !!
</li></ul></code></pre>
<p>El doble signo de exclamación repetirá el último comando. Lo precedemos con <code>sudo</code> para cambiar rápidamente de un comando sin privilegios a un comando con privilegios.</p>

<p>Para divertirse, puede añadir la siguiente línea a su archivo <code>/etc/sudoers</code> con <code>visudo</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Defaults    insults
. . .
</code></pre>
<p>Esto hará que <code>sudo</code> devuelva un insulto tonto cuando un usuario escriba una contraseña incorrecta para <code>sudo</code>. Podemos usar <code>sudo -k</code> para eliminar la contraseña anterior <code>sudo</code> del caché para probarla:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li><li class="line" data-prefix="$">sudo ls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[sudo] password for demo:    # <span class="highlight">enter an incorrect password here to see the results</span>
Your mind just hasn't been the same since the electro-shock, has it?
[sudo] password for demo:
My mind is going. I can feel it.
</code></pre>
<h2 id="conclusión">Conclusión</h2>

<p>Ahora, debería tener una compresión básica sobre cómo leer y modificar el archivo <code>sudoers</code>, y una comprensión de los diferentes métodos que puede usar para obtener privilegios <strong>root</strong>.</p>

<p>Recuerde que los privilegios de superusuario no se dan a los usuarios regulares por un motivo. Es fundamental que entienda qué hace cada comando que ejecuta con privilegios <strong>root</strong>. No tome esa responsabilidad a la ligera. Aprenda la mejor forma de utilizar estas herramientas para su caso de uso, y bloquee cualquier funcionalidad que no necesite.</p>
