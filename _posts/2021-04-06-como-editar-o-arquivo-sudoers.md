---
title : "Como editar o arquivo sudoers"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-pt
image: "https://sergio.afanou.com/assets/images/image-midres-54.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>A separação de privilégios é um dos paradigmas de segurança fundamentais implementados em sistemas operacionais do tipo Linux e Unix. Os usuários regulares operam com privilégios limitados para reduzir o escopo de sua influência em seu próprio ambiente, e não no sistema operacional como um todo.</p>

<p>Um usuário especial, chamado <strong>root</strong>, possui privilégios de <em>superusuário</em>. Essa é uma conta administrativa sem as restrições presentes em usuários normais. Os usuários podem executar comandos com privilégios de superusuário ou <strong>root</strong> de várias maneiras diferentes.</p>

<p>Neste artigo, vamos discutir como obter privilégios de <strong>root</strong> de uma maneira correta e segura, dando um foco especial na edição do arquivo <code>/etc/sudoers</code>.</p>

<p>Vamos completar esses passos em um servidor Ubuntu 20.04, mas a maioria das distribuições do Linux modernas, como o Debian e o CentOS, devem operar de maneira similar.</p>

<p>Este guia considera que você já completou a <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">configuração inicial de servidor</a> discutida aqui. Faça login no seu servidor como um usuário não <strong>raiz</strong> e continue abaixo.</p>

<p><span class='note'><strong>Nota:</strong> este tutorial aborda com profundidade a escalação de privilégios e o arquivo <code>sudoers</code>. Se quiser apenas adicionar privilégios <code>sudo</code> a um usuário, verifique nossos tutoriais de início rápido <em>Como criar um novo usuário habilitado para sudo</em> para o <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">Ubuntu</a> e <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">CentOS</a>.<br></span></p>

<h2 id="como-obter-privilégios-de-root">Como obter privilégios de root</h2>

<p>Há três maneiras básicas de obter privilégios de <strong>root</strong>, que variam em seu nível de sofisticação.</p>

<h3 id="faça-login-como-root">Faça login como root</h3>

<p>O método mais simples e direto de se obter privilégios de <strong>root</strong> é fazendo login diretamente em seu servidor como um usuário <strong>root</strong>.</p>

<p>Se você estiver fazendo login em uma máquina local (ou usando uma característica de acesso de console fora de banda em um servidor virtual), digite <code>root</code> como seu nome de usuário no prompt de login e digite a senha <strong>root</strong> solicitada.</p>

<p>Caso esteja fazendo login via SSH, especifique o usuário <strong>root</strong> antes do endereço IP ou nome de domínio em sua string de conexão via protocolo SSH:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">ssh root@<span class="highlight">server_domain_or_ip</span>
</li></ul></code></pre>
<p>Se você não tiver configurado chaves SSH para o usuário <strong>root</strong>, digite a senha de <strong>root</strong> solicitada.</p>

<h3 id="use-su-para-tornar-se-root">Use <code>su</code> para tornar-se root</h3>

<p>Fazer login diretamente como <strong>root</strong> não é recomendado, pois é fácil começar a usar o sistema para tarefas não administrativas, o que é uma atividade perigosa.</p>

<p>A próxima maneira de obter privilégios de superusuário permite que você se torne um usuário <strong>root</strong> a qualquer momento, conforme necessário.</p>

<p>Podemos fazer isso invocando o comando <code>su</code>, que significa &ldquo;substituir usuário&rdquo;. Para obter privilégios de <strong>root</strong>, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">su
</li></ul></code></pre>
<p>A senha do usuário <strong>root</strong> será solicitada e, depois disso, você será levado a uma sessão de shell <strong>root</strong>.</p>

<p>Quando terminar as tarefas que necessitam de privilégios de <strong>root</strong>, retorne ao seu shell padrão digitando:</p>
<pre class="code-pre super_user prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="#">exit
</li></ul></code></pre>
<h3 id="use-o-sudo-para-executar-comandos-como-root">Use o <code>sudo</code> para executar comandos como root</h3>

<p>A maneira final de obter privilégios de <strong>root</strong> que vamos discutir é com o comando <code>sudo</code>.</p>

<p>O comando <code>sudo</code> permite que sejam executados comandos únicos com privilégios de <strong>root</strong>, sem a necessidade de gerar um novo shell. Ele é executado desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo <span class="highlight">command_to_execute</span>
</li></ul></code></pre>
<p>Ao contrário do <code>su</code>, o comando <code>sudo</code> solicitará a senha do usuário <em>atual</em>, não a senha do <strong>root</strong>.</p>

