---
title : "Bereitstellen einer skalierbaren und sicheren Django-Anwendung mit Kubernetes"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes-de
image: "https://sergio.afanou.com/assets/images/image-midres-3.jpg"
---

<h3 id="einführung">Einführung</h3>

<p>In diesem Tutorial stellen Sie eine containerisierte Django-Umfrageanwendung in einem Kubernetes-Cluster bereit.</p>

<p><a href="https://www.djangoproject.com/">Django</a> ist ein leistungsfähiges Web-Framework, das Ihnen dabei helfen kann, Ihre Python-Anwendung schnell bereitzustellen. Es enthält mehrere praktische Funktionen wie einen <a href="https://en.wikipedia.org/wiki/Object-relational_mapping">objektrelationalen Mapper</a>, eine Benutzerauthentifizierung und eine anpassbare Verwaltungsoberfläche für Ihre Anwendung. Es beinhaltet auch ein <a href="https://docs.djangoproject.com/en/2.1/topics/cache/">Caching-Framework</a> und fördert ein sauberes App-Design durch seinen <a href="https://docs.djangoproject.com/en/2.1/topics/http/urls/">URL Dispatcher</a> und das <a href="https://docs.djangoproject.com/en/2.1/topics/templates/">Vorlagensystem</a>.</p>

<p>In <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Erstellen einer Django- und Gunicorn-Anwendung mit Docker</a> wurde die <a href="https://docs.djangoproject.com/en/3.1/intro/tutorial01/">Django Tutorial Umfrageanwendung</a> gemäß der <a href="https://12factor.net/">Zwölf-Faktor</a>-Methodik zur Erstellung skalierbarer Cloud-nativer Web-Apps modifiziert. Diese containerisierte Einrichtung wurde mit einem Nginx Reverse-Proxy und Let’s Encrypt-signierten TLS-Zertifikaten in <a href="https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt">Skalieren und Sichern einer Django-Anwendung mit Docker, Nginx und Let’s Encrypt</a> skaliert und gesichert. In diesem letzten Tutorial der Reihe <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">Von Containern zu Kubernetes mit Django</a> wird die modernisierte Django-Umfrageanwendung in einem Kubernetes-Cluster bereitgestellt.</p>

<p><a href="https://kubernetes.io/">Kubernetes</a> ist ein leistungsstarker Open-Source-Container-Orchestrator, der die Bereitstellung, das Skalieren und die Verwaltung von containerisierten Anwendungen automatisiert. Mit Kubernetes-Objekten wie ConfigMaps und Secrets können Sie die Konfiguration zentralisieren und von Ihren Containern entkoppeln, während Controller wie Deployments fehlgeschlagene Container automatisch neu starten und eine schnelle Skalierung von Container-Replikaten ermöglichen. Die TLS-Verschlüsselung wird mit einem Ingress-Objekt und dem Open-Source Ingress-Controller <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> aktiviert. Das Kubernetes Add-on <a href="https://github.com/jetstack/cert-manager">cert-manager</a> erneuert und stellt Zertifikate mit der kostenlosen Zertifizierungsstelle <a href="https://letsencrypt.org/">Let’s Encrypt</a> aus.</p>

<h2 id="voraussetzungen">Voraussetzungen</h2>

<p>Um dieser Anleitung zu folgen, benötigen Sie:</p>

<ul>
<li>Einen Kubernetes 1.15+-Cluster mit aktivierter <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">rollenbasierter Zugriffskontrolle</a> (RBAC). In diesem Setup wird ein <a href="https://www.digitalocean.com/products/kubernetes/">DigitalOcean Kubernetes</a>-Cluster verwendet, doch Sie können einen Cluster auch mit <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04">einer anderen Methode</a> erstellen.</li>
<li>Das Befehlszeilen-Tool <code>kubectl</code>, das auf Ihrem lokalen Rechner installiert und für die Verbindung mit Ihrem Cluster konfiguriert ist. Weitere Informationen zur Installation von <code>kubectl</code> finden Sie <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/">in der offiziellen Dokumentation</a>. Wenn Sie einen DigitalOcean Kubernetes-Cluster verwenden, lesen Sie bitte <a href="https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/">Verbinden mit einem DigitalOcean Kubernetes-Cluster</a>, um zu erfahren, wie Sie sich mit <code>kubectl</code> mit Ihrem Cluster verbinden können.</li>
<li>Einen registrierten Domänennamen. In diesem Tutorial wird durchgängig <code>your_domain.com</code> verwendet. Einen Domänennamen können Sie kostenlos bei <a href="http://www.freenom.com/en/index.html">Freenom</a> erhalten oder Sie nutzen eine Domänenregistrierungsstelle Ihrer Wahl.</li>
<li>Einen <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> Ingress-Controller und den TLS-Zertifizierungsmanager <a href="https://github.com/jetstack/cert-manager">cert-manager</a>, die in Ihrem Cluster installiert und zur Ausgabe von TLS-Zertifikaten konfiguriert sind. Um zu erfahren, wie Sie einen Ingress mit cert-manager installieren und konfigurieren, konsultieren Sie bitte <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">Einrichten eines Nginx Ingress mit Cert-Manager in DigitalOcean Kubernetes</a>.</li>
<li>Einen <code>A</code>-DNS-Datensatz mit <code>your_domain.com</code>, der auf die öffentliche IP-Adresse des Ingress Load Balancers verweist. Wenn Sie DigitalOcean zum Verwalten der DNS-Datensätze Ihrer Domäne verwenden, konsultieren Sie bitte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Verwalten von DNS-Datensätzen</a>, um zu erfahren, wie Sie <code>A</code>-Datensätze erstellen.</li>
<li>Einen S3-Objektspeicher-Bucket wie beispielsweise einen <a href="https://www.digitalocean.com/products/spaces/">DigitalOcean Space</a> zur Speicherung der statischen Dateien Ihres Django-Projekts und einen Satz von Zugriffsschlüsseln für diesen Space. Um zu erfahren, wie Sie einen Space erstellen können, lesen Sie die Produktdokumentation <a href="https://www.digitalocean.com/docs/spaces/how-to/create/">Erstellen von Spaces</a>. Um zu erfahren, wie Sie Zugriffsschlüssel für Spaces erstellen können, lesen Sie <a href="https://www.digitalocean.com/docs/spaces/how-to/administrative-access/#access-keys">Zugriff auf Spaces mit Zugriffsschlüsseln gemeinsam nutzen</a>. Mit geringfügigen Änderungen können Sie jeden Objektspeicherdienst verwenden, der das Plugin <a href="https://django-storages.readthedocs.io/en/latest/">django-storages</a> verwendet.</li>
<li>Eine PostgreSQL-Server-Instanz, Datenbank und Benutzer für Ihre Django-Anwendung. Mit geringfügigen Änderungen können Sie jede Datenbank verwenden, die <a href="https://docs.djangoproject.com/en/2.2/ref/databases/">Django unterstützt</a>.

