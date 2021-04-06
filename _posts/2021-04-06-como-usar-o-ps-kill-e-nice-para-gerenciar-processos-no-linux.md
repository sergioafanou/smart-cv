---
title : "Como usar o ps, kill e nice para gerenciar processos no Linux"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux-pt
image: "https://sergio.afanou.com/assets/images/image-midres-44.jpg"
---

<h3 id="introdução">Introdução</h3>

<hr>

<p>Um servidor Linux, assim como qualquer outro computador que você conheça, executa aplicativos.  Para o computador, eles são considerados &ldquo;processos&rdquo;.</p>

<p>Embora o Linux lide nos bastidores com o gerenciamento de baixo nível no ciclo de vida de um processo, você precisará de uma maneira de interagir com o sistema operacional para gerenciá-lo de um nível superior.</p>

<p>Neste guia, vamos discutir alguns aspectos simples do gerenciamento de processos.  O Linux oferece uma coleção abundante de ferramentas para esse propósito.</p>

<p>Vamos explorar essas ideias em um VPS Ubuntu 12.04, mas qualquer distribuição moderna do Linux funcionará de maneira similar.</p>

<h2 id="como-visualizar-processos-em-execução-no-linux">Como visualizar processos em execução no Linux</h2>

<hr>

<h3 id="top">top</h3>

<hr>

<p>A maneira mais fácil de descobrir quais processos estão sendo executados no seu servidor é executando o comando <code>top</code>:</p>
<pre class="code-pre "><code>top***

top - 15:14:40 up 46 min,  1 user,  load average: 0.00, 0.01, 0.05 Tasks:  56 total,   1 running,  55 sleeping,   0 stopped,   0 zombie Cpu(s):  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k total,   316576k used,   703024k free,     7652k buffers Swap:        0k total,        0k used,        0k free,   258976k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND               1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.07 ksoftirqd/0            6 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/0            7 root      RT   0     0    0    0 S  0.0  0.0   0:00.03 watchdog/0             8 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 cpuset                 9 root       0 -20     0    0    0 S  0.0  0.0   0:00.00 khelper               10 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kdevtmpfs          
</code></pre>
<p>A porção superior de informações mostra estatísticas do sistema, como a carga do sistema e o número total de tarefas.</p>

<p>É possível ver facilmente que há 1 processo em execução e 55 processos estão suspensos (ou seja, ociosos/sem utilizar recursos da CPU).</p>

<p>A parte inferior mostra os processos em execução e suas estatísticas de uso.</p>

<h3 id="htop">htop</h3>

<hr>

<p>Uma versão melhorada de <code>top</code>, chamada <code>htop</code>, está disponível nos repositórios.  Instale-o com este comando:</p>
<pre class="code-pre "><code>sudo apt-get install htop
</code></pre>
<p>Se executarmos o comando <code>htop</code>, veremos que as informação são exibidas de uma maneira mais inteligível:</p>
<pre class="code-pre "><code>htop***

  Mem[|||||||||||           49/995MB]     Load average: 0.00 0.03 0.05   CPU[                          0.0%]     Tasks: 21, 3 thr; 1 running   Swp[                         0/0MB]     Uptime: 00:58:11   PID USER      PRI  NI  VIRT   RES   SHR S CPU% MEM%   TIME+  Command  1259 root       20   0 25660  1880  1368 R  0.0  0.2  0:00.06 htop     1 root       20   0 24188  2120  1300 S  0.0  0.2  0:00.56 /sbin/init   311 root       20   0 17224   636   440 S  0.0  0.1  0:00.07 upstart-udev-brid   314 root       20   0 21592  1280   760 S  0.0  0.1  0:00.06 /sbin/udevd --dae   389 messagebu  20   0 23808   688   444 S  0.0  0.1  0:00.01 dbus-daemon --sys   407 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.02 rsyslogd -c5   408 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   409 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.00 rsyslogd -c5   406 syslog     20   0  243M  1404  1080 S  0.0  0.1  0:00.04 rsyslogd -c5   553 root       20   0 15180   400   204 S  0.0  0.0  0:00.01 upstart-socket-br
