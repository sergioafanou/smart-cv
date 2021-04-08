---
title : "Cómo hacer una copia de seguridad, restablecer y migrar una base de datos de MongoDB en Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04-es
image: "https://sergio.afanou.com/assets/images/image-midres-23.jpg"
---

<p><em>El autor seleccionó <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> para que reciba una donación como parte del programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h2 id="introducción">Introducción</h2>

<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">MongoDB</a> es uno de los motores de base de datos más populares de NoSQL. Es famoso por ser escalable, sólido, confiable y fácil de usar. En este artículo, respaldará, restaurará y migrará una base de datos de MongoDB de muestra.</p>

<p>Importar y exportar una base de datos implica gestionar los datos en un formato legible por el ser humano y compatible con otros productos de software. Por el contrario, las operaciones de copia de seguridad y restauración de MongoDB crean o utilizan datos binarios específicos de MongoDB, que preservan no solo la uniformidad y la integridad de sus datos, sino también sus atributos específicos de MongoDB. Por lo tanto, para migrar, generalmente es preferible usar la copia de seguridad y la restauración siempre que los sistemas de origen y destino sean compatibles.</p>

<h2 id="requisitos-previos">Requisitos previos</h2>

<p>Antes de seguir este tutorial, asegúrese de completar los siguientes requisitos previos:</p>

<ul>
<li><a href="https://www.digitalocean.com/products/droplets/">Un droplet de Ubuntu 20.0</a>4 configurado mediante la <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">guía de configuración inicial para servidores de Ubuntu 20.04</a>, incluyendo un usuario sudo no root y un firewall.</li>
<li>MongoDB instalado y configurado usando el artículo <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Cómo instalar MongoDB en Ubuntu 20.04</a>.</li>
<li>Ejemplo de la base de datos de MongoDB importada usando las instrucciones en <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04">Cómo importar y exportar una base de datos de MongoDB</a>.</li>
</ul>

<p>Salvo que se indique lo contrario, todos los comandos que requieren privilegios root en este tutorial deben ejecutarse como usuario no root con privilegios sudo.</p>

<h2 id="paso-1-usar-json-y-bson-en-mongodb">Paso 1: Usar JSON y BSON en MongoDB</h2>

<p>Antes de continuar con este artículo, es necesario tener algunos conocimientos básicos sobre la materia. Si tiene experiencia con otros sistemas de base de datos NoSQL como Redis, es posible que encuentre algunas similitudes cuando trabaje con MongoDB.</p>

<p>MongoDB utiliza los formatos <a href="http://json.org/">JSON</a> y BSON (JSON binario) para almacenar su información. JSON es el formato legible por el ser humano perfecto para exportar y, finalmente, importar sus datos. Además, puede administrar los datos exportados con cualquier herramienta que sea compatible con JSON, incluyendo un editor de texto simple.</p>

<p>Un ejemplo de documento JSON tiene el siguiente aspecto:</p>
<div class="code-label " title="Example of JSON Format">Example of JSON Format</div><pre class="code-pre "><code class="code-highlight language-bash">{"address":[
    {"building":"1007", "street":"Park Ave"},
    {"building":"1008", "street":"New Ave"},
]}
</code></pre>
<p>Es conveniente trabajar con JSON, pero no es compatible con todos los tipos de datos disponibles en BSON. Eso significa que habrá la llamada &ldquo;pérdida de fidelidad&rdquo; de la información si utiliza JSON. Para respaldar y restaurar, es mejor usar el BSON binario.</p>

<p>En segundo lugar, no tiene que preocuparse por crear explícitamente una base de datos de MongoDB. Si la base de datos que especifica para la importación no existe todavía, se creará automáticamente. Aún mejor es el caso de la estructura de las colecciones (tablas de base de datos). A diferencia de otros motores de bases de datos, en MongoDB, la estructura se crea de nuevo automáticamente al insertar el primer documento (fila de la base de datos).</p>