<ul>
<li>Die PostgreSQL-Datenbank sollte <strong>polls</strong> genannt werden (oder einen anderen einprägsamen Namen erhalten, den Sie unten in Ihre Konfigurationsdatei eingeben) und in diesem Tutorial wird der Django-Datenbankbenutzer <strong>sammy</strong> genannt. Führen Sie zum Erstellen dieser den Schritt 1 von <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-1-%E2%80%94-creating-the-postgresql-database-and-user">Erstellen einer Django- und Gunicorn-Anwendung mit Docker</a> aus. Sie sollten diese Schritte von Ihrem lokalen aus durchführen.</li>
<li>In diesem Tutorial wird ein DigitalOcean <a href="https://www.digitalocean.com/products/managed-databases/">Managed PostgreSQL-Cluster</a> verwendet. Um mehr über die Erstellung eines Clusters zu erfahren, lesen Sie die <a href="https://www.digitalocean.com/docs/databases/how-to/clusters/create/">Produktdokumentation zu verwalteten Datenbanken</a> von DigitalOcean.</li>
<li>Sie können auch Ihre eigene PostgreSQL-Instanz installieren und ausführen. Eine Anleitung zur Installation und Verwaltung von PostgreSQL auf einem Ubuntu-Server finden Sie unter <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04">Installieren und Verwenden von PostgreSQL unter Ubuntu 18.04</a>.</li>
</ul></li>
<li>Ein <a href="https://hub.docker.com/">Docker Hub</a>-Konto und ein öffentliches Repository. Weitere Informationen zu deren Erstellung finden Sie in <a href="https://docs.docker.com/docker-hub/repos/">Repositories</a> in der Docker-Dokumentation.</li>
<li>Die auf Ihrem lokalen Rechner installierte Docker-Engine. Weitere Informationen finden Sie unter <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04">Installieren und Verwenden von Docker unter Ubuntu 18.04</a>.</li>
</ul>

<p>Sobald Sie diese Komponenten eingerichtet haben, können Sie mit diesem Leitfaden beginnen.</p>

<h2 id="schritt-1-—-klonen-und-konfigurieren-der-anwendung">Schritt 1 — Klonen und Konfigurieren der Anwendung</h2>

<p>In diesem Schritt klonen wir den Anwendungscode von GitHub und konfigurieren Einstellungen wie Datenbankzugangsdaten und Objektspeicherschlüssel.</p>

<p>Der Anwendungscode und das Dockerfile befinden sich im Zweig <code>polls-docker</code> der Django Tutorial Umfrageanwendung <a href="https://github.com/do-community/django-polls/tree/polls-docker">GitHub-Repository</a>. Dieses Repository enthält Code für die <a href="https://docs.djangoproject.com/en/3.0/intro/">Beispiel-Umfrageanwendung</a> aus der Django-Dokumentation, in der Sie lernen, wie Sie eine Umfrageanwendung von Grund auf erstellen.</p>

<p>Der Zweig <code>polls-docker</code> enthält eine dockerisierte Version der Umfrageanwendung. Um zu erfahren, wie die Umfrageanwendung modifiziert wurde, um effektiv in einer containerisierten Umgebung zu arbeiten, lesen Sie bitte <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Erstellen einer Django- und Gunicorn-Anwendung mit Docker</a>.</p>

<p>Beginnen Sie mit der Verwendung von <code>git</code> zum Klonen des Zweigs <code>polls-docker</code> der Django Tutorial Umfrageanwendung <a href="https://github.com/do-community/django-polls">GitHub-Repository</a> auf Ihren lokalen Rechner:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
</li></ul></code></pre>
<p>Navigieren Sie in das Verzeichnis <code>django-polls</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd django-polls
</li></ul></code></pre>
<p>Dieses Verzeichnis enthält den Python-Code der Django-Anwendung, ein <code>Dockerfile</code>, das Docker zum Erstellen des Container-Images verwendet, sowie eine Datei <code>env</code>, die eine Liste von Umgebungsvariablen enthält, die an die laufende Umgebung des Containers übergeben werden müssen. Prüfen Sie das <code>Dockerfile</code>:</p>
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
<p>Dieses Dockerfile verwendet das offizielle Python 3.7.4 <a href="https://hub.docker.com/_/python">Docker-Image</a> als Basis und installiert die Python-Paketanforderungen von Django und Gunicorn, wie sie in der Datei <code>django-polls/requirements.txt</code> definiert sind. Anschließend entfernt es einige unnötige Builddateien, kopiert den Anwendungscode in das Image und legt den Ausführungspfad <code>PATH</code> fest. Schließlich gibt es an, dass Port <code>8000</code> verwendet wird, um eingehende Container-Verbindungen zu akzeptieren und <code>gunicorn</code> mit 3 Workern ausgeführt wird, die Port <code>8000</code> abhören.</p>

<p>Um mehr über die einzelnen Schritte in diesem Dockerfile zu erfahren, lesen Sie bitte Schritt 6 von <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-6-%E2%80%94-writing-the-application-dockerfile">Erstellen einer Django- und Gunicorn-Anwendung mit Docker</a>.</p>

<p>Erstellen Sie nun das Image mit <code>docker build</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker build -t polls .
</li></ul></code></pre>
<p>Wir benennen das Image <code>polls</code> mit dem Flag <code>-t</code> und übergeben im aktuellen Verzeichnis als <em>Build-Kontext</em> den Satz von Daten, auf den beim Erstellen des Images verwiesen werden soll.</p>

