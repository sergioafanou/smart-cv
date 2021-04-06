---
title : "Información sobre algoritmos de selección de bloques de servidores y ubicación de Nginx"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms-es
image: "https://sergio.afanou.com/assets/images/image-midres-26.jpg"
---

<h3 id="introducción">Introducción</h3>

<p>Nginx es uno de los servidores web más populares del mundo. Puede manejar correctamente altas cargas con muchas conexiones de clientes concurrentes y puede funcionar fácilmente como servidor web, servidor de correo o servidor de proxy inverso.</p>

<p>En esta guía, explicaremos algunos de los detalles en segundo plano que determinan cómo Nginx procesa las solicitudes de los clientes. Entender estas ideas puede ayudar a despejar las incógnitas sobre el diseño de bloques de servidores y ubicación, y puede hacer que el manejo de las solicitudes parezca menos impredecible.</p>

<h2 id="configuraciones-de-bloques-de-nginx">Configuraciones de bloques de Nginx</h2>

<p>Nginx divide de forma lógica las configuraciones destinadas a entregar distintos contenidos en bloques, que conviven en una estructura jerárquica. Cada vez que se realiza una solicitud de cliente, Nginx inicia un proceso para determinar qué bloques de configuración deben usarse para gestionar la solicitud. Este proceso de decisión es lo que explicaremos en esta guía.</p>

<p>Los bloques principales que explicaremos son el bloque de <strong>servidor</strong> y el bloque de <strong>ubicación</strong>.</p>

<p>Un bloque de servidor es un subconjunto de la configuración de Nginx que define un servidor virtual utilizado para gestionar las solicitudes de un tipo definido. Los administradores suelen configurar varios bloques de servidores y decidir qué bloque debe gestionar cada conexión según el nombre de dominio, el puerto y la dirección IP solicitados.</p>

<p>Un bloque de ubicación reside dentro de un bloque de servidor y se utiliza para definir la manera en que Nginx debe gestionar las solicitudes para diferentes recursos y URI para el servidor principal. El espacio URI puede subdividirse de la manera que el administrador quiera utilizando estos bloques. Es un modelo extremadamente flexible.</p>

<h2 id="cómo-decide-nginx-qué-bloque-de-servidor-gestionará-una-solicitud">Cómo decide Nginx qué bloque de servidor gestionará una solicitud</h2>

<p>Dado que Nginx permite que el administrador defina varios bloques de servidores que funcionan como instancias de servidores web virtuales independientes, necesita un procedimiento para determinar cuál de estos bloques de servidores se utilizará para satisfacer una solicitud.</p>

<p>Lo hace mediante un sistema definido de comprobaciones que se utilizan para encontrar la mejor coincidencia posible. Las principales directivas de bloques de servidores de las que se ocupa Nginx durante este proceso son la directiva <code>listen</code> y la directiva <code>server_name</code>.</p>

<h3 id="análisis-de-la-directiva-quot-listen-quot-para-encontrar-posibles-coincidencias">Análisis de la directiva &ldquo;listen&rdquo; para encontrar posibles coincidencias</h3>

<p>Primero, Nginx examina la dirección IP y el puerto de la solicitud. Luego, lo compara con la directiva <code>listen</code> de cada servidor para crear una lista de los bloques de servidores que pueden resolver la solicitud.</p>

<p>La directiva <code>listen</code> generalmente define la dirección IP y el puerto a los que responderá el bloque de servidor. De manera predeterminada, cualquier bloque de servidor que no incluya una directiva <code>listen</code> recibe los parámetros de escucha <code>0.0.0.0:80</code> (o <code>0.0.0.0:8080</code> si Nginx está siendo ejecutado por un usuario no root regular). Eso permite que estos bloques respondan a las solicitudes en cualquier interfaz en el puerto 80, pero este valor predeterminado no afecta demasiado al proceso de selección de servidores.</p>

<p>La directiva <code>listen</code> puede establecerse para las siguientes características:</p>

<ul>
<li>Una combinación de dirección IP y puerto.</li>
<li>Una dirección IP individual que escuchará en el puerto 80 de manera predeterminada.</li>
<li>Un puerto individual que escuchará cada interfaz en ese puerto.</li>
<li>La ruta al socket Unix.</li>
</ul>