<p>En tercer lugar, en MongoDB, leer o insertar grandes cantidades de datos, como las tareas de este artículo, puede requerir de muchos recursos y utilizar gran parte de su CPU, memoria y espacio de disco. Eso es crucial teniendo en cuenta que MongoDB se suele utilizar para las grandes bases de datos y los macrodatos. La solución más sencilla a este problema es ejecutar las exportaciones y las copias de seguridad durante la noche o en horas no pico.</p>

<p>En cuarto lugar, la consistencia de la información podría ser un problema si tiene un servidor MongoDB ocupado en el que la información cambia durante el proceso de exportación o respaldo de la base de datos. Una posible solución para este problema es <a href="https://docs.mongodb.com/manual/faq/replica-sets/">la replicación</a>, que puede considerar cuando avance en el tema de MongoDB.</p>

<p>Aunque puede usar las <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-14-04">funciones de importación y exportación</a> para respaldar y restaurar los datos, hay mejores formas de garantizar la integridad total de sus bases de datos de MongoDB. Para respaldar sus datos, debe usar el comando <code>mongodump</code>. Para restaurar, utilice <code>mongorestore</code>. Veamos cómo funcionan.</p>

<h2 id="paso-2-utilizar-mongodump-para-respaldar-una-base-de-datos-de-mongodb">Paso 2: Utilizar <code>mongodump</code> para respaldar una base de datos de MongoDB</h2>

<p>Primero, vamos a respaldar su base de datos de MongoDB.</p>

<p>Un argumento esencial para <code>mongodump</code> es <code>--db</code>, que especifica el nombre de la base de datos que desea respaldar. Si no se especifica el nombre de la base de datos, <code>mongodump</code> hace una copia de seguridad de todas las bases de datos. El segundo argumento importante es <code>--out</code>, que define el directorio en el que se verterán los datos. Por ejemplo, vamos a respaldar la base de datos <code>newdb</code> y vamos a almacenarla en el directorio <code>/var/backups/mongobackups</code>. Lo ideal es que tengamos cada una de nuestras copias de seguridad en un directorio con la fecha actual como <code>/var/backup/mongobackups/10-29-20</code>.</p>

<p>Primero, cree el directorio <code>/var/backup/mongobackups</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir /var/backups/mongobackups
</li></ul></code></pre>
<p>Luego, ejecute <code>mongodump</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</li></ul></code></pre>
<p>Verá un resultado similar a este:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:22:36.886+0000    writing newdb.restaurants to
2020-10-29T19:22:36.969+0000    done dumping newdb.restaurants (25359 documents)
</code></pre>
<p>Tenga en cuenta que en la ruta del directorio anterior, hemos usado <code>date +"%m-%d-%y"</code>, que obtiene automáticamente la fecha actual. Eso nos permitirá tener copias de seguridad dentro del directorio, como <code>/var/backup/<span class="highlight">10-29-20</span>/</code>. Esto es especialmente conveniente cuando automatizamos las copias de seguridad.</p>

<p>En este punto, tenemos una copia de seguridad competa de la base de datos <code>newdb</code> en el directorio <code>/var/backup/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>. Esta copia de seguridad tiene todo lo necesario para restaurar el <code>newdb</code> de forma adecuada y preservar la llamada &ldquo;fidelidad&rdquo;.</p>

<p>Como regla general, debe realizar copias de seguridad con regularidad y preferiblemente cuando el servidor está menos cargado. Por lo tanto, puede configurar el comando <code>mongodump</code> como tarea de cron para que se ejecute regularmente, por ejemplo, cada día a la 03:03 a.m.</p>

<p>Para ello, abra crontab, el editor de Cron:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Tenga en cuenta que cuando ejecute <code>sudo crontab</code>, editará las tareas de cron para el usuario root. Esto se recomienda porque si establece los crones de su usuario, podrían no ejecutarse correctamente, especialmente si su perfil sudo requiere verificación de contraseña.</p>