<p>Nachdem Docker das Image erstellt und mit Tags versehen hat, listen wir die verfügbaren Images mit <code>docker images</code> auf:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker images
</li></ul></code></pre>
<p>Sie sollten die <code>polls</code>-Images aufgelistet sehen:</p>
<pre class="code-pre "><code>OutputREPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
polls               latest              80ec4f33aae1        2 weeks ago         197MB
python              3.7.4-alpine3.10    f309434dea3a        8 months ago        98.7MB
</code></pre>
<p>Bevor wir den Django-Container ausführen, müssen wir seine Betriebsumgebung mithilfe der im aktuellen Verzeichnis vorhandenen Datei <code>env</code> konfigurieren. Diese Datei wird an den Befehl <code>docker run</code> übergeben, der zum Ausführen des Containers verwendet wird, und Docker injiziert die konfigurierten Umgebungsvariablen in die Betriebsumgebung des Containers.</p>

<p>Öffnen Sie die Datei <code>env</code> mit <code>nano</code> oder Ihrem bevorzugten Editor:</p>
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
<p>Geben Sie die fehlenden Werte für die folgenden Schlüssel ein:</p>

<ul>
<li><code>DJANGO_SECRET_KEY</code>: Setzen Sie diesen auf einen eindeutigen, nicht vorhersagbaren Wert, wie in den <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#secret-key">Django-Dokumentationen</a> beschrieben. Eine Methode zur Generierung dieses Wertes wird in <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Anpassen der Anwendungseinstellungen</a> in dem Tutorial <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Skalierbare Django-Anwendung</a> angeboten.</li>
<li><code>DJANGO_ALLOWED_HOSTS</code>: Diese Variable sichert die Anwendung und verhindert HTTP-Host-Header-Angriffe. Setzen Sie diese Variable für Testzwecke auf <code>*</code>, einen Platzhalter, der auf alle Hosts zutrifft. In der Produktion sollten Sie diese Variable auf <code>your_domain.com</code> setzen. Um mehr über diese Django-Einstellungen zu erfahren, konsultieren Sie die <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#allowed-hosts">Core-Einstellungen</a> der Django-Dokumentation.</li>
<li><code>DATABASE_USERNAME</code>: Setzen Sie diesen auf den in den vorbereitenden Schritten erstellten PostgreSQL Datenbankbenutzer.</li>
<li><code>DATABASE_NAME</code>: Setzen Sie diesen auf <code>polls</code> oder den in den vorbereitenden Schritten erstellten Namen der PostgreSQL-Datenbank.</li>
<li><code>DATABASE_PASSWORD</code>: Setzen Sie dieses auf das in den vorbereitenden Schritten erstellte Passwort für den PostgreSQL Benutzer.</li>
<li><code>DATABASE_HOST</code>: Setzen Sie diesen Wert auf den Hostnamen Ihrer Datenbank.</li>
<li><code>DATABASE_PORT</code>: Setzen Sie diesen Wert auf den Port Ihrer Datenbank.</li>
<li><code>STATIC_ACCESS_KEY_ID</code>: Setzen Sie dies auf den Zugriffsschlüssel Ihres Space oder Objektspeichers.</li>
<li><code>STATIC_SECRET_KEY</code>: Setzen Sie dies auf den Zugriffsschlüssel Ihres Space- oder Objektspeicher-Secret.</li>
<li><code>STATIC_BUCKET_NAME</code>: Setzen Sie dies auf den Namen Ihres Space- oder Objektspeicher-Buckets.</li>
<li><code>STATIC_ENDPOINT_URL</code>: Setzen Sie diese auf die entsprechende Endpunkt-URL des Space oder Objektspeichers, z. B. <code>https://<span class="highlight">space-name</span>.nyc3.digitaloceanspaces.com</code>, wenn sich Ihr Space in der Region <code>nyc3</code> befindet.</li>
</ul>

<p>Wenn Sie die Bearbeitung abgeschlossen haben, speichern und schließen Sie die Datei.</p>

<p>Im nächsten Schritt führen wir den konfigurierten Container lokal aus und erstellen das Datenbankschema. Wir laden ebenfalls statische Assets wie Stylesheets und Images in den Objektspeicher hoch.</p>

<h2 id="schritt-2-—-erstellen-des-datenbankschemas-und-hochladen-von-assets-in-den-objektspeicher">Schritt 2 — Erstellen des Datenbankschemas und Hochladen von Assets in den Objektspeicher</h2>

<p>Nachdem der Container erstellt und konfiguriert ist, verwenden wir nun <code>docker run</code>, um den <code>CMD</code>-Satz in dem Dockerfile zu überschreiben und das Datenbankschema mit den Befehlen <code>manage.py makemigrations</code> und <code>manage.py migrate</code> zu erstellen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"
</li></ul></code></pre>
<p>Wir führen das Container-Image <code>polls:latest</code> aus, übergeben die von uns gerade modifizierte Umgebungsvariablendatei und überschreiben den Dockerfile-Befehl mit <code>sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"</code>, wodurch das durch den Anwendungscode definierte Datenbankschema erstellt wird.</p>

<p>Wenn Sie dies zum ersten Mal ausführen, sollten Sie Folgendes sehen:</p>
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
<p>Dies zeigt an, dass das Datenbankschema erfolgreich erstellt wurde.</p>

<p>Wenn Sie <code>migrate</code> zu einem späteren Zeitpunkt ausführen, führt Django eine Nulloperation durch, es sei denn, das Datenbankschema wurde geändert.</p>

<p>Als Nächstes führen wir eine weitere Instanz des Anwendungscontainers aus und verwenden darin eine interaktive Shell, um einen Administratorbenutzer für das Django-Projekt zu erstellen.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -i -t --env-file env polls sh
</li></ul></code></pre>
<p>Dadurch erhalten Sie eine Shell-Eingabeaufforderung innerhalb des laufenden Containers, die Sie zum Erstellen des Django-Benutzers verwenden können:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python manage.py createsuperuser
</li></ul></code></pre>
<p>Geben Sie einen Benutzernamen, eine E-Mail-Adresse und ein Passwort für Ihren Benutzer ein. Drücken Sie nach dem Erstellen des Benutzers <code>STRG+D</code>, um den Container zu verlassen und zu beenden.</p>

