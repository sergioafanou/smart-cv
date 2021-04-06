---
title : "Cómo implementar una aplicación Django escalable y segura con Kubernetes"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes-es
image: "https://sergio.afanou.com/assets/images/image-midres-35.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>En este tutorial, implementará una aplicación de encuestas de Django en contenedor en un clúster de Kubernetes.</p>

<p><a href="https://www.djangoproject.com/">Django</a> es un poderoso framework web que puede ayudarle a poner en marcha rápidamente su aplicación o sitio web con Python. Incluye varias características convenientes como un <a href="https://en.wikipedia.org/wiki/Object-relational_mapping">mapeo objeto-relacional</a>, la autenticación de usuarios y una interfaz administrativa personalizable para su aplicación. También incluye un <a href="https://docs.djangoproject.com/en/2.1/topics/cache/">marco de almacenamiento en caché</a> y fomenta el diseño limpio de aplicaciones a través de su <a href="https://docs.djangoproject.com/en/2.1/topics/http/urls/">Despachador URL</a> y <a href="https://docs.djangoproject.com/en/2.1/topics/templates/">sistema de Plantillas</a></p>

<p>En <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Cómo crear una aplicación Django y Gunicorn con Docker</a>, la <a href="https://docs.djangoproject.com/en/3.1/intro/tutorial01/">aplicación de encuestas del Tutorial de Django</a> fue modificada de acuerdo con la metodología de <a href="https://12factor.net/">Twelve-Factor</a> para crear aplicaciones web escalables y nativas en la nube. Esta configuración en contenedores se escaló y protegió con un proxy inverso Nginx y certificados TLS proporcionados por Let&rsquo;s Encrypt en <a href="https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt">Cómo escalar y proteger una aplicación de Django con Docker, Nginx y Let&rsquo;s Encrypt</a>. En este tutorial final de la serie “<a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">De contenedores a Kubernetes con Django</a>”, la aplicación de encuestas de Django modernizada se implementará en un clúster de Kubernetes.</p>

<p><a href="https://kubernetes.io/">Kubernetes</a> es un potente orquestador de contenedores de código abierto que automatiza la implementación, el escalamiento y la administración de aplicaciones en contenedores. Los objetos de Kubernetes, como ConfigMaps y Secrets, permiten centralizar y desvincular la configuración de los contenedores, mientras que los controladores como las implementaciones reinician automáticamente los contenedores fallidos y habilitar el escalamiento rápido de las réplicas de contenedores. El cifrado TLS se activa con un objeto Ingress y el controlador de Ingress de código abierto llamado <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a>. El complemento <a href="https://github.com/jetstack/cert-manager">cert-manager</a> de Kubernetes renueva y emite certificados usando la autoridad de certificación de <a href="https://letsencrypt.org/">Let&rsquo;s Encrypt</a> gratuita.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Para seguir este tutorial, necesitará lo siguiente:</p>

<ul>
<li>Un clúster de Kubernetes 1.15, o una versión posterior, <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">con control de acceso basado en roles</a> (RBCA) activado. Esta configuración usará un clúster de <a href="https://www.digitalocean.com/products/kubernetes/">Kubernetes de DigitalOcean</a>, pero puede crear un clúster usando <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04">otro método</a>.</li>
<li>La herramienta de línea de comandos <code>kubectl</code> instalada en su equipo local y configurada para conectarse a su clúster. Puede leer más sobre la instalación de <code>kubectl</code> <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/">en la documentación oficial</a>. Si está usando un clúster de Kubernetes de DigitalOcean, consulte <a href="https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/">Cómo conectarse a un clúster Kubernetes de DigitalOcean</a> para aprender a conectarse a su clúster usando <code>kubectl</code>.</li>
<li>Un nombre de dominio registrado. Para este tutorial, se utilizará <code>your_domain.com</code> en todo momento. Puede obtener un ejemplar gratis en <a href="http://www.freenom.com/en/index.html">Freenom</a> o utilizar el registrador de dominios que desee.</li>
<li>Un controlador de Ingress <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> y el administrador de certificados TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a> instalado en su clúster y configurado para emitir certificados TLS. Para aprender a instalar y configurar un Ingress con cert-manager, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">Cómo configurar un Ingress de Nginx con Cert-Manager en Kubernetes de DigitalOcean</a>.</li>
<li>Un registro DNS <code>A</code> con <code>your_domain.com</code> orientado a la dirección IP pública del equilibrador de cargas de Ingress. Si usa DigitalOcean para administrar los registros DNS de su dominio, consulte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Cómo administrar registros DNS</a> para aprender a crear registros <code>A</code>.</li>
<li>Un depósito de almacenamiento de objetos S3, como un <a href="https://www.digitalocean.com/products/spaces/">Space de DigitalOcean</a>, para almacenar los archivos estáticos de su proyecto de Django y un conjunto de claves de acceso para ese espacio. Para obtener información sobre cómo crear Spaces, consulte la documentación de <a href="https://www.digitalocean.com/docs/spaces/how-to/create/">Cómo crear Spaces</a>. Para obtener información sobre cómo crear claves de acceso para Spaces, consulte <a href="https://www.digitalocean.com/docs/spaces/how-to/administrative-access/#access-keys">Compartir el acceso a Spaces con claves de acceso</a>. Con cambios menores, puede usar cualquier servicio de almacenamiento de objetos que admita el complemento <a href="https://django-storages.readthedocs.io/en/latest/">django-storages</a></li>
<li>Una instancia de un servidor de PostgreSQL, una base de datos y un usuario para su aplicación de Django. Con cambios menores, puede usar cualquier base de datos que <a href="https://docs.djangoproject.com/en/2.2/ref/databases/">admita Django</a>.

