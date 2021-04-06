---
title : "Развертывание масштабируемого и защищенного приложения Django с помощью Kubernetes"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes-ru
image: "https://sergio.afanou.com/assets/images/image-midres-43.jpg"
---

<h3 id="Введение">Введение</h3>

<p>В этом учебном модуле мы развернем контейнеризованное приложение Django polls в кластере Kubernetes.</p>

<p><a href="https://www.djangoproject.com/">Django</a> — это мощная веб-структура, позволяющая быстро развернуть ваше приложение Python с нуля. Она включает ряд удобных функций, в том числе <a href="https://en.wikipedia.org/wiki/Object-relational_mapping">реляционную карту объектов</a>, аутентификацию пользователей и настраиваемый административный интерфейс для вашего приложения. Также она включает <a href="https://docs.djangoproject.com/en/2.1/topics/cache/">систему кэширования</a> и помогает проектировать оптимизированные приложения с помощью <a href="https://docs.djangoproject.com/en/2.1/topics/http/urls/">диспетчера URL</a> и <a href="https://docs.djangoproject.com/en/2.1/topics/templates/">системы шаблонов</a>.</p>

<p>В учебном модуле <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Создание приложения Django и Gunicorn с помощью Docker</a> мы изменили <a href="https://docs.djangoproject.com/en/3.1/intro/tutorial01/">приложение Polls из учебного модуля Django</a> согласно методологии <a href="https://12factor.net/">Двенадцать факторов</a>, предназначенной для построения масштабируемых веб-приложений, оптимизированных для работы в облаке. Для масштабирования и защиты контейнера использовался обратный прокси-сервер Nginx и подписанный Let&rsquo;s Encrypt сертификат TLS (см. описание в учебном модуле <a href="https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt">Масштабирование и защита приложения Django с помощью Docker, Nginx и Let&rsquo;s Encrypt</a>). В этом последнем учебном модуле из серии <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">От контейнеров к Kubernetes с Django</a> мы покажем, как развернуть модернизированное приложение Django polls в кластере Kubernetes.</p>

<p><a href="https://kubernetes.io/">Kubernetes</a> — мощная система оркестрации контейнеров с открытым исходным кодом, помогающая автоматизировать развертывание, масштабирование и управление контейнеризованными приложениями. Такие объекты Kubernetes как ConfigMaps и Secrets позволяют централизовать конфигурацию и отсоединить ее от контейнеров, а такие контроллеры как Deployments автоматически перезагружают неработающие контейнеры и позволяют быстро масштабировать реплики контейнеров. Шифрование TLS активируется с помощью объекта Ingress и контроллера <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> с открытым исходным кодом. Надстройка Kubernetes <a href="https://github.com/jetstack/cert-manager">cert-manager</a> проверяет и выпускает сертификаты, используя бесплатный центр сертификации <a href="https://letsencrypt.org/">Let&rsquo;s Encrypt</a>.</p>

<h2 id="Предварительные-требования">Предварительные требования</h2>

<p>Для данного обучающего модуля вам потребуется следующее:</p>

<ul>
<li>Кластер Kubernetes версии 1.15 или выше с включенным <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">контролем доступа на основе ролей</a> (RBAC). В нашем примере будет использоваться кластер <a href="https://www.digitalocean.com/products/kubernetes/">DigitalOcean Kubernetes</a>, но вы можете создать кластер с помощью <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04">другого метода</a>.</li>
<li>Инструмент командной строки <code>kubectl</code>, установленный на локальном компьютере и настроенный для подключения к вашему кластеру. Дополнительную информацию об установке <code>kubectl</code> можно найти в <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/">официальной документации</a>. Если вы используете кластер DigitalOcean Kubernetes, ознакомьтесь с руководством <a href="https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/">Подключение к кластеру DigitalOcean Kubernetes</a>, чтобы узнать, как подключиться к кластеру с помощью <code>kubectl</code>.</li>
<li>Зарегистрированное доменное имя. В этом учебном модуле мы будем использовать имя <code>your_domain.com</code>. Вы можете получить бесплатный домен на <a href="http://www.freenom.com/en/index.html">Freenom</a> или зарегистрировать доменное имя по вашему выбору.</li>
<li>Контроллер <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> и диспетчер сертификатов TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a> должны быть установлены в вашем кластере и настроены на использование сертификатов TLS. Чтобы узнать, как установить и настроить Ingress с помощью cert-manager, воспользуйтесь руководством <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">Настройка Nginx Ingress с помощью Cert-Manager в кластере DigitalOcean Kubernetes</a>.</li>
<li>Запись DNS <code>A</code> для <code>your_domain.com</code>, указывающая на публичный IP-адрес балансировщика нагрузки Ingress. Если вы используете DigitalOcean для управления записями DNS вашего домена, руководство <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Управление записями DNS</a> поможет вам научиться создавать записи класса <code>A</code>.</li>
<li>Хранилище объектов S3, например <a href="https://www.digitalocean.com/products/spaces/">пространство DigitalOcean</a>, для хранения статических файлов проекта Django и набор ключей доступа к этому пространству. Чтобы узнать, как создать пространство, ознакомьтесь с документом <a href="https://www.digitalocean.com/docs/spaces/how-to/create/">Как создавать пространства</a>. Чтобы узнать, как создать ключи доступа, ознакомьтесь со статьей <a href="https://www.digitalocean.com/docs/spaces/how-to/administrative-access/#access-keys">Предоставление доступа к пространству с помощью ключей доступа</a>. Внеся незначительные изменения, вы можете использовать любой сервис хранения, который поддерживает плагин <a href="https://django-storages.readthedocs.io/en/latest/">django-storages</a>.</li>
<li>Экземпляр сервера PostgreSQL, база данных и пользователь для приложения Django. Внеся незначительные изменения, вы можете использовать любую базу данных, которую <a href="https://docs.djangoproject.com/en/2.2/ref/databases/">поддерживает Django</a>.