<p>Schließlich generieren wir die statischen Dateien für die Anwendung und laden sie mit <code>collectstatic</code> in den DigitalOcean Space hoch. Beachten Sie, dass dies möglicherweise einige Zeit dauern kann.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py collectstatic --noinput"
</li></ul></code></pre>
<p>Nachdem diese Dateien generiert und hochgeladen sind, erhalten Sie folgende Ausgabe.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>121 static files copied.
</code></pre>
<p>Wir können die Anwendung nun ausführen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env -p 80:8000 polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[2019-10-17 21:23:36 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-10-17 21:23:36 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2019-10-17 21:23:36 +0000] [1] [INFO] Using worker: sync
[2019-10-17 21:23:36 +0000] [7] [INFO] Booting worker with pid: 7
[2019-10-17 21:23:36 +0000] [8] [INFO] Booting worker with pid: 8
[2019-10-17 21:23:36 +0000] [9] [INFO] Booting worker with pid: 9
</code></pre>
<p>Hier führen wir den in dem Dockerfile definierten Standardbefehl <code>gunicorn ---bind :8000 --workers 3 mysite.wsgi:application</code> aus und geben den Container-Port <code>8000</code> frei, sodass Port <code>80</code> auf Ihrem lokalen Rechner dem Port <code>8000</code> des Containers <code>polls</code> zugeordnet wird.</p>

<p>Sie sollten nun über Ihren Webbrowser zu der Anwendung <code>polls</code> navigieren können, indem Sie <code>http://localhost</code> in die URL-Leiste eingeben. Da für den Pfad <code>/</code> keine Route definiert ist, erhalten Sie wahrscheinlich einen <code>404 Page Not Found</code>-Fehler, der zu erwarten ist.</p>

<p>Navigieren Sie zu <code>http://localhost/polls</code>, um die Benutzeroberfläche der Umfrageanwendung zu sehen:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Oberfläche der Umfrageanwendung"></p>

<p>Um die administrative Oberfläche anzuzeigen, besuchen Sie <code>http://localhost/admin</code>. Sie sollten das Authentifizierungsfenster für den Administrator der Umfrageanwendung sehen:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Authentifizierungsseite für Polls-Administrator"></p>

<p>Geben Sie den administrativen Benutzernamen und das Passwort ein, das Sie mit dem Befehl <code>createsuperuser</code> erstellt haben.</p>

<p>Nach der Authentifizierung können Sie auf die administrative Oberfläche der Umfrageanwendung zugreifen:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin_main.png" alt="Administrative Hauptoberfläche von Polls"></p>

<p>Beachten Sie, dass statische Assets für die Anwendungen <code>admin</code> und <code>polls</code> direkt aus dem Objektspeicher bereitgestellt werden. Um dies zu bestätigen, konsultieren Sie <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#testing-spaces-static-file-delivery">Prüfen der statischen Dateizustellung von Spaces</a>.</p>

<p>Wenn Sie die Erkundung abgeschlossen haben, drücken Sie <code>STRG+</code>C im Terminalfenster, in dem der Docker-Container ausgeführt wird, um den Container zu beenden.</p>

<p>Nachdem das Docker-Image der Django-Anwendung getestet, die statischen Assets in den Objektspeicher hochgeladen und das Datenbankschema konfiguriert und für die Verwendung mit Ihrer Anwendung bereit ist/sind, können Sie Ihr Django-App-Image in eine Bildregistrierung wie Docker Hub hochladen.</p>

<h2 id="schritt-3-—-verschieben-des-django-app-images-zu-docker-hub">Schritt 3 — Verschieben des Django-App-Images zu Docker Hub</h2>

<p>Um Ihre Anwendung unter Kubernetes bereitzustellen, muss Ihr App-Image in eine Registrierung wie <a href="https://hub.docker.com/">Docker Hub</a> hochgeladen werden. Kubernetes zieht das App-Image aus seinem Repository und stellt es dann in Ihren Cluster bereit.</p>

<p>Sie können eine private Docker-Registrierung verwenden, wie die <a href="https://www.digitalocean.com/products/container-registry/">DigitalOcean Container Registry</a>, die derzeit kostenlos in Early Access ist, oder eine öffentliche Docker-Registrierung wie Docker Hub. Mit Docker Hub können Sie auch private Docker-Repositorys erstellen. Ein öffentliches Repository erlaubt jedem, die Container-Images zu sehen und abzurufen, während Sie mit einem privaten Repository den Zugriff auf Sie und Ihre Teammitglieder beschränken können.</p>

<p>In diesem Tutorial verschieben wir das Django-Image in das öffentliche Docker Hub-Repository, das in den Voraussetzungen erstellt wurde. Sie können Ihr Image auch in ein privates Repository verschieben, aber das Abrufen von Images aus einem privaten Repository liegt außerhalb des Rahmens dieses Artikels. Um mehr über die Authentifizierung von Kubernetes mit Docker Hub und das Abrufen von privaten Images zu erfahren, lesen Sie bitte <a href="https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/">Abrufen eines Images aus einer privaten Registrierung</a> aus den Kubernetes-Dokumenten.</p>

<p>Beginnen Sie mit der Anmeldung an Docker Hub auf Ihrem lokalen Rechner:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
</code></pre>
<p>Geben Sie Ihren Docker Hub-Benutzernamen und Ihr Passwort ein, um sich anzumelden.</p>

<p>Das Django-Image hat derzeit die Markierung <code>polls:latest</code>. Um es in Ihr Docker Hub-Repository zu verschieben, markieren Sie das Image erneut mit Ihrem Docker Hub-Benutzernamen und dem Repository-Namen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag polls:latest <span class="highlight">your_dockerhub_username</span>/<span class="highlight">your_dockerhub_repo_name</span>:latest
</li></ul></code></pre>
<p>Verschieben Sie das Image in das Repository:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">sammy</span>/<span class="highlight">sammy-django</span>:latest
</li></ul></code></pre>
<p>In diesem Tutorial ist der Docker Hub-Benutzername <strong>sammy</strong> und der Repository-Name ist <strong>sammy-django</strong>. Sie sollten diese Werte durch Ihren eigenen Docker Hub-Benutzernamen und Repository-Namen ersetzen.</p>

<p>Sie sehen eine Ausgabe, die sich aktualisiert, wenn Image-Schichten in Docker Hub verschoben werden.</p>