<ul>
<li>La base de datos de PostgreSQL se debería llamar <strong>encuestas</strong> (o cualquier otro nombre fácil de recordar para ingresar en sus archivos de configuración a continuación). En este tutorial, el usuario de la base de datos de Django se denominará <strong>sammy</strong>. Para obtener información sobre cómo crearlos, siga el Paso 1 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-1-%E2%80%94-creating-the-postgresql-database-and-user">Cómo crear una aplicación de Django y Gunicorn con Docker</a>. Debe realizar estos pasos desde su máquina local.</li>
<li>En este tutorial, se utiliza un <a href="https://www.digitalocean.com/products/managed-databases/">clúster de PostgreSQL administrado</a> de DigitalOcean. Para obtener información sobre cómo crear un clúster, consulte la <a href="https://www.digitalocean.com/docs/databases/how-to/clusters/create/">documentación de Bases de datos administradas</a> de DigitalOcean.</li>
<li>También puede instalar y ejecutar su propia instancia de PostgreSQL. Para obtener información sobre la instalación y administración de PostgreSQL en un servidor de Ubuntu, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04">Cómo instalar y utilizar PostgreSQL en Ubuntu 18.04</a>.</li>
</ul></li>
<li>Un <a href="https://hub.docker.com/">Docker Hub</a> cuenta y repositorio público. Para obtener más información sobre cómo crearlos, consulte <a href="https://docs.docker.com/docker-hub/repos/">Repositorios</a> de la documentación de Docker.</li>
<li>El motor Docker instalado en su máquina local. Consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04">Cómo instalar y usar Docker en Ubuntu 18.04</a> para obtener más información.</li>
</ul>

<p>Una vez que haya configurado estos componentes, estará listo para comenzar con esta guía.</p>

<h2 id="paso-1-clonar-y-probar-la-aplicación">Paso 1: Clonar y probar la aplicación</h2>

<p>En este paso, clonaremos el código de la aplicación de GitHub y configuraremos ajustes como las credenciales de la base de datos y las claves de almacenamiento de objetos.</p>

<p>El código de la aplicación y Dockerfile se encuentran en la rama <code>polls-docker</code> del <a href="https://github.com/do-community/django-polls/tree/polls-docker">repositorio de GitHub</a> de la aplicacipon Polls en el tutorial de Django. Este repositorio contiene el código para la <a href="https://docs.djangoproject.com/en/3.0/intro/">aplicación Polls de muestra</a> de la documentación de Django, que le enseña cómo crear una aplicación de encuestas desde cero.</p>

<p>La rama <code>polls-docker</code> contiene una versión con Docker de esta aplicación Polls. Para obtener información sobre cómo se modificó la aplicación Polls para que funcionara de forma eficaz en un entorno de contenedores, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Cómo crear una aplicación de Django y Gunicorn con Docker</a>.</p>

<p>Comience usando <code>git</code> para clonar la rama <code>polls-docker</code> del <a href="https://github.com/do-community/django-polls">repositorio de GitHub</a> de la aplicación Polls del tutorial de Django en su máquina local:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
</li></ul></code></pre>
<p>Diríjase al directorio <code>django-polls</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd django-polls
</li></ul></code></pre>
<p>Este directorio contiene el código de Python de la aplicación de Django, un <code>Dockerfile</code> que Docker utilizará para crear la imagen del contenedor, y un archivo <code>env</code> que contiene una lista de las variables de entorno que se pasarán al entorno de ejecución del contenedor. Inspeccione el <code>Dockerfile</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cat Dockerfile
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>FROM python:3.7.4-alpine3.10

ADD django-polls/requirements.txt /app/requirements.txt

RUN set -ex \
    &amp;&amp; apk add --no-cache --virtual .build-deps postgresql-dev build-base \
    &amp;&amp; python -m venv /env \
    &amp;&amp; /env/bin/pip install --upgrade pip \
    &amp;&amp; /env/bin/pip install --no-cache-dir -r /app/requirements.txt \
    &amp;&amp; runDeps="$(scanelf --needed --nobanner --recursive /env \
        | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
        | sort -u \
        | xargs -r apk info --installed \
        | sort -u)" \
    &amp;&amp; apk add --virtual rundeps $runDeps \
    &amp;&amp; apk del .build-deps

ADD django-polls /app
WORKDIR /app

ENV VIRTUAL_ENV /env
ENV PATH /env/bin:$PATH

EXPOSE 8000

CMD ["gunicorn", "--bind", ":8000", "--workers", "3", "mysite.wsgi"]
</code></pre>
<p>Este Dockerfile utiliza la <a href="https://hub.docker.com/_/python">imagen de Docker</a> oficial de Python 3.7.4 como base e instala los requisitos del paquete de Python de Django y Gunicorn, tal como se define en el archivo <code>django-polls/requirements.txt</code>. A continuación, elimina algunos archivos de compilación innecesarios, copia el código de la aplicación en la imagen y establece el <code>PATH</code> de ejecución. Por último, declara que el puerto <code>8000</code> se utilizará para aceptar conexiones de contenedores entrantes y ejecuta <code>gunicorn</code> con 3 trabajadores, escuchando en el puerto <code>8000</code>.</p>

<p>Para obtener más información sobre cada uno de los pasos de este Dockerfile, consulte el Paso 6 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-6-%E2%80%94-writing-the-application-dockerfile">Cómo crear una aplicación de Django y Gunicorn con Docker</a>.</p>

<p>Ahora, compile la imagen con <code>docker build</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker build -t polls .
</li></ul></code></pre>
<p>Nombramos la imagen <code>polls</code> con el indicador <code>-t</code> y pasamos el directorio actual como <em>contexto de compilación</em>, el conjunto de archivos a los que se debe hacer referencia al construir la imagen.</p>

<p>Una vez que Docker haya compilado y etiquetado la imagen, enumere las imágenes disponibles utilizando <code>docker images</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker images
</li></ul></code></pre>
<p>Debería ver la imagen <code>polls</code> enumerada:</p>
<pre class="code-pre "><code>OutputREPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
polls               latest              80ec4f33aae1        2 weeks ago         197MB
python              3.7.4-alpine3.10    f309434dea3a        8 months ago        98.7MB
</code></pre>
<p>Antes de ejecutar el contenedor de Django, debemos configurar su entorno de ejecución utilizando el archivo <code>env</code> presente en el directorio actual. Este archivo se pasará al comando <code>docker run</code> que se utiliza para ejecutar el contenedor y Docker insertará las variables de entorno configuradas en el entorno en ejecución del contenedor.</p>

