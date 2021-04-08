---
title : "Como instalar o MongoDB a partir dos repositórios APT padrão no Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-from-the-default-apt-repositories-on-ubuntu-20-04-pt
image: "https://sergio.afanou.com/assets/images/image-midres-26.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>O <a href="https://www.mongodb.com/">MongoDB</a> é um banco de dados de documento NoSQL de código aberto e gratuito usado habitualmente em aplicativos Web modernos.</p>

<p>Neste tutorial, você irá instalar o MongoDB, gerenciar seus serviços e, de maneira opcional, habilitar o acesso remoto.</p>

<p><span class='note'><strong>Nota</strong>: no momento em que este artigo está sendo escrito, este tutorial instala a versão <span class="highlight">3.6</span> do MongoDB, que é a versão disponível nos repositórios padrão do Ubuntu. No entanto, geralmente recomendamos a instalação da versão mais recente do MongoDB — versão <span class="highlight">4.4</span> no momento em que este artigo está sendo escrito — ao invés disso. Se quiser instalar a versão mais recente do MongoDB, encorajamos que siga este guia sobre <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Como instalar o MongoDB no Ubuntu 20.04 da origem</a>.<br></span></p>

<h2 id="pré-requisitos">Pré-requisitos</h2>

<p>Para seguir este tutorial, será necessário:</p>

<ul>
<li>Um servidor Ubuntu 20.04 configurado seguindo o <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">Guia de configuração inicial de servidor</a>, incluindo um usuário não root com privilégios administrativos e um firewall configurado com o UFW.</li>
</ul>

<h2 id="passo-1-—-como-instalar-o-mongodb">Passo 1 — Como instalar o MongoDB</h2>

<p>Os repositórios de pacotes oficiais do Ubuntu incluem o MongoDB, ou seja, podemos instalar os pacotes necessários utilizando o <code>apt</code>. Como mencionado na introdução, a versão disponível dos repositórios padrão não é a mais recente. Para instalar a versão mais recente do Mongo, siga <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">este tutorial</a>.</p>

<p>Primeiramente, atualize a lista de pacotes para ter a versão mais recente dos registros:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li></ul></code></pre>
<p>Agora, instale o pacote MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install mongodb
</li></ul></code></pre>
<p>Esse comando solicitará que você confirme se deseja instalar o pacote <code>mongodb</code> e suas dependências. Para fazer isso, pressione <code>Y</code> e então <code>ENTER</code>.</p>

<p>Esse comando instala vários pacotes que contêm uma versão estável do MongoDB, junto com ferramentas de gerenciamento úteis para o servidor MongoDB. O servidor de banco de dados é iniciado automaticamente após a instalação.</p>

<p>Em seguida, vamos verificar se o servidor está funcionando corretamente.</p>

<h2 id="passo-2-—-como-verificar-o-serviço-e-o-banco-de-dados">Passo 2 — Como verificar o serviço e o banco de dados</h2>

<p>O processo de instalação iniciou o MongoDB automaticamente, mas vamos verificar se o serviço está iniciado e que o banco de dados está funcionando.</p>

<p>Primeiramente, verifique o estado do serviço:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Você verá este resultado:</p>
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
<p>De acordo com esse resultado, o servidor MongoDB está em funcionamento.</p>

<p>Podemos verificar isso mais adiante ao nos conectarmos ao servidor de banco de dados e executarmos um comando de diagnóstico. Isso mostrará a versão atual do banco de dados, o endereço e a porta do servidor, e o resultado do comando de estado:</p>
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
<p>Um valor de <code>1</code> para o campo <code>ok</code> na resposta indica que o servidor está funcionando corretamente.</p>

<p>Em seguida, vamos ver como gerenciar a instância do servidor.</p>

<h2 id="passo-3-—-como-gerenciar-o-serviço-mongodb">Passo 3 — Como gerenciar o serviço MongoDB</h2>

<p>O processo de instalação mostrado no Passo 1 configura o MongoDB como um serviço <code>systemd</code>, ou seja, você pode gerenciá-lo usando comandos <code>systemctl</code> padrão junto com todos os outros serviços de sistema no Ubuntu.</p>

<p>Para verificar o estado do serviço, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Para interromper o servidor a qualquer momento, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop mongodb
</li></ul></code></pre>
<p>Para iniciar o servidor quando ele for parado, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start mongodb
</li></ul></code></pre>
<p>Você também pode reiniciar o servidor com o comando a seguir:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>Por padrão, o MongoDB está configurado para iniciar automaticamente com o servidor. Se quiser desativar o inicializador automático, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable mongodb
</li></ul></code></pre>
<p>Você pode habilitar novamente a inicialização automática a qualquer momento com o seguinte comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable mongodb
</li></ul></code></pre>
<p>Em seguida, vamos ajustar as configurações de firewall para nossa instalação do MongoDB.</p>