<p>Nachdem Ihr Image nun für Kubernetes unter Docker Hub verfügbar ist, können Sie es in Ihrem Cluster bereitstellen.</p>

<h2 id="schritt-4-—-einrichten-der-configmap">Schritt 4 — Einrichten der ConfigMap</h2>

<p>Als wir den Django-Container lokal ausgeführt haben, haben wir die Datei <code>env</code> an <code>docker run</code> übergeben, um Konfigurationsvariablen in die Laufzeitumgebung zu injizieren. Bei Kubernetes können Konfigurationsvariablen mit <a href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/">ConfigMaps</a> und <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Secrets</a> injiziert werden.</p>

<p>ConfigMaps sollte verwendet werden, um nicht vertrauliche Konfigurationsdaten wie App-Einstellungen zu speichern, und Secrets sollte für sensible Informationen wie API-Schlüssel und Datenbank-Zugangsdaten verwendet werden. Sie werden beide auf ähnliche Weise in Container injiziert, aber Secrets haben zusätzliche Zugriffskontrolle und Sicherheitsfunktionen wie <a href="https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/">Verschlüsselung im Ruhezustand</a>. Secrets speichern außerdem Daten in base64, während ConfigMaps Daten im Klartext speichern.</p>

<p>Erstellen Sie zunächst ein Verzeichnis namens <code>yaml</code>, in dem wir unsere Kubernetes-Manifeste speichern werden. Navigieren Sie in das Verzeichnis.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir yaml
</li><li class="line" data-prefix="$">cd
</li></ul></code></pre>
<p>Öffnen Sie eine Datei namens <code>polls-configmap.yaml</code> in <code>nano</code> oder Ihrem bevorzugten Texteditor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-configmap.yaml
</li></ul></code></pre>
<p>Fügen Sie das folgende ConfigMap-Manifest ein:</p>
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
<p>Wir haben die nicht sensible Konfiguration aus der in <code>Schritt 1</code> geänderten Datei <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">env</a> extrahiert und in ein ConfigMap-Manifest eingefügt. Das ConfigMap-Objekt wird <code>polls-config</code> genannt. Kopieren Sie die gleichen Werte hinein, die Sie im vorherigen Schritt in die Datei <code>env</code> eingegeben haben.</p>

<p>Lassen Sie für Testzwecke <code>DJANGO_ALLOWED_HOSTS</code> auf <code>*</code> stehen, um die Host-Header-basierte Filterung zu deaktivieren. In einer Produktionsumgebung sollten Sie dies auf die Domäne Ihrer Anwendung setzen.</p>

<p>Wenn Sie mit der Bearbeitung der Datei fertig sind, speichern und schließen Sie sie.</p>

<p>Erstellen Sie die ConfigMap in Ihrem Cluster mit <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-configmap.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>configmap/polls-config created
</code></pre>
<p>Nachdem die ConfigMap erstellt wurde, erstellen wir im nächsten Schritt das von unserer Anwendung verwendete Secret.</p>

<h2 id="schritt-5-—-einrichten-des-secret">Schritt 5 — Einrichten des Secret</h2>

<p>Secret-Werte müssen <a href="https://en.wikipedia.org/wiki/Base64">base64-kodiert</a> sein, d. h. das Erstellen von Secret-Objekten in Ihrem Cluster ist etwas aufwendiger als das Erstellen von ConfigMaps. Sie können den Vorgang aus dem vorherigen Schritt wiederholen, indem Sie Secret-Werte manuell base64-kodieren und in eine Manifestdatei einfügen. Sie können sie auch mit einer Umgebungsvariablendatei <code>kubectl create</code> und dem Flag <code>--from-env-file</code> erstellen, was wir in diesem Schritt tun werden.</p>

<p>Wir verwenden erneut die Datei <code>env</code> von <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Schritt 1</a> und entfernen die in die ConfigMap eingefügten Variablen. Erstellen Sie eine Kopie der Datei <code>env</code> mit dem Namen <code>polls-secrets</code> im Verzeichnis <code>yaml</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp ../env ./polls-secrets
</li></ul></code></pre>
<p>Bearbeiten Sie die Datei in Ihrem bevorzugten Editor:</p>
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
<p>Löschen Sie alle in das ConfigMap-Manifest eingefügten Variablen. Wenn Sie fertig sind, sollte die Datei wie folgt aussehen:</p>
<div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=<span class="highlight">your_secret_key</span>
DATABASE_NAME=polls
DATABASE_USERNAME=<span class="highlight">your_django_db_user</span>
DATABASE_PASSWORD=<span class="highlight">your_django_db_user_password</span>
DATABASE_HOST=<span class="highlight">your_db_host</span>
DATABASE_PORT=<span class="highlight">your_db_port</span>
STATIC_ACCESS_KEY_ID=<span class="highlight">your_space_access_key</span>
STATIC_SECRET_KEY=<span class="highlight">your_space_access_key_secret</span>
</code></pre>
<p>Stellen Sie sicher, dass Sie die gleichen Werte wie in <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Schritt 1</a> verwenden. Wenn Sie fertig sind, speichern und schließen Sie die Datei.</p>

<p>Erstellen Sie das Secret in Ihrem Cluster mit <code>kubectl create secret</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl create secret generic polls-secret --from-env-file=poll-secrets
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>secret/polls-secret created
</code></pre>
<p>Hier erstellen wir ein Secret-Objekt namens <code>polls-secret</code> und übergeben die soeben erstellte Secrets-Datei.</p>

<p>Sie können das Secret mit <code>kubectl describe</code> inspizieren:</p>
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
<p>Zu diesem Zeitpunkt haben Sie die Konfiguration Ihrer Anwendung in Ihrem Kubernetes-Cluster mithilfe der Objekttypen Secret und ConfigMap gespeichert. Wir sind nun bereit, die Anwendung in dem Cluster bereitzustellen.</p>

<h2 id="schritt-6-—-bereitstellen-der-django-anwendung-mithilfe-eines-deployment">Schritt 6 — Bereitstellen der Django-Anwendung mithilfe eines Deployment</h2>

