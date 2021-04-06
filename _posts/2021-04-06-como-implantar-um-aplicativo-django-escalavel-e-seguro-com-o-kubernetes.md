---
title : "Como implantar um aplicativo Django escalável e seguro com o Kubernetes"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes-pt
image: "https://sergio.afanou.com/assets/images/image-midres-15.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Neste tutorial, você irá implantar um aplicativo de pesquisa do Django em contêiner em um cluster do Kubernetes.</p>

<p>O <a href="https://www.djangoproject.com/">Django</a> é um framework Web poderoso, que pode ajudar a acelerar a implantação do seu aplicativo Python. Ele inclui diversas funcionalidades convenientes, como um <a href="https://en.wikipedia.org/wiki/Object-relational_mapping">mapeador relacional do objeto</a>, autenticação de usuário e uma interface administrativa personalizável para seu aplicativo. Ele também inclui um <a href="https://docs.djangoproject.com/en/2.1/topics/cache/">framework de cache</a> e encoraja o design de aplicativos organizados através do seu <a href="https://docs.djangoproject.com/en/2.1/topics/http/urls/">URL Dispatcher</a> e <a href="https://docs.djangoproject.com/en/2.1/topics/templates/">Sistema de modelos</a>.</p>

<p>Em <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Como construir um aplicativo Django e Gunicorn com o Docker</a>, o <a href="https://docs.djangoproject.com/en/3.1/intro/tutorial01/">aplicativo Polls de tutorial do Django</a> foi modificado de acordo com a metodologia do <a href="https://12factor.net/">Twelve-Factor</a> para a construção de aplicativos Web escaláveis e nativos na nuvem. Essa configuração em contêiner foi dimensionada e protegida com um proxy reverso do Nginx e autenticada pelo Let&rsquo;s Encrypt com certificados TLS seguindo <a href="https://www.digitalocean.com/community/tutorials/how-to-scale-and-secure-a-django-application-with-docker-nginx-and-let-s-encrypt">Como dimensionar e proteger um aplicativo Django com o Docker, Nginx e Let&rsquo;s Encrypt</a>. Neste tutorial final da série <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">De contêineres ao Kubernetes com o Django</a>, o aplicativo modernizado de pesquisa do Django será implantado em um cluster do Kubernetes.</p>

<p>O <a href="https://kubernetes.io/">Kubernetes</a> é um orquestrador de contêineres de código aberto poderoso, que automatiza a implantação, dimensionamento e gerenciamento de aplicativos em contêiner. Os objetos do Kubernetes como os ConfigMaps e Segredos permitem centralizar e desacoplar a configuração dos seus contêineres, enquanto os controladores como Deployments reiniciam automaticamente contêineres que falharam e habilitam o dimensionamento rápido de réplicas de contêineres. A criptografia TLS é habilitada com um objeto Ingress e o Controlador Ingress de código aberto <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a>. O add-on <a href="https://github.com/jetstack/cert-manager">cert-manager</a> do Kubernetes renova e emite certificados usando a autoridade de certificação gratuita <a href="https://letsencrypt.org/">Let&rsquo;s Encrypt</a>.</p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para seguir este tutorial, será necessário:</p>

<ul>
<li>Um cluster do Kubernetes 1.15+ com <a href="https://kubernetes.io/docs/reference/access-authn-authz/rbac/">controle de acesso baseado na função</a> (RBAC) habilitado. Essa configuração irá usar um cluster do <a href="https://www.digitalocean.com/products/kubernetes/">Kubernetes da DigitalOcean</a>, mas você pode criar um cluster usando <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-kubernetes-cluster-using-kubeadm-on-ubuntu-18-04">outro método</a>.</li>
<li>A ferramenta <code>kubectl</code> de linha de comando instalada em sua máquina local e configurada para se conectar ao seu cluster. Você pode ler mais sobre como instalar o <code>kubectl</code> <a href="https://kubernetes.io/docs/tasks/tools/install-kubectl/">na documentação oficial</a>. Se você estiver usando um cluster do Kubernetes da DigitalOcean, consulte <a href="https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/">Como se conectar a um cluster do Kubernetes da DigitalOcean</a> para aprender como se conectar ao seu cluster usando o <code>kubectl</code>.</li>
<li>Um nome de domínio registrado. Este tutorial usará <code>your_domain.com</code> do início ao fim. Você pode obter um domínio gratuitamente através do <a href="http://www.freenom.com/en/index.html">Freenom</a>, ou usar o registrador de domínios de sua escolha.</li>
<li>Um Controlador Ingress <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> e o gerenciador de certificados TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a> instalados no seu cluster e configurados para emitir certificados TLS. Para aprender como instalar e configurar um Ingress com o cert-manager, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">Como configurar um Nginx Ingress com Cert-Manager no Kubernetes da plataforma DigitalOcean</a>.</li>
<li>Um registro de DNS <code>A</code> com <code>your_domain.com</code> apontando para o endereço IP público do Balanceador de carga do Ingress. Se estiver usando a DigitalOcean para gerenciar os registros de DNS do seu domínio, consulte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Como gerenciar os registros de DNS</a> para aprender como criar um registro <code>A</code>.</li>
<li>Um bucket de armazenamento de objetos S3, como um <a href="https://www.digitalocean.com/products/spaces/">espaço da DigitalOcean</a> para armazenar os arquivos estáticos do seu projeto Django e um conjunto de chaves de acesso para esse espaço. Para aprender como criar um espaço, consulte a documentação de produto <a href="https://www.digitalocean.com/docs/spaces/how-to/create/">Como criar espaços</a>. Para aprender como criar chaves de acesso para espaços, consulte <a href="https://www.digitalocean.com/docs/spaces/how-to/administrative-access/#access-keys">Compartilhando acesso a espaços com chaves de acesso</a>. Com pequenas alterações, você pode usar qualquer serviço de armazenamento de objetos que o plug-in <a href="https://django-storages.readthedocs.io/en/latest/">django-storages</a> suporte.</li>
<li>Uma instância de servidor PostgreSQL, banco de dados e usuário para seu aplicativo Django. Com pequenas alterações, você pode usar qualquer banco de dados <a href="https://docs.djangoproject.com/en/2.2/ref/databases/">compatível com o Django</a>.