<ul>
<li>База данных PostgreSQL должна называться <strong>polls</strong> (можно использовать любое другое запоминающееся имя для ввода в файлы конфигурации ниже). В качестве имени пользователя базы данных Django в данном учебном модуле будет использоваться <strong>sammy</strong>. Инструкции, как это сделать, см. в шаге 1 руководства <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-1-%E2%80%94-creating-the-postgresql-database-and-user">Создание приложения Django и Gunicorn с помощью Docker</a>. Эти действия следует выполнять на локальном компьютере.</li>
<li>В этом обучающем модуле используется <a href="https://www.digitalocean.com/products/managed-databases/">управляемый кластер PostgreSQL</a> от DigitalOcean. Чтобы узнать, как создать кластер, ознакомьтесь с <a href="https://www.digitalocean.com/docs/databases/how-to/clusters/create/">документацией по управляемым базам данных</a> от DigitalOcean.</li>
<li>Также вы можете установить и запустить свой собственный экземпляр PostgreSQL. Инструкции по установке и администрированию PostgreSQL на сервере Ubuntu см. в руководстве <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04">Установка и использование PostgreSQL на Ubuntu 18.04</a>.</li>
</ul></li>
<li>Учетная запись <a href="https://hub.docker.com/">Docker Hub</a> и публичный репозиторий. Более подробную информацию по их созданию можно найти в разделе <a href="https://docs.docker.com/docker-hub/repos/">Repositories</a> (Репозитории) в документации по Docker.</li>
<li>Система Docker, установленная на локальном компьютере. Более подробную информацию можно найти в руководстве <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04">Установка и использование Docker в Ubuntu 18.04</a>.</li>
</ul>

<p>Проверив наличие этих компонентов, вы можете начать прохождение этого обучающего модуля.</p>

<h2 id="Шаг-1-—-Клонирование-и-настройка-приложения">Шаг 1 — Клонирование и настройка приложения</h2>

<p>На этом шаге мы клонируем код приложения с GitHub и настроим такие параметры, как учетные данные БД и ключи хранения объектов.</p>

<p>Код приложения и файл Dockerfile можно найти в ветви <code>polls-docker</code> <a href="https://github.com/do-community/django-polls/tree/polls-docker">репозитория Django Tutorial Polls App на GitHub</a>. Этот репозиторий содержит код <a href="https://docs.djangoproject.com/en/3.0/intro/">приложения Polls</a>, используемого в документации по Django в качестве образца, на примере которого показывается, как создать приложение для опросов с нуля.</p>

<p>Ветвь <code>polls-docker</code> содержит размещенную в контейнере Docker версию приложения Polls. Более подробную информацию об изменениях, внесенных в приложение Polls для эффективной работы в контейнеризированной среде, можно найти в руководстве <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Создание приложения Django и Gunicorn с помощью Docker</a>.</p>

<p>Для начала используйте <code>git</code>, чтобы клонировать ветвь <code>polls-docker</code> <a href="https://github.com/do-community/django-polls">репозитория Django Tutorial Polls App на GitHub</a> на локальном компьютере:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
</li></ul></code></pre>
<p>Перейдите в каталог <code>django-polls</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd django-polls
</li></ul></code></pre>
<p>В этом каталоге содержится код Python приложения Django, файл <code>Dockerfile</code>, который Docker будет использовать для построения образа контейнера, а также файл <code>env</code>, содержащий список переменных среды для передачи в рабочую среду контейнера. Проверьте файл <code>Dockerfile</code>:</p>
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
<p>В качестве базы в файле Dockerfile используется официальный <a href="https://hub.docker.com/_/python">образ Docker</a> Python 3.7.4, который устанавливает требуемые пакеты Python для Django и Gunicorn в соответствии с файлом <code>django-polls/requirements.txt</code>. Затем он удаляет некоторые ненужные файлы сборки, копирует код приложения в образ и устанавливает параметр выполнения <code>PATH</code>. И в заключение заявляет, что порт <code>8000</code> будет использоваться для принятия входящих подключений контейнера, и запускает <code>gunicorn</code> с тремя рабочими элементами, прослушивающими порт <code>8000</code>.</p>

<p>Дополнительную информацию о каждом этапе в Dockerfile см. в шаге 6 руководства <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-6-%E2%80%94-writing-the-application-dockerfile">Создание приложения Django и Gunicorn с помощью Docker</a>.</p>

