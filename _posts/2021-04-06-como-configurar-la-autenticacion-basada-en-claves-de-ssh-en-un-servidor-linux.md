---
title : "Cómo configurar la autenticación basada en claves de SSH en un servidor Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server-es
image: "https://sergio.afanou.com/assets/images/image-midres-54.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>SSH, o shell seguro, es un protocolo cifrado que se usa para administrar servidores y comunicarse con ellos. Al trabajar con un servidor de Linux, es probable que pase la mayor parte de su tiempo en una sesión de terminal conectada a su servidor a través de SSH.</p>

<p>Aunque existen varias formas de iniciar sesión en un servidor SSH, en esta guía nos centraremos en la configuración de las claves SSH. Las claves SSH proporcionan una forma fácil y extremadamente segura de iniciar sesión en su servidor. Por este motivo, este es el método que recomendamos a todos los usuarios.</p>

<h2 id="¿cómo-funcionan-las-claves-ssh">¿Cómo funcionan las claves SSH?</h2>

<p>Un servidor SSH puede autenticar a los clientes usando varios métodos diferentes. El más básico de estos es la autenticación por contraseña, que es fácil de usar, pero no es la más segura.</p>

<p>A pesar de que las contraseñas se envían al servidor de forma segura, generalmente no son lo suficientemente complejas o largas como para resistir a los constantes y persistentes atacantes. La potencia de procesamiento moderna combinada con scripts automatizados hace que sea muy posible forzar una cuenta protegida con una contraseña. A pesar de que existen otros métodos para añadir seguridad adicional (<code>fail2ban</code>, etc.), las claves SSH son una alternativa confiable y segura.</p>

<p>Los pares de claves SSH son dos claves criptográficamente seguras que pueden usarse para autenticar a un cliente a un servidor SSH. Cada par de claves está compuesto por una clave pública y una clave privada.</p>

<p>El cliente mantiene la clave privada y debe mantenerla en absoluto secreto. Poner en riesgo la clave privada permitirá al atacante iniciar sesión en los servidores que están configurados con la clave pública asociada sin autenticación adicional. Como medida de precaución adicional, puede cifrar la clave en el disco con una frase de contraseña.</p>

<p>La clave pública asociada puede compartirse libremente sin ninguna consecuencia negativa. La clave pública puede usarse para cifrar mensajes que <strong>solo</strong> la clave privada puede descifrar. Esta propiedad se emplea como forma de autenticación mediante el uso del par de claves.</p>

<p>La clave pública se carga a un servidor remoto, en el que quiere iniciar sesión con SSH. La clave se añade a un archivo especial dentro de la cuenta de usuario con la que iniciará sesión, llamada <code>~/.ssh/authorized_keys</code>.</p>

<p>Cuando un cliente intente autenticarse usando claves SSH, el servidor puede comprobar si el cliente posee la clave privada. Si el cliente puede demostrar que posee la clave privada, se genera una sesión de shell o se ejecuta el comando solicitado.</p>

<h2 id="cómo-crear-claves-ssh">Cómo crear claves SSH</h2>

<p>El primer paso para configurar la autenticación con clave SSH en su servidor es generar un par de claves SSH en su computadora local.</p>

<p>Para ello, podemos usar una utilidad especial llamada <code>ssh-keygen</code>, que se incluye con el conjunto de herramientas estándar de OpenSSH. Por defecto, esto creará un par de claves RSA de 2048 bits, lo cual es muy útil para la mayoría de usos.</p>

<p>En su computadora local, genere un par de claves SSH escribiendo lo siguiente:</p>
<pre class="code-pre "><code>ssh-keygen
</code></pre><pre class="code-pre "><code>Generating public/private rsa key pair.
Enter file in which to save the key (/home/<span class="highlight">username</span>/.ssh/id_rsa):
</code></pre>
<p>La utilidad le solicitará que seleccione una ubicación para las claves que se generarán. Por defecto, las claves se almacenarán en el directorio <code>~/.ssh</code> dentro del directorio de inicio de su usuario. La clave privada se llamará <code>id_rsa</code>, y la clave pública asociada se llamará <code>id_rsa.pub</code>.</p>

<p>Normalmente, es mejor que conserve la ubicación predeterminada en esta etapa. Hacerlo permitirá a su cliente SSH encontrar automáticamente sus claves SSH al intentar autenticarse. Si desea elegir una ruta no estándar, introdúzcala ahora, de lo contrario, presione ENTER para aceptar la predeterminada.</p>