<p>Abra el archivo <code>env</code> con <code>nano</code> o su editor favorito:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano env
</li></ul></code></pre><div class="code-label " title="django-polls/env">django-polls/env</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=
DEBUG=True
DJANGO_ALLOWED_HOSTS=
DATABASE_ENGINE=postgresql_psycopg2
DATABASE_NAME=polls
DATABASE_USERNAME=
DATABASE_PASSWORD=
DATABASE_HOST=
DATABASE_PORT=
STATIC_ACCESS_KEY_ID=
STATIC_SECRET_KEY=
STATIC_BUCKET_NAME=
STATIC_ENDPOINT_URL=
DJANGO_LOGLEVEL=info
</code></pre>
<p>Complete los valores que faltan para las siguientes claves:</p>

<ul>
<li><code>DJANGO_SECRET_KEY</code>: establézcala en un valor único e impredecible, como se detalla en la <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#secret-key">documentación de Django</a>. Se proporciona un método para generar esta clave en la sección <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Ajustar la configuración de la aplicación</a> del tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Aplicaciones escalables de Django</a>.</li>
<li><code>DJANGO_ALLOWED_HOSTS</code>: esta variable asegura la aplicación y evita ataques a través del encabezado de host HTTP. Para propósitos de prueba, establézcala en <code>*</code>, un comodín que coincidirá con todos los hosts. En producción, debe establecerlo en <code>your_domain.com</code>. Para obtener más información sobre esta configuración de Django, consulte la sección <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#allowed-hosts">Configuración principal</a> en la documentación de Django.</li>
<li><code>DATABASE_USERNAME</code>: establézcalo en el usuario de la base de datos de PostgreSQL creado en los pasos de requisitos previos.</li>
<li><code>DATABASE_NAME</code>: establézcalo en <code>polls</code> o el nombre de la base de datos de PostgreSQL creado en los pasos de requisitos previos.</li>
<li><code>DATABASE_PASSWORD</code>: establézcala en la contraseña de la base de datos de PostgreSQL creada en los pasos de requisitos previos.</li>
<li><code>DATABASE_HOST</code>: establézcalo en el nombre de host de su base de datos.</li>
<li><code>DATABASE_PORT</code>: establézcalo en el puerto de su base de datos.</li>
<li><code>STATIC_ACCESS_KEY_ID</code>: establézcala en la clave de acceso de Space o en el almacenamiento de objetos.</li>
<li><code>STATIC_SECRET_KEY</code>: establézcala en Secret de la clave de acceso de su Space o en el almacenamiento de objetos.</li>
<li><code>STATIC_BUCKET_NAME</code>: establézcalo en el nombre de su Space o en el depósito de almacenamiento de objetos.</li>
<li><code>STATIC_ENDPOINT_URL</code>: establézcala en la URL de extremo de Spaces o del almacenamiento del objeto que corresponda, por ejemplo, <code>https://<span class="highlight">your_space_name</span>.nyc3.digitaloceanspaces.com</code>, si su Space está ubicado en la región <code>nyc3</code>.</li>
</ul>

<p>Una vez que haya finalizado la edición, guarde y cierre el archivo.</p>

<p>En el siguiente paso, ejecutaremos el contenedor configurado a nivel local y crearemos el esquema de la base de datos. También cargaremos activos estáticos como hojas de estilos e imágenes al almacenamiento de objetos.</p>

<h2 id="paso-2-crear-el-esquema-de-la-base-de-datos-y-cargar-activos-en-el-almacenamiento-de-objetos">Paso 2: Crear el esquema de la base de datos y cargar activos en el almacenamiento de objetos</h2>

<p>Con el contenedor creado y configurado, utilizaremos <code>docker run</code> para anular el <code>CMD</code> establecido en Dockerfile y crear el esquema de la base de datos utilizando los comandos <code>manage.py makemigrations</code> y <code>manage.py migrate</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"
</li></ul></code></pre>
<p>Ejecutamos la imagen del contenedor <code>polls:latest</code>, pasamos el archivo de variables de entorno que acabamos de modificar y anulamos el comando de Dockerfile con <code>sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"</code>, lo que creará el esquema de la base de datos definido mediante el código de la aplicación.</p>

<p>Si lo está ejecutando por primera vez, debería ver lo siguiente:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>No changes detected
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying polls.0001_initial... OK
  Applying sessions.0001_initial... OK
</code></pre>
<p>Esto indica que el esquema de la base de datos se ha creado correctamente.</p>

<p>Si no está ejecutando <code>migrate</code> por primera vez, Django realizará un no-op a menos que el esquema de la base de datos haya cambiado.</p>

<p>A continuación, ejecutaremos otra instancia del contenedor de la aplicación y utilizaremos una shell interactiva en su interior para crear un usuario administrativo para el proyecto de Django.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -i -t --env-file env polls sh
</li></ul></code></pre>
<p>Esto le proporcionará una línea de comandos de shell dentro del contenedor en ejecución que puede usar para crear el usuario de Django:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python manage.py createsuperuser
</li></ul></code></pre>
<p>Ingrese un nombre de usuario, una dirección de correo electrónico y una contraseña para su usuario y, una vez que haya creado el usuario, presione <code>CTRL+D</code> para salir del contenedor y cerrarlo.</p>

<p>Por último, generaremos los archivos estáticos de la aplicación y los subiremos al Space de DigitalOcean utilizando <code>collectstatic</code>. Tenga en cuenta que esta operación puede tardar un poco en completarse.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py collectstatic --noinput"
</li></ul></code></pre>
<p>Una vez que estos archivos se hayan generado y cargado, obtendrá el siguiente resultado.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>121 static files copied.
</code></pre>
<p>Ahora, podemos ejecutar la aplicación:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env -p 80:8000 polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[2019-10-17 21:23:36 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-10-17 21:23:36 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2019-10-17 21:23:36 +0000] [1] [INFO] Using worker: sync
[2019-10-17 21:23:36 +0000] [7] [INFO] Booting worker with pid: 7
[2019-10-17 21:23:36 +0000] [8] [INFO] Booting worker with pid: 8
[2019-10-17 21:23:36 +0000] [9] [INFO] Booting worker with pid: 9
</code></pre>
<p>Aquí, ejecutamos el comando predeterminado definido en el Dockerfile, <code>gunicorn --bind :8000 --workers 3 mysite.wsgi:application</code> y exponemos el puerto del contenedor <code>8000</code> para que el puerto <code>80</code> en su máquina local se asigne al puerto <code>8000</code> del contenedor <code>polls</code>.</p>