<ul>
<li>O banco de dados PostgreSQL deve ser chamado de <strong>polls</strong> (ou outro nome memorável para inserir em seus arquivos de configuração abaixo) e, neste tutorial, o usuário do banco de dados do Django será chamado <strong>sammy</strong>. Para orientação sobre como criar esses elementos, siga o Passo 1 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-1-%E2%80%94-creating-the-postgresql-database-and-user">Como construir um aplicativo Django e Gunicorn com o Docker</a>. É necessário realizar esses passos a partir da sua máquina local.</li>
<li>Um <a href="https://www.digitalocean.com/products/managed-databases/">cluster PostgreSQL gerenciado</a> pela DigitalOcean será usado neste tutorial. Para aprender como criar um cluster, consulte a <a href="https://www.digitalocean.com/docs/databases/how-to/clusters/create/">Documentação de produto de banco de dados gerenciados</a> da DigitalOcean</li>
<li>É possível instalar e executar sua própria instância do PostgreSQL. Para obter orientações sobre a instalação e administração do PostgreSQL em um servidor Ubuntu, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04">Como instalar e usar o PostgreSQL no Ubuntu 18.04</a>.</li>
</ul></li>
<li>Uma conta do <a href="https://hub.docker.com/">Docker Hub</a> e repositório público. Para obter mais informações sobre a criação deles, consulte <a href="https://docs.docker.com/docker-hub/repos/">Repositórios</a> da documentação do Docker.</li>
<li>O mecanismo Docker instalado em sua máquina local. Consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04">Como instalar e usar o Docker no Ubuntu 18.04</a> para aprender mais.</li>
</ul>

<p>Assim que tiver esses componentes configurados, tudo estará pronto para seguir o guia.</p>

<h2 id="passo-1-—-clonando-e-configurando-o-aplicativo">Passo 1 — Clonando e configurando o aplicativo</h2>

<p>Neste passo, vamos clonar o código do aplicativo do GitHub e definir algumas configurações como as credenciais de banco de dados e chaves de armazenamento de objetos.</p>

<p>O código do aplicativo e o Dockerfile podem ser encontrados na ramificação <code>polls-docker</code> do <a href="https://github.com/do-community/django-polls/tree/polls-docker">repositório GitHub</a> do aplicativo Polls de tutorial do Django. Esse repositório contém códigos para o <a href="https://docs.djangoproject.com/en/3.0/intro/">aplicativo de amostra Polls</a> da documentação do Django, que ensina como construir um aplicativo de pesquisa a partir do zero.</p>

<p>A ramificação <code>polls-docker</code> contém uma versão em Docker do aplicativo Polls. Para aprender como o aplicativo Polls foi modificado para funcionar efetivamente em um ambiente em contêiner, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker">Como construir um aplicativo Django e Gunicorn com o Docker</a>.</p>

<p>Comece usando o <code>git</code> para clonar o branch <code>polls-docker</code> do <a href="https://github.com/do-community/django-polls">repositório GitHub</a> do aplicativo Polls do tutorial do Django em sua máquina local:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">git clone --single-branch --branch polls-docker https://github.com/do-community/django-polls.git
</li></ul></code></pre>
<p>Navegue até o diretório <code>django-polls</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cd django-polls
</li></ul></code></pre>
<p>Esse diretório contém o código Python do aplicativo Django, um <code>Dockerfile</code> que o Docker usará para compilar a imagem do contêiner, bem como um arquivo <code>env</code> que contém uma lista de variáveis de ambiente a serem passadas para o ambiente de execução do contêiner. Verifique o <code>Dockerfile</code>:</p>
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
<p>Esse Dockerfile usa a <a href="https://hub.docker.com/_/python">imagem Docker</a> oficial do Python 3.7.4 como base e instala os requisitos de pacote Python do Django e do Gunicorn, conforme definido no arquivo <code>django-polls/requirements.txt</code>. Em seguida, ele remove alguns arquivos de compilação desnecessários, copia o código do aplicativo na imagem e define o <code>PATH</code> de execução. Por fim, ele declara que a porta <code>8000</code> será usada para aceitar conexões de contêiner recebidas e executa <code>gunicorn</code> com 3 trabalhadores, escutando na porta <code>8000</code>.</p>

<p>Para aprender mais sobre cada um dos passos nesse Dockerfile, confira o Passo 6 de <a href="https://www.digitalocean.com/community/tutorials/how-to-build-a-django-and-gunicorn-application-with-docker#step-6-%E2%80%94-writing-the-application-dockerfile">Como construir um aplicativo Django e Gunicorn com o Docker</a>.</p>

<p>Agora, crie a imagem usando o <code>docker build</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker build -t polls .
</li></ul></code></pre>
<p>Nós demos o nome de <code>polls</code> para a imagem usando o sinalizador <code>-t</code> e passamos o diretório atual como um <em>contexto de compilação</em>, que é o conjunto de arquivos de referência ao compilar a imagem.</p>

