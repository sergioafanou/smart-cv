---
title : "Comment déployer une application Django flexible et sécurisée avec Kubernetes"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes-fr
image: "https://sergio.afanou.com/assets/images/image-midres-7.jpg"
---

<h3 id="introduction">Introduction</h3>

<p>Au cours de ce tutoriel, vous allez déployer une application de sondage Django conteneurisée dans un cluster Kubernetes.</p>

<p><a href="https://www.djangoproject.com/">Django</a> est un framework web puissant qui peut vous aider à lancer votre application en Python. Il intègre plusieurs fonctionnalités pratiques comme un <a href="https://en.wikipedia.org/wiki/Object-relational_mapping">object-relational mapper</a>, l'authentification des utilisateurs et une interface d'administration personnalisable pour votre application. Il comprend également un <a href="https://docs.djangoproject.com/en/2.1/topics/cache/">caching framework</a> et encourage la conception de l'application propre grâce à son <a href="https://docs.djangoproject.com/en/2.1/topics/http/urls/">URL Dispatcher</a> et son <a href="https://docs.djangoproject.com/en/2.1/topics/templates/">système de modèles</a>.</p>

<p>Dans le tutoriel <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Comment créer une application Django et Gunicorn avec Docker</a>, le <a href="https://docs.djangoproject.com/en/3.1/intro/tutorial01/">tutoriel Django sur l'application de sondages</a> a été modifié en prenant en considération la méthodologie du <a href="https://12factor.net/">Twelve-Factor</a> pour créer des applications web natives en nuage. Cette configuration conteneurisée a été cadrée et sécurisée par un certificat TLS Nginx reverse-proxy et Let&rsquo;s Encrypt-signed dans le tutoriel <a href="https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt">Comment cadrer et sécuriser une application Django avec Docker, Nginx et Let&rsquo;s Encrypt</a>. Au cours de ce dernier tutoriel dans les séries <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">Des conteneurs à Kubernetes avec Django</a>, la nouvelle version de l'application de sondage Django sera déployée dans un cluster Kubernetes.</p>

<p><a href="https://kubernetes.io/">Kubernetes</a> est un orchestrateur de conteneurs libre puissant qui automatise le déploiement, la mise à l'échelle et la gestion des applications conteneurisées. Les objets Kubernetes comme ConfigMaps et Secrets vous permettent de centraliser et de découpler la configuration de vos conteneurs, tandis que les contrôleurs comme les Deployments redémarrent automatiquement les conteneurs défaillants et permettent une mise à l'échelle rapide des répliques de conteneurs. L'activation du chiffrement TLS se fait à l'aide d'un objet Ingress et le contrôleur Ingress open-source <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a>. L'add-on Kubernetes <a href="https://github.com/jetstack/cert-manager">cert-manager</a> renouvelle et publie des certificats à l'aide de l'autorité de certification gratuite <a href="https://letsencrypt.org/">Let&rsquo;s Encrypt</a>.</p>

<h2 id="conditions-préalables">Conditions préalables</h2>

<p>Pour suivre ce tutoriel, vous aurez besoin de :</p>

<ul>
<li>Un cluster 1.15+ Kubernetes avec <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">role-based access control</a> (RBAC) activé. Cette configuration utilisera un cluster <a href="https://www.digitalocean.com/products/kubernetes/">DigitalOcean Kubernetes</a>. Cependant, vous pouvez créer un cluster en utilisant une <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04">autre méthode</a>.</li>
<li>L'outil de ligne de commande <code>kubectl</code> installé sur votre machine locale et configuré pour vous connecter à votre cluster. Vous pouvez en savoir plus sur l'installation de <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/">kubectl</a> <code>dans la documentation officielle</code>. Si vous utilisez un cluster DigitalOcean Kubernetes, veuillez consulter : <a href="https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/">Comment se connecter à un cluster DigitalOcean Kubernetes</a> pour apprendre à vous connecter à votre cluster en utilisant <code>kubectl</code>.</li>
<li>Un nom de domaine enregistré. Tout au long de ce tutoriel, nous utiliserons <code>your_domain.com</code>. Vous pouvez en obtenir un gratuitement sur <a href="http://www.freenom.com/en/index.html">Freenom</a> ou utiliser le registre de domaine de votre choix.</li>
<li>Un contrôleur d'Ingress <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> et le gestionnaire de certificats TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a> installés sur votre cluster et configurés pour émettre des certificats TLS. Pour apprendre à installer et configurer un Ingress avec gestionnaire de certificats, veuillez consulter <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">Comment configurer un Ingress Nginx avec Cert-Manager sur DigitalOcean Kubernetes</a>.</li>
<li>Un enregistrement DNS <code>A</code> avec <code>your_domain.com</code> pointant sur l'adresse IP publique du load balancer Ingress. Si vous utilisez DigitalOcean pour gérer les enregistrements DNS de votre domaine, consultez <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Comment gérer des enregistrements DNS</a> pour apprendre à créer des enregistrements <code>A</code>.</li>
<li>Un object storage bucket S3 comme un <a href="https://www.digitalocean.com/products/spaces/">espace DigitalOcean</a>pour stocker les fichiers statiques de votre projet Django et un ensemble de clés d'accès pour cet espace. Pour apprendre à créer un espace, consultez la documentation produit <a href="https://www.digitalocean.com/docs/spaces/how-to/create/">Comment créer des espaces</a>. Pour apprendre à créer des clés d'accès pour les espaces, consultez <a href="https://www.digitalocean.com/docs/spaces/how-to/administrative-access/#access-keys">Partager l'accès aux espaces avec les clés d'accès</a>. Avec des modifications mineures, vous pouvez utiliser n'importe quel service de stockage d'objets que le plugin <a href="https://django-storages.readthedocs.io/en/latest/">django-storages</a> prend en charge.</li>
<li>Une instance serveur PostgreSQL, une base de données et un utilisateur pour votre application Django. Avec des modifications mineures, vous pouvez utiliser n'importe quelle base de données que <a href="https://docs.djangoproject.com/en/2.2/ref/databases/">Django prend en charge</a>.