<p>Теперь создайте образ с помощью <code>docker build</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker build -t polls .
</li></ul></code></pre>
<p>Назовем образ <code>polls</code>, используя флаг <code>-t</code>, и передадим в текущий каталог как <em>контекст сборки</em> набор файлов для справки при построении образа.</p>

<p>После того как Docker создаст и отметит образ, перечислите доступные образы, используя <code>docker images</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker images
</li></ul></code></pre>
<p>Вы должны увидеть образ <code>polls</code>:</p>
<pre class="code-pre "><code>OutputREPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
polls               latest              80ec4f33aae1        2 weeks ago         197MB
python              3.7.4-alpine3.10    f309434dea3a        8 months ago        98.7MB
</code></pre>
<p>Перед запуском контейнера Django нам нужно настроить его рабочую среду с помощью файла <code>env</code>, присутствующего в текущем каталоге. Этот файл передается в команду <code>docker run</code>, которая используется для запуска контейнера, после чего Docker внедрит настроенные переменные среды в рабочую среду контейнера.</p>

<p>Откройте файл <code>env</code> с помощью <code>nano</code> или своего любимого редактора:</p>
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
<p>Заполните недостающие значения для следующих ключей:</p>

<ul>
<li><code>DJANGO_SECRET_KEY</code>: задает уникальное, непрогнозируемое значение, как описано в <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#secret-key">документации Django</a>. Один метод генерирования этого ключа представлен в разделе <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Изменение настроек приложения</a> руководства <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Масштабируемое приложение Django</a>.</li>
<li><code>DJANGO_ALLOWED_HOSTS</code>: эта переменная защищает приложение и предотвращает атаки через заголовки хоста HTTP. С целью тестирования установите <code>*</code> как подстановочный символ, подходящий для всех хостов. В производственной среде следует использовать значение <code>your_domain.com</code>. Дополнительную информацию об этой настройке Django см. в разделе <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#allowed-hosts">Core Settings</a> (Базовые настройки) документации Django.</li>
<li><code>DATABASE_USERNAME</code>: задает пользователя базы данных PostgreSQL, созданного на предварительных этапах.</li>
<li><code>DATABASE_NAME</code>: задает <code>polls</code> или имя базы данных, созданной на предварительных этапах.</li>
<li><code>DATABASE_PASSWORD</code>: задает пароль пользователя базы данных PostgreSQL, созданного на предварительных этапах.</li>
<li><code>DATABASE_HOST</code>: задает имя хоста базы данных.</li>
<li><code>DATABASE_PORT</code>: укажите порт вашей базы данных.</li>
<li><code>STATIC_ACCESS_KEY_ID</code>: укажите ключ доступа своего пространства или хранилища объектов.</li>
<li><code>STATIC_SECRET_KEY</code>: укажите секретный ключ своего пространства или хранилища объектов.</li>
<li><code>STATIC_BUCKET_NAME</code>: укажите имя своего пространства или корзину хранилища объектов.</li>
<li><code>STATIC_ENDPOINT_URL</code>: укажите URL соответствующего пространства или конечного пункта хранилища объектов, например <code>https://<span class="highlight">your_space_name</span>.nyc3.digitaloceanspaces.com</code>, если ваше пространство находится в области <code>nyc3</code>.</li>
</ul>

<p>После завершения редактирования сохраните и закройте файл.</p>

<p>На следующем шаге мы запустим настроенный контейнер в локальной среде и создадим схему базы данных. Также мы выгрузим в хранилище объектов статические ресурсы, такие как таблицы стилей и изображения.</p>

<h2 id="Шаг-2-—-Создание-схемы-базы-данных-и-выгрузка-ресурсов-в-хранилище-объектов">Шаг 2 — Создание схемы базы данных и выгрузка ресурсов в хранилище объектов</h2>

<p>После построения и настройки контейнера используйте <code>docker run</code>, чтобы заменить заданную команду <code>CMD</code> в файле Dockerfile и создайте схему базы данных, используя команды <code>manage.py makemigrations</code> и <code>manage.py migrate</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"
</li></ul></code></pre>
<p>Мы запускаем образ контейнера <code>polls:latest</code>, передаем в только что измененный файл переменной среды и переопределяем команду Dockerfile с помощью <code>sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"</code>, которая создаст схему базы данных, определяемую кодом приложения.</p>

<p>Если запускаете эти команды впервые, вы должны увидеть следующее:</p>
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
<p>Это означает, что схема базы данных успешно создана.</p>

<p>При последующем запуске команды <code>migrate</code> Django не будет выполнять никаких операций, если схема базы данных не изменилась.</p>

<p>Затем мы запустим еще один экземпляр контейнера приложения и используем внутри него интерактивную оболочку для создания административного пользователя проекта Django.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -i -t --env-file env polls sh
</li></ul></code></pre>
<p>Вы получите строку оболочки внутри работающего контейнера, которую сможете использовать для создания пользователя Django:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python manage.py createsuperuser
</li></ul></code></pre>
<p>Введите имя пользователя, адрес электронной почты и пароль для пользователя, а после создания пользователя нажмите <code>CTRL+D</code> для выхода из контейнера и его удаления.</p>

