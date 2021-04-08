---
title : "Cómo instalar MongoDB desde los repositorios APT predeterminados de Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-from-the-default-apt-repositories-on-ubuntu-20-04-es
image: "https://sergio.afanou.com/assets/images/image-midres-16.jpg"
---

<h3 id="introducción">Introducción</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a> es una base de datos de documentos gratuita y de código abierto utilizada comúnmente en las aplicaciones web modernas.</p>

<p>En este tutorial, instalará MongoDB, administrará su servicio y, de forma opcional, habilitará el acceso remoto.</p>

<p><span class='note'><strong>Nota</strong>: Al momento de la publicación de este artículo, este tutorial instala la versión <span class="highlight">3.6</span> de MongoDB, que es la versión disponible en los repositorios predeterminados de Ubuntu. Sin embargo, generalmente recomendamos instalar la versión más reciente de MongoDB, es decir, la versión <span class="highlight">4.4</span> al momento de la publicación de este artículo. Si desea instalar la versión más reciente de MongoDB, le recomendamos que siga esta guía sobre <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Cómo instalar MongoDB en Ubuntu 20.04</a>.<br></span></p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para seguir este tutorial, necesitará lo siguiente:</p>

<ul>
<li>Un servidor de Ubuntu 20.04 configurado siguiendo el <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">tutorial de configuración inicial del servidor</a>, que incluye un usuario no root con privilegios administrativos y un firewall configurado con UFW.</li>
</ul>

<h2 id="paso-1-instalar-mongodb">Paso 1: Instalar MongoDB</h2>

<p>En los repositorios oficiales de paquetes de Ubuntu se incluye MongoDB, lo que significa que podemos instalar los paquetes necesarios utilizando <code>apt</code>. Como se mencionó en la introducción, la versión disponible en los repositorios predeterminados no es la más reciente. Para instalar la versión más reciente de Mongo, siga <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">este tutorial</a>.</p>

<p>Primero, actualice la lista de paquetes para tener la versión más reciente de listados de repositorios:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li></ul></code></pre>
<p>A continuación, instale el propio paquete de MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install mongodb
</li></ul></code></pre>
<p>Este comando le solicitará confirmar que quiere instalar el paquete <code>mongodb</code> y sus dependencias. Para hacerlo, presione <code>Y</code> y, luego, <code>ENTER</code>.</p>

<p>Este comando instala varios paquetes que contienen una versión estable de MongoDB, junto con herramientas de administración útiles para el servidor de MongoDB. El servidor de la base de datos se inicia de forma automática tras la instalación.</p>

<p>A continuación, comprobaremos que el servidor esté activo y funcione de forma correcta.</p>

<h2 id="paso-2-comprobar-el-servicio-y-la-base-de-datos">Paso 2: Comprobar el servicio y la base de datos</h2>

<p>El proceso de instalación inició MongoDB de forma automática, pero verificaremos que el servicio se inicie y que la base de datos funcione.</p>

<p>Primero, compruebe el estado del servicio:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Verá este resultado:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>● mongodb.service - An object/document-oriented database
     Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset: enabled)
     Active: <span class="highlight">active (running)</span> since Thu 2020-10-08 14:23:22 UTC; 49s ago
       Docs: man:mongod(1)
   Main PID: 2790 (mongod)
      Tasks: 23 (limit: 2344)
     Memory: 42.2M
     CGroup: /system.slice/mongodb.service
             └─2790 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /etc/mongodb.conf
</code></pre>
<p>Según este resultado, el servidor MongoDB está funcionando.</p>

<p>Podemos verificar esto en profundidad estableciendo conexión con el servidor de la base de datos y ejecutando el siguiente comando de diagnóstico. Con esto se mostrarán la versión actual de la base de datos, la dirección y el puerto del servidor, y el resultado del comando status:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mongo --eval 'db.runCommand({ connectionStatus: 1 })'
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>MongoDB shell version v<span class="highlight">3.6.8</span>
connecting to: <span class="highlight">mongodb://127.0.0.1:27017</span>
Implicit session: session { "id" : UUID("e3c1f2a1-a426-4366-b5f8-c8b8e7813135") }
MongoDB server version: <span class="highlight">3.6.8</span>
{
    "authInfo" : {
        "authenticatedUsers" : [ ],
        "authenticatedUserRoles" : [ ]
    },
    "ok" : 1
}
</code></pre>
<p>Un valor de <code>1</code> para el campo <code>ok</code> en la respuesta indica que el servidor funciona correctamente.</p>

<p>A continuación, veremos la forma de administrar la instancia del servidor.</p>

<h2 id="paso-3-gestionar-el-servicio-de-mongodb">Paso 3: Gestionar el servicio de MongoDB</h2>

<p>El proceso de instalación descrito en el Paso 1 configura MongoDB como servicio de <code>systemd</code>, lo que significa que puede administrarlo utilizando comandos <code>systemctl</code> estándares, junto con todos los demás servicios de sistema en Ubuntu.</p>