<p>Ahora, debería poder navegar a la aplicación <code>polls</code> desde su navegador web al <code>http://localhost</code> en la barra de direcciones URL. Dado que no hay una ruta definida para la ruta <code>/</code> , es probable que reciba un error de <code>404 Page Not Found</code> (Página no encontrada), lo que es de esperar.</p>

<p>Diríjase a <code>http://localhost/polls</code> para ver la interfaz de la aplicación Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interfaz de la aplicación Polls"></p>

<p>Para ver la interfaz administrativa, visite <code>http://localhost/admin</code>. Debería ver la ventana de autenticación de administración de la aplicación Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Página de autenticación de administración de Polls"></p>

<p>Ingrese el nombre de usuario administrativo y la contraseña que creó con el comando <code>createsuperuser</code>.</p>

<p>Después de la autenticación, podrá acceder a la interfaz administrativa de la aplicación Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin_main.png" alt="Interfaz principal de administración de Polls"></p>

<p>Tenga en cuenta que los que los recursos estáticos de las aplicaciones <code>admin</code> y <code>polls</code> se entregan directamente desde el almacenamiento de objetos. Para confirmar esto, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#testing-spaces-static-file-delivery">Prueba de la entrega de archivos estáticos de Spaces</a>.</p>

<p>Cuando haya terminado de explorar, presione <code>CTRL+C</code> en la ventana de terminal que está ejecutando el contenedor de Docker para cerrar el contenedor.</p>

<p>Con la imagen de Docker de la aplicación de Django probada, los activos estáticos que se cargan en el almacenamiento de objetos y el esquema de la base de datos configurado y listo para ser utilizado en su aplicación, estará listo para subir la imagen de su aplicación de Django a un registro de imágenes como Docker Hub.</p>

<h2 id="paso-3-introducir-la-imagen-de-la-aplicación-de-django-en-docker-hub">Paso 3: Introducir la imagen de la aplicación de Django en Docker Hub</h2>

<p>Para implementar su aplicación en Kubernetes, la imagen de su aplicación debe cargarse en un registro como <a href="https://hub.docker.com/">Docker Hub</a>. Kubernetes extraerá la imagen de la aplicación de su repositorio y luego la implementará en su clúster.</p>

<p>Puede usar un registro de Docker privado, como <a href="https://www.digitalocean.com/products/container-registry/">DigitalOcean Container Registry</a>, actualmente gratuito en Early Access o un registro de Docker público como Docker Hub. Docker Hub también le permite crear repositorios de Docker privados. Un repositorio público permite a cualquiera ver y extraer las imágenes del contenedor, mientras que un repositorio privado permite restringir el acceso a usted y a los miembros de su equipo.</p>

<p>En este tutorial, introduciremos la imagen de Django en el repositorio de Docker Hub público creado en los requisitos previos. También puede introducir su imagen en un repositorio privado, pero extraer imágenes de un repositorio privado no es el tema de este artículo. Para obtener más información sobre la autenticación de Kubernetes con Docker Hub y la extracción de imágenes privadas, consulte <a href="https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/">Extraer una imagen de un registro privado</a> en la documentación de Kubernetes.</p>

<p>Comience iniciando sesión en Docker Hub en su máquina local:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
</code></pre>
<p>Ingrese su nombre de usuario y contraseña de Docker Hub para iniciar sesión.</p>

<p>La imagen de Django actualmente tiene la etiqueta <code>polls:latest</code>. Para introducirla en su repositorio de Docker Hub, vuelva a etiquetar la imagen con su nombre de usuario de Docker Hub y nombre de repositorio:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag polls:latest <span class="highlight">your_dockerhub_username</span>/<span class="highlight">your_dockerhub_repo_name</span>:latest
</li></ul></code></pre>
<p>Introduzca la imagen en el repositorio:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">sammy</span>/<span class="highlight">sammy-django</span>:latest
</li></ul></code></pre>
<p>En este tutorial, el nombre de usuario de Docker Hub es <strong>sammy</strong> y el nombre de repositorio es <strong>sammy-django</strong>. Debe sustituir estos valores por su propio nombre de usuario y nombre de repositorio de Docker Hub.</p>

<p>Verá un resultado que se actualiza a medida que las capas de imágenes se insertan en Docker Hub.</p>

<p>Ahora que su imagen está disponible para Kubernetes en Docker Hub, puede comenzar a implementarla en su clúster.</p>

<h2 id="paso-4-configurar-configmap">Paso 4: Configurar ConfigMap</h2>

<p>Cuando ejecutamos el contenedor de Django a nivel local, pasamos el archivo <code>env</code> a <code>docker run</code> para insertar variables de configuración en el entorno de tiempo de ejecución. En Kubernetes, las variables de configuración pueden insertarse usando <a href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/">ConfigMaps</a> y <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Secrets</a>.</p>

<p>ConfigMaps debe usarse para almacenar información de configuración no confidencial, la configuración de la aplicación, mientras que Secrets debe usarse para información confidencial, como claves de API y credenciales de la base de datos. Ambos se insertan en contenedores de forma similar, pero Secrets tienen funciones de control de acceso y seguridad adicionales como <a href="https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/">encriptación en reposo</a>. Secrets también almacenan datos en base64, mientras que ConfigMaps almacenan datos en texto simple.</p>

<p>Para comenzar, cree un directorio llamado <code>yaml</code> en el que almacenaremos nuestros manifiestos de Kubernetes. Diríjase al directorio.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir yaml
</li><li class="line" data-prefix="$">cd
</li></ul></code></pre>
<p>Abra un archivo llamado <code>polls-configmap.yaml</code> en <code>nano</code> o su editor de texto preferido:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-configmap.yaml
</li></ul></code></pre>
<p>Pegue el siguiente manifiesto de ConfigMap:</p>
<div class="code-label " title="polls-configmap.yaml">polls-configmap.yaml</div><pre class="code-pre "><code class="code-highlight language-bash">apiVersion: v1
kind: ConfigMap
metadata:
  name: polls-config