<p>В заключение мы создадим статические файлы приложения и загрузим их в пространство DigitalOcean с помощью <code>collectstatic</code>. Обратите внимание, что для завершения процесса может потребоваться время.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py collectstatic --noinput"
</li></ul></code></pre>
<p>После создания и загрузки файлов вы получите следующий вывод.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>121 static files copied.
</code></pre>
<p>Теперь мы можем запустить приложение:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env -p 80:8000 polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[2019-10-17 21:23:36 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-10-17 21:23:36 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2019-10-17 21:23:36 +0000] [1] [INFO] Using worker: sync
[2019-10-17 21:23:36 +0000] [7] [INFO] Booting worker with pid: 7
[2019-10-17 21:23:36 +0000] [8] [INFO] Booting worker with pid: 8
[2019-10-17 21:23:36 +0000] [9] [INFO] Booting worker with pid: 9
</code></pre>
<p>Мы запустим определенную в Dockerfile команду по умолчанию <code>gunicorn --bind :8000 --workers 3 mysite.wsgi:application</code> и откроем порт контейнера <code>8000</code>, чтобы установить соответствие порта <code>80</code> на локальном компьютере и порта <code>8000</code> контейнера <code>polls</code>.</p>

<p>Теперь у вас должна появиться возможность открыть приложение <code>polls</code> в браузере, для чего нужно ввести <code>http://localhost</code> в адресную строку. Поскольку маршрут для пути <code>/</code> не определен, скорее всего, вы получите ошибку <code>404 Страница не найдена</code>.</p>

<p>Введите в адресную строку <code>http://localhost/polls</code>, чтобы увидеть интерфейс приложения Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Интерфейс приложения «Опросы»"></p>

<p>Чтобы открыть интерфейс администрирования, введите адрес <code>http://localhost/admin</code>. Вы должны увидеть окно аутентификации администратора приложения «Опросы»:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Страница аутентификации администратора приложения"></p>

<p>Введите имя администратора и пароль, которые вы создали с помощью команды <code>createsuperuser</code>.</p>

<p>После аутентификации вы сможете получить доступ к административному интерфейсу приложения «Опросы»:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin_main.png" alt="Основной интерфейс администратора приложения"></p>

<p>Обратите внимание, что статические активы приложений <code>admin</code> и <code>polls</code> поступают напрямую из хранилища объекта. Чтобы убедиться в этом, ознакомьтесь с материалами <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#testing-spaces-static-file-delivery">Тестирование доставки статических файлов пространства</a>.</p>

<p>После завершения изучения данных нажмите <code>CTRL+C</code> в окне терминала, где запущен контейнер Docker, чтобы удалить контейнер.</p>

<p>Когда образ Docker приложения Django будет протестирован, статичные ресурсы будут выгружены в хранилище объектов, а схема базы данных будет настроена и готова к выгрузке образа приложения Django в реестр образов, например в Docker Hub.</p>

<h2 id="Шаг-3-—-Размещение-образа-приложения-django-в-docker-hub">Шаг 3 — Размещение образа приложения Django в Docker Hub</h2>

<p>Чтобы развернуть приложение в Kubernetes, необходимо будет выгрузить образ приложения в реестр, например, в <a href="https://hub.docker.com/">Docker Hub</a>. Kubernetes извлечет образ приложения из репозитория, а затем развернет его в кластере.</p>

<p>Вы можете использовать частный реестр Docker, например, реестр контейнеров <a href="https://www.digitalocean.com/products/container-registry/">DigitalOcean Container Registry</a>, предварительная версия которого в настоящее время предоставляется бесплатно, или публичный реестр Docker, такой как Docker Hub. Docker Hub также позволяет создавать частные репозитории Docker. Публичный репозиторий позволяет любому пользователю видеть и извлекать образы контейнеров, а в частном репозитории доступ имеется только у вас и у членов вашей команды.</p>

<p>В этом учебном модуле мы разместим образ Django в публичном репозитории Docker Hub, созданном на этапе предварительных требований. Также вы можете перенести образ в частный репозиторий, однако извлечение образов из частного репозитория в этой статье не описывается. Чтобы получить более подробную информацию об аутентификации Kubernetes с Docker Hub и извлечении образов из частного репозитория, ознакомьтесь со статьей <a href="https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/">Pull an Image from a Private Registry</a> (Извлечение образа из частного реестра) в документации по Kubernetes.</p>

<p>Для начала выполните вход в Docker Hub на локальном компьютере:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
</code></pre>
<p>Введите имя пользователя и пароль Docker Hub для входа в систему.</p>

<p>Образ Django сейчас помечен тегом <code>polls:latest</code>. Чтобы поместить его в ваш репозиторий Docker Hub, измените теги образа, указав свое имя пользователя Docker Hub и имя репозитория:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag polls:latest <span class="highlight">your_dockerhub_username</span>/<span class="highlight">your_dockerhub_repo_name</span>:latest
</li></ul></code></pre>
<p>Разместите образ в репозитории:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">sammy</span>/<span class="highlight">sammy-django</span>:latest
</li></ul></code></pre>
<p>В этом учебном модуле мы используем имя пользователя Docker Hub <strong>sammy</strong> и имя репозитория <strong>sammy-django</strong>. Вам следует заменить эти значения своим именем пользователя Docker Hub и именем репозитория.</p>