<p>Devido às suas implicações de segurança, o acesso <code>sudo</code> não é concedido a usuários por padrão e deve ser configurado antes de funcionar corretamente. Verifique nossos tutoriais de início rápido <em>Como criar um novo usuário habilitado para sudo</em> para o <code>Ubuntu</code> e <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-20-04-quickstart">CentOS</a> para aprender como configurar um usuário habilitado para <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-centos-8-quickstart">sudo</a>.</p>

<p>Na seção seguinte, vamos discutir como modificar a configuração do <code>sudo</code> mais detalhadamente.</p>

<h2 id="o-que-é-o-visudo">O que é o Visudo?</h2>

<p>O comando <code>sudo</code> é configurado através de um arquivo localizado em <code>/etc/sudoers</code>.</p>

<p><span class='warning'><strong>Aviso:</strong> nunca edite este arquivo com um editor de texto normal! Use sempre o comando <code>visudo</code> em vez disso!<br></span></p>

<p>Considerando que uma sintaxe inadequada no arquivo <code>/etc/sudoers</code> pode tornar o seu sistema defeituoso, onde é impossível obter privilégios elevados, é importante usar o comando <code>visudo</code> para editar o arquivo.</p>

<p>O comando <code>visudo</code> abre um editor de texto aparentemente normal, com a diferença que ele valida a sintaxe do arquivo no momento de salvar. Isso impede que erros de configuração bloqueiem as operações do <code>sudo</code>, que pode ser a única maneira disponível para obter privilégios de <strong>root</strong>.</p>

<p>Tradicionalmente, o <code>visudo</code> abre o arquivo <code>/etc/sudoers</code> com o editor de texto <code>vi</code>. No entanto, o Ubuntu configurou o <code>visudo</code> para usar o editor de texto <code>nano</code> ao invés disso.</p>

<p>Se quiser alterá-lo de volta para o <code>vi</code>, emita o seguinte comando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo update-alternatives --config editor
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>There are 4 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/vim.basic   30        manual mode
  4            /usr/bin/vim.tiny    10        manual mode

Press &lt;enter&gt; to keep the current choice[*], or type selection number:
</code></pre>
<p>Selecione o número que corresponde à escolha que deseja fazer.</p>

<p>No CentOS, é possível alterar esse valor adicionando a seguinte linha ao seu <code>~/.bashrc</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">export EDITOR=`which <span class="highlight">name_of_editor</span>`
</li></ul></code></pre>
<p>Habilite o arquivo para implementar as alterações:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">. ~/.bashrc
</li></ul></code></pre>
<p>Após ter configurado o <code>visudo</code>, execute o comando para acessar o arquivo <code>/etc/sudoers</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre>
<h2 id="como-modificar-o-arquivo-sudoers">Como modificar o arquivo sudoers</h2>

<p>O arquivo <code>/etc/sudoers</code> será apresentado no seu editor de texto selecionado.</p>

<p>O arquivo foi copiado e colado do Ubuntu 18.04, com comentários removidos. O arquivo <code>/etc/sudoers</code> do CentOS possui muitas linhas a mais, algumas das quais não vamos discutir neste guia.</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

root    ALL=(ALL:ALL) ALL

%admin ALL=(ALL) ALL
%sudo   ALL=(ALL:ALL) ALL

#includedir /etc/sudoers.d
</code></pre>
<p>Vamos dar uma olhada no que essas linhas fazem.</p>

<h3 id="linhas-padrão">Linhas padrão</h3>

<p>A primeira linha, &ldquo;Defaults env_reset&rdquo;, reinicia o ambiente de terminal para remover qualquer variável de usuário. Essa é uma medida de segurança usada para limpar variáveis de ambiente potencialmente nocivas na sessão <code>sudo</code>.</p>

<p>A segunda linha, <code>Defaults mail_badpass</code>, diz ao sistema para enviar e-mails ao usuário <code>mailto</code> configurado informando sobre tentativas de senha incorretas do <code>sudo</code>. Por padrão, ela é a conta <strong>root</strong>.</p>

<p>A terceira linha, que começa com &ldquo;Defaults secure_path=&hellip;&rdquo;, especifica o <code>PATH</code> (os lugares no sistema de arquivos em que o sistema operacional irá procurar aplicativos) que será usado para as operações <code>sudo</code>. Isso impede o uso de caminhos de usuário que possam ser nocivos.</p>