<p>La última opción, por lo general, solo tendrá implicaciones al pasar solicitudes entre distintos servidores.</p>

<p>Cuando intente determinar a qué bloque de servidor enviar una solicitud, Nginx tratará primero de decidir según la especificidad de la directiva <code>listen</code> usando las siguientes reglas:</p>

<ul>
<li>Nginx traduce todas las directivas <code>listen</code> &ldquo;incompletas&rdquo; sustituyendo los valores que faltan por sus valores predeterminados para que cada bloque pueda evaluarse por su dirección IP y su puerto. Algunos ejemplos de estas traslaciones son:

<ul>
<li>Un bloque sin directiva <code>listen</code> utiliza el valor <code>0.0.0.0:80</code>.</li>
<li>Un bloque establecido en una dirección IP <code>111.111.111.111</code> sin puerto se convierte en <code>111.111.111.111:80</code>.</li>
<li>Un bloque establecido en el puerto <code>8888</code> sin dirección IP se convierte en <code>0.0.0.0:8888</code>.</li>
</ul></li>
<li>Luego, Nginx intenta recopilar una lista de los bloques de servidores que coinciden con la solicitud más específicamente basada en la dirección IP y el puerto. Eso significa que cualquier bloque que esté usando de forma funcional <code>0.0.0.0</code> como su dirección IP (para coincidir con cualquier interfaz), no se seleccionará si hay bloques coincidentes que enumeran una dirección IP específica. En todo caso, el puerto debe coincidir con exactitud.</li>
<li>Si solo hay una coincidencia más específica, ese bloque de servidor se utilizará para atender la solicitud. Si hay varios bloques de servidores con el mismo nivel de coincidencia de especificidad, Nginx comienza a evaluar la directiva <code>server_name</code> de cada bloque de servidores.</li>
</ul>

<p>Es importante entender que Nginx solo evaluará la directiva <code>server_name</code> cuando necesite distinguir entre bloques de servidores que coinciden con el mismo nivel de especificidad en la directiva <code>listen</code>. Por ejemplo, si <code>example.com</code> está alojado en el puerto <code>80</code> de <code>192.168.1.10</code>, una solicitud para <code>example.com</code> siempre será atendida por el primer bloque de este ejemplo, a pesar de la directiva <code>server_name</code> en el segundo bloque.</p>
<pre class="code-pre "><code>server {
    listen 192.168.1.10;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}
</code></pre>
<p>En caso de que más de un bloque de servidor coincida con la misma especificidad, el siguiente paso es verificar la directiva <code>server_name</code>.</p>

<h3 id="análisis-de-la-directiva-quot-server_name-quot-para-elegir-una-coincidencia">Análisis de la directiva &ldquo;server_name&rdquo; para elegir una coincidencia</h3>

<p>Luego, para evaluar más a fondo las solicitudes que tienen directivas <code>listen</code> igualmente específicas, Nginx verifica el encabezado &ldquo;Host&rdquo; de la solicitud. Ese valor contiene el dominio o la dirección IP que el cliente estaba intentando alcanzar.</p>

<p>Nginx intenta encontrar la mejor coincidencia para el valor que encuentra al ver la directiva <code>server_name</code> dentro de cada uno de los bloques de servidores que aún son candidatos a selección. Nginx lo evalúa usando la siguiente formula:</p>

<ul>
<li>Nginx intentará primero encontrar un bloque de servidor con un <code>server_name</code> que coincida con el valor en el encabezado &ldquo;Host&rdquo; de la solicitud de manera <em>exacta</em>. Si lo encuentra, ese bloque asociado se utilizará para atender la solicitud. Si se encuentran varias coincidencias exactas, se utiliza la <strong>primera</strong> coincidencia.</li>
<li>Si no se encuentra ninguna coincidencia exacta, Nginx intentará encontrar un bloque de servidor con un <code>server_name</code> que coincida usando un comodín inicial (indicado por un <code>*</code> al principio del nombre en la configuración). Si lo encuentra, ese bloque se utilizará para atender la solicitud. Si se encuentran varias coincidencias, la coincidencia <strong>más larga</strong> se utilizará para atender la solicitud.</li>
<li>Si no se encuentra ninguna coincidencia con el comodín inicial, Nginx buscará entonces un bloque de servidor con un <code>server_name</code> que coincida usando un comodín final (indicado por un nombre de servidor que termina con un <code>*</code> en la configuración). Si lo encuentra, ese bloque se utilizará para atender la solicitud. Si se encuentran varias coincidencias, la coincidencia <strong>más larga</strong> se utilizará para atender la solicitud.</li>
<li>Si no se encuentra ninguna coincidencia usando un comodín final, Nginx evalúa los bloques de servidor que definen el <code>server_name</code> usando expresiones regulares (indicadas por un <code>~</code> antes del nombre). El <strong>primer</strong> <code>server_name</code> con una expresión regular que coincida con el encabezado &ldquo;Host&rdquo; se utilizará para atender la solicitud.</li>
<li>Si no se encuentra ninguna coincidencia de expresión regular, Nginx selecciona el bloque de servidor predeterminado para esa dirección IP y ese puerto.</li>
</ul>