<p>По мере размещения слоев образа в Docker Hub вы будете видеть на экране определенные сообщения.</p>

<p>Теперь ваш образ доступен Kubernetes в репозитории Docker Hub, и вы можете начать его развертывание в своем кластере.</p>

<h2 id="Шаг-4-—-Настройка-configmap">Шаг 4 — Настройка ConfigMap</h2>

<p>При локальном запуске контейнера Django мы передали файл <code>env</code> в <code>docker run</code> для вставки переменных конфигурации в среду исполнения. Для вставки переменных конфигурации в Kubernetes используются элементы <a href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/">ConfigMap</a> и <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Secret</a>.</p>

<p>Элементы ConfigMap следует использовать для сохранения неконфиденциальной информации о конфигурации, такой как параметры приложения, а элементы Secret следует использовать для хранения важной информации, такой как ключи API и учетные данные для входа в базу данных. Они вставляются в контейнеры примерно одинаково, но у элементов Secret имеются дополнительные функции контроля доступа и безопасности, такие как <a href="https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/">шифрование в состоянии покоя</a>. Элементы Secret хранят данные в формате base64, а элементы ConfigMap хранят данные в формате обычного текста.</p>

<p>Для начала необходимо создать каталог <code>yaml</code>, где мы будем хранить наши манифесты Kubernetes. Перейдите в каталог.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir yaml
</li><li class="line" data-prefix="$">cd
</li></ul></code></pre>
<p>Откройте файл <code>polls-configmap.yaml</code> в <code>nano</code> или в другом предпочитаемом текстовом редакторе:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-configmap.yaml
</li></ul></code></pre>
<p>Вставьте в него следующий манифест ConfigMap:</p>
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
<p>Мы извлекли неконфиденциальные данные конфигурации из файла <code>env</code>, измененного на <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">шаге 1</a>, и передали их в манифест ConfigMap. Объект ConfigMap носит имя <code>polls-config</code>. Скопируйте те же самые значения, введенные в файл <code>env</code> на предыдущем шаге.</p>

<p>Для тестирования оставьте для <code>DJANGO_ALLOWED_HOSTS</code> значение <code>*</code>, чтобы отключить фильтрацию на основе заголовков хоста. В производственной среде здесь следует указать домен вашего приложения.</p>

<p>Когда вы завершите редактирование файла, сохраните и закройте его.</p>

<p>Создайте в своем кластере элемент ConfigMap, используя <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-configmap.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>configmap/polls-config created
</code></pre>
<p>Мы создали элемент ConfigMap, а на следующем шаге мы создадим элемент Secret, который будет использоваться нашим приложением.</p>

<h2 id="Шаг-5-—-Настройка-элемента-secret">Шаг 5 — Настройка элемента Secret</h2>

<p>Значения Secret должны иметь <a href="https://en.wikipedia.org/wiki/Base64">кодировку base64</a>, то есть создавать объекты Secret в кластере немного сложнее, чем объекты ConfigMaps. Вы можете повторить описанную на предыдущем шаге процедуру, выполнив кодирование значений Secret в формате base64 вручную и вставив их в файл манифеста. Также вы можете создавать их, используя файл переменных среды, <code>kubectl create</code> и флаг <code>--from-env-file</code>, что мы и сделаем на этом шаге.</p>

<p>Мы снова используем файл <code>env</code> из <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">шага 1</a>, удалив переменные, вставленные в ConfigMap. Создайте копию файла <code>env</code> с именем <code>polls-secrets</code> в каталоге <code>yaml</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp ../env ./polls-secrets
</li></ul></code></pre>
<p>Отредактируйте файл в предпочитаемом редакторе:</p>
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
<p>Удалите все переменные, вставленные в манифест ConfigMap. Когда вы закончите, содержимое файла должно выглядеть следующим образом:</p>
<div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=<span class="highlight">your_secret_key</span>
DATABASE_NAME=polls
DATABASE_USERNAME=<span class="highlight">your_django_db_user</span>
DATABASE_PASSWORD=<span class="highlight">your_django_db_user_password</span>
DATABASE_HOST=<span class="highlight">your_db_host</span>
DATABASE_PORT=<span class="highlight">your_db_port</span>
STATIC_ACCESS_KEY_ID=<span class="highlight">your_space_access_key</span>
STATIC_SECRET_KEY=<span class="highlight">your_space_access_key_secret</span>
</code></pre>
<p>Обязательно используйте те же значения, что и на <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">шаге 1</a>. Закончив, сохраните и закройте файл.</p>

<p>Создайте в кластере объект Secret, используя <code>kubectl create secret</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl create secret generic polls-secret --from-env-file=poll-secrets
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>secret/polls-secret created
</code></pre>
<p>Здесь мы создадим объект Secret с именем <code>polls-secret</code> и передадим в него созданный нами файл secrets.</p>

<p>Вы можете проинспектировать объект Secret, используя <code>kubectl describe</code>:</p>
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
<p>Мы сохранили конфигурацию вашего приложения в кластере Kubernetes, используя типы объектов Secret и ConfigMap. Мы готовы развернуть приложение в кластере.</p>

<h2 id="Шаг-6-—-Развертывание-приложения-django-с-помощью-контроллера-deployment">Шаг 6 — Развертывание приложения Django с помощью контроллера Deployment</h2>