<h3 id="linhas-de-privilégio-de-usuário">Linhas de privilégio de usuário</h3>

<p>A quarta linha, que dita os privilégios <strong>sudo</strong> do usuário <code>root</code>, é diferente das linhas anteriores. Vamos dar uma olhada no que os diferentes campos significam:</p>

<ul>
<li><p><code><span class="highlight">root</span> ALL=(ALL:ALL) ALL</code> O primeiro campo indica o nome do usuário ao qual a regra se aplicará (<strong>root</strong>).</p></li>
<li><p><code>root <span class="highlight">ALL</span>=(ALL:ALL) ALL</code> O primeiro &ldquo;ALL&rdquo; indica que essa regra se aplica a todos os hosts.</p></li>
<li><p><code>root ALL=(<span class="highlight">ALL</span>:ALL) ALL</code> Esse &ldquo;ALL&rdquo; indica que o usuário <strong>root</strong> pode executar comandos como todos os usuários.</p></li>
<li><p><code>root ALL=(ALL:<span class="highlight">ALL</span>) ALL</code> Esse &ldquo;ALL&rdquo; indica que o usuário <strong>root</strong> pode executar comandos como todos os grupos.</p></li>
<li><p><code>root ALL=(ALL:ALL) <span class="highlight">ALL</span></code> O último &ldquo;ALL&rdquo; indica que essas regras se aplicam a todos os comandos.</p></li>
</ul>

<p>Isso significa que o usuário <strong>root</strong> pode executar qualquer comando usando o <code>sudo</code>, desde que forneçam sua senha.</p>

<h3 id="linhas-de-privilégio-de-grupo">Linhas de privilégio de grupo</h3>

<p>As duas linhas seguintes são semelhantes às linhas de privilégio de usuário, mas especificam as regras <code>sudo</code> para grupos.</p>

<p>Os nomes que começam com um <code>%</code> indicam nomes de grupo.</p>

<p>Aqui, vemos que o grupo <strong>admin</strong> pode executar qualquer comando como qualquer usuário em qualquer host. De maneira similar, o grupo <strong>sudo</strong> possui os mesmos privilégios, mas também pode executar como qualquer grupo.</p>

<h3 id="linha-etc-sudoers-d-incluída">Linha /etc/sudoers.d incluída</h3>

<p>A última linha aparentemente é um comentário à primeira vista:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .

#includedir /etc/sudoers.d
</code></pre>
<p>Ele <em>realmente</em> começa com um <code>#</code>, que geralmente indica um comentário. No entanto, essa linha, na realidade, indica que os arquivos dentro do diretório <code>/etc/sudoers.d</code> também serão fornecidos e aplicados.</p>

<p>Os arquivos dentro desse diretório seguem as mesmas regras do arquivo <code>/etc/sudoers</code> em si. Qualquer arquivo que não termine em <code>~</code>, nem tenha um <code>.</code> nele será lido e anexado à configuração do <code>sudo</code>.</p>

<p>Isso existe principalmente para os aplicativos alterarem privilégios <code>sudo</code> na instalação. Colocar todas as regras associadas dentro de um único arquivo no diretório <code>/etc/sudoers.d</code> pode facilitar a visualização de quais privilégios estão associados a quais contas e reverter credenciais facilmente sem precisar tentar manipular o arquivo <code>/etc/sudoers</code> diretamente.</p>

<p>Assim como o arquivo <code>/etc/sudoers</code>, é recomendado sempre editar arquivos dentro do diretório <code>/etc/sudoers.d</code> com o <code>visudo</code>. A sintaxe para editar esses arquivos seria:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo -f /etc/sudoers.d/<span class="highlight">file_to_edit</span>
</li></ul></code></pre>
<h2 id="como-conceder-privilégios-sudo-a-um-usuário">Como conceder privilégios sudo a um usuário</h2>

<p>A operação mais comum requisitada pelos usuários ao gerenciar permissões <code>sudo</code> é conceder o acesso <code>sudo</code> geral a um novo usuário. Isso é útil se você quiser conceder a uma conta o acesso administrativo total ao sistema.</p>

<p>A maneira mais fácil de fazer isso em um sistema configurado com um grupo de administração de fins gerais, como o sistema Ubuntu neste guia, é adicionando o usuário em questão a esse grupo.</p>