data:
  DJANGO_ALLOWED_HOSTS: "*"
  STATIC_ENDPOINT_URL: "https://<span class="highlight">your_space_name</span>.<span class="highlight">space_region</span>.digitaloceanspaces.com"
  STATIC_BUCKET_NAME: "<span class="highlight">your_space_name</span>"
  DJANGO_LOGLEVEL: "info"
  DEBUG: "True"
  DATABASE_ENGINE: "postgresql_psycopg2"
</code></pre>
<p>Extrajimos la configuración no sensible del archivo <code>env</code> modificado en el <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Paso 1</a> y la pegamos en un manifiesto de ConfigMap. El objeto ConfigMap se llama <code>polls-config</code>. Copie los mismos valores que ingresó en el archivo <code>env</code> en el paso anterior.</p>

<p>Para propósitos de prueba, establezca <code>DJANGO_ALLOWED_HOSTS</code> como <code>*</code> para desactivar el filtrado del encabezado de host. En un entorno de producción, debe establecerlo en el dominio de su aplicación.</p>

<p>Cuando haya terminado de editar el archivo, guárdelo y ciérrelo.</p>

<p>Cree ConfigMap en su clúster usando <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-configmap.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>configmap/polls-config created
</code></pre>
<p>Después de crear ConfigMap, crearemos el Secret que usó nuestra aplicación en el siguiente paso.</p>

<h2 id="paso-5-configurar-secret">Paso 5: Configurar Secret</h2>

<p>Los valores de Secret deben <a href="https://en.wikipedia.org/wiki/Base64">estar codificados en base64</a>, lo que significa que crear objetos de Secret en su clúster es ligeramente más complicado que crear ConfigMaps. Puede repetir el proceso del paso anterior, codificando de forma manual los valores de Secret de base64 y pegándolos en un archivo de manifiesto. También puede crearlos usando un archivo de variable de entorno, <code>kubectl create</code> y el indicador <code>--from-env-file</code>, que realizaremos en este paso.</p>

<p>Una vez más, usaremos el archivo <code>env</code> del <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Paso 1</a>, eliminando las variables insertadas en ConfigMap. Cree una copia del archivo <code>env</code> llamado <code>polls-secrets</code> en el directorio <code>yaml</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp ../env ./polls-secrets
</li></ul></code></pre>
<p>Edite el archivo en el editor de su preferencia:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-secrets
</li></ul></code></pre><div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=
DEBUG=True
DJANGO_ALLOWED_HOSTS=
DATABASE_ENGINE=postgresql_psycopg2
DATABASE_NAME=polls
DATABASE_USERNAME=
DATABASE_PASSWORD=
DATABASE_HOST=
DATABASE_PORT=
STATIC_ACCESS_KEY_ID=
STATIC_SECRET_KEY=
STATIC_BUCKET_NAME=
STATIC_ENDPOINT_URL=
DJANGO_LOGLEVEL=info
</code></pre>
<p>Elimine todas las variables insertadas en el manifiesto de ConfigMap. Cuando haya terminado, debe verse así:</p>
<div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=<span class="highlight">your_secret_key</span>
DATABASE_NAME=polls
DATABASE_USERNAME=<span class="highlight">your_django_db_user</span>
DATABASE_PASSWORD=<span class="highlight">your_django_db_user_password</span>
DATABASE_HOST=<span class="highlight">your_db_host</span>
DATABASE_PORT=<span class="highlight">your_db_port</span>
STATIC_ACCESS_KEY_ID=<span class="highlight">your_space_access_key</span>
STATIC_SECRET_KEY=<span class="highlight">your_space_access_key_secret</span>
</code></pre>
<p>Asegúrese de usar los mismos valores que se utilizan en el <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Paso 1</a>. Cuando haya terminado, guarde y cierre el archivo.</p>

<p>Cree el Secret en su clúster usando <code>kubectl create secret</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl create secret generic polls-secret --from-env-file=poll-secrets
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>secret/polls-secret created
</code></pre>
<p>Aquí creamos un objeto Secret llamado <code>polls-secret</code> y pasamos el archivo de secrets que acabamos de crear.</p>

<p>Puede inspeccionar el Secret usando <code>kubectl describe</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe secret polls-secret
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Name:         polls-secret
Namespace:    default
Labels:       &lt;none&gt;
Annotations:  &lt;none&gt;

Type:  Opaque

Data
====
DATABASE_PASSWORD:     8 bytes
DATABASE_PORT:         5 bytes
DATABASE_USERNAME:     5 bytes
DJANGO_SECRET_KEY:     14 bytes
STATIC_ACCESS_KEY_ID:  20 bytes
STATIC_SECRET_KEY:     43 bytes
DATABASE_HOST:         47 bytes
DATABASE_NAME:         5 bytes
</code></pre>
<p>En este punto, almacenó la configuración de su aplicación en el clúster de Kubernetes usando los tipos de objeto Secret y ConfigMap. Ahora estamos listos para implementar la aplicación en el clúster.</p>

<h2 id="paso-6-implementar-la-aplicación-de-django-usando-una-implementación">Paso 6: Implementar la aplicación de Django usando una implementación</h2>

<p>En este paso, creará una implementación para su aplicación de Django. Una implementación de Kubernetes es un <em>controlador</em> que puede usarse para administrar aplicaciones sin estado en su clúster. Un controlador es un bucle de control que regula las cargas de trabajo aumentándolas o reduciéndolas. Los controladores también reinician y eliminan los contenedores fallidos.</p>

<p>Las implementaciones controlan uno o más Pods, la unidad implementable más pequeña de un clúster de Kubernetes. Los Pods están compuestos por uno o más contenedores. Para obtener más información sobre los diferentes tipos de cargas de trabajo que puede iniciar, consulte <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes">Introducción a Kubernetes</a>.</p>

<p>Empiece por abrir un archivo llamado <code>polls-deployment.yaml</code> en su editor favorito:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-deployment.yaml
</li></ul></code></pre>
<p>Pegue el siguiente manifiesto de implementación:</p>
<div class="code-label " title="polls-deployment.yaml">polls-deployment.yaml</div><pre class="code-pre "><code class="code-highlight language-bash">apiVersion: apps/v1
kind: Deployment
metadata:
  name: polls-app
  labels:
    app: polls