<p>На этом шаге мы создадим Deployment для вашего приложения Django. Kubernetes Deployment — <em>контроллер</em>, который можно использовать для управления приложениями без состояния в вашем кластере. Контроллер — это контур управления, регулирующий рабочие задачи посредством вертикального масштабирования. Контроллеры также перезапускают и очищают неисправные контейнеры.</p>

<p>Контроллеры Deployment контролируют один или несколько подов. Под — это наименьшая развертываемая единица в кластере Kubernetes. Поды содержат один или несколько контейнеров. Чтобы узнать о различных типах рабочих задач, ознакомьтесь с учебным модулем <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes">Введение в Kubernetes</a>.</p>

<p>Для начала откройте файл <code>polls-deployment.yaml</code> в предпочитаемом текстовом редакторе:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-deployment.yaml
</li></ul></code></pre>
<p>Вставьте в него следующий манифест Deployment:</p>
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
<p>Введите соответствующее имя образа контейнера, ссылаясь на образ Django Polls, который вы разместили в Docker Hub на <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-2-%E2%80%94-creating-the-database-schema-and-uploading-assets-to-object-storage">шаге 2</a>.</p>

<p>Здесь мы определяем контроллер Kubernetes Deployment с именем <code>polls-app</code> и присваиваем ему пару ключ-значение <code>app: polls</code>. Мы указываем, что хотим запустить две реплики пода, определенного под полем <code>template</code>.</p>

<p>Используя <code>envFrom</code> с <code>secretRef</code> и <code>configMapRef</code>, мы указываем, что все данные из объектов <code>polls-secret</code> и <code>polls-config</code> следует вставить в контейнеры как переменные среды. Ключи ConfigMap и Secret становятся именами переменных среды.</p>

<p>В заключение мы откроем порт <code>containerPort</code> <code>8000</code> и назовем его <code>gunicorn</code>.</p>

<p>Чтобы узнать больше о настройке контроллеров Kubernetes Deployment, ознакомьтесь с разделом <a href="https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/">Deployments</a> в документации по Kubernetes.</p>

<p>Когда вы завершите редактирование файла, сохраните и закройте его.</p>

<p>Создайте в кластере контроллер Deployment, используя <code>kubectl apply -f</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-deployment.yaml
</li></ul></code></pre><pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">deployment.apps/polls-app created
</li></ul></code></pre>
<p>Убедитесь, что контроллер Deployment развернут правильно, используя <code>kubectl get</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get deploy polls-app
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME        READY   UP-TO-DATE   AVAILABLE   AGE
polls-app   2/2     2            2           6m38s
</code></pre>
<p>Если вы столкнетесь с ошибкой или что-то не будет работать, вы можете использовать <code>kubectl describe</code> для проверки неработающего контроллера Deployment:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe deploy
</li></ul></code></pre>
<p>Чтобы проинспектировать два пода, используйте <code>kubectl get pod</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get pod
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                         READY   STATUS    RESTARTS   AGE
polls-app-847f8ccbf4-2stf7   1/1     Running   0          6m42s
polls-app-847f8ccbf4-tqpwm   1/1     Running   0          6m57s
</code></pre>
<p>В кластеры запущены и работают две реплики вашего приложения Django. Чтобы получить доступ к приложению, вам нужно создать Kubernetes Service, и мы сделаем это на следующем шаге.</p>

<h2 id="Шаг-7-—-Предоставление-внешнего-доступа-с-использованием-service">Шаг 7 — Предоставление внешнего доступа с использованием Service</h2>

<p>На этом шаге мы создаем Service для нашего приложения Django. Kubernetes Service — это абстракция, позволяющая предоставить доступ к набору работающих подов как к сетевой службе. Используя Service, вы можете создать для вашего приложения стабильный конечный пункт, который не будет изменяться по мере уничтожения и воссоздания подов.</p>

<p>Существует несколько типов служб Service, в том числе службы ClusterIP, открывающие доступ к Service через внутренний IP-адрес кластера, службы NodePort, открывающие доступ к Service на каждом узле статического порта NodePort, и службы LoadBalancer, предоставляющие облачный балансировщик нагрузки для управления внешним трафиком подов вашего кластера (через порты NodePort, которые он создает автоматически). Чтобы узнать больше об этом, ознакомьтесь с разделом <a href="https://kubernetes.io/docs/concepts/services-networking/service/">Service</a> в документации по Kubernetes.</p>

<p>На заключительном шаге настройки мы используем службу ClusterIP Service, доступ к которой открыт через Ingress и контроллер Ingress, настроенный на этапе подготовки к этому учебному модулю. Сейчас мы убедимся, что все элементы работают корректно. Для этого мы создадим временную службу NodePort Service для доступа к приложению Django.</p>

<p>Для начала создайте файл с именем <code>polls-svc.yaml</code> в предпочитаемом редакторе:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Вставьте в него следующий манифест Service:</p>
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
<p>Здесь мы создаем службу NodePort под названием <code>polls</code> и присваиваем ей ярлык <code>app: polls</code>. Мы выберем поды серверной части с ярлыком <code>app: polls</code> и установим в качестве цели порты <code>8000</code>.</p>