</code></pre>
<p><a href="https://www.digitalocean.com/community/articles/how-to-use-top-netstat-du-other-tools-to-monitor-server-resources#process">Aprenda mais sobre como usar o top e htop</a> aqui.</p>

<h2 id="como-usar-o-ps-para-listar-processos">Como usar o ps para listar processos</h2>

<hr>

<p>Tanto o <code>top</code> quanto o <code>htop</code> fornecem uma interface agradável para visualizar processos em execução, de maneira semelhante a um gerenciador de tarefas gráfico.</p>

<p>No entanto, essas ferramentas nem sempre são flexíveis o suficiente para abranger adequadamente todos os cenários.  Um comando poderoso chamado <code>ps</code> é geralmente a resposta para esses problemas.</p>

<p>Quando chamado sem argumentos, o resultado pode ser um pouco confuso:</p>
<pre class="code-pre "><code>ps***

  PID TTY          TIME CMD  1017 pts/0    00:00:00 bash  1262 pts/0    00:00:00 ps
</code></pre>
<p>Esse resultado mostra todos os processos associados ao usuário e sessão de terminal atuais.  Isso faz sentido porque estamos executando apenas o <code>bash</code> e <code>ps</code> com esse terminal atualmente.</p>

<p>Para conseguirmos uma visão mais completa dos processos neste sistema, podemos executar o seguinte:</p>
<pre class="code-pre "><code>ps aux***

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND root         1  0.0  0.2  24188  2120 ?        Ss   14:28   0:00 /sbin/init root         2  0.0  0.0      0     0 ?        S    14:28   0:00 [kthreadd] root         3  0.0  0.0      0     0 ?        S    14:28   0:00 [ksoftirqd/0] root         6  0.0  0.0      0     0 ?        S    14:28   0:00 [migration/0] root         7  0.0  0.0      0     0 ?        S    14:28   0:00 [watchdog/0] root         8  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [cpuset] root         9  0.0  0.0      0     0 ?        S&lt;   14:28   0:00 [khelper] . . .
</code></pre>
<p>Essas opções dizem ao <code>ps</code> para mostrar processos de propriedade de todos os usuários (independentemente da sua associação de terminais) em um formato facilmente inteligível.</p>

<p>Para ver um modo de exibição em <em>árvore</em>, onde relações hierárquicas são mostradas, executamos o comando com essas opções:</p>
<pre class="code-pre "><code>ps axjf***

 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND     0     2     0     0 ?           -1 S        0   0:00 [kthreadd]     2     3     0     0 ?           -1 S        0   0:00  \_ [ksoftirqd/0]     2     6     0     0 ?           -1 S        0   0:00  \_ [migration/0]     2     7     0     0 ?           -1 S        0   0:00  \_ [watchdog/0]     2     8     0     0 ?           -1 S&lt;       0   0:00  \_ [cpuset]     2     9     0     0 ?           -1 S&lt;       0   0:00  \_ [khelper]     2    10     0     0 ?           -1 S        0   0:00  \_ [kdevtmpfs]     2    11     0     0 ?           -1 S&lt;       0   0:00  \_ [netns] . . .
</code></pre>
<p>Como se vê, o processo <code>kthreadd</code> é mostrado como um pai do processo <code>ksoftirqd/0</code> e de outros.</p>

<h3 id="uma-nota-sobre-ids-de-processo">Uma nota sobre IDs de processo</h3>

<hr>

<p>No Linux e em sistemas do tipo Unix, cada processo é atribuído a um <strong>ID de processo</strong>, ou <strong>PID</strong>.  É assim que o sistema operacional identifica e mantém o controle dos processos.</p>

<p>Uma maneira rápida de obter o PID de um processo é com o comando <code>pgrep</code>:</p>
<pre class="code-pre "><code>pgrep bash***

1017
</code></pre>
<p>Isso irá simplesmente consultar o ID do processo e retorná-lo.</p>

<p>Ao primeiro processo gerado na inicialização do sistema, chamado <em>init</em>, é atribuído o PID de “1&quot;.</p>
<pre class="code-pre "><code>pgrep init***

1
</code></pre>
<p>Esse processo é então responsável por gerar todos os processos no sistema.  Os processos posteriores recebem números PID maiores.</p>