<p>In diesem Schritt erstellen Sie ein Deployment für Ihre Django-Anwendung. Eine Kubernetes Deployment ist ein <em>Controller</em>, der zur Verwaltung von zustandslosen Anwendungen in Ihrem Cluster verwendet werden kann. Ein Controller ist eine Kontrollschleife, die Arbeitslasten reguliert, indem er sie herauf- oder herunterskaliert. Controller starten auch fehlgeschlagene Container neu und leeren sie aus.</p>

<p>Deployments steuern ein oder mehrere Pods, die kleinste einsetzbare Einheit in einem Kubernetes-Cluster. Pods umschließen einen oder mehrere Container. Um mehr über die verschiedenen Arten von Arbeistlasten zu erfahren, die Sie starten können, lesen Sie bitte <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes">Eine Einführung in Kubernetes</a>.</p>

<p>Öffnen Sie zunächst eine Datei namens <code>polls-deployment.yaml</code> in Ihrem bevorzugten Editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-deployment.yaml
</li></ul></code></pre>
<p>Fügen Sie das folgende Deployment-Manifest ein:</p>
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
<p>Geben Sie den entsprechenden Container-Image-Namen ein und verweisen Sie dabei auf das Django Umfrage-Image, das Sie in <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-2-%E2%80%94-creating-the-database-schema-and-uploading-assets-to-object-storage">Schritt 2</a> in Docker Hub verschoben haben.</p>

<p>Hier definieren wir eine Kubernetes Deployment namens <code>polls-app</code> und kennzeichnen es mit dem Schlüsselwertpaar <code>app: polls</code>. Wir geben an, dass wir zwei Replikate des Pods ausführen möchten, der unterhalb des Felds <code>template</code> definiert ist.</p>

<p>Durch die Verwendung von <code>envFrom</code> mit <code>secretRef</code> und <code>configMapRef</code> legen wir fest, dass alle Daten aus dem Secret <code>polls-secret</code> und der ConfigMap <code>polls-config</code> als Umgebungsvariablen in die Container injiziert werden sollen. Die Schlüssel ConfigMap und Secret werden zu den Namen der Umgebungsvariablen.</p>

<p>Abschließend geben wir <code>containerPort</code> <code>8000</code> frei und nennen ihn <code>gunicorn</code>.</p>

<p>Um mehr über die Konfiguration von Kubernetes Deployments zu erfahren, konsultieren Sie bitte <a href="https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/">Deployments</a> aus der Kubernetes-Dokumentation.</p>

<p>Wenn Sie mit der Bearbeitung der Datei fertig sind, speichern und schließen Sie sie.</p>

<p>Erstellen Sie das Deployment in Ihrem Cluster mit <code>kubectl apply -f</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-deployment.yaml
</li></ul></code></pre><pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">deployment.apps/polls-app created
</li></ul></code></pre>
<p>Überprüfen Sie mit <code>kubectl get</code>, ob das Deployment korrekt bereitgestellt wurde:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get deploy polls-app
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME        READY   UP-TO-DATE   AVAILABLE   AGE
polls-app   2/2     2            2           6m38s
</code></pre>
<p>Wenn Sie auf einen Fehler stoßen, oder etwas nicht so ganz funktioniert, können Sie <code>kubectl describe</code> verwenden, um das fehlgeschlagene Deployment zu inspizieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe deploy
</li></ul></code></pre>
<p>Sie können die beiden Pods mit <code>kubectl get pod</code> inspizieren:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get pod
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                         READY   STATUS    RESTARTS   AGE
polls-app-847f8ccbf4-2stf7   1/1     Running   0          6m42s
polls-app-847f8ccbf4-tqpwm   1/1     Running   0          6m57s
</code></pre>
<p>Zwei Replikate Ihrer Django-Anwendung sind nun im Cluster in Betrieb. Um auf die Anwendung zuzugreifen, müssen Sie einen Kubernetes-Dienst erstellen, was wir als Nächstes tun.</p>

<h2 id="schritt-7-—-erlauben-des-externen-zugriffs-unter-verwendung-eines-dienstes">Schritt 7 — Erlauben des externen Zugriffs unter Verwendung eines Dienstes</h2>

<p>In diesem Schritt erstellen Sie einen Dienst für Ihre Django-Anwendung. Ein Kubernetes-Dienst ist eine Abstraktion, die es Ihnen ermöglicht, einen Satz laufender Pods als Netzwerkdienst bereitzustellen. Mit einem Dienst können Sie einen stabilen Endpunkt für Ihre Anwendung erstellen, der sich nicht ändert, wenn Pods sterben und neu erstellt werden.</p>

<p>Es gibt mehrere Dienstarten, einschließlich ClusterIP-Dienste, die den Dienst auf einer clusterinternen IP-Adresse bereitstellen, NodePort-Dienste, die den Dienst auf jedem Knoten an einem statischen Port, dem Nodeport, bereitstellen, und LoadBalancer-Dienste, die einen Cloud-Load-Balancer bereitstellen, um externen Datenverkehr zu den Pods in Ihrem Cluster zu leiten (über NodePorts, die er automatisch erstellt). Um mehr über diese zu erfahren, lesen Sie bitte <a href="https://kubernetes.io/docs/concepts/services-networking/service/">Service</a> in den Kubernetes-Dokumenten.</p>

<p>In unserer endgültigen Einrichtung verwenden wir einen ClusterIP-Dienst, der über einen Ingress und den in den Voraussetzungen für diesen Leitfaden eingerichteten Ingress-Controller freigegeben wird. Um zu testen, ob alles korrekt funktioniert, erstellen wir zunächst einen temporären NodePort-Dienst, um auf die Django-Anwendung zuzugreifen.</p>

<p>Beginnen Sie mit dem Erstellen einer Datei namens <code>polls-svc.yaml</code> mit Ihrem bevorzugten Editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Fügen Sie das folgende Dienst-Manifest ein:</p>
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
<p>Hier erstellen wir einen NodePort-Dienst namens <code>polls</code> und geben ihm die Kennzeichnung <code>app: polls</code>. Dann wählen wir Backend-Pods mit der Kennzeichnung <code>app: polls</code>, und zielen auf deren Ports <code>8000</code>.</p>

<p>Wenn Sie mit der Bearbeitung der Datei fertig sind, speichern und schließen Sie sie.</p>

