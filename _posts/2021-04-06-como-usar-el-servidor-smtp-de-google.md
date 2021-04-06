---
title : "Cómo usar el servidor SMTP de Google"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server-es
image: "https://sergio.afanou.com/assets/images/image-midres-16.jpg"
---

<p><em>El autor seleccionó el <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> para que reciba una donación como parte del programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introducción">Introducción</h3>

<p>Una función poco conocida sobre el correo electrónico de Gmail y Google Apps es el servidor SMTP portátil de Google. En vez de tener que administrar su propio servidor de correo saliente en su <a href="https://www.digitalocean.com/products/droplets/">Droplet de DigitalOcean</a> o <a href="https://try.digitalocean.com/kubernetes-in-minutes/?utm_campaign=amer_brand_kw_en_cpc&amp;utm_adgroup=digitalocean_kubernetes_exact&amp;_keyword=digitalocean%20kubernetes&amp;_device=c&amp;_adposition=&amp;utm_medium=cpc&amp;utm_source=google&amp;gclid=EAIaIQobChMImcKuus2e7AIVhY5bCh1otQGSEAAYAiAAEgJEu_D_BwE">clúster de Kubernetes</a>, puede configurar los ajustes del servidor SMTP de Google en cualquier script o programa desde el que desee enviar correos electrónicos. Lo único que necesita es ya sea (i) una cuenta de Gmail gratuita o (ii) una cuenta de G Suite pagada.</p>

<h2 id="beneficios">Beneficios</h2>

<p>Tiene la opción de que Google almacene e indexe los correos electrónicos que envíe a través de su servidor SMTP, por lo que podrá buscar y respaldar todos sus correos electrónicos enviados en los servidores de Google. Si decide usar su cuenta de Gmail o G Suite también para su correo electrónico entrante, tendrá todo su correo electrónico en un solo lugar. Además, dado que el servidor SMTP de Google no utiliza el Puerto 25, reducirá la probabilidad de que un ISP pueda bloquear su correo electrónico o etiquetarlo como spam.</p>

<h2 id="ajustes">Ajustes</h2>

<p>El servidor SMTP de Google requiere autenticación, por lo que aquí le mostramos cómo configurarlo en su cliente o aplicación de correo:</p>

<p><span class='note'><strong>Nota:</strong> Antes de comenzar, considere investigar la calificación de seguridad de su cliente de correo o aplicación según Google. Si usa un programa que Google no considera seguro, su uso se bloqueará a menos que habilite aplicaciones menos seguras (un ajuste de seguridad que Google no recomienda) o genere una contraseña de aplicación específica para la aplicación. <a href="https://support.google.com/accounts/answer/6010255?hl=en">Para obtener más información sobre seguridad, consulte este enlace</a> para determinar el mejor método para su cliente o aplicación de correo.<br></span></p>

<ol>
<li>Servidor SMTP (es decir, servidor de correo saliente): <strong><a href="http://smtp.gmail.com">smtp.gmail.com</a></strong></li>
<li>Nombre de usuario SMTP: <strong>Su dirección de correo electrónico completa de Gmail o G Suite</strong> (por ejemplo, <code><span class="highlight">example@gmail.com</span></code> o <code><span class="highlight">example@your_domain</span></code>)</li>
<li>Contraseña SMTP: <code><span class="highlight">Su contraseña de correo electrónico de Gmail o G Suite</span></code></li>
<li>Puerto SMTP: <code>465</code></li>
<li><strong>TLS o SSL SMTP requerido</strong>: <strong>sí</strong></li>
</ol>

<p>Para que Google copie automáticamente sus correos electrónicos enviados a la carpeta de enviados, también debe verificar que el acceso IMAP esté habilitado en su cuenta.</p>

<p>Para hacerlo, diríjase a los ajustes de Gmail y haga clic en la pestaña <strong>Reenvío y correo POP/IMAP</strong>. Desplácese hasta la sección <strong>Acceso IMAP</strong> y asegúrese de que el acceso IMAP esté habilitado para su cuenta.</p>

<span class='note'><p>
<strong>Nota:</strong> Google reescribirá automáticamente la línea <strong>De</strong> de cualquier correo electrónico que envíe a través de su servidor SMTP a la dirección de correo electrónico predeterminada asociada a la cuenta si la que se usa no se encuentra en la lista de <strong>correo Enviado como</strong> direcciones de la configuración de Gmail o G Suite. Puede verificar la lista dirigiéndose a la pestaña <strong>Cuentas e importación</strong> en la pantalla de ajustes.</p>

<p>Debe tener en cuenta este matiz porque afecta la presentación de su correo electrónico, desde el punto de vista del destinatario, y también puede afectar la configuración <strong>Responder a</strong> de algunos programas.<br></p></span>

<h2 id="límites-de-envío">Límites de envío</h2>

<p>Google limita la cantidad de correo que un usuario puede enviar a través de su servidor SMTP portátil. Este límite restringe el número de mensajes enviados por día a 99 correos electrónicos; la restricción se elimina automáticamente 24 horas después de alcanzar el límite.</p>

<h2 id="conclusión">Conclusión</h2>

<p>Ahora tiene la opción de usar el servidor SMTP de Google. Si esta opción ligera no es suficiente, puede considerar <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04">instalar y configurar postfix como servidor SMTP solamente de envío</a>.</p>