<p>Когда вы завершите редактирование файла, сохраните и закройте его.</p>

<p>Разверните Service с помощью команды <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls created
</code></pre>
<p>Убедитесь, что служба была создана с использованием <code>kubectl get svc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
polls   NodePort   10.245.197.189   &lt;none&gt;        8000:32654/TCP   59s
</code></pre>
<p>Этот вывод показывает внутренний IP-адрес кластера службы и номер порта NodePort (<code>32654</code>). Чтобы подключиться к службе, нам потребуется внешний IP-адрес для нашего узла кластера:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get node -o wide
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-7no0qd9e0-364fd   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.5    203.0.113.1   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fi   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.4    203.0.113.2    Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fv   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.3    203.0.113.3   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
</code></pre>
<p>Откройте в браузере приложение Polls, используя внешний IP-адрес и порт NodePort любого узла. С учетом приведенного выше вывода URL приложения будет выглядеть так: <code>http://<span class="highlight">203.0.113.1</span>:32654/polls</code>.</p>

<p>Вы должны увидеть тот же интерфейс приложения Polls, который вы открыли локально на шаге 1:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Интерфейс приложения «Опросы»"></p>

<p>Вы можете повторить ту же проверку, используя маршрут <code>/admin</code>: <code>http://<span class="highlight">203.0.113.1</span>:32654/admin</code>. Вы увидите тот же интерфейс администрирования, что и раньше:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Страница аутентификации администратора приложения"></p>

<p>Мы развернули две реплики контейнера приложения Django Polls, используя Deployment.  Также мы создали стабильный конечный пункт сети для этих двух реплик и обеспечили внешний доступ к ним через службу NodePort.</p>

<p>Заключительный шаг этого учебного модуля заключается в том, чтобы защитить внешний трафик нашего приложения, используя HTTPS. Для этого мы используем контроллер <code>ingress-nginx</code>, установленный на этапе предварительных требований, и создадим объект Ingress для перенаправления внешнего трафика в службу Kubernetes <code>polls</code>.</p>

<h2 id="Шаг-8-—-Настройка-https-с-использованием-nginx-ingress-и-cert-manager">Шаг 8 — Настройка HTTPS с использованием Nginx Ingress и cert-manager</h2>

<p>Сущности <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingress</a> в Kubernetes обеспечивают гибкую маршрутизацию внешнего трафика кластера Kubernetes среди служб внутри кластера. Это достигается с помощью ресурсов Ingress, которые определяют правила маршрутизации трафика HTTP и HTTPS для служб Kubernetes, и <em>контроллеров</em> Ingress, которые реализуют правила посредством балансировки нагрузки трафика и его перенаправления на соответствующие службы серверной части.</p>

<p>На этапе предварительных требований мы установили контроллер <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> и надстройку <a href="https://github.com/jetstack/cert-manager">cert-manager</a> для автоматизации сертификатов TLS. Также мы настроили испытательную и производственную среду ClusterIssuers для вашего домена, используя центр сертификации Let&rsquo;s Encrypt, и создали объект Ingress для проверки выдачи сертификатов и шифрования TLS для двух фиктивных серверных служб. Прежде чем продолжить выполнение этого шага, удалите объект <code>echo-ingress</code>, созданный в <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">подготовительном учебном модуле</a>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl delete ingress echo-ingress
</li></ul></code></pre>
<p>При желании вы также можете удалить фиктивные службы и развертывания, используя команды <code>kubectl delete svc</code> и <code>kubectl delete deploy</code>, но для прохождения этого учебного модуля это не обязательно.</p>

<p>Также вы должны были создать запись DNS типа <code>A</code> для <code><span class="highlight">your_domain.com</span></code>, указывающую на публичный IP-адрес балансировщика нагрузки Ingress. Если вы используете балансировщик нагрузки DigitalOcean, вы можете найти этот IP-адрес в разделе <a href="https://cloud.digitalocean.com/networking/load_balancers">Load Balancers</a> панели управления. Если вы также используете DigitalOcean для управления записями DNS вашего домена, руководство <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Управление записями DNS</a> поможет вам научиться создавать записи класса <code>A</code>.</p>

<p>Если вы используете DigitalOcean Kubernetes, убедитесь, что вы внедрили обходное решение, описанное в <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-(optional)">шаге 5 учебного модуля Настройка Nginx Ingress с помощью Cert-Manager в DigitalOcean Kubernetes</a>.</p>

<p>Когда у вас будет запись <code>A</code>, указывающая на балансировщик нагрузки контроллера Ingress, вы можете создать Ingress для <code><span class="highlight">your_domain.com</span></code> и службы <code>polls</code>.</p>

<p>Откройте файл с именем <code>polls-ingress.yaml</code> в предпочитаемом текстовом редакторе:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Вставьте следующий манифест Ingress:</p>
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
<p>Мы создаем объект Ingress под именем <code>polls-ingress</code> и добавляем к нему аннотацию, чтобы уровень управления использовал контроллер ingress-nginx и размещение ClusterIssuer. Также мы включаем TLS для домена <code>your_domain.com</code> и сохраняем сертификат и закрытый ключ в объекте Secret с именем <code>polls-tls</code>. В заключение мы определяем правило перенаправления трафика для хоста <code>your_domain.com</code> в службу <code>polls</code> через порт <code>8000</code>.</p>