<p>Stellen Sie den Dienst mit <code>kubectl apply</code> bereit:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls created
</code></pre>
<p>Bestätigen Sie mit <code>kubectl get svc</code>, dass Ihr Dienst erstellt wurde:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
polls   NodePort   10.245.197.189   &lt;none&gt;        8000:32654/TCP   59s
</code></pre>
<p>Diese Ausgabe zeigt die clusterinterne IP des Dienstes und den NodePort (<code>32654</code>). Um eine Verbindung mit dem Dienst herzustellen, benötigen wir die externen IP-Adressen für unsere Cluster-Knoten:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get node -o wide
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-7no0qd9e0-364fd   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.5    203.0.113.1   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fi   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.4    203.0.113.2    Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fv   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.3    203.0.113.3   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
</code></pre>
<p>Rufen Sie in Ihrem Webbrowser Ihre Umfrageanwendung mit der externen IP-Adresse eines beliebigen Knotens und dem NodePort auf. Mit der vorstehenden Ausgabe würde die URL der Anwendung lauten: <code>http://<span class="highlight">203.0.113.1</span>:32654/polls</code>.</p>

<p>Sie sollten die gleiche Oberfläche der Umfrageanwendung sehen, auf die Sie in Schritt 1 lokal zugegriffen haben:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Oberfläche der Umfrageanwendung"></p>

<p>Sie können den gleichen Test mit der Route <code>/admin</code> wiederholen: <code>http://<span class="highlight">203.0.113.1</span>:32654/admin</code>. Sie sollten die gleiche Admin-Oberfläche wie zuvor sehen:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Authentifizierungsseite für Polls-Administrator"></p>

<p>Zu diesem Zeitpunkt haben Sie zwei Replikate des Django Umfrageanwendungs-Containers mithilfe eines Deployments bereitgestellt. Außerdem haben Sie einen stabilen Netzwerk-Endpunkt für diese beiden Replikate erstellt und ihn mit einem NodePort-Dienst von außen zugänglich gemacht.</p>

<p>Der letzte Schritt in diesem Tutorial ist die Sicherung des externen Datenverkehrs zu Ihrer Anwendung mit HTTPS. Dazu verwenden wir den in den Voraussetzungen installierten Ingress-Controller <code>ingress-nginx</code> und erstellen ein Ingress-Objekt, um den externen Verkehr in den Kubernetes-Dienst <code>polls</code> zu leiten.</p>

<h2 id="schritt-8-—-konfigurieren-von-https-unter-verwendung-von-nginx-ingress-und-cert-manager">Schritt 8 — Konfigurieren von HTTPS unter Verwendung von Nginx Ingress und cert-manager</h2>

<p>Mit <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingresses</a> von Kubernetes können Sie den Datenverkehr von außerhalb Ihres Kubernetes-Clusters flexibel an Dienste innerhalb Ihres Clusters leiten. Dies geschieht mit der Verwendung von Ingress-Objekten, die Regeln für das Routing von HTTP- und HTTPS-Verkehr zu Kubernetes-Diensten definieren, und Ingress-<em>Controllern</em>, die die Regeln umsetzen, indem sie den Verkehr durch Lastverteilung an die entsprechenden Backend-Dienste weiterleiten.</p>

<p>In den Voraussetzungen haben den Ingress-Controller <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> und das TLS-Zertifizierungsautomatisierungs-Add-on <a href="https://github.com/jetstack/cert-manager">cert-manager</a> installiert. Außerdem haben Sie die Staging- und Produktions-ClusterIssuers für Ihre Domäne unter Verwendung der Zertifizierungsstelle Let’s Encrypt eingerichtet und einen Ingress erstellt, um die Ausstellung von Zertifikaten und die TLS-Verschlüsselung für zwei Dummy-Backend-Dienste zu testen. Bevor Sie mit diesem Schritt fortfahren, sollten Sie den in dem <code>Voraussetzungs-Tutorial</code> erstellten Ingress <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">echo-ingress</a> löschen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl delete ingress echo-ingress
</li></ul></code></pre>
<p>Wenn Sie möchten, können Sie auch die Dummy-Dienste und -Deployments mit <code>kubectl delete svc</code> und <code>kubectl delete delploy</code> löschen, aber dies ist für die Durchführung des Tutorials nicht unbedingt erforderlich.</p>

<p>Sie sollten auch einen DNS-<code>A</code>-Datensatz mit <code><span class="highlight">your_domain.com</span></code> erstellt haben, der auf die öffentliche IP-Adresse des Ingress Load Balancers verweist. Wenn Sie einen DigitalOcean Load Balancer verwenden, finden Sie diese IP-Adresse im Abschnitt <a href="https://cloud.digitalocean.com/networking/load_balancers">Load Balancers</a> des Bedienfelds. Wenn Sie DigitalOcean auch für die Verwaltung der DNS-Datensätze Ihrer Domäne verwenden, konsultieren Sie bitte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Verwalten von DNS-Datensätzen</a>, um zu erfahren, wie Sie <code>A</code>-Datensätze erstellen.</p>

<p>Wenn Sie DigitalOcean Kubernetes verwenden, stellen Sie außerdem sicher, dass Sie die in <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-(optional)">Schritt 5 von Einrichten eines Nginx-Ingress mit Cert-Manager unter DigitalOcean Kubernetes</a> beschriebene Problemumgehung umgesetzt haben.</p>

<p>Sobald Sie einen <code>A</code>-Datensatz haben, der auf den Ingress Controller Load Balancer verweist, können Sie einen Ingress für <code><span class="highlight">your_domain.com</span></code> und den Dienst <code>polls</code> erstellen.</p>

<p>Öffnen Sie eine Datei namens <code>polls-ingress.yaml</code> mit Ihrem bevorzugten Editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Fügen Sie das folgende Ingress-Manifest ein:</p>
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
<p>Wir erstellen ein Ingress-Objekt namens <code>polls-ingress</code> und annotieren es, um die Steuerungsebene anzuweisen, den Ingress-Controller ingress-nginx und den ClusterIssuer „staging“ zu verwenden. Außerdem aktivieren wir TLS für <code>your_domain.com</code> und speichern das Zertifikat und den privaten Schlüssel in einem Secret namens <code>polls-tls</code>. Schließlich definieren wir eine Regel, um den Verkehr für den Host <code>your_domain.com</code> an den Dienst <code>polls</code> auf Port <code>8000</code> zu leiten.</p>