<p>Cada combinación de dirección IP/puerto tiene un bloque de servidor predeterminado que se utilizará cuando no se pueda determinar un curso de acción con los métodos anteriores. En el caso de una combinación de dirección IP y puerto, será el primer bloque de la configuración o el bloque que contiene la opción <code>default_server</code> como parte de la directiva <code>listen</code> (que anularía el primer algoritmo encontrado). Solo puede haber una declaración <code>default_server</code> por cada combinación de dirección IP y puerto.</p>

<h3 id="ejemplos">Ejemplos</h3>

<p>Si se define un <code>server_name</code> que coincida exactamente con el valor de encabezado &ldquo;Host&rdquo;, se selecciona ese bloque de servidor para procesar la solicitud.</p>

<p>En este ejemplo, si el encabezado &ldquo;Host&rdquo; de la solicitud se estableció en &ldquo;host1.example.com&rdquo;, se seleccionaría el segundo servidor:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name *.example.com;

    . . .

}

server {
    listen 80;
    server_name host1.example.com;

    . . .

}
</code></pre>
<p>Si no se encuentra ninguna coincidencia exacta, Nginx comprueba entonces si hay un <code>server_name</code> con un comodín inicial que coincida. Se seleccionará la coincidencia más larga que comience con un comodín para satisfacer la solicitud.</p>

<p>En este ejemplo, si la solicitud tuviera un encabezado &ldquo;Host&rdquo; de &ldquo;<a href="http://www.example.org">www.example.org</a>&rdquo;, se seleccionaría el segundo bloque de servidor:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name www.example.*;

    . . .

}

server {
    listen 80;
    server_name *.example.org;

    . . .

}

server {
    listen 80;
    server_name *.org;

    . . .

}
</code></pre>
<p>Si no se encuentra ninguna coincidencia con un comodín inicial, Nginx verá si existe una coincidencia usando un comodín al final de la expresión. En este punto, se seleccionará la coincidencia más larga que termine con un comodín para atender la solicitud.</p>

<p>Por ejemplo, si la solicitud tiene un encabezado &ldquo;Host&rdquo; establecido en &ldquo;<a href="http://www.example.com">www.example.com</a>&rdquo;, se seleccionará el tercer bloque de servidor:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name host1.example.com;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name www.example.*;

    . . .

}
</code></pre>
<p>Si no se encuentran coincidencias con los comodines, Nginx pasará a intentar coincidir con las directivas <code>server_name</code> que utilicen expresiones regulares. Se seleccionará la <em>primera</em> expresión regular que coincida para responder a la solicitud.</p>

<p>Por ejemplo, si el encabezado &ldquo;Host&rdquo; de la solicitud está configurado en &ldquo;<a href="http://www.example.com">www.example.com</a>&rdquo;, se seleccionará el segundo bloque de servidor para satisfacer la solicitud:</p>
<pre class="code-pre "><code>server {
    listen 80;
    server_name example.com;

    . . .

}

server {
    listen 80;
    server_name ~^(www|host1).*\.example\.com$;

    . . .

}

server {
    listen 80;
    server_name ~^(subdomain|set|www|host1).*\.example\.com$;

    . . .

}
</code></pre>
<p>Si ninguno de los pasos anteriores puede satisfacer la solicitud, la solicitud se pasará al servidor <em>predeterminado</em> para la dirección IP y el puerto que coincidan.</p>

<h2 id="bloques-de-ubicación-que-coinciden">Bloques de ubicación que coinciden</h2>