<p>Si generó previamente un par de claves SSH, es posible que vea un mensaje como el siguiente:</p>
<pre class="code-pre "><code>/home/<span class="highlight">username</span>/.ssh/id_rsa already exists.
Overwrite (y/n)?
</code></pre>
<p>Si elige sobrescribir la clave en el disco, ya <strong>no</strong> podrá autenticar usando la clave anterior. Tenga mucho cuidado al convalidar la operación, ya que este es un proceso destructivo que no puede revertirse.</p>
<pre class="code-pre "><code>Created directory '/home/<span class="highlight">username</span>/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
</code></pre>
<p>A continuación, se le solicitará que introduzca una frase de contraseña para la clave. Esta es una frase de contraseña opcional que puede usarse para cifrar el archivo de clave privada en el disco.</p>

<p>Es posible que se esté preguntando qué ventajas ofrece una clave SSH si aún así necesita introducir una frase de contraseña. Algunas de las ventajas son las siguientes:</p>

<ul>
<li>La clave SSH privada (la parte que puede protegerse con una frase de contraseña), nunca se expone en la red. La frase de contraseña solo se usa para descifrar la clave en la máquina local. Esto significa que los ataques de fuerza bruta basada en la red no podrán contra la frase de contraseña.</li>
<li>La clave privada se guarda dentro de un directorio restringido. El cliente SSH no reconocerá claves privadas que no estén guardadas en directorios restringidos. La propia clave también debe tener permisos restringidos (de lectura y escritura solo disponibles para el propietario). Esto significa que otros usuarios del sistema no pueden espiar.</li>
<li>Cualquier atacante que desee descifrar la frase de contraseña de la clave SSH privada debe tener acceso previo al sistema. Esto significa que ya tendrán acceso a su cuenta de usuario o a la cuenta root. Si se encuentra en esta posición, la frase de contraseña puede evitar que el atacante inicie sesión de inmediato en sus otros servidores. Esto le dará tiempo para crear e implementar un nuevo par de claves SSH y eliminar el acceso de la clave comprometida.</li>
</ul>

<p>Dado que la clave privada nunca se expone a la red y está protegida mediante permisos de archivo, nadie más que usted debería tener acceso a este archivo (y el usuario root). La frase de contraseña sirve como capa de protección adicional en caso de que estas condiciones se vean comprometidas.</p>

<p>Una frase de contraseña es un complemento opcional. Si introduce una, deberá proporcionarla cada vez que utilice esta clave (a menos que esté ejecutando un software de agente SSH que almacene la clave descifrada). Recomendamos usar una frase de contraseña, pero si no desea establecer una frase de contraseña, puede simplemente pulsar ENTER para omitir esta pregunta.</p>
<pre class="code-pre "><code>Your identification has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.
Your public key has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.pub.
The key fingerprint is:
a9:49:2e:2a:5e:33:3e:a9:de:4e:77:11:58:b6:90:26 <span class="highlight">username</span>@remote_host
The key's randomart image is:
+--[ RSA 2048]----+
|     ..o         |
|   E o= .        |
|    o. o         |
|        ..       |
|      ..S        |
|     o o.        |
|   =o.+.         |
|. =++..          |
|o=++.            |
+-----------------+
</code></pre>
<p>Ahora dispondrá de una clave pública y privada que puede usar para realizar la autenticación. El siguiente paso es ubicar la clave pública en su servidor, a fin de poder utilizar la autenticación basada en la clave SSH para iniciar sesión.</p>

<h2 id="cómo-incorporar-su-clave-pública-al-crear-su-servidor">Cómo incorporar su clave pública al crear su servidor</h2>

<p>Si está iniciando un nuevo servidor de DigitalOcean, puede incorporar automáticamente su clave pública SSH en la cuenta root de su nuevo servidor.</p>

<p>En la parte inferior de la página de creación de Droplet, existe una opción para añadir claves SSH a su servidor:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_options.png" alt="Clave SSH incorporada"></p>

<p>Si ya ha añadido un archivo de clave pública a su cuenta de DigitalOcean, lo verá aquí como una opción seleccionable (hay dos claves existentes en el ejemplo anterior: &ldquo;clave de trabajo&rdquo; y &ldquo;clave de inicio&rdquo;). Para incorporar una clave existente, simplemente haga clic en ella y se resaltará Puede incorporar múltiples claves en un solo servidor:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_selection.png" alt="Selección de clave SSH"></p>

<p>Si aún no tiene una clave SSH pública cargada en su cuenta o si quiere añadir una nueva clave a su cuenta, haga clic en el botón &ldquo;+ Add SSH Key&rdquo; (+ Añadir clave SSH). Esto se ampliará a un mensaje:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_prompt.png" alt="Mensaje de clave SSH"></p>