<p>Depois que o Docker compilar e marcar a imagem, liste as imagens disponíveis usando <code>docker images</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker images
</li></ul></code></pre>
<p>Você deve ver a imagem <code>polls</code> listada:</p>
<pre class="code-pre "><code>OutputREPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
polls               latest              80ec4f33aae1        2 weeks ago         197MB
python              3.7.4-alpine3.10    f309434dea3a        8 months ago        98.7MB
</code></pre>
<p>Antes de executarmos o contêiner Django, precisamos configurar seu ambiente de execução usando o arquivo <code>env</code> presente no diretório atual. Esse arquivo será passado para o comando <code>docker run</code> usado para executar o contêiner, e o Docker irá injetar as variáveis de ambiente configuradas no ambiente de execução do contêiner.</p>

<p>Abra o arquivo <code>env</code> com o <code>nano</code> ou com o seu editor favorito:</p>
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
<p>Preencha os valores que estão faltando para as seguintes chaves:</p>

<ul>
<li><code>DJANGO_SECRET_KEY</code>: defina isso como um valor único e imprevisível, conforme detalhado na <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#secret-key">documentação do Django</a>. Um método para gerar essa chave é fornecido em <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Ajustando as configurações do aplicativo</a> do tutorial sobre o <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#step-5-%E2%80%94-adjusting-the-app-settings">Aplicativo Django escalável</a>.</li>
<li><code>DJANGO_ALLOWED_HOSTS</code>: essa variável protege o aplicativo e impede ataques de cabeçalho de host HTTP. Para fins de teste, defina isso como <code>*</code>, um coringa que irá corresponder a todos os hosts. Na produção, isso deve ser definido como <code>your_domain.com</code>. Para aprender mais sobre esse ajuste do Django, consulte as <a href="https://docs.djangoproject.com/en/3.0/ref/settings/#allowed-hosts">Core Settings</a> da documentação do Django.</li>
<li><code>DATABASE_USERNAME</code>: defina isso como o usuário do banco de dados PostgreSQL criado nos passos pré-requisitos.</li>
<li><code>DATABASE_NAME</code>: defina isso como <code>polls</code> ou o nome do banco de dados PostgreSQL criado nos passos pré-requisitos.</li>
<li><code>DATABASE_PASSWORD</code>: defina isso como a senha do usuário do banco de dados PostgreSQL criada nos passos pré-requisitos.</li>
<li><code>DATABASE_HOST</code>: defina isso como o nome do host do seu banco de dados.</li>
<li><code>DATABASE_PORT</code>: defina isso como a porta do seu banco de dados.</li>
<li><code>STATIC_ACCESS_KEY_ID</code>: defina isso como a chave de acesso do seu espaço ou armazenamento de objetos.</li>
<li><code>STATIC_SECRET_KEY</code>: defina isso como o segredo da chave de acesso do seu espaço ou armazenamento de objetos.</li>
<li><code>STATIC_BUCKET_NAME</code>: defina isso como seu nome de espaço ou bucket de armazenamento de objetos.</li>
<li><code>STATIC_ENDPOINT_URL</code>: defina isso como o URL do ponto de extremidade do espaço apropriado ou armazenamento de objetos, como <code>https://<span class="highlight">your_space_name</span>.nyc3.digitaloceanspaces.com</code> se o espaço estiver localizado na região <code>nyc3</code>.</li>
</ul>

<p>Assim que terminar a edição, salve e feche o arquivo.</p>

<p>No próximo passo, vamos executar o contêiner configurado localmente e criar o esquema de banco de dados. Além disso, vamos carregar ativos estáticos como folhas de estilos e imagens para o armazenamento de objetos.</p>

<h2 id="passo-2-—-criando-o-esquema-de-banco-de-dados-e-carregando-ativos-para-o-armazenamento-de-objetos">Passo 2 — Criando o esquema de banco de dados e carregando ativos para o armazenamento de objetos</h2>

<p>Com o contêiner construído e configurado, use o <code>docker run</code> para substituir o conjunto <code>CMD</code> no Dockerfile e criar o esquema de banco de dados usando os comandos <code>manage.py makemigrations</code> e <code>manage.py migrate</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"
</li></ul></code></pre>
<p>Executamos a imagem de contêiner <code>polls:latest</code>, passamos o arquivo de variável de ambiente que acabamos de modificar e substituímos o comando do Dockerfile com <code>sh -c "python manage.py makemigrations &amp;&amp; python manage.py migrate"</code>, o que irá criar o esquema de banco de dados definido pelo código do aplicativo.</p>

<p>Se estiver executando isso pela primeira vez, você deve ver:</p>
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
<p>Isso indica que o esquema de banco de dados foi criado com sucesso.</p>

<p>Se estiver executando <code>migrate</code> uma outra vez, o Django irá cancelar a operação a menos que o esquema de banco de dados tenha sido alterado.</p>

<p>Em seguida, vamos executar outra instância do contêiner de aplicativo e usar um shell interativo dentro dela para criar um usuário administrativo para o projeto Django.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run -i -t --env-file env polls sh
</li></ul></code></pre>
<p>Isso lhe fornecerá um prompt do shell dentro do contêiner em execução que você pode usar para criar o usuário do Django:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">python manage.py createsuperuser
</li></ul></code></pre>
<p>Digite um nome de usuário, endereço de e-mail e senha para o seu usuário e, depois de criá-lo, pressione <code>CTRL+D</code> para sair do contêiner e encerrá-lo.</p>