<ul>
<li>La base de données PostgreSQL doit être appelée <strong>polls</strong> (ou un autre nom facile à retenir à saisir dans vos fichiers de configuration ci-dessous). Et, dans ce tutoriel, l'utilisateur de la base de données Django se nommera <strong>sammy</strong>. Pour obtenir des conseils sur la création de ces applications, suivez l'Étape 1 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-1-%E2%80%94-creating-the-postgresql-database-and-user">Comment construire une application Django et Gunicorn avec Docker</a>. Vous devez suivre ces étapes à partir de votre machine locale.</li>
<li>Un <a href="https://www.digitalocean.com/products/managed-databases/">cluster PostgreSQL géré</a> par DigitalOcean est utilisé dans ce tutoriel. Pour apprendre à créer un cluster, consultez la <a href="https://www.digitalocean.com/docs/databases/how-to/clusters/create/">documentation produit des bases de données</a> gérées par DigitalOcean</li>
<li>Vous pouvez également installer et exécuter votre propre instance PostgreSQL. Pour obtenir des conseils sur l'installation et l'administration de PostgreSQL sur un serveur Ubuntu, consultez <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04">Comment installer et utiliser PostgreSQL sur Ubuntu 18.04</a>.</li>
</ul></li>
<li>Un compte <a href="https://hub.docker.com/">Docker Hub</a> et un référentiel public. Pour plus d'informations sur la création de ces éléments, veuillez consulter le document <a href="https://docs.docker.com/docker-hub/repos/">Référentiels</a> de la documentation fournie par Docker.</li>
<li>Le moteur Docker installé sur votre machine locale. Veuillez-vous reporter <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04">Comment installer et utiliser Docker sur Ubuntu 18.04</a> pour en savoir plus.</li>
</ul>

<p>Une fois ces composants configurés, vous êtes prêt à suivre ce guide.</p>

<h2 id="Étape-1-—-cloner-et-configurer-l-39-application">Étape 1 — Cloner et configurer l'application</h2>

<p>Au cours de cette étape, nous allons cloner le code de l'application de GitHub et configurer des paramètres comme les identifiants de la base de données et les clés de stockage d'objets.</p>

<p>Vous pourrez trouver le code de l'application et le Dockerfile dans la branche <code>polls-docker</code> du <a href="https://github.com/do-community/django-polls/tree/polls-docker">GitHub repository</a> de l'application de sondage du tutorial Django. Ce référentiel contient le code pour l&rsquo;<a href="https://docs.djangoproject.com/en/3.0/intro/">exemple d'application de sondage</a> utilisé dans la documentation de Django, qui vous apprend à développer une application de sondage à partir de zéro.</p>

<p>La branche <code>polls-docker</code> contient une version dockerisée de l'application de sondage. Pour savoir de quelle manière l'application de sondage a été modifiée pour fonctionner efficacement dans un environnement conteneurisé, consultez <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Comment construire une application Django et Gunicorn avec Docker</a>.</p>

<p>Tout d'abord, utilisez <code>git</code> pour cloner la branche <code>polls-docker</code> du <a href="https://github.com/do-community/django-polls">GitHub repository</a> de l'application de sondage Django sur votre machine locale :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
</li></ul></code></pre>
<p>Naviguez dans le répertoire <code>django-polls</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd django-polls
</li></ul></code></pre>
<p>Ce répertoire contient le code Python de l'application Django, un <code>Dockerfile</code> que Docker utilisera pour construire l'image du conteneur, ainsi qu'un fichier <code>env</code> qui contient une liste de variables d'environnement à passer dans l'environnement d'exécution du conteneur. Inspectez le <code>Dockerfile</code> :</p>
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
<p>Ce Dockerfile utilise la <a href="https://hub.docker.com/_/python">Docker image</a> officielle de Python 3.7.4 comme base et installe les exigences du paquet Python de Django et Gunicorn, telles que définies dans le fichier <code>django-polls/requirements.txt</code>. Il supprime ensuite quelques fichiers de construction inutiles, copie le code de l'application dans l'image, et définit le <code>PATH</code> d'exécution. Enfin, il déclare que le port <code>8000</code> sera utilisé pour accepter les connexions de conteneurs entrantes, et exécute <code>gunicorn</code> avec 3 travailleurs, en écoutant sur le port <code>8000</code>.</p>

<p>Pour en savoir plus sur chacune des étapes de ce Dockerfile, consultez l'Étape 6 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-6-%E2%80%94-writing-the-application-dockerfile">Comment construire une application Django et Gunicorn avec Docker</a>.</p>

<p>Maintenant, construisez l'image à l'aide de <code>docker build</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker build -t polls .
</li></ul></code></pre>
<p>Nous nommons l'image <code>polls</code> en utilisant le drapeau <code>-t</code> et passons dans le répertoire courant comme <em>contexte de construction</em>, l'ensemble de fichiers à faire référence lors de la construction de l'image.</p>

