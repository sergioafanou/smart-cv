---
title : "Como configurar a autenticação baseada em chaves SSH em um servidor Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server-pt
image: "https://sergio.afanou.com/assets/images/image-midres-26.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>O SSH, ou shell seguro, é um protocolo criptografado usado para administrar e se comunicar com servidores. Ao trabalhar com um servidor Linux, existem boas chances de você gastar a maior parte do seu tempo em uma sessão de terminal conectada ao seu servidor através do SSH.</p>

<p>Embora existam outras maneiras diferentes de fazer login em um servidor SSH, neste guia, iremos focar na configuração de chaves SSH.  As chaves SSH oferecem uma maneira fácil e extremamente segura de fazer login no seu servidor.  Por esse motivo, este é o método que recomendamos para todos os usuários.</p>

<h2 id="como-as-chaves-ssh-funcionam">Como as chaves SSH funcionam?</h2>

<p>Um servidor SSH pode autenticar clientes usando uma variedade de métodos diferentes.  O mais básico deles é a autenticação por senha, que embora fácil de usar, mas não é o mais seguro.</p>

<p>Apesar de as senhas serem enviadas ao servidor de maneira segura, elas geralmente não são complexas ou longas o suficiente para resistirem a invasores persistentes.  O poder de processamento moderno combinado com scripts automatizados torna possível forçar a entrada de maneira bruta em uma conta protegida por senha.  Embora existam outros métodos para adicionar segurança adicional (<code>fail2ban</code>, etc), as chaves SSH são comprovadamente uma alternativa confiável e segura.</p>

<p>Os pares de chaves SSH são duas chaves criptografadas e seguras que podem ser usadas para autenticar um cliente em um servidor SSH.  Cada par de chaves consiste em uma chave pública e uma chave privada.</p>

<p>A chave privada é mantida pelo cliente e deve ser mantida em absoluto sigilo.  Qualquer comprometimento da chave privada permitirá que o invasor faça login em servidores que estejam configurados com a chave pública associada sem autenticação adicional.  Como uma forma de precaução adicional, a chave pode ser criptografada em disco com uma frase secreta.</p>

<p>A chave pública associada pode ser compartilhada livremente sem consequências negativas.  A chave pública pode ser usada para criptografar mensagens que <strong>apenas</strong> a chave privada pode descriptografar.  Essa propriedade é usada como uma maneira de autenticar usando o par de chaves.</p>

<p>A chave pública é enviada a um servidor remoto de sua preferência para que você possa fazer login via SSH.  A chave é adicionada a um arquivo especial dentro da conta de usuário em que você estará fazendo login chamado <code>~/.ssh/authorized_keys</code>.</p>

<p>Quando um cliente tenta autenticar-se usando chaves SSH, o servidor testa o cliente para verificar se ele tem posse da chave privada.  Se o cliente puder provar que possui a chave privada, a sessão do shell é gerada ou o comando solicitado é executado.</p>

<h2 id="como-criar-chaves-ssh">Como criar chaves SSH</h2>

<p>O primeiro passo para configurar a autenticação de chaves SSH para seu servidor é gerar um par de chaves SSH no seu computador local.</p>

<p>Para fazer isso, podemos usar um utilitário especial chamado <code>ssh-keygen</code>, que vem incluso com o conjunto padrão de ferramentas do OpenSSH.  Por padrão, isso criará um par de chaves RSA de 2048 bits, que é suficiente para a maioria dos usos.</p>

<p>No seu computador local, gere um par de chaves SSH digitando:</p>
<pre class="code-pre "><code>ssh-keygen
</code></pre><pre class="code-pre "><code>Generating public/private rsa key pair.
Enter file in which to save the key (/home/<span class="highlight">username</span>/.ssh/id_rsa):
</code></pre>
<p>O utilitário irá solicitar que seja selecionado um local para as chaves que serão geradas.  Por padrão, as chaves serão armazenadas no diretório <code>~/.ssh</code> dentro do diretório home do seu usuário.  A chave privada será chamada de <code>id_rsa</code> e a chave pública associada será chamada de <code>id_rsa.pub</code>.</p>