<h2 id="passo-4-—-como-ajustar-o-firewall-opcional">Passo 4 — Como ajustar o Firewall (opcional)</h2>

<p>Considerando que você seguiu todas as instruções do <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">tutorial de configuração inicial do servidor</a> para habilitar o firewall no seu servidor, então o servidor MongoDB estará inacessível pela internet.</p>

<p>Se quiser usar o servidor MongoDB apenas localmente com aplicativos funcionando no mesmo servidor, esta é a configuração recomendada e segura. Entretanto, se quiser poder se conectar ao seu servidor MongoDB pela internet, será necessário permitir as conexões de entrada adicionando uma regra UFW.</p>

<p>Para permitir o acesso ao MongoDB em sua porta padrão <code>27017</code> de todos os lugares, utilize o comando <code>sudo ufw allow <span class="highlight">27017</span></code>. Entretanto, habilitar o acesso à internet ao servidor MongoDB em uma instalação padrão dá a qualquer um acesso ilimitado ao servidor de banco de dados e seus dados.</p>

<p>Na maioria dos casos, o MongoDB deve ser acessado apenas por certos locais confiáveis, como outro servidor que hospeda um aplicativo. Para permitir o acesso à porta padrão do MongoDB apenas por outro servidor confiável, especifique o endereço IP do servidor remoto no comando <code>ufw</code>. Dessa forma, apenas essa máquina será explicitamente autorizada a se conectar:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">trusted_server_ip</span>/32 to any port <span class="highlight">27017</span>
</li></ul></code></pre>
<p>Verifique a mudança nas configurações do firewall com o <code>ufw</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>
<p>Você deve ver o tráfego permitido para a porta <code>27017</code> no resultado. Se tiver decidido permitir que apenas um certo endereço IP se conecte ao servidor MongoDB, o endereço IP do local permitido estará listado ao invés de <code>Anywhere</code> no resultado do comando:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
<span class="highlight">27017                      ALLOW       Anywhere</span>
OpenSSH (v6)               ALLOW       Anywhere (v6)
<span class="highlight">27017 (v6)                 ALLOW       Anywhere (v6)</span>
</code></pre>
<p>Você pode encontrar configurações de firewall mais avançadas para restringir o acesso a serviços em <a href="https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands">Essenciais do UFW: regras e comandos comuns do firewall</a>.</p>

<p>Embora a porta esteja aberta, o MongoDB estará escutando apenas no endereço local <code>127.0.0.1</code>. Para permitir conexões remotas, adicione o endereço IP roteável publicamente do seu servidor ao arquivo <code>mongodb.conf</code>.</p>

<p>Abra o arquivo de configuração do MongoDB em seu editor de texto preferido. Este comando de exemplo usa o <code>nano</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongodb.conf
</li></ul></code></pre>
<p>Adicione o endereço IP do seu servidor MongoDB ao valor <code>bindIP</code>. Certifique-se de colocar uma vírgula entre o endereço IP existente e o que você adicionou:</p>
<div class="code-label " title="/etc/mongodb.conf">/etc/mongodb.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
logappend=true

bind_ip = 127.0.0.1<span class="highlight">,your_server_ip</span>
#port = 27017

...
</code></pre>
<p>Salve o arquivo e saia do editor. Se você usou o <code>nano</code> para editar o arquivo, faça isso pressionando <code>CTRL + X</code>, <code>Y</code>, depois <code>ENTER</code>.</p>

<p>Em seguida, reinicie o serviço MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>O MongoDB agora está escutando conexões remotas, mas qualquer um pode acessá-lo. Siga <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-mongodb-on-ubuntu-20-04">Como proteger o MongoDB no Ubuntu 20.04</a> para adicionar um usuário administrativo e bloquear as coisas mais adiante.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Encontre tutoriais mais aprofundados sobre como configurar e usar o MongoDB <a href="https://www.digitalocean.com/community/search?q=mongodb">nestes artigos da comunidade DigitalOcean</a>. A <a href="https://docs.mongodb.com/v3.6/">documentação oficial do MongoDB</a> também é um ótimo recurso sobre as possibilidades que o MongoDB proporciona.</p>