<p>Après que Docker ait construit et étiqueté l'image, listez les images disponibles à l'aide de <code>docker images</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker images
</li></ul></code></pre>
<p>Vous devriez voir l'image <code>polls</code> listée :</p>
<pre class="code-pre "><code>OutputREPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
polls               latest              80ec4f33aae1        2 weeks ago         197MB
python              3.7.4-alpine3.10    f309434dea3a        8 months ago        98.7MB
</code></pre>
<p>Avant de lancer le conteneur Django, nous devons configurer son environnement d'exécution à l'aide du fichier <code>env</code> présent dans le répertoire actuel. Ce fichier sera transmis dans la commande <code>docker run</code> utilisée pour exécuter le conteneur, et Docker injectera les variables d'environnement configurées dans l'environnement d'exécution du conteneur.</p>

<p>Ouvrez le fichier <code>env</code> avec <code>nano</code> ou votre éditeur préféré :</p>
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
<p>Remplissez les valeurs manquantes des clés suivantes :</p>

<ul>
<li><code>DJANGO_SECRET_KEY</code> : définissez cette valeur à une valeur unique et imprévisible, comme indiqué dans les <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#secret-key">docs de Django</a>. Une méthode de génération de cette clé est fournie dans <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Ajustement des paramètres du tutoriel</a> sur les <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">applications Django dimensionnables</a>.</li>
<li><code>DJANGO_ALLOWED_HOSTS</code>: : cette variable sécurise l'application et prévient les attaques d'en-tête d'hôte HTTP. Pour les besoins de test, définissez cette variable à <code>*</code>, un joker qui correspondra à tous les hôtes. En production, vous devriez la définir sur <code>your_domain.com</code>. Pour en savoir plus sur ce paramètre Django, consultez <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#allowed-hosts">les paramètres de base</a> dans les docs Django.</li>
<li><code>DATABASE_USERNAME</code> : définissez ce paramètre sur l'utilisateur de la base de données PostgreSQL créé dans les étapes préalables.</li>
<li><code>DATABASE_NAME</code> : définissez ce paramètres sur <code>polls</code> ou le nom de la base de données PostgreSQL créée dans les étapes préalables.</li>
<li><code>DATABASE_PASSWORD</code> : définissez ce paramètre sur le mot de passe de l'utilisateur PostgreSQL créé dans les étapes préalables.</li>
<li><code>DATABASE_HOST</code> : définissez ce paramètre sur le nom d'hôte de votre base de données.</li>
<li><code>DATABASE_PORT</code> : définissez ce paramètre sur le port de votre base de données.</li>
<li><code>STATIC_ACCESS_KEY_ID</code> : définissez ce paramètre sur la clé d'accès de votre espace ou stockage d'objets.</li>
<li><code>STATIC_SECRET_KEY</code> : définissez ce paramètre sur la clé d'accès de votre espace ou stockage d'objets Secret.</li>
<li><code>STATIC_BUCKET_NAME</code> : définissez ce paramètre sur votre nom d'espace ou votre object storage bucket.</li>
<li><code>STATIC_ENDPOINT_URL</code> : définissez ce paramètre sur les URL applicables de vos espaces ou du point final du stockage des abjets, par exemple <code>https://<span class="highlight">space-name</span>.nyc3.digitaloceanspaces.com</code> si votre espace se trouve dans la région <code>nyc3</code>.</li>
</ul>

<p>Une fois que vous avez terminé vos modifications, enregistrez et fermez le fichier.</p>

<p>Au cours de la prochaine étape, nous allons exécuter un conteneur configuré localement et créer un schéma de base de données Nous allons également charger des actifs statiques, comme des feuilles de style ou des images, dans le stockage d'objets.</p>

<h2 id="Étape-2-création-du-schéma-de-la-base-de-données-et-chargement-d-39-actifs-dans-le-stockage-d-39-objets">Étape 2 - Création du schéma de la base de données et chargement d'actifs dans le stockage d'objets</h2>

<p>Une fois le conteneur créé et configuré, utilisez <code>docker run</code> pour remplacer le paramètre <code>CMD</code> dans le Dockerfile et créer le schéma de la base de données à l'aide des commandes <code>manage.py</code> et <code>manage.py migrate</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"
</li></ul></code></pre>
<p>Nous lançons le container d'images <code>polls:latest</code>, nous passons dans le fichier variable d'environnement que nous venons de modifier, et remplacons la commande Dockerfile par <code>sh -c "python manage.py makemigrations python manage.py image"</code>, qui créera le schéma de base de données défini par le code de l'application.</p>

<p>Si vous exécutez cette opération pour la première fois, vous devriez voir apparaître ce qui suit :</p>
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
<p>Cela indique que le schéma de base de données a été créé avec succès.</p>

<p>Si vous exécutez <code>migrate</code> une fois de plus, Django effectuera un no-op à moins que le schéma de base de données ait changé.</p>

<p>Ensuite, nous allons exécuter une autre instance du conteneur de l'application et utiliser un shell interactif à l'intérieur de celui-ci pour créer un utilisateur administratif pour le projet Django.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -i -t --env-file env polls sh
</li></ul></code></pre>
<p>Vous obtiendrez une invite shell à l'intérieur du conteneur en cours d'exécution que vous pouvez utiliser pour créer l'utilisateur Django :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python manage.py createsuperuser
</li></ul></code></pre>
<p>Entrez un nom d'utilisateur, une adresse email et un mot de passe pour votre utilisateur, et après avoir créé l'utilisateur, appuyez sur <code>CTRL+D</code> pour quitter le conteneur et le fermer.</p>