<p>Normalmente, é melhor manter utilizar o local padrão neste estágio.  Fazer isso permitirá que seu cliente SSH encontre automaticamente suas chaves SSH ao tentar autenticar-se.  Se quiser escolher um caminho não padrão, digite-o agora. Caso contrário, pressione ENTER para aceitar o padrão.</p>

<p>Caso tenha gerado um par de chaves SSH anteriormente, pode ser que você veja um prompt parecido com este:</p>
<pre class="code-pre "><code>/home/<span class="highlight">username</span>/.ssh/id_rsa already exists.
Overwrite (y/n)?
</code></pre>
<p>Se escolher substituir a chave no disco, você <strong>não</strong> poderá autenticar-se usando a chave anterior. Seja cuidadoso ao selecionar o sim, uma vez que este é um processo destrutivo que não pode ser revertido.</p>
<pre class="code-pre "><code>Created directory '/home/<span class="highlight">username</span>/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
</code></pre>
<p>Em seguida, você será solicitado a digitar uma frase secreta para a chave.  Esta é uma frase secreta opcional que pode ser usada para criptografar o arquivo de chave privada no disco.</p>

<p>Você pode estar se perguntando sobre quais são as vantagens que uma chave SSH oferece se ainda é necessário digitar uma frase secreta.  Algumas das vantagens são:</p>

<ul>
<li>A chave SSH privada (a parte que pode ser protegida por uma frase secreta), nunca é exposta na rede.  A frase secreta é usada apenas para descriptografar a chave na máquina local.  Isso significa que utilizar força bruta na rede não será possível contra a frase secreta.</li>
<li>A chave privada é mantida dentro de um diretório restrito.  O cliente SSH não irá reconhecer chaves privadas que não são mantidas em diretórios restritos.  A chave em si também precisa ter permissões restritas (leitura e gravação apenas disponíveis para o proprietário).  Isso significa que outros usuários no sistema não podem bisbilhotar.</li>
<li>Qualquer invasor que queira decifrar a frase secreta da chave SSH privada precisa já ter acesso ao sistema.  Isso significa que eles já terão acesso à sua conta de usuário ou conta root.  Se você estiver nesta posição, a frase secreta pode impedir que o invasor faça login imediatamente em seus outros servidores.  Espera-se que isso dê a você tempo suficiente para criar e implementar um novo par de chaves SSH e remover o acesso da chave comprometida.</li>
</ul>

<p>Como a chave privada nunca é exposta à rede e é protegida através de permissões de arquivos, este arquivo nunca deve ser acessível a qualquer um que não seja você (e o usuário root).  A frase secreta serve como uma camada adicional de proteção caso essas condições sejam comprometidas.</p>

<p>Uma frase secreta é uma adição opcional.  Se você inserir uma, será necessário fornecê-la sempre que for usar essa chave (a menos que você esteja executando um software de agente SSH que armazena a chave descriptografada).  Recomendamos a utilização de uma frase secreta, mas se você não quiser definir uma, basta pressionar ENTER para ignorar este prompt.</p>
<pre class="code-pre "><code>Your identification has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.
Your public key has been saved in /home/<span class="highlight">username</span>/.ssh/id_rsa.pub.
The key fingerprint is:
a9:49:2e:2a:5e:33:3e:a9:de:4e:77:11:58:b6:90:26 <span class="highlight">username</span>@remote_host
The key's randomart image is:
+--[ RSA 2048]----+
|     ..o         |
|   E o= .        |
|    o. o         |
|        ..       |
|      ..S        |
|     o o.        |
|   =o.+.         |
|. =++..          |
|o=++.            |
+-----------------+
</code></pre>
<p>Agora, você tem uma chave pública e privada que pode usar para se autenticar. O próximo passo é colocar a chave pública no seu servidor para que você possa usar a autenticação baseada em chaves SSH para fazer login.</p>

