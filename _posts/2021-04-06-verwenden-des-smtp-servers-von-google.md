---
title : "Verwenden des SMTP-Servers von Google"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server-de
image: "https://sergio.afanou.com/assets/images/image-midres-14.jpg"
---

<p><em>Der Autor hat den <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> dazu ausgewählt, eine Spende im Rahmen des Programms <a href="https://do.co/w4do-cta">Write for DOnations</a> zu erhalten.</em></p>

<h3 id="einführung">Einführung</h3>

<p>Eine wenig bekannte Funktion von Gmail und Google Apps E-Mail ist der portable SMTP-Server von Google. Anstatt Ihren eigenen Postausgangsserver auf Ihrem <a href="https://www.digitalocean.com/products/droplets/">DigitalOcean-Droplet</a> oder <a href="https://try.digitalocean.com/kubernetes-in-minutes/?utm_campaign=amer_brand_kw_en_cpc&amp;utm_adgroup=digitalocean_kubernetes_exact&amp;_keyword=digitalocean%20kubernetes&amp;_device=c&amp;_adposition=&amp;utm_medium=cpc&amp;utm_source=google&amp;gclid=EAIaIQobChMImcKuus2e7AIVhY5bCh1otQGSEAAYAiAAEgJEu_D_BwE">Kubernetes-Cluster</a> verwalten zu müssen, können Sie die SMTP-Servereinstellungen von Google mit einem beliebigen Skript oder Programm konfigurieren, das Sie zum Versenden von E-Mails verwenden möchten. Sie benötigen lediglich entweder (i) ein kostenloses Gmail-Konto oder (ii) ein kostenpflichtiges G Suite-Konto.</p>

<h2 id="vorteile">Vorteile</h2>

<p>Sie haben die Möglichkeit, die von Ihnen über den SMTP-Server gesendeten E-Mails von Google speichern und indizieren zu lassen, sodass alle Ihre gesendeten E-Mails auf den Servern von Google durchsuchbar und gesichert sind. Wenn Sie sich entscheiden, Ihr Gmail- oder G Suite-Konto auch für Ihre eingehenden E-Mails zu verwenden, haben Sie alle Ihre E-Mails an einem praktischen Ort. Da der SMTP-Server von Google nicht den Port 25 verwendet, verringern Sie außerdem die Wahrscheinlichkeit, dass ein ISP Ihre E-Mails blockiert oder als Spam kennzeichnet.</p>

<h2 id="einstellungen">Einstellungen</h2>

<p>Der SMTP-Server von Google erfordert Authentifizierung. Hier erfahren Sie, wie Sie diese in Ihrem Mail-Client oder Ihrer Anwendung einrichten:</p>

<p><span class='note'><strong>Anmerkung:</strong> Bevor Sie beginnen, sollten Sie laut Google die Sicherheitseinstufung Ihres Mail-Clients oder Ihrer Anwendung überprüfen. Wenn Sie ein Programm verwenden, das von Google als nicht sicher eingestuft wird, wird Ihre Nutzung blockiert, es sei denn, Sie aktivieren weniger sichere Anwendungen (eine Sicherheitseinstellung, die von Google nicht empfohlen wird) oder erstellen ein anwendungsspezifisches App-Passwort. <a href="https://support.google.com/accounts/answer/6010255?hl=en">Weitere Sicherheitsinformationen finden Sie unter diesem Link</a> zur Ermittlung der besten Vorgehensweise für Ihren Mail-Client oder Ihre Anwendung.<br></span></p>

<ol>
<li>SMTP-Server (d. h., Postausgangsserver): <strong><a href="http://smtp.gmail.com">smtp.gmail.com</a></strong></li>
<li>SMTP-Benutzername: <strong>Ihre vollständige Gmail- oder G Suite-E-Mail-Adresse</strong> (z. B. <code><span class="highlight">example@gmail.com</span></code> oder <code><span class="highlight">example@your_domain</span></code>)</li>
<li>SMTP-Passwort: <code><span class="highlight">Ihr Gmail- oder G Suite-E-Mail-Passwort</span></code></li>
<li>SMTP-Port: <code>465</code></li>
<li>SMTP <strong>TLS/SSL erforderlich</strong>: <strong>Ja</strong></li>
</ol>

<p>Damit Google Ihre gesendeten E-Mails automatisch in den Ordner „Gesendet“ kopiert, müssen Sie außerdem überprüfen, ob der IMAP-Zugriff für Ihr Konto aktiviert ist.</p>

<p>Gehen Sie dazu in die Gmail-Einstellungen und klicken Sie auf die Registerkarte <strong>Weiterleitung und POP/IMAP</strong>. Scrollen Sie nach unten zu dem Abschnitt <strong>IMAP-Zugriff</strong> und stellen Sie sicher, dass der IMAP-Zugriff für Ihr Konto aktiviert ist.</p>

<span class='note'><p>
<strong>Anmerkung:</strong> Google schreibt die <strong>Absenderzeile</strong> jeder E-Mail, die Sie über seinen SMTP-Server senden, automatisch in die mit dem Konto verknüpfte Standard-E-Mail-Adresse um, wenn die verwendete Adresse nicht in der Adressenliste <strong>E-Mail senden als</strong> in den Einstellungen von Gmail oder G Suite enthalten ist. Sie können die Liste überprüfen, indem Sie auf dem Einstellungsbildschirm auf die Registerkarte <strong>Konten und Import</strong> gehen.</p>

<p>Sie müssen sich dieser Nuance bewusst sein, da sie sich auf die Darstellung Ihrer E-Mail aus der Sicht des Empfängers auswirkt und auch die Einstellung <strong>Antworten an</strong> einiger Programme beeinflussen kann.<br></p></span>

<h2 id="sendebeschränkungen">Sendebeschränkungen</h2>

<p>Google begrenzt die Menge der E-Mails, die ein Benutzer über den portablen SMTP-Server senden kann. Diese Begrenzung beschränkt die Anzahl der täglich gesendeten Nachrichten auf 99 E-Mails; die Beschränkung wird automatisch 24 Stunden nach Erreichen der Beschränkung aufgehoben.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>Sie haben nun die Möglichkeit, den SMTP-Server von Google zu verwenden. Wenn diese vereinfachte Option nicht ausreicht, können Sie die <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04">Installation und Konfiguration von Postfix als reinen Sende-SMTP-Server</a> in Betracht ziehen.</p>