spec:
    replicas: 2
  selector:
    matchLabels:
      app: polls
  template:
    metadata:
      labels:
        app: polls
    spec:
      containers:
        - image: <span class="highlight">your_dockerhub_username</span>/<span class="highlight">app_repo_name</span>:latest
          name: polls
          envFrom:
          - secretRef:
              name: polls-secret
          - configMapRef:
              name: polls-config
          ports:
            - containerPort: 8000
              name: gunicorn
</code></pre>
<p>Complete el nombre de la imagen del contenedor correspondiente, haciendo referencia a la imagen de Django Polls que agregó a Docker Hub en el <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-2-%E2%80%94-creating-the-database-schema-and-uploading-assets-to-object-storage">Paso 2</a>.</p>

<p>Aquí definimos una implementación de Kubernetes llamada <code>polls-app</code> y la etiquetamos con el par clave-valor <code>app: polls</code>. Especificamos que queremos ejecutar dos réplicas del Pod especificado debajo del campo <code>template</code>.</p>

<p>Usando <code>envFrom</code> con <code>secretRef</code> y <code>configMapRef</code>, especificamos que todos los datos del Secret <code>polls-secret</code> y el ConfigMap <code>polls-config</code> deben insertarse en los contenedores como variables de entorno. Las claves ConfigMap y Secret se convierten en los nombres de las variables de entorno.</p>

<p>Por último, exponemos el <code>containerPort</code> <code>8000</code> y lo nombramos <code>gunicorn</code>.</p>

<p>Para obtener más información sobre la configuración de las implementaciones de Kubernetes, consulte <a href="https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/">Implementaciones</a> en la documentación de Kubernetes.</p>

<p>Cuando haya terminado de editar el archivo, guárdelo y ciérrelo.</p>

<p>Cree la implementación en su clúster usando <code>kubectl apply -f</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-deployment.yaml
</li></ul></code></pre><pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">deployment.apps/polls-app created
</li></ul></code></pre>
<p>Compruebe que la implementación se implementó correctamente usando <code>kubectl get</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get deploy polls-app
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME        READY   UP-TO-DATE   AVAILABLE   AGE
polls-app   2/2     2            2           6m38s
</code></pre>
<p>Si encuentra un error o considera que algo no está funcionando, puede usar <code>kubectl describe</code> para inspeccionar la implementación fallida:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe deploy
</li></ul></code></pre>
<p>Puede inspeccionar los dos Pods usando <code>kubectl get pod</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get pod
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                         READY   STATUS    RESTARTS   AGE
polls-app-847f8ccbf4-2stf7   1/1     Running   0          6m42s
polls-app-847f8ccbf4-tqpwm   1/1     Running   0          6m57s
</code></pre>
<p>Ahora hay dos réplicas de su aplicación de Django ejecutándose en el clúster. Para acceder a la aplicación, es necesario crear un Service de Kubernetes, lo cual es lo que haremos a continuación.</p>

<h2 id="paso-7-permitir-el-acceso-externo-mediante-un-service">Paso 7: Permitir el acceso externo mediante un Service</h2>

<p>En este paso, creará un Service para su aplicación de Django. Un Service de Kubernetes es una abstracción que le permite exponer un conjunto de Pods en ejecución como servicio de red. Mediante un Service, puede crear un extremo estable para su aplicación que no cambia a medida que los Pods mueren y se vuelvan a crear.</p>

<p>Existen varios tipos de Service, incluidos ClusterIP Services, que exponen el servicio en un IP interno del clúster, NodePort Services que exponen el servicio en cada Nodo en un puerto estático llamado NodePort y LoadBalancer Services que proporcionan un equilibrador de carga en la nube para dirigir el tráfico externo a los Pods de su clúster (a través de NodePorts, que crea automáticamente). Para obtener más información sobre esos servicios, consulte el artículo <a href="https://kubernetes.io/docs/concepts/services-networking/service/">Service</a> en la documentación de Kubernetes.</p>

<p>En nuestra configuración final, usaremos un Service ClusterIP que se expone usando un Ingress y el controlador de Ingress configurado en los requisitos previos de esta guía. Por ahora, para comprobar que todo funciona correctamente, crearemos un servicio de NodePort temporal para acceder a la aplicación de Django.</p>

<p>Comience creando un archivo llamado <code>polls-svc.yaml</code> usando el editor de su preferencia:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Pegue el siguiente manifiesto de Service:</p>
<div class="code-label " title="polls-svc.yaml">polls-svc.yaml</div><pre class="code-pre "><code class="code-highlight language-bash">apiVersion: v1
kind: Service
metadata:
  name: polls
  labels:
    app: polls
spec:
  type: NodePort
  selector:
    app: polls
  ports:
    - port: 8000
      targetPort: 8000
</code></pre>
<p>Aquí creamos un Service NodePort llamado <code>polls</code> y le asignamos la etiqueta <code>app: polls</code>. A continuación, seleccionamos los Pods backend con la etiqueta <code>app: polls</code> y orientamos sus puertos <code>8000</code>.</p>

<p>Cuando haya terminado de editar el archivo, guárdelo y ciérrelo.</p>

<p>Implemente el Service usando <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls created
</code></pre>
<p>Confirme que su Service se creó usando <code>kubectl get svc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
polls   NodePort   10.245.197.189   &lt;none&gt;        8000:32654/TCP   59s
</code></pre>
<p>Este resultado muestra la IP y NodePort internos del clúster de Service (<code>32654</code>). Para conectarse al servicio, se necesitan las direcciones IP externas de los nodos del clúster:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get node -o wide
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-7no0qd9e0-364fd   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.5    203.0.113.1   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fi   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.4    203.0.113.2    Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fv   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.3    203.0.113.3   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
</code></pre>
<p>En su navegador web, visite su aplicación Polls usando la dirección IP externa de cualquier nodo y el NodePort. Dado el resultado anterior, la URL de la aplicación sería la siguiente: <code>http://<span class="highlight">203.0.113.1</span>:32654/polls</code>.</p>