<h2 id="como-incorporar-sua-chave-pública-ao-criar-seu-servidor">Como incorporar sua chave pública ao criar seu servidor</h2>

<p>Se você estiver iniciando um novo servidor da DigitalOcean, é possível incorporar automaticamente sua chave pública SSH na nova conta raiz do seu servidor.</p>

<p>No final da página de criação do Droplet, há uma opção para adicionar chaves SSH ao seu servidor:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_options.png" alt="Incorporação de chaves SSH"></p>

<p>Se você já tiver adicionado um arquivo de chave pública à sua conta da DigitalOcean, verá ela aqui como uma opção selecionável (há duas chaves já existentes no exemplo acima: &ldquo;Work key&rdquo; e &ldquo;Home key&rdquo;).  Para incorporar uma chave existente, basta clicar nela para que fique destacada.  É possível incorporar várias chaves em um único servidor:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_selection.png" alt="Seleção de chaves SSH"></p>

<p>Caso ainda não tenha uma chave SSH pública carregada em sua conta, ou se quiser adicionar uma nova chave à sua conta, clique no botão &ldquo;+ Add SSH Key&rdquo;.  Isso irá abrir um prompt:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/key_prompt.png" alt="Prompt de chaves SSH"></p>

<p>Na caixa &ldquo;SSH Key content&rdquo;, cole o conteúdo da sua chave SSH pública.  Se você tiver gerado suas chaves usando o método acima, é possível obter o conteúdo de sua chave pública em seu computador local digitando:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre><pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDNqqi1mHLnryb1FdbePrSZQdmXRZxGZbo0gTfglysq6KMNUNY2VhzmYN9JYW39yNtjhVxqfW6ewc+eHiL+IRRM1P5ecDAaL3V0ou6ecSurU+t9DR4114mzNJ5SqNxMgiJzbXdhR+j55GjfXdk0FyzxM3a5qpVcGZEXiAzGzhHytUV51+YGnuLGaZ37nebh3UlYC+KJev4MYIVww0tWmY+9GniRSQlgLLUQZ+FcBUjaqhwqVqsHe4F/woW1IHe7mfm63GXyBavVc+llrEzRbMO111MogZUcoWDI9w7UIm8ZOTnhJsk7jhJzG2GpSXZHmly/a/buFaaFnmfZ4MYPkgJD username@example.com
</code></pre>
<p>Cole este valor, em sua totalidade, na caixa maior.  Na caixa &ldquo;Comment (optional)&rdquo;, você pode escolher um rótulo para a chave.  Isso será exibido como o nome da chave na interface da DigitalOcean:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/new_key.png" alt="Nova chave SSH"></p>

<p>Ao criar seu Droplet, as chaves SSH públicas que você selecionou serão colocadas no arquivo <code>~/.ssh/authorized_keys</code> da conta do usuário root.  Isso permitirá fazer login no servidor a partir do computador com sua chave privada.</p>

<h2 id="como-copiar-uma-chave-pública-para-seu-servidor">Como copiar uma chave pública para seu servidor</h2>

<p>Se você já tiver um servidor disponível e não incorporou chaves em sua criação, ainda é possível enviar sua chave pública e usá-la para autenticar-se no seu servidor.</p>

<p>O método a ser usado depende em grande parte das ferramentas disponíveis e dos detalhes da sua configuração atual.  Todos os métodos a seguir geram o mesmo resultado final.  O método mais fácil e automatizado é o primeiro e cada método depois dele necessita de passos manuais adicionais se você não conseguir usar os métodos anteriores.</p>

<h3 id="copiando-sua-chave-pública-usando-o-ssh-copy-id">Copiando sua chave pública usando o SSH-Copy-ID</h3>

<p>A maneira mais fácil de copiar sua chave pública para um servidor existente é usando um utilitário chamado <code>ssh-copy-id</code>.  Por conta da sua simplicidade, este método é recomendado se estiver disponível.</p>