<p>Когда вы завершите редактирование файла, сохраните и закройте его.</p>

<p>Создайте в кластере объект Ingress, используя <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress created
</code></pre>
<p>Вы можете использовать <code>kubectl describe</code> для отслеживания состояния только что созданного объекта Ingress:</p>
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
<p>Также вы можете запустить команду <code>describe</code> для сертификата <code>polls-tls</code>, чтобы убедиться, что он был успешно создан:</p>
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
<p>Это подтверждает, что сертификат TLS успешно выдан, и шифрование HTTPS для домена <code>your_domain.com</code> активно.</p>

<p>Поскольку мы использовали тестовую версию ClusterIssuer, большинство браузеров не будут доверять поддельному сертификату Let&rsquo;s Encrypt, который она выдает, и поэтому при переходе по адресу <code>your_domain.com</code> появится страница с сообщением об ошибке.</p>

<p>Чтобы отправить тестовый запрос, мы используем <code>wget</code> из командной строки:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget -O - http://<span class="highlight">your_domain.com</span>/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
ERROR: cannot verify your_domain.com's certificate, issued by ‘CN=Fake LE Intermediate X1’:
  Unable to locally verify the issuer's authority.
To connect to your_domain.com insecurely, use `--no-check-certificate'.
</code></pre>
<p>Мы используем предлагаемый флаг <code>--no-check-certificate</code>, чтобы пропустить проверку сертификата:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget --no-check-certificate -q -O - http://your_domain.com/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>

&lt;link rel="stylesheet" type="text/css" href="https://your_space.nyc3.digitaloceanspaces.com/django-polls/static/polls/style.css"&gt;


    &lt;p&gt;No polls are available.&lt;/p&gt;
</code></pre>
<p>В этом выводе показан код HTML для страницы интерфейса <code>/polls</code>, а также подтверждается выдача таблицы стилей из хранилища объектов.</p>

<p>Мы успешно проверили выдачу сертификата с помощью тестовой версии ClusterIssuer и теперь можем изменить Ingress для использования производственной версии ClusterIssuer.</p>

<p>Снова откройте файл <code>polls-ingress.yaml</code> для редактирования:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Измените аннотацию <code>cluster-issuer</code>:</p>
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
<p>Внесите необходимые изменения, после чего сохраните и закройте файл. Обновите Ingress, используя <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress configured
</code></pre>
<p>Вы можете использовать команды <code>kubectl describe certificate polls-tls</code> и <code>kubectl describe ingress polls-ingress</code> для отслеживания статуса выдачи сертификата:</p>
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
<p>Приведенный выше вывод подтверждает, что новый производственный сертификат успешно выдан и сохранен в объекте Secret <code>polls-tls</code>.</p>

<p>Откройте в браузере адрес <code>your_domain.com/polls</code>, чтобы убедиться, что шифрование HTTPS включено, и все работает ожидаемым образом. Вы должны увидеть интерфейс приложения Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Интерфейс приложения «Опросы»"></p>

<p>Убедитесь, что в браузере включено шифрование HTTPS. Если вы используете Google Chrome, то если вышеуказанная страница откроется без ошибок, это будет означать, что все работает правильно. Кроме того, на панели URL должно быть изображение замка. Нажав на замок, вы сможете просмотреть детали сертификата Let&rsquo;s Encrypt.</p>

<p>Для окончательной очистки вы можете переключить тип службы <code>polls</code> с NodePort на внутренний тип ClusterIP.</p>

<p>Отредактируйте файл <code>polls-svc.yaml</code> в текстовом редакторе:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Измените <code>type</code> с <code>NodePort</code> на <code>ClusterIP</code>:</p>
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
<p>Когда вы завершите редактирование файла, сохраните и закройте его.</p>

<p>Разверните изменения с помощью команды <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml --force
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls configured
</code></pre>
<p>Убедитесь, что служба Service была изменена, используя <code>kubectl get svc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
polls   ClusterIP   10.245.203.186   &lt;none&gt;        8000/TCP   22s
</code></pre>
<p>Этот вывод показывает, что тип Service — ClusterIP. Для доступа можно использовать только домен и Ingress, созданный на этом шаге.</p>

<h2 id="Заключение">Заключение</h2>

<p>В этом учебном модуле мы развернули масштабируемое приложение Django с защитой HTTPS в кластере Kubernetes. Статичный контент выдается напрямую из хранилища объектов, а количество работающих подов можно быстро увеличивать или уменьшать, используя поле <code>replicas</code> в манифесте Deployment <code>polls-app</code>.</p>

<p>Если вы используете пространство DigitalOcean Space, вы также можете включить доставку статичных ресурсов через сеть доставки контента и создать настраиваемый субдомен для своего пространства. Дополнительную информацию можно найти в разделе <strong>Включение CDN</strong> учебного модуля <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#enabling-cdn-optional">Настройка масштабируемого приложения Django с управляемыми базами данных и пространствами DigitalOcean</a>.</p>

<p>С остальными частями серии можно ознакомиться на странице <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">От контейнеров к Kubernetes с Django</a>.</p>