<p>Debería poder ver la misma interfaz de la aplicación Polls a la que accedió a nivel local en el Paso 1:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interfaz de la aplicación Polls"></p>

<p>Puede repetir la misma prueba usando la ruta <code>/admin:</code> <code>http://<span class="highlight">203.0.113.1</span>:32654/admin</code>. Debería ver la misma interfaz administrativa que antes:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Página de autenticación de administración de Polls"></p>

<p>En esta etapa, ya implementó dos réplicas del contenedor de la aplicación Polls de Django usando una implementación. También creó un extremo de red estable para estas dos réplicas e hizo que se pueda acceder a ellas externamente usando un servicio de NodePort</p>

<p>El paso final de este tutorial es proteger el tráfico externo a su aplicación usando HTTPS. Para ello, usaremos el controlador de <code>Ingress-nginx</code> instalado en los requisitos previos y crearemos un objeto de Ingress para dirigir el tráfico externo al servicio de Kubernetes de <code>polls</code>.</p>

<h2 id="paso-8-configurar-https-usando-el-ingress-y-cert-manager-de-nginx">Paso 8: Configurar HTTPS usando el Ingress y cert-manager de Nginx</h2>

<p>Los <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingress</a> de Kubernetes le permiten dirigir de manera flexible el tráfico del exterior de su clúster de Kubernetes a servicios dentro de su clúster. Esto se realiza usando objetos de Ingress, que definen reglas para dirigir el tráfico HTTP y HTTPS a servicios de Kubernetes, y <em>controladores</em> de Ingress, que implementan las reglas equilibrando la carga de tráfico y dirigiéndola a los servicios de backend correspondientes.</p>

<p>En los requisitos previos, instaló el controlador de <a href="https://github.com/kubernetes/ingress-nginx">Ingress-nginx</a> y el complemento para automatizar certificados TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a>. También ha configurado el ClusterIssuers de ensayo y producción de su dominio usando la autoridad de certificación Let&rsquo;s Encrypt, y ha creado un Ingress para probar la emisión de certificados y el cifrado TLS en dos servicios de backend ficticios. Antes de continuar con este paso, debe eliminar el Ingress <code>echo-ingress</code> creado en el <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">tutorial de requisitos previos</a>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl delete ingress echo-ingress
</li></ul></code></pre>
<p>Si desea, también puede eliminar los servicios y las implementaciones ficticios usando <code>kubectl delete svc</code> y <code>kubectl delete deploy</code>, pero no es necesario que lo haga para completar este tutorial.</p>

<p>También debería haber creado un registro DNS <code>A</code> con <code><span class="highlight">your_domain.com</span></code> orientado a la dirección IP pública del equilibrador de cargas de Ingress. Si usa un equilibrador de carga de DigitalOcean, puede encontrar esta dirección IP en la sección <a href="https://cloud.digitalocean.com/networking/load_balancers">Equilibradores de carga</a> del panel de control. Si también usa DigitalOcean para administrar los registros DNS de su dominio, consulte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Cómo administrar registros DNS</a> para aprender a crear registros <code>A</code>.</p>

<p>Si usa Kubernetes de DigitalOcean, también asegúrese de implementar la solución descrita en el <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-(optional)">Paso 5 de Cómo configurar un Ingress de Nginx con Cert-Manager en Kubernetes de DigitalOcean</a>.</p>

<p>Una vez que tenga un registro <code>A</code> que apunte al equilibrador de carga del controlador de Ingress, puede crear un Ingress para <code><span class="highlight">your_domain.com</span></code> y el servicio <code>polls</code>.</p>

<p>Abra un archivo llamado <code>polls-ingress.yaml</code> usando el editor de su preferencia:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Pegue el siguiente manifiesto de Ingress:</p>
<pre class="code-pre "><code>[polls-ingress.yaml]
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: polls-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-staging"
spec:
  tls:
  - hosts:
    - <span class="highlight">your_domain.com</span>
    secretName: polls-tls
  rules:
  - host: <span class="highlight">your_domain.com</span>
    http:
      paths:
      - backend:
          serviceName: polls
          servicePort: 8000
</code></pre>
<p>Creamos un objeto de Ingress llamado <code>polls-ingress</code> y lo anotamos para indicarle al plano de control que utilice el controlador de Ingress-nginx y el ClusterIssuer de ensayo. También habilitamos TLS para <code>your_domain.com</code> y almacenamos el certificado y la clave privada en un secret llamado <code>polls-tls</code>. Por último, definimos una regla para dirigir el tráfico del host <code>your_domain.com</code> al servicio <code>polls</code> del puerto <code>8000</code>.</p>

<p>Cuando haya terminado de editar el archivo, guárdelo y ciérrelo.</p>

<p>Cree el Ingress en su clúster usando <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress created
</code></pre>
<p>Puede usar <code>kubectl describe</code> para rastrear el estado del Ingress que acaba de crear:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe ingress polls-ingress
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Name:             polls-ingress
Namespace:        default
Address:          workaround.your_domain.com
Default backend:  default-http-backend:80 (&lt;error: endpoints "default-http-backend" not found&gt;)
TLS:
  polls-tls terminates your_domain.com
Rules:
  Host        Path  Backends
  ----        ----  --------
  your_domain.com
                 polls:8000 (10.244.0.207:8000,10.244.0.53:8000)
Annotations:  cert-manager.io/cluster-issuer: letsencrypt-staging
              kubernetes.io/ingress.class: nginx
Events:
  Type    Reason             Age   From                      Message
  ----    ------             ----  ----                      -------
  Normal  CREATE             51s   nginx-ingress-controller  Ingress default/polls-ingress
  Normal  CreateCertificate  51s   cert-manager              Successfully created Certificate "polls-tls"
  Normal  UPDATE             25s   nginx-ingress-controller  Ingress default/polls-ingress
</code></pre>
<p>También puede ejecutar un <code>describe</code> en el certificado <code>polls-tls</code> para confirmar una vez más que se creó de manera correcta:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe certificate polls-tls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
Events:
  Type    Reason     Age    From          Message
  ----    ------     ----   ----          -------
  Normal  Issuing    3m33s  cert-manager  Issuing certificate as Secret does not exist
  Normal  Generated  3m32s  cert-manager  Stored new private key in temporary Secret resource "polls-tls-v9lv9"
  Normal  Requested  3m32s  cert-manager  Created new CertificateRequest resource "polls-tls-drx9c"
  Normal  Issuing    2m58s  cert-manager  The certificate has been successfully issued