<p>A ferramenta <code>ssh-copy-id</code> vem inclusa nos pacotes OpenSSH em muitas distribuições, de forma que você pode tê-la disponível em seu sistema local.  Para que este método funcione, você já deve ter acesso via SSH baseado em senha ao seu servidor.</p>

<p>Para usar o utilitário, você precisa especificar apenas o host remoto ao qual gostaria de se conectar e a conta do usuário que tem acesso SSH via senha. Esta é a conta na qual sua chave SSH pública será copiada.</p>

<p>A sintaxe é:</p>
<pre class="code-pre "><code>ssh-copy-id <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Pode ser que apareça uma mensagem como esta:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Isso significa que seu computador local não reconhece o host remoto. Isso acontecerá na primeira vez que você se conectar a um novo host. Digite &ldquo;yes&rdquo; e pressione ENTER para continuar.</p>

<p>Em seguida, o utilitário irá analisar sua conta local em busca da chave <code>id_rsa.pub</code> que criamos mais cedo. Quando ele encontrar a chave, irá solicitar a senha da conta do usuário remoto:</p>
<pre class="code-pre "><code>/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
username@111.111.11.111's password:
</code></pre>
<p>Digite a senha (sua digitação não será exibida para fins de segurança) e pressione ENTER.  O utilitário se conectará à conta no host remoto usando a senha que você forneceu. Então, ele copiará o conteúdo da sua chave <code>~/.ssh/id_rsa.pub</code> em um arquivo no diretório da conta remota home <code>~/.ssh</code> chamado <code>authorized_keys</code>.</p>

<p>Você verá um resultado que se parece com este:</p>
<pre class="code-pre "><code>Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'username@111.111.11.111'"
and check to make sure that only the key(s) you wanted were added.
</code></pre>
<p>Neste ponto, sua chave <code>id_rsa.pub</code> foi enviada para a conta remota. Continue para a próxima seção.</p>

<h3 id="copiando-sua-chave-pública-usando-o-ssh">Copiando sua chave pública usando o SSH</h3>

<p>Se não tiver o <code>ssh-copy-id</code> disponível, mas tiver acesso SSH baseado em senha a uma conta do seu servidor, você pode fazer o upload das suas chaves usando um método SSH convencional.</p>

<p>É possível fazer isso resgatando o conteúdo da nossa chave SSH pública do nosso computador local e enviando-o através de uma conexão via protocolo SSH ao servidor remoto.  Do outro lado, certificamo-nos de que o diretório <code>~/.ssh</code> existe na conta que estamos usando e então enviamos o conteúdo recebido em um arquivo chamado <code>authorized_keys</code> dentro deste diretório.</p>

<p>Vamos usar o símbolo de redirecionamento <code>&gt;&gt;</code> para adicionar o conteúdo ao invés de substituí-lo. Isso permitirá que adicionemos chaves sem destruir chaves previamente adicionadas.</p>

<p>O comando completo ficará parecido com este:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub | ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span> "mkdir -p ~/.ssh &amp;&amp; cat &gt;&gt; ~/.ssh/authorized_keys"
</code></pre>
<p>Pode ser que apareça uma mensagem como esta:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Isso significa que seu computador local não reconhece o host remoto. Isso acontecerá na primeira vez que você se conectar a um novo host. Digite &ldquo;yes&rdquo; e pressione ENTER para continuar.</p>

<p>Depois disso, você será solicitado a inserir a senha da conta na qual está tentando se conectar:</p>
<pre class="code-pre "><code>username@111.111.11.111's password:
</code></pre>
<p>Após digitar sua senha, o conteúdo da sua chave <code>id_rsa.pub</code> será copiado para o final do arquivo <code>authorized_keys</code> da conta do usuário remoto. Continue para a próxima seção se o processo foi bem-sucedido.</p>

<h3 id="copiando-sua-chave-pública-manualmente">Copiando sua chave pública manualmente</h3>