<p>Enfin, nous allons générer les fichiers statiques pour l'application et les télécharger sur l'espace DigitalOcean à l'aide de <code>collectstatic</code>. Notez que cela peut prendre un peu de temps.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py collectstatic --noinput"
</li></ul></code></pre>
<p>Une fois que ces fichiers sont générés et téléchargés, vous obtiendrez la sortie suivante.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>121 static files copied.
</code></pre>
<p>Nous pouvons maintenant exécuter l'application :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env -p 80:8000 polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[2019-10-17 21:23:36 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-10-17 21:23:36 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2019-10-17 21:23:36 +0000] [1] [INFO] Using worker: sync
[2019-10-17 21:23:36 +0000] [7] [INFO] Booting worker with pid: 7
[2019-10-17 21:23:36 +0000] [8] [INFO] Booting worker with pid: 8
[2019-10-17 21:23:36 +0000] [9] [INFO] Booting worker with pid: 9
</code></pre>
<p>Ici, nous exécutons la commande par défaut définie dans le Dockerfile <code>gunicorn --bind :8000 --workers 3 mysite.wsgi:application</code> et exposons le port de conteneur <code>8000</code> afin que le port <code>80</code> de votre machine locale soit mappé sur le port <code>8000</code> du conteneur <code>polls</code>.</p>

<p>Vous devriez maintenant pouvoir naviguez jusqu'à l'application <code>polls</code> à l'aide de votre navigateur web en tapant : <code>http://localhost</code>. Comme il n'y a pas de route définie pour le chemin d'accès <code>/</code> , vous obtiendrez probablement une erreur de recherche <code>404 Page Not Found</code>, qui est prévisible.</p>

<p>Naviguez sur <code>http://localhost/polls</code> pour voir l'interface de l'application de sondage :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface des applications de sondage"></p>

<p>Pour voir l'interface administrative, allez à <code>http://localhost/admin</code>. Vous devriez voir la fenêtre d'authentification de l'application de sondage :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Page Auth admin des sondages"></p>

<p>Entrez le nom d'utilisateur administratif et le mot de passe que vous avez créé avec la commande <code>createsuperuser</code>.</p>

<p>Après avoir été authentifié, vous pouvez accéder à l'interface administrative de l'application de sondage :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin_main.png" alt="Interface principale de l'administration de sondages"></p>

<p>Notez que les actifs statiques pour les applications d&rsquo;<code>administration</code> et <code>de sondage</code> sont livrées directement depuis le stockage d'objets. Pour confirmer ceci, consultez <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#testing-spaces-static-file-delivery">Testing Spaces Static File Delivery</a>.</p>

<p>Lorsque vous avez terminé d'explorer, appuyez sur <code>CTRL+C</code> dans la fenêtre de terminal en exécutant le conteneur de Docker pour terminer le conteneur.</p>

<p>Une fois que l'image Docker de l'application Django est testée, les actifs statiques chargés sur le stockage d'objets et le schéma de base de données configuré et fonctionnel avec votre application, vous êtes prêt à charger votre image de l'application Django sur un registre d'images comme Docker Hub.</p>

<h2 id="Étape-3-pousser-l-39-image-de-l-39-application-django-sur-docker-hub">Étape 3 - Pousser l'image de l'application Django sur Docker Hub</h2>

<p>Pour lancer votre application sur Kubernetes, votre image d'application doit être chargée sur un registre comme <a href="https://hub.docker.com/">Docker Hub</a>. Kubernetes ira extraire l'image de l'application de son référentiel, puis la déploiera sur votre cluster.</p>

<p>Vous pouvez utiliser un registre Docker privé, comme <a href="https://www.digitalocean.com/products/container-registry/">DigitalOcean Container Registry</a>, actuellement gratuit en Early Access ou un registre Docker public comme Docker Hub. Docker Hub vous permet également de créer des référentiels Docker privés. Un référentiel public permet à quiconque de voir et d'extraire les images du conteneur, tandis qu'un référentiel privé vous permet de restreindre l'accès à vous et aux membres de votre équipe.</p>

<p>Au cours de ce tutoriel, nous allons pousser l'image Django sur le référentiel public Docker Hub créé dans les conditions préalablement citées. Vous pouvez également pousser votre image sur un référentiel privé, mais l'extraction d'images à partir d'un référentiel privé n'est pas traité dans le cadre de cet article. Pour en savoir plus sur l'authentification de Kubernetes avec Docker Hub et l'extraction d'images privées, veuillez consulter la section <a href="https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/">Extraire une image d'un référentiel privé</a> dans les documents de Kubernetes.</p>

<p>Commencez par vous connecter à Docker Hub à partir de votre machine locale :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
</code></pre>
<p>Pour vous connecter, utilisez votre nom d'utilisateur et votre mot de passe Docker Hub.</p>

<p>L'image Django a actuellement la balise <code>polls:latest</code>. Pour la pousser sur votre référentiel Docker Hub, balisez à nouveau l'image en utilisant votre nom d'utilisateur Docker Hub et votre nom de référentiel :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag polls:latest <span class="highlight">your_dockerhub_username</span>/<span class="highlight">your_dockerhub_repo_name</span>:latest
</li></ul></code></pre>
<p>Poussez l'image sur le référentiel :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">sammy</span>/<span class="highlight">sammy-django</span>:latest
</li></ul></code></pre>
<p>Pour ce tutoriel, nous utilisons le nom d'utilisateur Docker Hub <strong>sammy</strong> et le nom de référentiel <strong>sammy-django</strong>. Vous devez remplacer ces valeurs par votre propre nom d'utilisateur Docker Hub et votre nom de référentiel.</p>

<p>Vous verrez quelques résultats se mettre à jour alors que les couches des images sont poussées sur Docker Hub.</p>