</code></pre>
<p>Esto confirma que el certificado TLS ha sido emitido con éxito y que el cifrado HTTPS ahora está activo para <code>your_domain.com</code>.</p>

<p>Dado que usamos el ClusterIssuer de ensayo, la mayoría de los navegadores de Internet no confiarán en el certificado Let&rsquo;s Encrypt que emitió. Por lo tanto, dirigirse a <code>your_domain.com</code> lo llevará a una página de error.</p>

<p>Para enviar una solicitud de prueba, usaremos <code>wget</code> desde la línea de comandos:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget -O - http://<span class="highlight">your_domain.com</span>/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
ERROR: cannot verify your_domain.com's certificate, issued by ‘CN=Fake LE Intermediate X1’:
  Unable to locally verify the issuer's authority.
To connect to your_domain.com insecurely, use `--no-check-certificate'.
</code></pre>
<p>Usaremos el indicador <code>--no-check-certificate</code> sugerido para omitir la validación del certificado:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget --no-check-certificate -q -O - http://your_domain.com/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>

&lt;link rel="stylesheet" type="text/css" href="https://your_space.nyc3.digitaloceanspaces.com/django-polls/static/polls/style.css"&gt;


    &lt;p&gt;No polls are available.&lt;/p&gt;
</code></pre>
<p>Este resultado muestra el HTML de la página de la interfaz <code>/polls</code>, lo que confirma también que la hoja de estilos se toma desde el almacenamiento de objetos.</p>

<p>Ahora que probó con éxito la emisión de certificados usando el ClusterIssuer de ensayo, puede modificar el Ingress para usar el ClusterIssuer de producción.</p>

<p>Abra <code>polls-ingress.yaml</code> para editar una vez más:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Modifique la anotación <code>cluster-issuer</code>:</p>
<pre class="code-pre "><code>[polls-ingress.yaml]
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: polls-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-<span class="highlight">prod</span>"
spec:
  tls:
  - hosts:
    - <span class="highlight">your_domain.com</span>
    secretName: polls-tls
  rules:
  - host: <span class="highlight">your_domain.com</span>
    http:
      paths:
      - backend:
          serviceName: polls
          servicePort: 8000
</code></pre>
<p>Cuando termine, guarde y cierre el archivo. Actualice el Ingress usando <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress configured
</code></pre>
<p>Puede usar <code>kubectl describe certificate polls-tls</code> y <code>kubectl describe ingress polls-ingress</code> para rastrear el estado de emisión del certificado:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe ingress polls-ingress
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
Events:
  Type    Reason             Age                From                      Message
  ----    ------             ----               ----                      -------
  Normal  CREATE             23m                nginx-ingress-controller  Ingress default/polls-ingress
  Normal  CreateCertificate  23m                cert-manager              Successfully created Certificate "polls-tls"
  Normal  UPDATE             76s (x2 over 22m)  nginx-ingress-controller  Ingress default/polls-ingress
  Normal  UpdateCertificate  76s                cert-manager              Successfully updated Certificate "polls-tls"
</code></pre>
<p>El resultado anterior confirma que el nuevo certificado de producción se emitió y almacenó con éxito en el Secret <code>polls-tls</code>.</p>

<p>Diríjase a <code>your_domain.com/polls</code> en su navegador web para confirmar que el cifrado HTTPS está habilitado y que todo funciona según lo previsto. Debería ver la interfaz de la aplicación Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interfaz de la aplicación Polls"></p>

<p>Verifique que el cifrado HTTPS está activo en su navegador web. Si usa Google Chrome, llegar a la página anterior sin errores confirma que todo funciona correctamente. Además, debe ver un candado en la barra de direcciones URL. Al hacer clic en el candado, se le permitirá inspeccionar los detalles del certificado Let&rsquo;s Encrypt.</p>

<p>Como tarea de limpieza final, puede cambiar opcionalmente el tipo de servicio <code>polls</code> de NodePort al tipo ClusterIP interno solamente.</p>

<p>Modifique <code>polls-svc.yaml</code> usando su editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Cambie el <code>type</code> de <code>NodePort</code> a <code>ClusterIP</code>:</p>
<div class="code-label " title="polls-svc.yaml">polls-svc.yaml</div><pre class="code-pre "><code class="code-highlight language-bash">apiVersion: v1
kind: Service
metadata:
  name: polls
  labels:
    app: polls
spec:
  type: <span class="highlight">ClusterIP</span>
  selector:
    app: polls
  ports:
    - port: 8000
      targetPort: 8000
</code></pre>
<p>Cuando haya terminado de editar el archivo, guárdelo y ciérrelo.</p>

<p>Implemente los cambios usando <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml --force
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls configured
</code></pre>
<p>Confirme que su Service se modificó usando k<code>ubectl get svc:</code></p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
polls   ClusterIP   10.245.203.186   &lt;none&gt;        8000/TCP   22s
</code></pre>
<p>Este resultado muestra que el tipo de servicio es ahora ClusterIP. La única forma de acceder a él es a través de su dominio y del Ingress creado en este paso.</p>

<h2 id="conclusión">Conclusión</h2>

<p>En este tutorial, implementó una aplicación de Django escalable y protegida con HTTPS en un clúster de Kubernetes. El contenido estático se toma directamente desde el almacenamiento de objetos y el número de Pods en ejecución puede aumentar o disminuir rápidamente usando el campo <code>réplicas</code> en el manifiesto de implementación <code>polls-app</code>.</p>

<p>Si usa un Space de DigitalOcean, también puede habilitar la entrega de activos estáticos a través de una red de entrega de contenido y crear un subdominio personalizado para su Space. Consulte <strong>Habilitar CDN</strong> en <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#enabling-cdn-optional">Cómo configurar una aplicación de Django escalable con bases de datos y Spaces administrados por DigitalOcean</a> para obtener más información.</p>

<p>Para revisar el resto de la serie, visite nuestra <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">página “De contenedores a Kubernetes con Django”</a>.</p>