<p>Dentro de la línea de comandos de crontab, inserte el siguiente comando <code>mongodump</code>:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">3 3 * * * mongodump --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</code></pre>
<p>En el comando anterior, omitimos el argumento <code>--db</code> a propósito porque normalmente querrá tener todas las bases de datos respaldadas.</p>

<p>Dependiendo del tamaño de su base de datos de MongoDB, es posible que pronto se quede sin espacio en el disco con demasiadas copias de seguridad. Es por eso que también se recomienda limpiar las copias de seguridad antiguas con regularidad o comprimirlas.</p>

<p>Por ejemplo, para eliminar todas las copias de seguridad de más de siete días, puede usar el siguiente comando bash:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</li></ul></code></pre>
<p>De forma similar al comando <code>mongodump</code> anterior, también puede añadirlo como tarea de cron. Debe ejecutarse justo antes de iniciar la siguiente copia de seguridad, por ejemplo, a las 03:01 a.m. Para ello, abra crontab de nuevo:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Después de eso, inserte la siguiente línea:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</code></pre>
<p>Guarde y cierre el archivo.</p>

<p>Completar todas las tareas en este paso garantizará una solución de respaldo adecuada para sus bases de datos de MongoDB.</p>

<h2 id="paso-3-usar-mongorestore-para-restaurar-y-migrar-una-base-de-datos-de-mongodb">Paso 3: Usar <code>mongorestore</code> para restaurar y migrar una base de datos de MongoDB</h2>

<p>Cuando restaura su base de datos de MongoDB desde una copia de seguridad anterior, tiene la copia exacta de su información de MongoDB en un momento determinado, incluyendo todos los índices y los tipos de datos. Esto es especialmente útil cuando desea migrar sus bases de datos de MongoDB. Para restaurar MongoDB, usaremos el comando <code>mongorestore</code>, que funciona con las copias de seguridad binarias que produce <code>mongodump</code>.</p>

<p>Continuemos nuestros ejemplos con la base de datos <code>newdb</code> y veremos cómo podemos restaurarla desde la copia de seguridad tomada anterior. Primero, especificaremos el nombre de la base de datos con el argumento <code>--nsInclude</code> Usaremos <code>newdb*</code> para restaurar todas las colecciones. Para restaurar una colección única como los <code>restaurantes</code>, utilice <code>newdb.restaurants</code> en su lugar.</p>

<p>Luego, con <code>--drop</code>, nos aseguraremos de que la base de datos de destino se elimine primero para que la copia de seguridad se restaure en una base de datos limpia. Como argumento final, especificaremos el directorio de la última copia de seguridad, que tendrá un aspecto similar a este: <code>/var/backup/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<p>Una vez que tenga una copia de seguridad con marca de tiempo, puede restaurarla usando este comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongorestore --db newdb --drop /var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/
</li></ul></code></pre>
<p>Verá un resultado similar a este:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:25:45.825+0000    the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --nsInclude instead
2020-10-29T19:25:45.826+0000    building a list of collections to restore from /var/backups/mongobackups/10-29-20/newdb dir
2020-10-29T19:25:45.829+0000    reading metadata for newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.metadata.json
2020-10-29T19:25:45.834+0000    restoring newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.bson
2020-10-29T19:25:46.130+0000    no indexes to restore
2020-10-29T19:25:46.130+0000    finished restoring newdb.restaurants (25359 documents)
2020-10-29T19:25:46.130+0000    done
</code></pre>
<p>En el caso anterior, estamos restaurando los datos en el mismo servidor donde creamos la copia de seguridad. Si desea migrar los datos a otro servidor y usar la misma técnica, deberá copiar el directorio de la copia de seguridad, que es <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code> en nuestro caso, al otro servidor.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Ahora, ha realizado algunas tareas esenciales relacionadas con el respaldo, la restauración y la migración de sus bases de datos de MongoDB. Ningún servidor MongoDB de producción debería funcionar sin una estrategia de respaldo fiable, como la que se describe aquí.</p>