<p>Maintenant que Kubernetes peut accéder à votre image sur Docker Hub, vous pouvez commencer à la déployer dans votre cluster.</p>

<h2 id="Étape-4-configuration-de-configmap">Étape 4 - Configuration de ConfigMap</h2>

<p>Lorsque nous avons exécuté le conteneur Django localement, nous avons passé le fichier <code>env</code> dans <code>docker run</code> pour injecter des variables de configuration dans l'environnement d'exécution. Sur Kubernetes, les variables de configuration peuvent être injectées à l'aide de <a href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/">ConfigMaps</a> et <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Secrets</a>.</p>

<p>ConfigMaps permet de stocker des informations de configuration non confidentielles comme les paramètres de l'application. Secrets permet de stocker des informations sensibles comme les clés API et les identifiants de la base de données. Ils sont tous les deux injectés dans des conteneurs de manière similaire, mais Secrets dispose d'un contrôle d'accès et de fonctionnalités de sécurité supplémentaires comme <a href="https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/">encryption at rest</a>. Secrets stocke également les données dans base64, tandis que ConfigMaps stocke les données en texte clair.</p>

<p>Pour commencer, créez un répertoire que vous nommerez <code>yaml</code> dans lequel nous allons stocker nos manifestes Kubernetes. Naviguez dans le répertoire.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir yaml
</li><li class="line" data-prefix="$">cd
</li></ul></code></pre>
<p>Ouvrez un fichier appelé <code>polls-configmap.yaml</code> dans <code>nano</code> ou votre éditeur de texte préféré :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-configmap.yaml
</li></ul></code></pre>
<p>Collez&ndash;y le manifeste ConfigMap suivant :</p>
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
<p>Nous avons extrait la configuration non sensible du fichier <code>env</code> modifié à <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">l'étape 1</a> et l'avons collée dans un manifeste ConfigMap. L'objet ConfigMap se nomme <code>polls-config</code>. Copiez-y les mêmes valeurs que celles que vous avez saisies dans le fichier <code>env</code> à l'étape précédente.</p>

<p>Pour les besoins de test, laissez <code>DJANGO_ALLOWED_HOSTS</code> avec <code>*</code> pour désactiver le filtre configuré sur les en-têtes Host. Dans un environnement de production, vous devriez procéder à la même configuration sur le domaine de votre application.</p>

<p>Une fois que vous avez terminé de le modifier, enregistrez et fermez votre fichier.</p>

<p>Créez la ConfigMap dans votre cluster en utilisant <code>kubectl apply</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-configmap.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>configmap/polls-config created
</code></pre>
<p>Une fois la ConfigMap créée, nous allons créer le Secret que notre application utilisera à l'étape suivante.</p>

<h2 id="Étape-5-configuration-du-secret">Étape 5 - Configuration du secret</h2>

<p>Les valeurs de Secret doivent être <a href="https://en.wikipedia.org/wiki/Base64">base64-encoded</a>, ce qui signifie que la création d'objets Secret dans votre cluster exige un peu plus d'implication que la création de ConfigMaps. Vous pouvez répéter le processus décrit à l'étape précédente, en codant manuellement les valeurs Secret en baseb64 et en les collant dans un fichier de manifeste. Vous pouvez également les créer à l'aide d'un fichier variable d'environnement, <code>kubectl create</code>, et la balise <code>--from-env-file</code>, ce que nous ferons au cours de cette étape.</p>

<p>Nous utiliserons à nouveau le fichier <code>env</code> de <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">l'étape 1</a>, en supprimant les variables insérées dans la ConfigMap. Copiez le fichier <code>env</code> appelé <code>polls-secrets</code> dans le répertoire <code>yaml</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp ../env ./polls-secrets
</li></ul></code></pre>
<p>Modifiez le fichier dans votre éditeur de texte préféré :</p>
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
<p>Supprimez toutes les variables insérées dans le manifeste ConfigMap. Une fois que vous aurez terminé, vous devriez obtenir un résultat similaire à ce qui suit :</p>
<div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=<span class="highlight">your_secret_key</span>
DATABASE_NAME=polls
DATABASE_USERNAME=<span class="highlight">your_django_db_user</span>
DATABASE_PASSWORD=<span class="highlight">your_django_db_user_password</span>
DATABASE_HOST=<span class="highlight">your_db_host</span>
DATABASE_PORT=<span class="highlight">your_db_port</span>
STATIC_ACCESS_KEY_ID=<span class="highlight">your_space_access_key</span>
STATIC_SECRET_KEY=<span class="highlight">your_space_access_key_secret</span>
</code></pre>
<p>Veillez à utiliser les mêmes valeurs que celles utilisées à <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">l'étape 1</a>. Une fois que vous avez terminé, enregistrez et fermez le fichier.</p>

<p>Créez le Secret dans votre cluster en utilisant <code>kubectl create secret</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl create secret generic polls-secret --from-env-file=poll-secrets
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>secret/polls-secret created
</code></pre>
<p>Ici, nous créons un objet Secret appelé <code>polls-secret</code> dans lequel vous intégrerez le fichier secrets que nous venons de créer.</p>

<p>Vous pouvez inspecter le Secret en utilisant <code>kubectl describe</code>:</p>
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
<p>À ce stade, vous avez stocké la configuration de votre application dans votre cluster Kebernetes en utilisant des types d'objets Secret et ConfigMap. Nous sommes maintenant prêts à déployer l'application dans le cluster.</p>

<h2 id="Étape-6-déploiement-de-l-39-application-django-avec-un-déployment">Étape 6 - Déploiement de l'application Django avec un Déployment</h2>