<p>Pegue su clave pública SSH en el cuadro &ldquo;Contenido de la clave SSH&rdquo;. Suponiendo que haya generado sus claves usando el método anterior, puede obtener el contenido de la clave pública en su computadora local escribiendo lo siguiente:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre><pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDNqqi1mHLnryb1FdbePrSZQdmXRZxGZbo0gTfglysq6KMNUNY2VhzmYN9JYW39yNtjhVxqfW6ewc+eHiL+IRRM1P5ecDAaL3V0ou6ecSurU+t9DR4114mzNJ5SqNxMgiJzbXdhR+j55GjfXdk0FyzxM3a5qpVcGZEXiAzGzhHytUV51+YGnuLGaZ37nebh3UlYC+KJev4MYIVww0tWmY+9GniRSQlgLLUQZ+FcBUjaqhwqVqsHe4F/woW1IHe7mfm63GXyBavVc+llrEzRbMO111MogZUcoWDI9w7UIm8ZOTnhJsk7jhJzG2GpSXZHmly/a/buFaaFnmfZ4MYPkgJD username@example.com
</code></pre>
<p>Pegue este valor completo en el cuadro más grande. En el cuadro &ldquo;Comment (optional)&rdquo; (Comentario [opcional]), puede elegir una etiqueta para la clave. Esta se mostrará como el nombre de la clave en la interfaz de DigitalOcean:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/new_key.png" alt="Nueva clave SSH"></p>

<p>Cuando cree su Droplet, las claves SSH públicas que haya seleccionado se ubicarán en el archivo <code>~/.ssh/authorized_keys</code> de la cuenta del usuario root. Esto le permitirá iniciar sesión en el servidor desde la computadora con su clave privada.</p>

<h2 id="cómo-copiar-una-clave-pública-en-su-servidor">Cómo copiar una clave pública en su servidor</h2>

<p>Si ya tiene un servidor disponible y no incorporó claves al crearlo, aún puede cargar su clave pública y usarla para autenticarse en su servidor.</p>

<p>El método que utilice depende en gran medida de las herramientas que tenga disponibles y los detalles de su configuración actual. Todos los siguientes métodos producen el mismo resultado final. El método más fácil y automatizado es el primero y los que se siguen requieren pasos manuales adicionales si no puede usar los métodos anteriores.</p>

<h3 id="cómo-copiar-su-clave-pública-con-ssh-copy-id">Cómo copiar su clave pública con SSH-Copy-ID</h3>

<p>La forma más sencilla de copiar su clave pública a un servidor existente es usar una utilidad llamada <code>ssh-copy-id</code>. Debido a su simplicidad, se recomienda este método si está disponible.</p>

<p>La herramienta <code>ssh-copy-id</code> está incluida en los paquetes de OpenSSH en muchas distribuciones, por lo que es posible que esté disponible en su sistema local. Para que este método funcione, ya debe disponer de acceso con SSH basado en contraseña en su servidor.</p>

<p>Para usar la utilidad, solo necesita especificar el host remoto al que desee conectarse y la cuenta de usuario a la que tenga acceso SSH con contraseña. Esta es la cuenta donde se copiará su clave SSH pública.</p>

<p>La sintaxis es la siguiente:</p>
<pre class="code-pre "><code>ssh-copy-id <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Es posible que vea un mensaje como el siguiente:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Esto solo significa que su computadora local no reconoce el host remoto. Esto sucederá la primera vez que establezca conexión con un nuevo host. Escriba “yes” y presione ENTER para continuar.</p>

<p>A continuación, la utilidad analizará su cuenta local en busca de la clave <code>id_rsa.pub</code> que creamos antes. Cuando la encuentre, le solicitará la contraseña de la cuenta del usuario remoto:</p>
<pre class="code-pre "><code>/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
username@111.111.11.111's password:
</code></pre>
<p>Escriba la contraseña (por motivos de seguridad, no se mostrará lo que escriba) y presione ENTER. La utilidad se conectará a la cuenta en el host remoto usando la contraseña que proporcionó. Luego, copie el contenido de su clave <code>~/.ssh/id_rsa.pub</code> a un archivo en el directorio principal de la cuenta remota <code>~/.ssh</code>, llamado <code>authorized_keys</code>.</p>

<p>Verá un resultado similar a este:</p>
<pre class="code-pre "><code>Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'username@111.111.11.111'"
and check to make sure that only the key(s) you wanted were added.
</code></pre>
<p>En este punto, su clave <code>id_rsa.pub</code> se habrá cargado en la cuenta remota. Puede continuar con la siguiente sección.</p>