<p>De forma similar al proceso que utiliza Nginx para seleccionar el bloque del servidor que procesará una solicitud, Nginx también tiene un algoritmo establecido para decidir qué bloque de ubicación dentro del servidor se utilizará para gestionar las solicitudes.</p>

<h3 id="sintaxis-de-bloques-de-ubicación">Sintaxis de bloques de ubicación</h3>

<p>Antes de ver cómo Nginx decide qué bloque de ubicación utilizar para gestionar las solicitudes, repasemos parte de la sintaxis que se podría encontrar en las definiciones de los bloques de ubicación. Los bloques de ubicación se encuentran dentro de los bloques de servidor (u otros bloques de ubicación) y se utilizan para decidir cómo procesar la URI de la solicitud (la parte de la solicitud que viene después del nombre de dominio o la dirección IP/puerto).</p>

<p>Los bloques de ubicación generalmente tienen la siguiente forma:</p>
<pre class="code-pre "><code>location <span class="highlight">optional_modifier</span> <span class="highlight">location_match</span> {

    . . .

}
</code></pre>
<p><code><span class="highlight">location_match</span></code> que aparece arriba define contra qué debe comprobar Nginx la URI de la solicitud. La existencia o la inexistencia del modificador en el ejemplo anterior afecta a la forma en que Nginx intenta hacer coincidir el bloque de ubicación. Los modificadores que se muestran abajo harán que el bloque de ubicación asociado se interprete de la siguiente manera:</p>

<ul>
<li><strong>(none)</strong>: si no hay modificadores, la ubicación se interpreta como una coincidencia de <em>prefijo</em>. Eso significa que la ubicación dada coincidirá con el principio del URI de la solicitud para determinar una coincidencia.</li>
<li><strong><code>=</code></strong>: si se utiliza un signo igual, este bloque se considerará coincidente si el URI de la solicitud coincide exactamente con la ubicación indicada.</li>
<li><strong><code>~</code></strong>: si hay una tilde de la eñe, esta ubicación se interpretará como una coincidencia de expresión regular que distingue entre mayúsculas y minúsculas.</li>
<li><strong><code>~*</code></strong>: si se utiliza un modificador de tilde de la eñe y asterisco, el bloque de ubicación se interpretará como una coincidencia de expresión regular que no distingue entre mayúsculas y minúsculas.</li>
<li><strong><code>^~</code></strong>: si hay un modificador de acento circunflejo y tilde de la eñe, y si este bloque se selecciona como la mejor coincidencia de expresión no regular, no se realizará la coincidencia de expresión regular.</li>
</ul>

<h3 id="ejemplos-que-demuestran-la-sintaxis-del-bloque-de-ubicación">Ejemplos que demuestran la sintaxis del bloque de ubicación</h3>

<p>Como ejemplo de concordancia de prefijos, se puede seleccionar el siguiente bloque de ubicación para responder a los URI de solicitud que tienen el aspecto <code>/site</code>, <code>/site/page1/index.html</code> o <code>/site/index.html</code>:</p>
<pre class="code-pre "><code>location /site {

    . . .

}
</code></pre>
<p>Para una demostración de la coincidencia exacta del URI de la solicitud, este bloque siempre se utilizará para responder a un URI de solicitud que tenga el aspecto <code>/page1</code>. <strong>No</strong> se utilizará para responder a un URI de solicitud <code>/page1/index.html</code>. Tenga en cuenta que, si se selecciona este bloque y la solicitud se cumple usando una página de índice, se realizará un redireccionamiento interno a otra ubicación que será la que realmente gestione la solicitud:</p>
<pre class="code-pre "><code>location = /page1 {

    . . .

}
</code></pre>
<p>Como ejemplo de una ubicación que debe interpretarse como una expresión regular que distingue entre mayúsculas y minúsculas, este bloque puede usarse para gestionar las solicitudes de <code>/tortoise.jpg</code>, pero <strong>no</strong> para <code>/FLOWER.PNG</code>:</p>
<pre class="code-pre "><code>location ~ \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>A continuación, se muestra un bloque que permitiría una coincidencia sin distinción ente mayúsculas y minúsculas similar al que se mostró anteriormente. Este bloque podría gestionar <code>/tortoise.jpg</code> <em>y</em> <code>/FLOWER.PNG</code>:</p>
<pre class="code-pre "><code>location ~* \.(jpe?g|png|gif|ico)$ {

    . . .

}
</code></pre>
<p>Por último, este bloque evitaría que se produjera una coincidencia de expresión regular si se determina que es la mejor coincidencia de expresión no regular. Podría gestionar las solicitudes de <code>/costumes/ninja.html</code>:</p>
<pre class="code-pre "><code>location ^~ /costumes {

    . . .

}
</code></pre>
<p>Como ve, los modificadores indican cómo debe interpretarse el bloque de ubicación. Sin embargo, esto <em>no</em> nos indica el algoritmo que utiliza Nginx para decidir a qué bloque de ubicación enviar la solicitud. Lo repasaremos a continuación.</p>