<p>Por exemplo, no Ubuntu 20.04, o grupo <code>sudo</code> possui privilégios completos de administrador. Podemos conceder esses mesmos privilégios a um usuário adicionando ele ao grupo desta forma:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG sudo <span class="highlight">username</span>
</li></ul></code></pre>
<p>O comando <code>gpasswd</code> também pode ser usado:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> sudo
</li></ul></code></pre>
<p>Ambos alcançarão o mesmo resultado.</p>

<p>No CentOS, o grupo em questão é geralmente o <code>wheel</code> ao invés do grupo <code>sudo</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo usermod -aG wheel <span class="highlight">username</span>
</li></ul></code></pre>
<p>Ou, é possível usar o <code>gpasswd</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo gpasswd -a <span class="highlight">username</span> wheel
</li></ul></code></pre>
<p>No CentOS, se adicionar o usuário ao grupo não funcionar imediatamente, pode ser necessário editar o arquivo <code>/etc/sudoers</code> para descomentar o nome do grupo:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
%wheel ALL=(ALL) ALL
. . .
</code></pre>
<h2 id="como-configurar-regras-personalizadas">Como configurar regras personalizadas</h2>

<p>Agora que nos familiarizamos com a sintaxe geral do arquivo, vamos criar algumas novas regras.</p>

<h3 id="como-criar-pseudônimos">Como criar pseudônimos</h3>

<p>O arquivo <code>sudoers</code> pode ser organizado mais facilmente agrupando coisas com vários tipos de “pseudônimos”, ou ”aliases”.</p>

<p>Por exemplo, é possível criar três grupos diferentes de usuários, com associações coincidentes:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
User_Alias      GROUPONE = abby, brent, carl
User_Alias      GROUPTWO = brent, doris, eric,
User_Alias      GROUPTHREE = doris, felicia, grant
. . .
</code></pre>
<p>Os nomes de grupo devem começar com uma letra maiúscula. Então, podemos permitir que membros do <code>GROUPTWO</code> atualizem o banco de dados <code>apt</code> criando uma regra como esta:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = /usr/bin/apt-get update
. . .
</code></pre>
<p>Se não especificarmos um usuário/grupo para executar o comando, como acima, o <code>sudo</code> utiliza o usuário <strong>root</strong> como padrão.</p>

<p>Podemos permitir que membros do <code>GROUPTHREE</code> desliguem e reinicializem a máquina criando um “pseudônimo de comando” e usando ele em uma regra para o <code>GROUPTHREE</code>:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Cmnd_Alias      POWER = /sbin/shutdown, /sbin/halt, /sbin/reboot, /sbin/restart
GROUPTHREE  ALL = POWER
. . .
</code></pre>
<p>Criamos um pseudônimo de comando chamado <code>POWER</code> que contém comandos para desligar e reinicializar a máquina. Em seguida, permitimos que os membros do <code>GROUPTHREE</code> executem esses comandos.</p>

<p>Também podemos criar pseudônimos “Executar como”, que podem substituir a parte da regra que especifica o usuário com o qual será executado o comando:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Runas_Alias     WEB = www-data, apache
GROUPONE    ALL = (WEB) ALL
. . .
</code></pre>
<p>Isso permitirá que qualquer membro do <code>GROUPONE</code> execute comandos como o usuário <code>www-data</code> ou o usuário <code>apache</code>.</p>

<p>Lembre-se apenas que as regras posteriores irão substituir regras mais antigas quando houver um conflito entre elas.</p>

<h3 id="como-bloquear-regras">Como bloquear regras</h3>

<p>Há várias maneiras de ganhar mais controle sobre como o <code>sudo</code> reage a uma chamada.</p>

<p>O comando <code>updatedb</code> associado ao pacote <code>mlocate</code> é relativamente inofensivo em um sistema com um único usuário. Se quisermos permitir que os usuários o executem com privilégios de <strong>root</strong> <em>sem</em> precisar digitar uma senha, podemos criar uma regra como esta:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPONE    ALL = NOPASSWD: /usr/bin/updatedb
. . .
</code></pre>
<p>O <code>NOPASSWD</code> é uma “etiqueta”, que significa que nenhuma senha será solicitada. Ele possui um comando companheiro chamado <code>PASSWD</code>, que é o comportamento padrão. Uma etiqueta é relevante para o resto da regra, a menos que seja anulada por sua etiqueta “gêmea” mais tarde no processo.</p>

<p>Por exemplo, podemos ter uma linha como esta:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
GROUPTWO    ALL = NOPASSWD: /usr/bin/updatedb, PASSWD: /bin/kill
. . .
</code></pre>
<p>Outra etiqueta útil é a <code>NOEXEC</code>, que pode ser usada para evitar um comportamento perigoso em certos programas.</p>