<p>Por fim, vamos gerar os arquivos estáticos para o aplicativo e fazer o upload deles para o espaço da DigitalOcean usando o <code>collectstatic</code>. Observe que esse processo pode demorar um pouco de tempo para ser concluído.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env polls sh -c "python manage.py collectstatic --noinput"
</li></ul></code></pre>
<p>Depois que esses arquivos forem gerados e enviados, você receberá a seguinte saída.</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>121 static files copied.
</code></pre>
<p>Agora, podemos executar o aplicativo:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker run --env-file env -p 80:8000 polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[2019-10-17 21:23:36 +0000] [1] [INFO] Starting gunicorn 19.9.0
[2019-10-17 21:23:36 +0000] [1] [INFO] Listening at: http://0.0.0.0:8000 (1)
[2019-10-17 21:23:36 +0000] [1] [INFO] Using worker: sync
[2019-10-17 21:23:36 +0000] [7] [INFO] Booting worker with pid: 7
[2019-10-17 21:23:36 +0000] [8] [INFO] Booting worker with pid: 8
[2019-10-17 21:23:36 +0000] [9] [INFO] Booting worker with pid: 9
</code></pre>
<p>Aqui, executamos o comando padrão definido no Dockerfile, <code>gunicorn --bind :8000 --workers 3 mysite.wsgi:application</code> e expomos a porta do contêiner <code>8000</code> para que a porta <code>80</code> na sua máquina local seja mapeada para a porta <code>8000</code> do contêiner <code>polls</code>.</p>

<p>Agora, você deve ser capaz de navegar até o aplicativo <code>polls</code> usando seu navegador Web digitando <code>http://localhost</code> na barra de URL. Como não há nenhuma rota definida para o caminho <code>/</code>, você provavelmente receberá um erro <code>404 Page Not Found</code>, o que é esperado.</p>

<p>Navegue até <code>http://localhost/polls</code> para ver a interface do aplicativo Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface do aplicativo Polls"></p>

<p>Para visualizar a interface administrativa, visite <code>http://localhost/admin</code>. Você deve ver a janela de autenticação do administrador do aplicativo Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Página de autenticação de administrador do Polls"></p>

<p>Digite o nome e a senha do usuário administrativo que você criou com o comando <code>createsuperuser</code>.</p>

<p>Depois de autenticar-se, você pode acessar a interface administrativa do aplicativo Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin_main.png" alt="Interface administrativa principal do Polls"></p>

<p>Observe que os ativos estáticos para os aplicativos <code>admin</code> e <code>polls</code> estão sendo entregues diretamente do armazenamento de objetos. Para confirmar isso, consulte <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#testing-spaces-static-file-delivery">Testando a entrega de arquivos estáticos de espaços</a>.</p>

<p>Quando terminar de explorar, aperte <code>CTRL+C</code> na janela do terminal executando o contêiner Docker para encerrar o contêiner.</p>

<p>Com a imagem Docker do aplicativo Django testada, ativos estáticos carregados no armazenamento de objetos e o esquema de banco de dados configurado e pronto para ser usado com seu aplicativo, tudo está pronto para carregar sua imagem do aplicativo Django em um registro de imagem como o Docker Hub.</p>

<h2 id="passo-3-—-enviando-a-imagem-do-aplicativo-django-para-o-docker-hub">Passo 3 — Enviando a imagem do aplicativo Django para o Docker Hub</h2>

<p>Para implementar seu aplicativo no Kubernetes, sua imagem de aplicativo deve ser carregada em um registro como o <a href="https://hub.docker.com/">Docker Hub</a>. O Kubernetes irá puxar a imagem do aplicativo do seu repositório e implantá-la em seu cluster.</p>

<p>É possível usar um registro privado do Docker, como <a href="https://www.digitalocean.com/products/container-registry/">o registro do contêiner da DigitalOcean</a>, atualmente gratuito em acesso antecipado, ou um registro público do Docker como o Docker Hub. O Docker Hub também permite criar repositórios privados do Docker. Um repositório público permite que qualquer pessoa veja e puxe as imagens do contêiner, enquanto um repositório privado permite restringir o acesso a você e seus membros de equipe.</p>

<p>Neste tutorial, vamos enviar a imagem do Django ao repositório público do Docker Hub criado nos pré-requisitos. Também é possível enviar sua imagem a um repositório privado, mas puxar imagens de um repositório privado está além do escopo deste artigo. Para aprender mais sobre a autenticação do Kubernetes com o Docker Hub e puxar imagens privadas, consulte <a href="https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/">Puxar uma imagem de um registro privado</a> dos documentos do Kubernetes.</p>

<p>Comece fazendo login no Docker Hub em sua máquina local:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker login
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
</code></pre>
<p>Digite seu nome de usuário e senha do Docker Hub para fazer login.</p>

<p>A imagem do Django possui atualmente o sinalizador <code>polls:latest</code>. Para enviá-la ao seu repositório do Docker Hub, sinalize novamente a imagem com seu nome de usuário e nome de repositório do Docker Hub:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker tag polls:latest <span class="highlight">your_dockerhub_username</span>/<span class="highlight">your_dockerhub_repo_name</span>:latest
</li></ul></code></pre>
<p>Carregue a imagem no repositório:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">docker push <span class="highlight">sammy</span>/<span class="highlight">sammy-django</span>:latest
</li></ul></code></pre>
<p>Neste tutorial, o nome de usuário do Docker Hub é <strong>sammy</strong> e o nome do repositório é <strong>sammy-django</strong>. Você deve substituir esses valores pelo seu nome de usuário e nome de repositório do Docker Hub.</p>

<p>Você verá um resultado que se atualiza conforme as camadas da imagem são enviadas ao Docker Hub.</p>