<h3 id="cómo-elige-nginx-qué-ubicación-utilizar-para-gestionar-las-solicitudes">Cómo elige Nginx qué ubicación utilizar para gestionar las solicitudes</h3>

<p>Nginx elige la ubicación que se utilizará para atender una solicitud de forma similar a cómo selecciona un bloque de servidor. Se ejecuta a través de un proceso que determina el mejor bloque de ubicación para cualquier solicitud. Entender este proceso es un requisito crucial para poder configurar Nginx de forma fiable y precisa.</p>

<p>Teniendo en cuenta los tipos de declaraciones de ubicación que hemos descrito anteriormente, Nginx evalúa los posibles contextos de ubicación comparando el URI de solicitud con cada una de las ubicaciones. Lo hace mediante el siguiente algoritmo:</p>

<ul>
<li>Nginx comienza comprobando todas las coincidencias de ubicación basadas en prefijos (todos los tipos de ubicación que no implican una expresión regular). Comprueba cada ubicación con el URI completo de la solicitud.</li>
<li>Primero, Nginx busca una coincidencia exacta. Si se encuentra un bloque de ubicación que utilice el modificador <code>=</code> y que coincida con el URI de la solicitud de manera exacta, este bloque de ubicación se selecciona inmediatamente para atender la solicitud.</li>
<li>Si no se encuentran coincidencias exactas con bloques de ubicación (con el modificador <code>=</code>), Nginx pasa a evaluar los prefijos no exactos. Descubre la ubicación del prefijo más largo que coincide con el URI de la solicitud dada que luego evalúa de la siguiente manera:

<ul>
<li>Si la ubicación del prefijo más largo que coincide tiene el modificador <code>^~</code>, Nginx finalizará inmediatamente su búsqueda y seleccionará esta ubicación para atender la solicitud.</li>
<li>Si la ubicación del prefijo más largo que coincide <em>no</em> utiliza el modificador <code>^~</code>, Nginx almacena la coincidencia de momento para poder cambiar el enfoque de la búsqueda.</li>
</ul></li>
<li>Una vez que se determina y almacena la ubicación del prefijo más largo que coincide, Nginx procede a evaluar las ubicaciones de expresiones regulares (las que distinguen entre mayúsculas y minúsculas, y las que no). Si hay alguna ubicación de expresiones regulares <em>dentro de</em> la ubicación del prefijo más largo que coincide, Nginx la pondrá a la parte superior de su lista de ubicaciones de regex para comprobar. Luego, Nginx intentará hacer coincidir con las ubicaciones de expresiones regulares de forma secuencial. La <strong>primera</strong> ubicación de expresión regular que coincida con el URI de la solicitud se seleccionará inmediatamente para atender la solicitud.</li>
<li>Si no se encuentra ninguna ubicación de expresión regular que coincida con el URI de la solicitud, se seleccionará la ubicación de prefijo previamente almacenado para atender la solicitud.</li>
</ul>

<p>Es importante entender que, de manera predeterminada, Nginx atenderá las coincidencias de expresiones regulares con mayor preferencia en comparación con las coincidencias de prefijos. Sin embargo, primero <em>evalúa</em> las ubicaciones de prefijos, lo que permite al administrador anular esta tendencia especificando las ubicaciones usando los modificadores <code>=</code> y <code>^~</code>.</p>

<p>También es importante tener en cuenta que, aunque las ubicaciones de prefijos generalmente se seleccionan según la coincidencia más larga y específica, la evaluación de la expresión regular se detiene cuando se encuentra la primera ubicación que coincida. Eso significa que el posicionamiento dentro de la configuración tiene amplias implicaciones para las ubicaciones de expresiones regulares.</p>