<p>Au cours de cette étape, vous allez créer un Deployment pour votre application Django. Un Deployment est un <em>contrôleur</em> que vous pouvez utiliser pour gérer des applications apatrides dans votre cluster. Un contrôleur est une boucle de contrôle qui régule les charges de travail en les augmentant ou en les réduisant. Les contrôleurs redémarrent et suppriment les conteneurs défaillants.</p>

<p>Les Deployments contrôlent un ou plusieurs Pods, la plus petite unité déployable dans un cluster Kubernetes. Les Pods intègrent un ou plusieurs conteneurs. Pour en savoir plus sur les différents types de charges de travail que vous pouvez lancer, veuillez consulter <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes">Une introduction à Kubernetes</a>.</p>

<p>Commencez par ouvrir le fichier appelé <code>polls-deployment.yaml</code> dans votre éditeur de texte préféré :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-deployment.yaml
</li></ul></code></pre>
<p>Collez-y le manifeste de Deployment suivant :</p>
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
<p>Renseignez le nom de l'image du conteneur applicable, qui doit faire référence à l'image Django Polls que vous avez poussée sur Docker Hub à l&rsquo;<a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-2-%E2%80%94-creating-the-database-schema-and-uploading-assets-to-object-storage">étape 2</a>.</p>

<p>Ici, nous allons définir un Deployment Kubernetes appelé <code>polls-app</code> et l'étiqueter avec la paire de valeur <code>app: pools</code>. Nous spécifions que nous souhaitons exécuter deux répliques du Pod défini sous le champ <code>template</code>.</p>

<p>En utilisant <code>envFrom</code> avec <code>secretRef</code> et <code>configMapRef</code>, nous spécifions que toutes les données du Secret <code>polls-secret</code> et de la ConfigMap <code>polls-config</code> doivent être injectées dans les conteneurs sous forme de variables d'environnement. Les clés ConfigMap et Secret deviennent les noms des variables d'environnement.</p>

<p>Enfin, nous allons exposer <code>containerPort</code> <code>8000</code> et le nommer <code>gunicorn</code>.</p>

<p>Pour en savoir plus sur la configuration des Deployments de Kubernetes, veuillez consulter le document <a href="https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/">Deployments</a> dans la documentation de Kubernetes.</p>

<p>Une fois que vous avez terminé de le modifier, enregistrez et fermez votre fichier.</p>

<p>Créez le Deployment dans votre cluster en utilisant <code>kubectl apply -f</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-deployment.yaml
</li></ul></code></pre><pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">deployment.apps/polls-app created
</li></ul></code></pre>
<p>Vérifiez que le Deployment s'est correctement déployé en utilisant <code>kubectl get</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get deploy polls-app
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME        READY   UP-TO-DATE   AVAILABLE   AGE
polls-app   2/2     2            2           6m38s
</code></pre>
<p>Si vous rencontrez une erreur ou que quelque chose qui ne fonctionne pas correctement, vous pouvez utiliser <code>kubectl describe</code> pour inspecter le Deployment défaillant :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe deploy
</li></ul></code></pre>
<p>Vous pouvez inspecter les deux Pods en utilisant <code>kubectl get pod</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get pod
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                         READY   STATUS    RESTARTS   AGE
polls-app-847f8ccbf4-2stf7   1/1     Running   0          6m42s
polls-app-847f8ccbf4-tqpwm   1/1     Running   0          6m57s
</code></pre>
<p>Deux répliques de votre application Django sont maintenant opérationnelles dans le cluster. Pour accéder à l'application, vous devez créer un service Kubernetes, ce que nous ferons ensuite.</p>

<h2 id="Étape-7-autoriser-un-accès-externe-à-l-39-aide-d-39-un-service">Étape 7 - Autoriser un accès externe à l'aide d'un Service</h2>

<p>Au cours de cette étape, vous allez créer un Service pour votre application Django. Un Service Kubernetes est une abstraction qui vous permet d'exposer un ensemble de Pods en cours d'exécution en tant que service réseau. En utilisant un Service, vous pouvez créer un point final stable pour votre application qui ne change pas à mesure que les Pods périssent et sont recréés.</p>

<p>Il existe plusieurs types de Services, dont : les Services ClusterIP qui exposent le Service sur un IP interne de cluster, les NodePort Services qui exposent le Service sur chaque nœud au niveau d'un port statique appelé NodePort et les LoadBalancer Services qui intègrent un équilibreur de charge du trafic externe vers les Pods dans votre cluster (via NodePorts, ce qu'il crée automatiquement). Pour en savoir plus sur ces éléments, consultez le document <a href="https://kubernetes.io/docs/concepts/services-networking/service/">Service</a> dans la documentation de Kubernetes.</p>

<p>Pour notre configuration finale, nous allons utiliser un Service ClusterIP qui est exposé à l'aide d'un Ingress et du Controller Ingress configurés dans les conditions préalablement requises pour ce guide. Pour l'instant, pour vérifier que tout fonctionne correctement, nous allons créer un Service NodePort temporaire pour accéder à l'application Django.</p>

<p>Commencez par créer un fichier appelé <code>polls-svc.yaml</code> en utilisant votre éditeur de texte préféré :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Collez-y le manifeste de Service suivant :</p>
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
<p>Ici, nous créons un NodePort Service appelé <code>polls</code> et lui donnons l'étiquette <code>app: polls</code>. Nous sélectionnons ensuite les Pods de backend portant l'étiquette <code>app: polls</code> et ciblons leurs ports <code>8000</code>.</p>