<p>Agora que sua imagem está disponível no Kubernetes no Docker Hub, você pode começar a implantá-la em seu cluster.</p>

<h2 id="passo-4-—-configurando-o-configmap">Passo 4 — Configurando o ConfigMap</h2>

<p>Quando executamos o contêiner do Django localmente, passamos o arquivo <code>env</code> ao <code>docker run</code> para injetar variáveis de configuração no ambiente de tempo de execução. No Kubernetes, as variáveis de configuração podem ser injetadas usando o <a href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/">ConfigMaps</a> e <a href="https://kubernetes.io/docs/concepts/configuration/secret/">Segredos</a>.</p>

<p>Os ConfigMaps devem ser usados para armazenar informações de configuração não confidenciais, como as configurações do aplicativo, enquanto os Segredos devem ser usados para informações confidenciais como chaves de API e credenciais de banco de dados. Ambos são injetados em contêineres de maneira similar, mas os Segredos têm recursos de controle de acesso e segurança adicionais, como a <a href="https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/">criptografia em repouso</a>. Os Segredos também armazenam dados em base64, enquanto os ConfigMaps armazenam dados em texto simples.</p>

<p>Para começar, crie um diretório chamado <code>yaml</code> no qual vamos armazenar nossos manifestos do Kubernetes. Navegue até o diretório.</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">mkdir yaml
</li><li class="line" data-prefix="$">cd
</li></ul></code></pre>
<p>Abra um arquivo chamado <code>polls-configmap.yaml</code> no <code>nano</code> ou seu editor de texto preferido:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-configmap.yaml
</li></ul></code></pre>
<p>Cole o seguinte manifesto do ConfigMap:</p>
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
<p>As configurações não confidenciais foram extraídas do arquivo <code>env</code> modificado no <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Passo 1</a> e coladas em um manifesto do ConfigMap. O objeto do ConfigMap chama-se <code>polls-config</code>. Copie os mesmos valores inseridos no arquivo <code>env</code> no passo anterior.</p>

<p>Por motivos de teste, deixe o <code>DJANGO_ALLOWED_HOSTS</code> como <code>*</code> para desativar a filtragem baseada em cabeçalhos de host. Em um ambiente de produção, isso deve ser definido como o domínio do seu aplicativo.</p>

<p>Quando terminar de editar o arquivo, salve e feche-o.</p>

<p>Crie o ConfigMap em seu cluster usando o <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-configmap.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>configmap/polls-config created
</code></pre>
<p>Com o ConfigMap criado, vamos criar o Segredo usado pelo nosso aplicativo no próximo passo.</p>

<h2 id="passo-5-—-configurando-o-segredo">Passo 5 — Configurando o Segredo</h2>

<p>Os valores do Segredo devem ser <a href="https://en.wikipedia.org/wiki/Base64">codificados em base64</a>, o que significa que criar objetos Segredo em seu cluster é ligeiramente mais complicado do que criar ConfigMaps. É possível repetir o processo do passo anterior, manualmente codificar os valores do Segredo em base64 e colá-los em um arquivo de manifesto. Também é possível criá-los usando um arquivo de variável de ambiente,  <code>kubectl create</code> e o sinalizador <code>--from-env-file</code>, o que será feito neste passo.</p>

<p>Novamente, vamos usar o arquivo  <code>env</code> do <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Passo 1</a>, removendo variáveis inseridas no ConfigMap. Faça uma cópia do arquivo <code>env</code> chamado <code>polls-secrets</code> no diretório <code>yaml</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">cp ../env ./polls-secrets
</li></ul></code></pre>
<p>Edite o arquivo em seu editor de preferência:</p>
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
<p>Exclua todas as variáveis inseridas no manifesto do ConfigMap. Quando terminar, ele deve ficar parecido com isto:</p>
<div class="code-label " title="polls-secrets">polls-secrets</div><pre class="code-pre "><code class="code-highlight language-bash">DJANGO_SECRET_KEY=<span class="highlight">your_secret_key</span>
DATABASE_NAME=polls
DATABASE_USERNAME=<span class="highlight">your_django_db_user</span>
DATABASE_PASSWORD=<span class="highlight">your_django_db_user_password</span>
DATABASE_HOST=<span class="highlight">your_db_host</span>
DATABASE_PORT=<span class="highlight">your_db_port</span>
STATIC_ACCESS_KEY_ID=<span class="highlight">your_space_access_key</span>
STATIC_SECRET_KEY=<span class="highlight">your_space_access_key_secret</span>
</code></pre>
<p>Certifique-se de usar os mesmos valores usados no <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-1-%E2%80%94-cloning-and-configuring-the-application">Passo 1</a>. Quando terminar, salve e feche o arquivo.</p>

<p>Crie o Segredo em seu cluster usando o <code>kubectl create secret</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl create secret generic polls-secret --from-env-file=poll-secrets
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>secret/polls-secret created
</code></pre>
<p>Aqui, criamos um objeto Segredo chamado <code>polls-secret</code> e o passamos no arquivo de Segredos que acabamos de criar.</p>

<p>Verifique o Segredo usando o <code>kubectl describe</code>:</p>
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
<p>Neste ponto, você armazenou a configuração do seu aplicativo em seu cluster do Kubernetes usando os tipos de objeto Segredo e ConfigMap. Agora, estamos prontos para implantar o aplicativo no cluster.</p>

<h2 id="passo-6-—-implementando-o-aplicativo-do-django-usando-uma-implantação">Passo 6 — Implementando o aplicativo do Django usando uma Implantação</h2>