<p>Por último, es importante entender que las coincidencias de expresiones regulares <em>dentro</em> de la coincidencia del prefijo más largo &ldquo;saltarán la línea&rdquo; cuando Nginx evalúe las ubicaciones de regex. Estas se evaluarán en orden, antes de que se consideren las demás coincidencias de expresiones regulares. Maxim Dounin, un desarrollador de Nginx increíblemente atento, explica en <a href="https://www.ruby-forum.com/topic/4422812#1136698">esta publicación</a> esta parte del algoritmo de selección.</p>

<h3 id="¿cuándo-salta-la-evaluación-del-bloque-de-ubicaciones-a-otras-ubicaciones">¿Cuándo salta la evaluación del bloque de ubicaciones a otras ubicaciones?</h3>

<p>En general, cuando se selecciona un bloque de ubicación para atender una solicitud, la solicitud se gestiona completamente dentro de ese contexto a partir de ese momento. Solo la ubicación seleccionada y las directivas heredadas determinan cómo se procesa la solicitud, sin interferencia de los bloques de ubicación hermanos.</p>

<p>Aunque esta es una regla general que le permitirá diseñar sus bloques de ubicación de una manera predecible, es importante darse cuenta de que, a veces, una nueva búsqueda de ubicación se activa por ciertas directivas dentro de la ubicación seleccionada. Las excepciones a la regla &ldquo;solo un bloque de ubicación&rdquo; pueden tener implicaciones sobre cómo se atiende realmente la solicitud y pueden no coincidir con las expectativas que tenía al diseñar sus bloques de ubicación.</p>

<p>Algunas directivas que pueden dar como resultado este tipo de redireccionamiento interno son:</p>

<ul>
<li><strong>index</strong></li>
<li><strong>try_files</strong></li>
<li><strong>rewrite</strong></li>
<li><strong>error_page</strong></li>
</ul>

<p>Las repasaremos brevemente.</p>

<p>La directiva <code>index</code> siempre conduce a un redireccionamiento interno si se utiliza para gestionar la solicitud. Las coincidencias de ubicación exactas se utilizan a menudo para acelerar el proceso de selección, terminando inmediatamente la ejecución del algoritmo. Sin embargo, si realiza una coincidencia de ubicación exacta que sea un <em>directorio</em>, hay una buena probabilidad de que la solicitud sea redirigida a una ubicación diferente para el procesamiento real.</p>

<p>En este ejemplo, la primera ubicación coincide con un URI de solicitud de <code>/exact</code>, pero, para gestionar la solicitud, la directiva <code>index</code> heredada por el bloque inicia un redireccionamiento interno al segundo bloque:</p>
<pre class="code-pre "><code>index index.html;

location = /exact {

    . . .

}

location / {

    . . .

}
</code></pre>
<p>En el caso anterior, si realmente necesita que la ejecución permanezca en el primer bloque, tendrá que encontrar un método diferente para satisfacer la solicitud al directorio. Por ejemplo, podría establecer un <code>index</code> no válido para ese bloque y activar <code>autoindex</code>:</p>
<pre class="code-pre "><code>location = /exact {
    index nothing_will_match;
    autoindex on;
}

location  / {

    . . .

}
</code></pre>
<p>Esta es una forma de evitar que un <code>index</code> cambie de contexto, pero probablemente no sea útil para la mayoría de las configuraciones. Generalmente, una coincidencia exacta en directorios puede ser útil para acciones como reescribir la solicitud (lo que también genera una nueva búsqueda de ubicación).</p>

<p>Otro caso en que se puede reevaluar la ubicación del procesamiento es con la directiva <code>try_files</code>. Esa directiva le indica a Nginx que busque la existencia de un conjunto determinado de archivos o directorios. El último parámetro puede ser un URI al que Nginx realizará un redireccionamiento interno.</p>

<p>Analice la siguiente configuración:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>En el ejemplo anterior, si se realiza una solicitud para <code>/blahblah</code>, la primera ubicación obtendrá inicialmente la solicitud. Intentará encontrar un archivo llamado <code>blahblah</code> en el directorio <code>/var/www/main</code>. Si no puede encontrar ninguno, procederá a buscar un archivo llamado <code>blahblah.html</code>. Luego, intentará buscar un directorio llamado <code>blahblah/</code> dentro del directorio <code>/var/www/main</code>. Si todos esos intentos fallan, se redirigirá a <code>/fallback/index.html</code>. Eso activará otra búsqueda de ubicación que el segundo bloque de ubicación capturará. Y atenderá el archivo <code>/var/www/another/fallback/index.html</code>.</p>