<p>O <em>pai</em> de um processo é o processo que foi responsável por gerá-lo.  Os processos pais possuem um <strong>PPID</strong>, que pode ser visto no cabeçalho da coluna correspondente em muitos aplicativos de gerenciamento de processos, incluindo o <code>top</code>, <code>htop</code> e <code>ps</code>.</p>

<p>Qualquer comunicação entre o usuário e o sistema operacional em relação aos processos envolve a tradução entre os nomes de processos e PIDs em algum ponto durante a operação.  É por isso que os utilitários informam o PID.</p>

<h3 id="relacionamentos-pai-filho">Relacionamentos pai/filho</h3>

<hr>

<p>A criação de um processo filho acontece em dois passos: fork(), que cria  espaços de endereço e copia os recursos de propriedade do pai via copia em gravação para que fique disponível ao processo filho; e exec(), que carrega um executável no espaço de endereço e o executa.</p>

<p>Caso um processo filho morra antes do seu pai, o filho torna-se um zumbi até que o pai tenha coletado informações sobre ele ou indicado ao kernel que ele não precisa dessas informações.  Os recursos do processo filho serão então libertados.  No entanto, se o processo pai morrer antes do filho, o filho será adotado pelo init, embora também possa ser reatribuído a outro processo.</p>

<h2 id="como-enviar-sinais-de-processos-no-linux">Como enviar sinais de processos no Linux</h2>

<hr>

<p>Todos os processos no Linux respondem a <em>sinais</em>.  Os sinais são uma maneira ao nível de SO de dizer aos programas para terminarem ou modificarem seu comportamento.</p>

<h3 id="como-enviar-sinais-de-processos-por-pid">Como enviar sinais de processos por PID</h3>

<hr>

<p>A maneira mais comum de passar sinais para um programa é com o comando <code>kill</code>.</p>

<p>Como se espera, a funcionalidade padrão desse utilitário é tentar encerrar um processo:</p>

<p>&lt;pre&gt;kill &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>of</em>target_process&lt;/span&gt;&lt;/pre&gt;</p>

<p>Isso envia o sinal <strong>TERM</strong> ao processo.  O sinal TERM pede ao processo para encerrar.  Isso permite que o programa execute operações de limpeza e seja finalizado sem problemas.</p>

<p>Se o programa estiver se comportando incorretamente e não se encerrar quando um sinal TERM for passado, podemos escalar o sinal passando o sinal <code>KILL</code>:</p>

<p>&lt;pre&gt;kill -KILL &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>of</em>target_process&lt;/span&gt;&lt;/pre&gt;</p>

<p>Esse é um sinal especial que não é enviado ao programa.</p>

<p>Ao invés disso, ele é dado ao kernel do sistema operacional, que encerra o processo.  Isso é usado para passar por cima de programas que ignoram os sinais que lhe são enviados.</p>

<p>Cada sinal possui um número associado que pode ser passado ao invés de seu nome.  Por exemplo, é possível passar “-15&quot; ao invés de ”-TERM” e ”-9&quot; ao invés de ”-KILL”.</p>

<h3 id="como-usar-sinais-para-outros-fins">Como usar sinais para outros fins</h3>

<hr>

<p>Os sinais não são usados apenas para encerrar programas.  Eles também podem ser usados para realizar outras ações.</p>

<p>Por exemplo, muitos daemons são reiniciados quando recebem o <code>HUP</code>, ou sinal de desligamento.  O Apache é um programa que funciona dessa forma.</p>

<p>&lt;pre&gt;sudo kill -HUP &lt;span class=&ldquo;highlight&rdquo;&gt;pid<em>of</em>apache&lt;/span&gt;&lt;/pre&gt;</p>

<p>O comando acima fará com que o Apache recarregue seu arquivo de configuração e retome o serviço de conteúdo.</p>

<p>Liste todos os sinais que podem ser enviados com o kill digitando:</p>
<pre class="code-pre "><code>kill -l***

1) SIGHUP    2) SIGINT   3) SIGQUIT  4) SIGILL   5) SIGTRAP  6) SIGABRT  7) SIGBUS   8) SIGFPE   9) SIGKILL 10) SIGUSR1 11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM . . .
</code></pre>
<h3 id="como-enviar-sinais-de-processos-pelo-nome">Como enviar sinais de processos pelo nome</h3>