<p>Neste passo, você criará um Deployment (implantação) para seu aplicativo do Django. Uma Implantação do Kubernetes é um <em>controlador</em> que pode ser usado para gerenciar aplicativos sem estado em seu cluster. Um controlador é um loop de controle que regula cargas de trabalho aumentando ou diminuindo-as. Os controladores também reiniciam e limpam contêineres com falhas.</p>

<p>As Implantações controlam um ou mais Pods, a menor unidade implantável em um cluster do Kubernetes. Os Pods incluem um ou mais contêineres. Para aprender mais sobre os diferentes tipos de cargas de trabalho que você pode inicializar, consulte <a href="https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes">Uma introdução ao Kubernetes</a>.</p>

<p>Inicie abrindo um arquivo chamado <code>polls-deployment.yaml</code> no seu editor de texto favorito:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-deployment.yaml
</li></ul></code></pre>
<p>Cole o manifesto de Implantação a seguir:</p>
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
<p>Preencha o nome apropriado da imagem do contêiner, referenciando a imagem do Polls do Django que você enviou para o Docker Hub no <a href="https://www.digitalocean.com/community/tutorials/how-to-deploy-a-scalable-and-secure-django-application-with-kubernetes#step-2-%E2%80%94-creating-the-database-schema-and-uploading-assets-to-object-storage">Passo 2</a>.</p>

<p>Aqui, definimos uma Implantação do Kubernetes chamada <code>polls-app</code> e a rotulamos com o par de chave-valor <code>app: polls</code>. Especificamos que queremos executar duas réplicas do Pod definido abaixo do campo <code>template</code>.</p>

<p>Usando o <code>envFrom</code> com o <code>secretRef</code> e o <code>configMapRef</code>, especificamos que todos os dados do Segredo <code>polls-secret</code> e do ConfigMap <code>polls-config</code> devem ser injetados nos contêineres como variáveis de ambiente. As chaves ConfigMap e Segredo tornam-se os nomes das variáveis de ambiente.</p>

<p>Por fim, expomos a <code>containerPort</code> <code>8000</code> e a nomeamos <code>gunicorn</code>.</p>

<p>Para aprender mais sobre como configurar as Implantações do Kubernetes, consulte <a href="https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/">Deployments</a> na documentação do Kubernetes.</p>

<p>Quando terminar de editar o arquivo, salve e feche-o.</p>

<p>Crie uma Implantação no seu cluster usando o <code>kubectl apply -f</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-deployment.yaml
</li></ul></code></pre><pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">deployment.apps/polls-app created
</li></ul></code></pre>
<p>Verifique se a Implantação foi implantada corretamente usando o <code>kubectl get</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get deploy polls-app
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME        READY   UP-TO-DATE   AVAILABLE   AGE
polls-app   2/2     2            2           6m38s
</code></pre>
<p>Se encontrar um erro ou algo não estiver funcionando, use o <code>kubectl describe</code> para verificar o Deployment que falhou:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl describe deploy
</li></ul></code></pre>
<p>Verifique os dois Pods usando o <code>kubectl get pod</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get pod
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                         READY   STATUS    RESTARTS   AGE
polls-app-847f8ccbf4-2stf7   1/1     Running   0          6m42s
polls-app-847f8ccbf4-tqpwm   1/1     Running   0          6m57s
</code></pre>
<p>Agora, duas réplicas do seu aplicativo Django estão em funcionamento no cluster. Para acessar o aplicativo, é necessário criar um Service (serviço) do Kubernetes, que vamos fazer em seguida.</p>

<h2 id="passo-7-—-permitindo-o-acesso-externo-usando-um-serviço">Passo 7 — Permitindo o acesso externo usando um Serviço</h2>

<p>Neste passo, você irá criar um Serviço para seu aplicativo Django. Um Serviço do Kubernetes é uma abstração que permite expor um conjunto de Pods em execução como um serviço de rede. Ao usar um Serviço, é possível criar um ponto de extremidade estável para seu aplicativo que não muda à medida que os Pods são destruídos e recriados.</p>

<p>Existem vários tipos de Serviço, incluindo os Serviços de ClusterIP, que expõem o Serviço em um IP interno do cluster, os Serviços de NodePort, que expõem o Serviço em cada nó em uma porta estática chamada NodePort, além de os Serviços de LoadBalancer, que fornecem um balanceador de carga em nuvem para direcionar o tráfego externo aos Pods no seu cluster (através dos NodePorts, criados por ele automaticamente). Para aprender mais sobre isso, consulte <a href="https://kubernetes.io/docs/concepts/services-networking/service/">Service</a> nos documentos do Kubernetes.</p>

<p>Em nossa configuração final, vamos usar um Serviço de ClusterIP que é exposto usando um Ingress e um Controlador Ingress configurados nos pré-requisitos deste guia. Por enquanto, para testar se tudo está funcionando corretamente, vamos criar um Serviço de NodePort temporário para acessar o aplicativo Django.</p>

<p>Inicie criando um arquivo chamado <code>polls-svc.yaml</code> usando seu editor favorito:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Cole o manifesto de Serviço a seguir:</p>
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
<p>Aqui, criamos um Serviço de NodePort chamado <code>polls</code> e damos a ele o rótulo <code>app: polls</code>. Em seguida, selecionamos os Pods de backend com o rótulo <code>app: polls</code> e miramos em suas portas <code>8000</code>.</p>

<p>Quando terminar de editar o arquivo, salve e feche-o.</p>