<p>Wenn Sie mit der Bearbeitung der Datei fertig sind, speichern und schließen Sie sie.</p>

<p>Erstellen Sie den Ingress in Ihrem Cluster mit <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress created
</code></pre>
<p>Sie können <code>kubectl describe</code> verwenden, um den Status des soeben erstellten Ingress zu verfolgen:</p>
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
<p>Sie können auch <code>describe</code> für das Zertifikat <code>polls-tls</code> ausführen, um dessen erfolgreiche Erstellung weiter zu bestätigen:</p>
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
<p>Dies bestätigt, dass das TLS-Zertifikat erfolgreich ausgestellt wurde und die HTTPS-Verschlüsselung nun für <code>your_domain.com</code> aktiv ist.</p>

<p>Da wir den Staging-ClusterIssuer verwendet haben, werden die meisten Webbrowser dem gefälschten Let’s Encrypt-Zertifikat nicht vertrauen, das es ausgestellt hat, sodass die Navigation zu <code>your_domain.com</code> Sie zu einer Fehlerseite führt.</p>

<p>Um eine Testanfrage zu senden, verwenden wir <code>wget</code> von der Befehlszeile aus:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget -O - http://<span class="highlight">your_domain.com</span>/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
ERROR: cannot verify your_domain.com's certificate, issued by ‘CN=Fake LE Intermediate X1’:
  Unable to locally verify the issuer's authority.
To connect to your_domain.com insecurely, use `--no-check-certificate'.
</code></pre>
<p>Wir verwenden das vorgeschlagene Flag <code>--no-check-certificate</code>, um die Zertifikatsprüfung zu umgehen:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget --no-check-certificate -q -O - http://your_domain.com/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>

&lt;link rel="stylesheet" type="text/css" href="https://your_space.nyc3.digitaloceanspaces.com/django-polls/static/polls/style.css"&gt;


    &lt;p&gt;No polls are available.&lt;/p&gt;
</code></pre>
<p>Diese Ausgabe zeigt das HTML für die Benutzeroberflächenseite <code>/polls</code> und bestätigt auch, dass das Stylesheet aus dem Objektspeicher bedient wird.</p>

<p>Nachdem Sie nun die Zertifikatausstellung mit dem Staging-ClusterIssuer erfolgreich getestet haben, können Sie den Ingress ändern, um den ClusterIssuer, zu verwenden.</p>

<p>Öffnen Sie <code>polls-ingress.yaml</code> zur erneuten Bearbeitung:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Ändern Sie die Annotation <code>cluster-issuer</code>:</p>
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
<p>Wenn Sie fertig sind, speichern und schließen Sie die Datei. Aktualisieren Sie den Ingress mit <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress configured
</code></pre>
<p>Um den Status der Zertifikatausstellung zu verfolgen, können Sie <code>kubectl describe certificate polls-tls</code> und <code>kubectl describe ingress polls-ingress</code> verwenden:</p>
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
<p>Die obige Ausgabe bestätigt, dass das neue Produktionszertifikat erfolgreich ausgestellt und im Secret <code>polls-tls</code> gespeichert wurde.</p>

<p>Navigieren Sie in Ihrem Webbrowser zu <code>your_domain.com/polls</code>, um zu bestätigen, dass die HTTPS-Verschlüsselung aktiviert ist und alles wie erwartet funktioniert. Sie sollten die Oberfläche der Umfrageanwendung sehen:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Oberfläche der Umfrageanwendung"></p>

<p>Überprüfen Sie, ob die HTTPS-Verschlüsselung in Ihrem Webbrowser aktiv ist. Wenn Sie Google Chrome verwenden, bestätigt die Ankunft auf der obigen Seite ohne Fehler, dass alles korrekt funktioniert. Außerdem sollten Sie ein Vorhängeschloss in der URL-Leiste sehen. Durch Klicken auf das Vorhängeschloss können Sie die Details des Let’s Encrypt-Zertifikats einsehen.</p>

<p>Als letzte Bereinigungsaufgabe können Sie optional den Typ des Dienstes <code>polls</code> von NodePort auf den nut internen Typ ClusterIP umstellen.</p>

<p>Ändern Sie <code>polls-svc.yaml</code> mit Ihrem Editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Ändern Sie den <code>type</code> von <code>NodePort</code> auf <code>ClusterIP</code>:</p>
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
<p>Wenn Sie mit der Bearbeitung der Datei fertig sind, speichern und schließen Sie sie.</p>

<p>Stellen Sie die Änderungen mit <code>kubectl apply</code> bereit:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml --force
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls configured
</code></pre>
<p>Bestätigen Sie mit <code>kubectl get svc</code>, dass Ihr Dienst geändert wurde:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
polls   ClusterIP   10.245.203.186   &lt;none&gt;        8000/TCP   22s
</code></pre>
<p>Diese Ausgabe zeigt, dass der Diensttyp nun ClusterIP ist. Der einzige Weg darauf zuzugreifen, ist über Ihre Domäne und den in diesem Schritt erstellten Ingress.</p>

<h2 id="zusammenfassung">Zusammenfassung</h2>

<p>In diesem Tutorial haben Sie eine skalierbare HTTPS-gesicherte Django-Anwendung in einem Kubernetes-Cluster bereitgestellt. Statische Inhalte werden direkt aus dem Objektspeicher bedient, und die Anzahl der laufenden Pods kann über das Feld <code>replicas</code> in dem Deployment-Manifest <code>polls-app</code> schnell herauf- oder herunterskaliert werden.</p>

<p>Wenn Sie einen DigitalOcean Space verwenden, können Sie auch die Bereitstellung statischer Assets über ein Content Delivery-Netzwerk aktivieren und eine benutzerdefinierte Subdomäne für Ihren Space erstellen. Lesen Sie bitte <strong>Aktivieren von CDN</strong> aus <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#enabling-cdn-optional">Einrichten einer skalierbaren Django-Anwendung mit von DigitalOcean verwalteten Datenbanken und Spaces</a>, um mehr zu erfahren.</p>

<p>Um den Rest der zu lesen, besuchen Sie bitte unsere <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">Seite Von Containern zu Kubernetes mit Django</a>.</p>