<p>Se o acesso SSH baseado em senha ao seu servidor ainda não estiver disponível, será necessário completar o processo acima manualmente.</p>

<p>O conteúdo do seu arquivo <code>id_rsa.pub</code> precisará ser adicionado a um arquivo em <code>~/.ssh/authorized_keys</code> em sua máquina remota de alguma maneira.</p>

<p>Para exibir o conteúdo de sua chave <code>id_rsa.pub</code>, digite o seguinte em seu computador local:</p>
<pre class="code-pre "><code>cat ~/.ssh/id_rsa.pub
</code></pre>
<p>Você verá o conteúdo da chave, que deve ser parecido com este:</p>
<pre class="code-pre "><code>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCqql6MzstZYh1TmWWv11q5O3pISj2ZFl9HgH1JLknLLx44+tXfJ7mIrKNxOOwxIxvcBF8PXSYvobFYEZjGIVCEAjrUzLiIxbyCoxVyle7Q+bqgZ8SeeM8wzytsY+dVGcBxF6N4JS+zVk5eMcV385gG3Y6ON3EG112n6d+SMXY0OEBIcO6x+PnUSGHrSgpBgX7Ks1r7xqFa7heJLLt2wWwkARptX7udSq05paBhcpB0pHtA1Rfz3K2B+ZVIpSDfki9UVKzT8JUmwW6NNzSgxUfQHGwnW7kj4jp4AT0VZk3ADw497M2G/12N0PPB5CnhHf7ovgy6nL1ikrygTKRFmNZISvAcywB9GVqNAVE+ZHDSCuURNsAInVzgYo9xgJDW8wUw2o8U77+xiFxgI5QSZX3Iq7YLMgeksaO4rBJEa54k8m5wEiEE1nUhLuJ0X/vh2xPff6SQ1BL/zkOhvJCACK6Vb15mDOeCSq54Cr7kvS46itMosi/uS66+PujOO+xt/2FWYepz6ZlN70bRly57Q06J+ZJoc9FfBCbCyYH7U/ASsmY095ywPsBo1XQ9PqhnN1/YOorJ068foQDNVpm146mUpILVxmq41Cj55YKHEazXGsdBIbXWhcrRf4G2fJLRcGUr9q8/lERo9oxRm5JFX6TCmj6kmiFqv+Ow9gI0x8GvaQ== demo@test
</code></pre>
<p>Acesse seu host remoto usando algum método que você tenha disponível.  Por exemplo, se seu servidor for um Droplet da DigitalOcean, faça login usando o console Web no painel de controle:</p>

<p><img src="https://assets.digitalocean.com/articles/ssh_key_overview/console_access.png" alt="Acesso via console da DigitalOcean"></p>

<p>Assim que tiver acesso à sua conta no servidor remoto, certifique-se de que o diretório <code>~/.ssh</code> foi criado.  Este comando criará o diretório se necessário, ou não fará nada se ele já existir:</p>
<pre class="code-pre "><code>mkdir -p ~/.ssh
</code></pre>
<p>Agora, você pode criar ou modificar o arquivo <code>authorized_keys</code> dentro deste diretório. Você pode adicionar o conteúdo do seu arquivo <code>id_rsa.pub</code> ao final do arquivo <code>authorized_keys</code>, criando-o se for necessário, usando este comando:</p>
<pre class="code-pre "><code>echo <span class="highlight">public_key_string</span> &gt;&gt; ~/.ssh/authorized_keys
</code></pre>
<p>No comando acima, substitua o <code><span class="highlight">public_key_string</span></code> pelo resultado do comando <code>cat ~/.ssh/id_rsa.pub</code> que você executou no seu sistema local. Ela deve começar com <code>ssh-rsa AAAA...</code>.</p>

<p>Se isso funcionar, continue para tentar autenticar-se sem uma senha.</p>