<p>Implemente o Serviço usando o <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls created
</code></pre>
<p>Confirme se seu Serviço foi criado usando o <code>kubectl get svc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
polls   NodePort   10.245.197.189   &lt;none&gt;        8000:32654/TCP   59s
</code></pre>
<p>Esse resultado mostra o IP interno do cluster do Serviço e o NodePort (<code>32654</code>). Para nos conectar ao serviço, precisamos dos endereços IP externos para nossos nós de cluster:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get node -o wide
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME                   STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                       KERNEL-VERSION          CONTAINER-RUNTIME
pool-7no0qd9e0-364fd   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.5    203.0.113.1   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fi   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.4    203.0.113.2    Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
pool-7no0qd9e0-364fv   Ready    &lt;none&gt;   27h   v1.18.8   10.118.0.3    203.0.113.3   Debian GNU/Linux 10 (buster)   4.19.0-10-cloud-amd64   docker://18.9.9
</code></pre>
<p>Em seu navegador Web, visite seu aplicativo Polls usando um endereço IP externo de qualquer nó e o NodePort. De acordo com o resultado acima, a URL do aplicativo seria: <code>http://<span class="highlight">203.0.113.1</span>:32654/polls</code>.</p>

<p>Você deve ver a mesma interface do aplicativo Polls que você acessou localmente no Passo 1:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface do aplicativo Polls"></p>

<p>É possível repetir o mesmo teste usando a rota <code>/admin</code>: <code>http://<span class="highlight">203.0.113.1</span>:32654/admin</code>. Você deve ver a mesma interface de administrador que antes:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_admin.png" alt="Página de autenticação de administrador do Polls"></p>

<p>Neste estágio, você já implantou duas réplicas do contêiner do aplicativo Polls do Django usando uma Implantação. Você também criou um ponto de extremidade de rede estável para essas duas réplicas, e o tornou externamente acessível usando um Serviço de NodePort.</p>

<p>O passo final neste tutorial é proteger o tráfego externo para seu aplicativo usando HTTPS. Para fazer isso, vamos usar o Controlador Ingress <code>ingress-nginx</code> instalado nos pré-requisitos e criar um objeto Ingress para rotear o tráfego externo para o Serviço <code>polls</code> do Kubernetes.</p>

<h2 id="passo-8-—-configurando-o-https-usando-o-nginx-ingress-e-o-cert-manager">Passo 8 — Configurando o HTTPS usando o Nginx Ingress e o cert-manager</h2>

<p>Os <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingresses</a> do Kubernetes permitem o roteamento do tráfego externo ao cluster do seu Kubernetes de maneira flexível para os Serviços dentro de seu cluster. Isso é alcançado usando os objetos do Ingress, que definem as regras para rotear o tráfego HTTP e HTTPS para os Serviços do Kubernetes e para os <em>Controladores</em> do Ingress, os quais implementam as regras fazendo o balanceamento da carga do tráfego e o seu roteamento para os Serviços de backend apropriados.</p>

<p>Nos pré-requisitos, você instalou o Controlador Ingress <a href="https://github.com/kubernetes/ingress-nginx">ingress-nginx</a> e o add-on de automação de certificados TLS <a href="https://github.com/jetstack/cert-manager">cert-manager</a>. Você também definiu a preparação e a produção de ClusterIssuers para seu domínio usando a autoridade de certificação Let&rsquo;s Encrypt e criou um Ingress para testar a emissão de certificados e a criptografia TLS em dois Serviços de backend fictícios. Antes de continuar com este passo, deve-se excluir o Ingress <code>echo-ingress</code> criado no <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes">tutorial pré-requisito</a>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl delete ingress echo-ingress
</li></ul></code></pre>
<p>Se desejar, também pode excluir os Serviços e Implantações fictícios usando o <code>kubectl delete svc</code> e o <code>kubectl delete deploy</code>, mas isso não é essencial para completar este tutorial.</p>

<p>Você também deve ter criado um registro de DNS <code>A</code> com <code><span class="highlight">your_domain.com</span></code> apontando para o endereço IP público do balanceador de carga do Ingress. Se estiver usando um balanceador de carga da DigitalOcean, é possível encontrar esse endereço IP na seção <a href="https://cloud.digitalocean.com/networking/load_balancers">Load Balancers</a> do Painel de controle. Se estiver usando a DigitalOcean para gerenciar os registros de DNS do seu domínio, consulte <a href="https://www.digitalocean.com/docs/networking/dns/how-to/manage-records/">Como gerenciar os registros de DNS</a> para aprender como criar registros <code>A</code>.</p>

<p>Se estiver usando o Kubernetes da DigitalOcean, também certifique-se de ter implementado a solução descrita no <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes#step-5-%E2%80%94-enabling-pod-communication-through-the-load-balancer-(optional)">Passo 5 de Como configurar um Nginx Ingress com o Cert-Manager no Kubernetes da DigitalOcean</a>.</p>

<p>Assim que tiver um registro <code>A</code> apontando para o balanceador de carga do Ingress, crie um Ingress para <code><span class="highlight">your_domain.com</span></code> e o Serviço <code>polls</code>.</p>

<p>Abra um arquivo chamado <code>polls-ingress.yaml</code> no seu editor favorito:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Cole o manifesto de Ingress a seguir:</p>
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
<p>Criamos um objeto Ingress chamado <code>polls-ingress</code> e o anotamos para instruir o plano de controle para usar o Controlador Ingress ingress-nginx e o ClusterIssuer de preparo. Também habilitamos o TLS para <code>your_domain.com</code> e armazenamos o certificado e a chave privada em um segredo chamado <code>polls-tls</code>. Por fim, definimos uma regra para rotear o tráfego para o host <code>your_domain.com</code> para o Serviço <code>polls</code> na porta <code>8000</code>.</p>

<p>Quando terminar de editar o arquivo, salve e feche-o.</p>