<p>Otra directiva que puede pasar el bloque de ubicación es la directiva <code>rewrite</code>. Cuando utiliza el <code>último</code> parámetro con la directiva <code>rewrite</code> o cuando no utiliza ningún parámetro, Nginx buscará una nueva ubicación que coincida según los resultados de la reescritura.</p>

<p>Por ejemplo, si modificamos el último ejemplo para incluir una reescritura, podemos ver que la solicitud a veces se pasa directamente a la segunda ubicación sin depender de la directiva <code>try_files</code>:</p>
<pre class="code-pre "><code>root /var/www/main;
location / {
    rewrite ^/rewriteme/(.*)$ /$1 last;
    try_files $uri $uri.html $uri/ /fallback/index.html;
}

location /fallback {
    root /var/www/another;
}
</code></pre>
<p>En el ejemplo de arriba, el primer bloque de ubicación gestionará inicialmente una solicitud de <code>/rewriteme/hello</code>. Se reescribirá a <code>/hello</code> y se buscará una ubicación. En este caso, volverá a coincidir con la primera ubicación y se procesará mediante <code>try_files</code> de manera habitual, probablemente regresando a <code>/fallback/index.html</code> si no se encuentra nada (usando el redireccionamiento interno de <code>try_files</code> que mencionamos antes).</p>

<p>Sin embargo, si se realiza una solicitud para <code>/rewriteme/fallback/hello</code>, el primer bloque volverá a coincidir. Se aplicará la reescritura de nuevo, dando como resultado <code>/fallback/hello</code> esta vez. La solicitud se atenderá desde el segundo bloque de ubicación.</p>

<p>Algo similar sucede con la directiva <code>return</code> cuando se envían los códigos de estado <code>301</code> o <code>302</code>. La diferencia en este caso es que da como resultado una solicitud completamente nueva en forma de un redireccionamiento visible desde el exterior. Eso mismo puede suceder con la directiva <code>rewrite</code> cuando se utilizan los indicadores <code>redirect</code> o <code>permanent</code>. Sin embargo, estas búsquedas de ubicación no deberían ser inesperadas, ya que los redireccionamientos visibles externamente siempre dan como resultado una nueva solicitud.</p>

<p>La directiva <code>error_page</code> puede dar como resultado un redireccionamiento interno similar al que se creó con <code>try_files</code>. Esa directiva se utiliza para definir lo que debe suceder cuando se encuentran ciertos códigos de estado. Esto probablemente nunca se ejecutará si se establece <code>try_files</code>, ya que esa directiva maneja todo el ciclo de vida de una solicitud.</p>

<p>Analice este ejemplo:</p>
<pre class="code-pre "><code>root /var/www/main;

location / {
    error_page 404 /another/whoops.html;
}

location /another {
    root /var/www;
}
</code></pre>
<p>Todas las solicitudes (aparte de las que comienzan con <code>/another</code>) se gestionarán mediante el primer bloque, que atenderá archivos desde <code>/var/www/main</code>. Sin embargo, si no se encuentra un archivo (un estado 404), se producirá un redireccionamiento interno a <code>/another/whoops.html</code>, lo que llevará a una nueva búsqueda de ubicación que finalmente aterrizará en el segundo bloque. Este archivo se atenderá desde <code>/var/www/another/whoops.html</code>.</p>

<p>Como se puede ver, entender las circunstancias en que Nginx activa una nueva búsqueda de ubicación puede ayudar a predecir el comportamiento que habrá cuando se hagan solicitudes.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Entender las formas en que Nginx procesa las solicitudes de los clientes puede hacer que su trabajo como administrador sea mucho más fácil. Podrá saber qué bloque de servidor seleccionará Nginx según cada solicitud del cliente. También podrá saber cómo se seleccionará el bloque de ubicación según el URI de la solicitud. En general, saber la forma en que Nginx selecciona los diferentes bloques le permitirá rastrear los contextos que Nginx aplicará para atender cada solicitud.</p>