<p>Por exemplo, alguns programas, como o <code>less</code>, podem gerar outros comandos digitando isto a partir de sua interface:</p>
<pre class="code-pre "><code>!<span class="highlight">command_to_run</span>
</code></pre>
<p>Isso executa basicamente qualquer comando dado pelo usuário com as mesmas permissões nas quais <code>less</code> está sendo executado, o que pode ser bastante perigoso.</p>

<p>Para restringir isso, poderíamos usar uma linha como esta:</p>
<div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
<span class="highlight">username</span>  ALL = NOEXEC: /usr/bin/less
. . .
</code></pre>
<h2 id="informações-diversas">Informações diversas</h2>

<p>Há mais algumas informações que podem ser úteis ao lidar com o <code>sudo</code>.</p>

<p>Se você especificou um usuário ou grupo para “executar como” no arquivo de configuração, é possível executar comandos como esses usuários usando os sinalizadores <code>-u</code> e <code>-g</code>, respectivamente:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -u <span class="highlight">run_as_user command</span>
</li><li class="line" data-prefix="$">sudo -g <span class="highlight">run_as_group command</span>
</li></ul></code></pre>
<p>Por conveniência, o <code>sudo</code> salvará por padrão seus detalhes de autenticação por uma certa quantidade de tempo em um terminal. Isso significa que não será necessário digitar sua senha novamente até que esse temporizador se esgote.</p>

<p>Por fins de segurança, se quiser limpar esse temporizador quando terminar de executar comandos administrativos, execute:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li></ul></code></pre>
<p>Se, por outro lado, quiser “preparar” o comando <code>sudo</code> para que você não seja solicitado mais tarde, ou renovar seu lease do <code>sudo</code>, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -v
</li></ul></code></pre>
<p>A sua senha será solicitada e então colocada em cache para utilização posterior do <code>sudo</code>, até que o intervalo de tempo do <code>sudo</code> expire.</p>

<p>Se quiser apenas saber quais tipos de privilégios estão definidos para seu nome de usuário, digite:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -l
</li></ul></code></pre>
<p>Isso irá listar todas as regras no arquivo <code>/etc/sudoers</code> que se aplicam ao seu usuário. Isso dá uma boa ideia do que será ou não permitido fazer com o <code>sudo</code> como qualquer usuário.</p>

<p>Haverá muitas vezes em que você executará um comando e ele falhará por ter esquecido de iniciá-lo com o <code>sudo</code>. Para evitar ter que digitar novamente o comando, utilize uma funcionalidade bash que significa “repetir o último comando”:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo !!
</li></ul></code></pre>
<p>O ponto de exclamação duplo repetirá o último comando. Adicionamos o <code>sudo</code> no início para alterar rapidamente o comando não privilegiado para um comando privilegiado.</p>

<p>Para se divertir um pouco, adicione a linha a seguir ao seu arquivo <code>/etc/sudoers</code> com o <code>visudo</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo visudo
</li></ul></code></pre><div class="code-label " title="/etc/sudoers">/etc/sudoers</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
Defaults    insults
. . .
</code></pre>
<p>Isso fará com que o <code>sudo</code> retorne um insulto bobo quando um usuário digitar uma senha incorreta para o <code>sudo</code>. Podemos usar o <code>sudo -k</code> para limpar a senha <code>sudo</code> anterior em cache para testar essa funcionalidade:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo -k
</li><li class="line" data-prefix="$">sudo ls
</li></ul></code></pre><pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>[sudo] password for demo:    # <span class="highlight">enter an incorrect password here to see the results</span>
Your mind just hasn't been the same since the electro-shock, has it?
[sudo] password for demo:
My mind is going. I can feel it.
</code></pre>
<h2 id="conclusão">Conclusão</h2>

<p>Agora, você deve ter um conhecimento básico sobre como ler e modificar o arquivo <code>sudoers</code>, e uma noção acerca dos vários métodos que podem ser usados para se obter privilégios de <strong>root</strong>.</p>

<p>Lembre-se: os privilégios de superusuário não são dados a usuários comuns por um motivo. É essencial que você compreenda o que cada comando que você executa com privilégios de <strong>root</strong> faz. Não deixe de levar a sério toda a responsabilidade existente. Aprenda a melhor maneira de usar essas ferramentas para o seu caso de uso e bloqueie todas as funcionalidades que não sejam necessárias.</p>