<p>Crie o Ingress no seu cluster usando o <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress created
</code></pre>
<p>É possível usar o <code>kubectl describe</code> para rastrear o estado do Ingress que acabou de ser criado:</p>
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
<p>Também é possível executar um <code>describe</code> no certificado <code>polls-tls</code> para confirmar ainda mais se sua criação foi bem-sucedida:</p>
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
<p>Isso confirma que o certificado TLS foi emitido com sucesso e a criptografia do HTTPS agora está ativa para <code>your_domain.com</code>.</p>

<p>Como usamos o ClusterIssuer de preparo, a maior parte dos navegadores Web não irá confiar no certificado falso do Let&rsquo;s Encrypt que ele emitiu, de forma que navegar até <code>your_domain.com</code> irá resultar em uma página de erro.</p>

<p>Para enviar um pedido de teste, vamos usar o <code>wget</code> a partir da linha de comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget -O - http://<span class="highlight">your_domain.com</span>/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>. . .
ERROR: cannot verify your_domain.com's certificate, issued by ‘CN=Fake LE Intermediate X1’:
  Unable to locally verify the issuer's authority.
To connect to your_domain.com insecurely, use `--no-check-certificate'.
</code></pre>
<p>Vamos usar o sinalizador sugerido <code>--no-check-certificate</code> para ignorar a validação de certificados:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">wget --no-check-certificate -q -O - http://your_domain.com/polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>

&lt;link rel="stylesheet" type="text/css" href="https://your_space.nyc3.digitaloceanspaces.com/django-polls/static/polls/style.css"&gt;


    &lt;p&gt;No polls are available.&lt;/p&gt;
</code></pre>
<p>Esse resultado mostra o HTML para a página de interface de <code>/polls</code>, o que também confirma que a folha de estilos está sendo exibida a partir do armazenamento de objetos.</p>

<p>Agora que você testou com sucesso a emissão de certificados usando o ClusterIssuer de preparo, modifique o Ingress para usar o ClusterIssuer de produção.</p>

<p>Abra o <code>polls-ingress.yaml</code> para editar mais uma vez:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-ingress.yaml
</li></ul></code></pre>
<p>Modifique a anotação do <code>cluster-issuer</code>:</p>
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
<p>Quando terminar, salve e feche o arquivo. Atualize o Ingress usando o <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-ingress.yaml
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>ingress.networking.k8s.io/polls-ingress configured
</code></pre>
<p>É possível usar o <code>kubectl describe certificate polls-tls</code> e o <code>kubectl describe ingress polls-ingress</code> para rastrear o status da emissão de certificados:</p>
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
<p>O resultado acima confirma que o novo certificado de produção foi emitido com sucesso e armazenado no Secredo <code>polls-tls</code>.</p>

<p>Navegue até <code>your_domain.com/polls</code> no seu navegador Web para confirmar se a criptografia do HTTPS está habilitada e tudo está funcionando como esperado. Você deve ver a interface do aplicativo Polls:</p>

<p><img src="https://assets.digitalocean.com/articles/scalable_django/polls_app.png" alt="Interface do aplicativo Polls"></p>

<p>Verifique se a criptografia do HTTPS está ativa no seu navegador Web. Se estiver usando o Google Chrome, chegar na página acima sem erros confirma que tudo está funcionando corretamente. Além disso, você deve ver um cadeado na barra de URL. Clicar no cadeado permitirá verificar os detalhes do certificado do Let&rsquo;s Encrypt.</p>

<p>Como uma tarefa de limpeza final, você pode alterar opcionalmente o tipo de Serviço <code>polls</code> do NodePort para o tipo exclusivamente interno ClusterIP.</p>

<p>Modifique o <code>polls-svc.yaml</code> usando seu editor:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">nano polls-svc.yaml
</li></ul></code></pre>
<p>Altere o <code>type</code> de <code>NodePort</code> para <code>ClusterIP</code>:</p>
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
<p>Quando terminar de editar o arquivo, salve e feche-o.</p>

<p>Implemente as alterações usando o <code>kubectl apply</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl apply -f polls-svc.yaml --force
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>service/polls configured
</code></pre>
<p>Confirme se seu Serviço foi modificado usando o <code>kubectl get svc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">kubectl get svc polls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>NAME    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
polls   ClusterIP   10.245.203.186   &lt;none&gt;        8000/TCP   22s
</code></pre>
<p>Esse resultado mostra que o tipo de Serviço é agora ClusterIP. A única maneira de acessá-lo é através do seu domínio e do Ingress criados neste passo.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Neste tutorial, você implantou um aplicativo Django dimensionável e seguro via HTTPS em um cluster do Kubernetes. O conteúdo estático é servido diretamente do armazenamento de objetos, e o número de Pods em execução pode ser aumentado ou reduzido rapidamente usando o campo <code>replicas</code> no manifesto de Implantação <code>polls-app</code>.</p>

<p>Se estiver usando um espaço da DigitalOcean, também é possível habilitar a entrega de ativos estáticos através de uma rede de entrega de conteúdo e criar um subdomínio personalizado para seu espaço. Por favor, consulte <strong>Habilitando o CDN</strong> de <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-a-scalable-django-app-with-digitalocean-managed-databases-and-spaces#enabling-cdn-optional">Como configurar um aplicativo Django dimensionável com os bancos de dados e espaços gerenciados da DigitalOcean</a> para aprender mais.</p>

<p>Para revisar o resto da série, visite nossa <a href="https://www.digitalocean.com/community/tutorial_series/from-containers-to-kubernetes-with-django">página da série De contêineres ao Kubernetes com o Django</a>.</p>