<p>Une fois que vous avez terminé de le modifier, enregistrez et fermez votre fichier.</p>

<p>Déploiement du Service avec <code>kubectl apply</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls created
</code></pre>
<p>Confirmez que votre Service a été créé en utilisant <code>kubectl get svc</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
polls   NodePort   10.245.197.189   &lt;none&gt;        8000:32654/TCP   59s
</code></pre>
<p>Ce résultat affiche l'adresse IP interne du cluster de Service et NodePort (<code>32654</code>). Pour nous connecter au service, nous avons besoin de l'adresse IP externe de nos nœuds de cluster :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get node -o wide
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-7no0qd9e0-364fd   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.5    203.0.113.1   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fi   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.4    203.0.113.2    Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fv   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.3    203.0.113.3   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
</code></pre>
<p>Dans votre navigateur Web, consultez votre application de sondage en utilisant l'adresse IP externe de n'importe quel nœud et le NodePort. Compte tenu du résultat ci-dessus, l'URL de l'application devrait être : <code>http://<span class="highlight">203.0.113.1</span>:32654/polls</code>.</p>

<p>Vous devriez voir apparaître la même interface d'application de sondage que celle à laquelle vous avez accédé localement à l'étape 1 :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface des applications de sondage"></p>

<p>Vous pouvez répéter le même test en utilisant le chemin <code>/admin</code> : <code>http://<span class="highlight">203.0.113.1</span>:32654/admin</code>. Vous devriez voir apparaître la même interface d'administration qu'auparavant :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Page Auth admin des sondages"></p>

<p>À ce stade, vous avez déployé deux répliques du conteneur de l'application Django Polls en utilisant un Deployment. Vous avez également créé un terminal de réseau stable pour ces deux répliques et l'avez rendu accessible à l'extérieur à l'aide d'un Service NodePort.</p>

<p>La dernière étape de ce tutoriel consiste à sécuriser le trafic externe de votre application en utilisant HTTPS. Pour ce faire, nous allons utiliser le contrôleur Ingress <code>ingress-nginx</code> installé dans les conditions préalables requises et créer un objet Ingress pour acheminer le trafic externe vers le Service Kubernetes <code>polls</code>.</p>

<h2 id="Étape-8-configuration-du-https-en-utilisant-nginx-ingress-et-cert-manager">Étape 8 - Configuration du HTTPS en utilisant Nginx Ingress et cert-manager</h2>

<p>Les <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingresses</a> de Kubernetes vous permettent d'acheminer le trafic de manière flexible depuis votre cluster Kubernetes vers Services à l'intérieur de votre cluster. Ceci se fait en utilisant des objets Ingress qui définissent des règles pour acheminer le trafic HTTP et HTTPS aux Services Kubernetes et les Ingress <em>Controllers</em>, qui implémentent les règles en équilibrant le trafic de charge et en l'acheminant vers les Services du terminal applicables.</p>

<p>Dans les conditions préalablement requises, vous avez installé le contrôleur Ingress <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> et l'add-on d'automatisation des certificats TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a>. Vous avez également défini des ClusterIssuers de simulation et de production pour votre domaine en utilisant l'autorité de certification Let&rsquo;s Encrypt, et créé un Ingress pour tester l'émission de certificats et le cryptage TLS sur deux Services de backend factices. Avant de poursuivre avec cette étape, vous devez supprimer Ingress <code>echo-ingress</code> créée dans le <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">tutoriel préalable</a> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl delete ingress echo-ingress
</li></ul></code></pre>
<p>Si vous le voulez, vous pouvez également supprimer les Services et Deployments factices en utilisant <code>kubectl delete svc</code> et <code>kubectl delete deploy</code>, mais cela n'est pas essentiel pour terminer ce tutoriel.</p>

<p>Vous devriez également avoir créé un dossier <code>A</code> DNS avec <code><span class="highlight">your_domain.com</span></code> pointant sur l'adresse IP publique de l'équilibreur de charge Ingress. Si vous utilisez un équilibreur de charge DigitalOcean, vous pouvez trouver cette adresse IP dans la section <a href="https://cloud.digitalocean.com/networking/load_balancers">Load Balancer</a> du panneau de configuration. Si vous utilisez également DigitalOcean pour gérer les enregistrements DNS de votre domaine, consultez <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Comment gérer des enregistrements DNS</a> pour apprendre à créer des enregistrements <code>A</code></p>

<p>Si vous utilisez DigitalOcean Kubernetes, assurez-vous également de bien avoir implémenté le détour décrit à l&rsquo;<a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-(optional)">étape 5 de Comment configurer un Ingress Nginx avec Cert-Manager sur DigitalOcean Kubernetes</a>.</p>

<p>Une fois que vous disposez d'un enregistrement <code>A</code> pointant sur l'équilibreur de charge du contrôleur Ingress vous pouvez créer un Ingress pour <code><span class="highlight">your_domain.com</span></code> et le Service <code>polls</code>.</p>

<p>Ouvrez un fichier appelé <code>polls-ingress.yaml</code> en utilisant votre éditeur de texte préféré :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Collez-y le manifeste Ingress suivant :</p>
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
<p>Nous créons un objet Ingress appelé <code>polls-ingress</code> et nous l'annotons pour instruire le plan de contrôle d'utiliser le contrôleur Ingress ingress-nginx et le ClusterIssuer de simulation. Nous activons également TLS pour <code>your_domain.com</code> et stockons le certificat et la clé privée dans un secret appelé <code>polls-tls</code>. Enfin, nous définissons une règle pour acheminer le trafic de l'hôte <code>your_domain.com</code> vers le Service <code>polls</code> sur le port <code>8000</code>.</p>