<p>Para verificar el estado del servicio, escriba lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Puede detener el servidor en cualquier momento escribiendo lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop mongodb
</li></ul></code></pre>
<p>Para iniciar el servidor cuando esté detenido, escriba lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start mongodb
</li></ul></code></pre>
<p>También puede reiniciar el servidor con el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>Por defecto, MongoDB se configura para iniciarse de forma automática con el servidor. Si desea desactivar el inicio automático, escriba lo siguiente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable mongodb
</li></ul></code></pre>
<p>Puede volver a habilitar el inicio automático en cualquier momento con el siguiente comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable mongodb
</li></ul></code></pre>
<p>A continuación, ajustaremos la configuración del firewall para nuestra instalación MongoDB.</p>

<h2 id="paso-4-ajustar-el-firewall-opcional">Paso 4: Ajustar el firewall (opcional)</h2>

<p>Suponiendo que siguió las instrucciones <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">del tutorial de configuración inicial</a> para servidores para habilitar el firewall en su servidor, no será posible acceder al servidor de MongoDB desde Internet.</p>

<p>Si tiene intención de usar el servidor de MongoDB solo a nivel local con aplicaciones que se ejecuten en el mismo servidor, este es el ajuste recomendado y seguro. Sin embargo, si desea poder conectarse con su servidor MongoDB desde Internet, deberá permitir las conexiones entrantes añadiendo una regla UFW.</p>

<p>Para permitir el acceso a MongoDB en su puerto predeterminado <code>27017</code> desde cualquier parte, podría ejecutar <code>sudo ufw allow <span class="highlight">27017</span></code>. Sin embargo, permitir el acceso a Internet al servidor de MongoDB en una instalación predeterminada proporciona acceso ilimitado al servidor de la base de datos y a sus datos.</p>

<p>En la mayoría de los casos, solo se debe acceder a MongoDB desde determinadas ubicaciones de confianza, como otro servidor que aloje una aplicación. Para permitir solo el acceso al puerto predeterminado de MongoDB mediante otro servidor de confianza, puede especificar la dirección IP del servidor remoto en el comando <code>ufw</code>. De esta manera, solo se permitirá que esa máquina se conecte de forma explícita:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">trusted_server_ip</span>/32 to any port <span class="highlight">27017</span>
</li></ul></code></pre>
<p>Puede verificar el cambio en los ajustes del firewall con <code>ufw:</code></p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>
<p>Debería ver la habilitación del tráfico hacia el puerto <code>27017</code> en el resultado. Tenga en cuenta que si decidió permitir que solo una dirección IP determinada se conecte con el servidor de MongoDB, en el resultado de este comando se enumerará la dirección IP de la ubicación autorizada en lugar de <code>Anywhere</code>.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
<span class="highlight">27017                      ALLOW       Anywhere</span>
OpenSSH (v6)               ALLOW       Anywhere (v6)
<span class="highlight">27017 (v6)                 ALLOW       Anywhere (v6)</span>
</code></pre>
<p>Puede encontrar ajustes de firewall más avanzados para restringir el acceso a servicios en <a href="https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands">Aspectos básicos de UFW: Reglas y comandos comunes de firewall</a>.</p>

<p>Aunque el puerto esté abierto, MongoDB seguirá escuchando solo en la dirección local <code>127.0.0.1</code>. Para permitir conexiones remotas, agregue la dirección IP pública de su servidor al archivo <code>mongodb.conf</code>.</p>

<p>Abra el archivo de configuración de MongoDB en su editor de texto preferido: Este comando de ejemplo utiliza <code>nano</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongodb.conf
</li></ul></code></pre>
<p>Agregue la dirección IP de su servidor de MongoDB al valor <code>bindIP</code>: Asegúrese de colocar una coma entre la dirección IP existente y la que agregó.</p>
<div class="code-label " title="/etc/mongodb.conf">/etc/mongodb.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
logappend=true

bind_ip = 127.0.0.1<span class="highlight">,your_server_ip</span>
#port = 27017

...
</code></pre>
<p>Guarde el archivo y salga del editor. Si utilizó <code>nano</code> para editar el archivo, hágalo pulsando <code>CTRL + X</code>, <code>Y</code> y, luego, <code>ENTER</code>.</p>

<p>Luego, reinicie el servicio de MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>MongoDB ahora recibirá conexiones remotas, pero cualquiera podrá acceder a él. Siga el tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-mongodb-on-ubuntu-20-04">Cómo instalar y proteger MongoDB en Ubuntu 20.04</a> para agregar un usuario administrativo y ampliar el bloqueo.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Puede encontrar tutoriales más detallados sobre cómo configurar y utilizar MongoDB en <a href="https://www.digitalocean.com/community/search?q=mongodb">estos artículos de la comunidad de DigitalOcean</a>. <a href="https://docs.mongodb.com/v3.6/">La documentación oficial de MongoDB</a> también es un gran recurso en el que se abordan las posibilidades que ofrece MongoDB.</p>