<h3 id="cómo-copiar-la-clave-pública-con-ssh">Cómo copiar la clave pública con SSH</h3>

<p>Si no tiene <code>ssh-copy-id</code> disponible, pero tiene acceso de SSH basado en contraseña a una cuenta de su servidor, puede cargar sus claves usando un método de SSH convencional.</p>

<p>Para ello, podemos extraer el contenido de nuestra clave pública SSH en nuestra computadora local y canalizarlo a través de una conexión SSH al servidor remoto. Por otro lado, podemos asegurarnos de que el directorio <code>~/.sh</code> exista bajo la cuenta que estamos usando y luego salga el contenido que canalizamos en un archivo llamado <code>authorized_keys</code> dentro de este directorio.</p>

<p>Usaremos el símbolo de redireccionamiento <code>&gt;&gt;</code> para añadir el contenido en lugar de sobrescribirlo. Esto nos permitirá agregar claves sin eliminar las claves previamente agregadas.</p>

<p>El comando completo tendrá el siguiente aspecto:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub | ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span> "mkdir -p ~/.ssh &amp;&amp; cat &gt;&gt; ~/.ssh/authorized_keys"
</code></pre>
<p>Es posible que vea un mensaje como el siguiente:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Esto solo significa que su computadora local no reconoce el host remoto. Esto sucederá la primera vez que establezca conexión con un nuevo host. Escriba “yes” y presione ENTER para continuar.</p>

<p>A continuación, se le solicitará la contraseña de la cuenta a la que está intentando conectarse:</p>
<pre class="code-pre "><code>username@111.111.11.111's password:
</code></pre>
<p>Una vez que ingrese su contraseña, el contenido de su clave <code>id_rsa.pub</code> se copiará al final del archivo <code>authorized_keys</code> de la cuenta del usuario remoto. Continúe con la siguiente sección si se realizó exitosamente.</p>

<h3 id="copiar-su-clave-pública-de-forma-manual">Copiar su clave pública de forma manual</h3>

<p>Si no dispone de acceso SSH con contraseña a su servidor, deberá completar el proceso anterior de forma manual.</p>

<p>El contenido de su archivo <code>id_rsa.pub</code> deberá agregarse a un archivo en <code>~/.ssh/authorized_keys</code> en su máquina remota de alguna forma.</p>

<p>Para mostrar el contenido de su clave <code>id_rsa.pub</code>, escriba esto en su computadora local:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre>
<p>Verá el contenido de la clave, que debería tener un aspecto similar a este:</p>
<pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCqql6MzstZYh1TmWWv11q5O3pISj2ZFl9HgH1JLknLLx44+tXfJ7mIrKNxOOwxIxvcBF8PXSYvobFYEZjGIVCEAjrUzLiIxbyCoxVyle7Q+bqgZ8SeeM8wzytsY+dVGcBxF6N4JS+zVk5eMcV385gG3Y6ON3EG112n6d+SMXY0OEBIcO6x+PnUSGHrSgpBgX7Ks1r7xqFa7heJLLt2wWwkARptX7udSq05paBhcpB0pHtA1Rfz3K2B+ZVIpSDfki9UVKzT8JUmwW6NNzSgxUfQHGwnW7kj4jp4AT0VZk3ADw497M2G/12N0PPB5CnhHf7ovgy6nL1ikrygTKRFmNZISvAcywB9GVqNAVE+ZHDSCuURNsAInVzgYo9xgJDW8wUw2o8U77+xiFxgI5QSZX3Iq7YLMgeksaO4rBJEa54k8m5wEiEE1nUhLuJ0X/vh2xPff6SQ1BL/zkOhvJCACK6Vb15mDOeCSq54Cr7kvS46itMosi/uS66+PujOO+xt/2FWYepz6ZlN70bRly57Q06J+ZJoc9FfBCbCyYH7U/ASsmY095ywPsBo1XQ9PqhnN1/YOorJ068foQDNVpm146mUpILVxmq41Cj55YKHEazXGsdBIbXWhcrRf4G2fJLRcGUr9q8/lERo9oxRm5JFX6TCmj6kmiFqv+Ow9gI0x8GvaQ== demo@test
</code></pre>
<p>Acceda a su host remoto usando el método que esté a su disposición. Por ejemplo, si su servidor es un Droplet de DigitalOcean, puede iniciar sesión usando la consola web del panel de control:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/console_access.png" alt="Acceso a la consola de DigitalOcean"></p>