<hr>

<p>Embora a maneira convencional de enviar sinais seja através do uso de PIDs, também existem métodos para fazer isso com os nomes regulares dos processos.</p>

<p>O comando <code>pkill</code> funciona de maneira bastante similar ao <code>kill</code>, com a diferença de operar com um nome de processo:</p>
<pre class="code-pre "><code>pkill -9 ping
</code></pre>
<p>O comando acima é equivalente a:</p>
<pre class="code-pre "><code>kill -9 `pgrep ping`
</code></pre>
<p>Se quiser enviar um sinal para todas as instâncias de um determinado processo, utilize o comando <code>killall</code>:</p>
<pre class="code-pre "><code>killall firefox
</code></pre>
<p>O comando acima enviará o sinal TERM para todas as instâncias do firefox em execução no computador.</p>

<h2 id="como-ajustar-prioridades-de-processos">Como ajustar prioridades de processos</h2>

<hr>

<p>Geralmente, é desejável ajustar quais processos recebem prioridade em um ambiente de servidor.</p>

<p>Alguns processos podem ser considerados críticos para sua situação, enquanto outros podem ser executados sempre que houver recursos sobrando no sistema.</p>

<p>O Linux controla a prioridade através de um valor chamado <strong>niceness</strong> (gentileza).</p>

<p>As tarefas de alta prioridade são consideradas menos <em>nice</em> (gentis), porque não compartilham recursos tão bem.  Por outro lado, os processos de baixa prioridade são <em>nice</em>, porque insistem em utilizar apenas a menor quantidade de recursos.</p>

<p>Quando executamos o <code>top</code> no início do artigo, havia uma coluna marcada “NI”.  Esse é o valor de <em>gentileza</em> do processo:</p>
<pre class="code-pre "><code>top***

 Tasks:  56 total,   1 running,  55 sleeping,   0 stopped,   0 zombie Cpu(s):  0.0%us,  0.3%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st Mem:   1019600k total,   324496k used,   695104k free,     8512k buffers Swap:        0k total,        0k used,        0k free,   264812k cached   PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND            1635 root      20   0 17300 1200  920 R  0.3  0.1   0:00.01 top                    1 root      20   0 24188 2120 1300 S  0.0  0.2   0:00.56 init                   2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd               3 root      20   0     0    0    0 S  0.0  0.0   0:00.11 ksoftirqd/0
</code></pre>
<p>Os valores de gentileza podem variar entre “-19/-20” (prioridade mais alta) e ”19/20” (prioridade mais baixa), dependendo do sistema.</p>

<p>Para executar um programa com um certo valor de gentileza, podemos usar o comando <code>nice</code>:</p>

<p>&lt;pre&gt;nice -n 15 &lt;span class=&ldquo;highlight&rdquo;&gt;command<em>to</em>execute&lt;/span&gt;&lt;/pre&gt;</p>

<p>Isso funciona apenas ao iniciar um novo programa.</p>

<p>Para alterar o valor de gentileza de um programa que já está sendo executado, usamos uma ferramenta chamada <code>renice</code>:</p>

<p>&lt;pre&gt;renice 0 &lt;span class=&ldquo;highlight&rdquo;&gt;PID<em>to</em>prioritize&lt;/span&gt;&lt;/pre&gt;</p>

<p><strong>Nota: embora o comando nice opere necessariamente com um nome de comando, o renice opera chamando o PID do processo</strong></p>

<h2 id="conclusão">Conclusão</h2>

<hr>

<p>O gerenciamento de processos é um tópico que pode ser de difícil compreensão para novos usuários, pois as ferramentas usadas são diferentes de seus equivalentes gráficos.</p>

<p>No entanto, as ideias são habituais e intuitivas e, com um pouco de prática, se tornarão naturais.  Como os processos estão envolvidos em tudo o que é feito em um sistema de computador, aprender como controlá-los de maneira eficaz é uma habilidade essencial.</p>

<p>&lt;div class=&ldquo;author&rdquo;&gt;Por Justin Ellingwood&lt;/div&gt;</p>