<p>Une fois que vous avez terminé de le modifier, enregistrez et fermez votre fichier.</p>

<p>Créez l'Ingress dans votre cluster en utilisant <code>kubectl apply</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress created
</code></pre>
<p>Vous pouvez utiliser <code>kubectl describe</code> pour suivre l'état de l'Ingress que vous venez de créer :</p>
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
<p>Vous pouvez également exécuter un <code>describe</code> sur le certificat <code>polls-tls</code> afin de confirmer à nouveau que sa création est probante :</p>
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
<p>Cela confirme que le certificat TLS a bien été émis et que le chiffrement HTTPS est maintenant actif pour <code>your_domain.com</code>.</p>

<p>Étant donné que nous avons utilisé le ClusterIssuer de simulation, la plupart des navigateurs web ne feront pas confiance au faux certificat Let&rsquo;s Encrypt qu'il a émis, de sorte que la navigation sur <code>your_domain.com</code> vous renverra vers une page d'erreur.</p>

<p>Pour envoyer une requête de test, nous utiliserons <code>wget</code> à partir de la ligne de commande :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget -O - http://<span class="highlight">your_domain.com</span>/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
ERROR: cannot verify your_domain.com's certificate, issued by ‘CN=Fake LE Intermediate X1’:
  Unable to locally verify the issuer's authority.
To connect to your_domain.com insecurely, use `--no-check-certificate'.
</code></pre>
<p>Nous allons utiliser la balise <code>--no-check-certificate</code> suggérée pour contourner la validation du certificat :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget --no-check-certificate -q -O - http://your_domain.com/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>

&lt;link rel="stylesheet" type="text/css" href="https://your_space.nyc3.digitaloceanspaces.com/django-polls/static/polls/style.css"&gt;


    &lt;p&gt;No polls are available.&lt;/p&gt;
</code></pre>
<p>Le résultat ainsi obtenu affiche le HTML de la page d'interface <code>/polls</code>, confirmant également que la feuille de style est servie à partir du stockage d'objets.</p>

<p>Maintenant que vous avez réussi à tester la délivrance de certificats en utilisant le ClusterIssuer de simulation, vous pouvez modifier l'Ingress pour utiliser le ClusterIssuer de production.</p>

<p>Ouvrez <code>polls-ingress.yaml</code> pour l'éditer à nouveau :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Modifiez l'annotation <code>cluster-issuer</code></p>
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
<p>Lorsque vous avez terminé, enregistrez et fermez le fichier. Mettez à jour l'Ingress en utilisant <code>kubectl apply</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress configured
</code></pre>
<p>Vous pouvez utiliser <code>kubectl describe certificate polls-tls</code> et <code>kubectl describe ingress polls-ingress</code> pour suivre l'état de délivrance du certificat :</p>
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
<p>Le résultat ci-dessus confirme que le nouveau certificat de production a bien été émis et stocké avec succès dans le Secret <code>polls-tls</code>.</p>

<p>Naviguez vers <code>your_domain.com/polls</code> dans votre navigateur web pour confirmer que le cryptage HTTPS est activé et que tout fonctionne comme prévu. Vous devriez voir l'interface de l'application de sondage :</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface des applications de sondage"></p>

<p>Vérifiez que le cryptage HTTPS est actif dans votre navigateur web. Si vous utilisez Google Chrome, tout fonctionne correctement si vous atteignez la page ci-dessus sans aucune erreur. En outre, vous devriez voir un cadenas dans la barre d'URL. Cliquez sur le cadenas pour inspecter les détails du certificat Let&rsquo;s Encrypt.</p>

<p>Pour procéder à la tâche finale de nettoyage, vous pouvez facultativement commuter le type de Service <code>polls</code> de NodePort à Type ClusterIP interne uniquement.</p>

<p>Modifiez <code>polls-svc.yaml</code> en utilisant votre éditeur de texte :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Changez le <code>type</code> de <code>NodePort</code> à <code>ClusterIP</code>:</p>
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
<p>Une fois que vous avez terminé de le modifier, enregistrez et fermez votre fichier.</p>

<p>Déployez les changements en utilisant <code>kubectl apply</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml --force
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls configured
</code></pre>
<p>Confirmez que votre Service a bien été modifié en utilisant <code>kubectl get svc</code> :</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
polls   ClusterIP   10.245.203.186   &lt;none&gt;        8000/TCP   22s
</code></pre>
<p>Ce résultat montre que le type de Service est désormais configuré sur ClusterIP. La seule façon d'y accéder consiste à le faire via votre domaine et l'Ingress créé à cette étape.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Au cours de ce tutoriel, vous avez déployé une application Django évolutive et sécurisée HTTPS dans un cluster Kubernetes. Le contenu statique est directement extrait du stockage d'objets. Le nombre de Pods en cours d'exécution peut rapidement être augmenté ou diminué en utilisant le champ <code>replicas</code> dans le manifeste de Deployment <code>polls-app</code>.</p>

<p>Si vous utilisez un espace DigitalOcean, vous pouvez également activer la livraison d'actifs statiques via un réseau de distribution de contenu et créer un sous-domaine personnalisé pour votre espace. Veuillez consulter la section <strong>Activer CDN</strong> du document <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#enabling-cdn-optional">Comment configurer une application Django évolutive avec des bases de données et des espaces gérés par DigitalOcean</a> pour en savoir plus.</p>

<p>Pour découvrir le reste de la série, veuillez consulter notre <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">page sur la série Du conteneur aux Kubernetes avec Django</a>.</p>