<h2 id="autenticar-se-em-seu-servidor-usando-chaves-ssh">Autenticar-se em seu servidor usando chaves SSH</h2>

<p>Se tiver completado um dos procedimentos acima, você deve conseguir fazer login no host remoto <em>sem</em> a senha da conta remota.</p>

<p>O processo básico é o mesmo:</p>
<pre class="code-pre "><code>ssh <span class="highlight">username</span>@<span class="highlight">remote_host</span>
</code></pre>
<p>Se essa é a primeira vez que você se conecta a este host (caso tenha usado o último método acima), pode ser que veja algo como isso:</p>
<pre class="code-pre "><code>The authenticity of host '111.111.11.111 (111.111.11.111)' can't be established.
ECDSA key fingerprint is fd:fd:d4:f9:77:fe:73:84:e1:55:00:ad:d6:6d:22:fe.
Are you sure you want to continue connecting (yes/no)? yes
</code></pre>
<p>Isso significa que seu computador local não reconhece o host remoto. Digite &ldquo;yes&rdquo; e então pressione ENTER para continuar.</p>

<p>Se não forneceu uma frase secreta para sua chave privada, você será logado imediatamente. Se forneceu uma frase secreta para a chave privada quando a criou, você será solicitado a digitá-la agora.  Depois disso, uma nova sessão de shell deve ser-lhe gerada com a conta no sistema remoto.</p>

<p>Caso isso dê certo, continue para descobrir como bloquear o servidor.</p>

<h2 id="desativando-a-autenticação-por-senha-no-seu-servidor">Desativando a autenticação por senha no seu servidor</h2>

<p>Se conseguiu logar na sua conta usando o SSH sem uma senha, você configurou com sucesso a autenticação baseada em chaves SSH na sua conta.  Entretanto, seu mecanismo de autenticação baseado em senha ainda está ativo, o que significa que seu servidor ainda está exposto a ataques por força bruta.</p>

<p>Antes de completar os passos nesta seção, certifique-se de que você tenha uma autenticação baseada em chaves SSH configurada para a conta root neste servidor, ou, de preferência, que tenha uma autenticação baseada em chaves SSH configurada para uma conta neste servidor com privilégios <code>sudo</code>.  Este passo irá bloquear os logins baseados em senha. Por isso, garantir que você ainda terá acesso de administrador será essencial.</p>

<p>Assim que as condições acima forem verdadeiras, entre no seu servidor remoto com chaves SSH como root ou com uma conta com privilégios <code>sudo</code>.  Abra o arquivo de configuração do daemon do SSH:</p>
<pre class="code-pre "><code>sudo nano /etc/ssh/sshd_config
</code></pre>
<p>Dentro do arquivo, procure por uma diretiva chamada <code>PasswordAuthentication</code>. Isso pode ser transformado em comentário. Descomente a linha e configure o valor em &ldquo;no&rdquo;. Isso irá desativar a sua capacidade de fazer login via SSH usando senhas de conta:</p>
<pre class="code-pre "><code>PasswordAuthentication no
</code></pre>
<p>Salve e feche o arquivo quando você terminar. Para realmente implementar as alterações que acabamos de fazer, reinicie o serviço.</p>

<p>Em máquinas Ubuntu ou Debian, emita este comando:</p>
<pre class="code-pre "><code>sudo service ssh restart
</code></pre>
<p>Em máquinas CentOS/Fedora, o daemon chama-se <code>sshd</code>:</p>
<pre class="code-pre "><code>sudo service sshd restart
</code></pre>
<p>Após completar este passo, você alterou seu daemon do SSH com sucesso para responder apenas a chaves SSH.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Agora, você deve ter uma autenticação baseada em chaves SSH configurada no seu servidor, permitindo fazer login sem fornecer uma senha de conta.  A partir daqui, há muitas direções em que você pode seguir.  Se você quiser aprender mais sobre como trabalhar com o SSH, veja nosso <a href="https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys">Guia de Noções Básicas sobre SSH</a>.</p>