<p>Una vez que tenga acceso a su cuenta en el servidor remoto, debe asegurarse de que el directorio <code>~/.ssh</code> haya sido creado. Con este comando se creará el directorio, si es necesario. Si este último ya existe, no se creará:</p>
<pre class="code-pre "><code>mkdir -p ~/.ssh
</code></pre>
<p>Ahora, podrá crear o modificar el archivo <code>authorized_keys</code> dentro de este directorio. Puede agregar el contenido de su archivo <code>id_rsa.pub</code> al final del archivo <code>authorized_keys</code> y, si es necesario, crearlo usando el siguiente comando:</p>
<pre class="code-pre "><code>echo <span class="highlight">public_key_string</span> &gt;&gt; ~/.ssh/authorized_keys
</code></pre>
<p>En el comando anterior, reemplace <code><span class="highlight">public_key_string</span></code> por el resultado del comando <code>cat ~/.ssh/id_rsa.pub</code> que ejecutó en su sistema local. Debería iniciar con <code>ssh-rsa AAAA...</code>.</p>

<p>Si esto funciona, puede continuar con la autenticación sin contraseña.</p>

<h2 id="autenticación-en-su-servidor-con-claves-ssh">Autenticación en su servidor con claves SSH</h2>

<p>Si completó con éxito uno de los procedimientos anteriores, debería poder iniciar sesión en el host remoto <em>sin</em> la contraseña de la cuenta remota.</p>

<p>El proceso básico es el mismo:</p>
<pre class="code-pre "><code>ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Si es la primera vez que establece conexión con este host (si empleó el último método anterior), es posible que vea algo como esto:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Esto solo significa que su computadora local no reconoce el host remoto. Escriba “yes” y presione ENTER para continuar.</p>

<p>Si no proporcionó una frase de contraseña para su clave privada, se iniciará sesión de inmediato. Si proporcionó una frase de contraseña para la clave privada al crearla, se le solicitará que la introduzca ahora. A continuación, se debería generar una nueva sesión de shell con la cuenta en el sistema remoto.</p>

<p>Si lo realiza con éxito, continúe para averiguar cómo bloquear el servidor.</p>

<h2 id="desactivar-la-autenticación-por-contraseña-en-el-servidor">Desactivar la autenticación por contraseña en el servidor</h2>

<p>Si pudo iniciar sesión en su cuenta usando SSH sin una contraseña, habrá configurado con éxito la autenticación basada en claves de SSH para su cuenta. Sin embargo, su mecanismo de autenticación basado en contraseña sigue activo. Esto significa que su servidor sigue expuesto a ataques de fuerza bruta.</p>

<p>Antes de completar los pasos de esta sección, asegúrese de tener configurada la autenticación basada en claves SSH para la cuenta root en este servidor o, preferiblemente, la autenticación basada en clave SSH para una cuenta no root en este servidor con acceso <code>sudo</code>. Con este paso, se bloquearán los inicios de sesión basados en contraseñas. Por lo tanto, es fundamental que se asegure de que aún podrá obtener acceso administrativo.</p>

<p>Una vez que se cumplan las condiciones anteriores, inicie sesión en su servidor remoto con claves SSH, ya sea como root o con una cuenta con privilegios <code>sudo</code>. Abra el archivo de configuración del demonio SSH:</p>
<pre class="code-pre "><code>sudo nano /etc/ssh/sshd_config
</code></pre>
<p>Dentro del archivo, busque una directiva llamada <code>PasswordAuthentication</code>. Puede insertar comentarios sobre esto. Elimine los comentarios de la línea y fije el valor en “no”. Esto inhabilitará su capacidad de iniciar sesión a través de SSH usando contraseñas de cuenta:</p>
<pre class="code-pre "><code>PasswordAuthentication no
</code></pre>
<p>Guarde y cierre el archivo cuando termine. Para implementar realmente los cambios que acabamos de realizar, debe reiniciar el servicio.</p>

<p>En las máquinas Ubuntu o Debian, puede emitir este comando:</p>
<pre class="code-pre "><code>sudo service ssh restart
</code></pre>
<p>En las máquinas CentOS/Fedora, el demonio se llama <code>sshd</code>:</p>
<pre class="code-pre "><code>sudo service sshd restart
</code></pre>
<p>Después de completar este paso, ha trasladado correctamente a su demonio SHH para que responda únicamente a claves SSH.</p>

<h2 id="conclusión">Conclusión</h2>

<p>De esta manera, la autenticación basada en claves SSH debería quedar configurada y funcionando en su servidor, lo que le permitirá iniciar sesión sin proporcionar una contraseña de cuenta. Desde aquí, hay muchas direcciones a las que puede dirigirte. Si desea obtener más información sobre cómo trabajar con SSH, consulte nuestra <a href="https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys">Guía de aspectos básicos de SSH</a>.</p>
