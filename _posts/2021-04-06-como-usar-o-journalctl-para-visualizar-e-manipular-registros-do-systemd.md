---
title : "Como usar o Journalctl para visualizar e manipular registros do systemd"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs-pt
image: "https://sergio.afanou.com/assets/images/image-midres-13.jpg"
---

<h3 id="introdução">Introdução</h3>

<p>Algumas das vantagens mais interessantes do <code>systemd</code> são aquelas envolvidas com o registro de processos e do sistema.  Ao usar outros sistemas, os registros ficam geralmente dispersos, e são manuseados por daemons e processos diferentes. Isso torna a interpretação deles bastante difícil quando englobam vários aplicativos.  O <code>systemd</code> tenta resolver esses problemas fornecendo uma solução de gerenciamento centralizada para registrar todos os processos do kernel e do userland.  O sistema que coleta e gerencia esses registros é conhecido como journal (diário).</p>

<p>O diário é implementado com o daemon <code>journald</code>, que manuseia todas as mensagens produzidas pelo kernel, initrd, serviços etc.  Neste guia, vamos discutir como utilizar o utilitário <code>journalctl</code>, que pode ser usado para acessar e manipular os dados mantidos dentro do diário.</p>

<h2 id="ideal-geral">Ideal geral</h2>

<p>Um dos motivos da existência do diário do <code>systemd</code> é o de centralizar o gerenciamento de registros independentemente de onde as mensagens estão sendo originadas.  Como uma grande parte do processo de inicialização do sistema e gerenciamento de serviços é manuseada pelo processo <code>systemd</code>, faz sentido padronizar a maneira como os registros são coletados e acessados.  O daemon <code>journald</code> coleta dados de todas as fontes disponíveis e os armazena em um formato binário para uma manipulação fácil e dinâmica.</p>

<p>Isso nos dá várias vantagens significativas.  Ao interagir com os dados usando um único utilitário, os administradores são capazes de exibir dinamicamente dados de registro de acordo com suas necessidades.  Isso pode ser algo simples como visualizar os dados de inicialização de três inicializações de sistema atrás ou combinar as entradas de registro sequencialmente de dois serviços relacionados para consertar um problema de comunicação.</p>

<p>Armazenar os dados de registro em um formato binário também faz com que eles possam ser exibidos em formatos de saída arbitrários, dependendo da sua necessidade naquele momento.  Por exemplo, para o gerenciamento de registros diários, você pode estar acostumado a visualizar os registros no formato <code>syslog</code> padrão. No entanto, se você quiser representar interrupções de serviço em gráficos mais tarde, é possível gerar um objeto JSON para cada entrada para que seja consumível pelo seu serviço de criação de gráficos.  Como os dados não são escritos no disco em texto simples, nenhuma conversão é necessária quando houver a demanda por um formato diferente.</p>

<p>O diário do <code>systemd</code> pode ser usado com uma implementação do <code>syslog</code> existente, ou pode substituir a funcionalidade <code>syslog</code>, dependendo das suas necessidades.  Embora o diário do <code>systemd</code> atenda a maioria das necessidades de registro de administrador, ele também pode complementar mecanismos de registro existentes.  Por exemplo, pode ser que você tenha um servidor <code>syslog</code> centralizado que usa para compilar dados de vários servidores, mas também queira intercalar os registros de vários serviços em um único sistema com o diário do <code>systemd</code>.  É possível fazer as duas coisas combinando essas tecnologias.</p>

<h2 id="configurando-o-horário-do-sistema">Configurando o horário do sistema</h2>

<p>Um dos benefícios do uso de um diário binário para registro é a capacidade de visualizar registros em UTC ou em seu horário local à vontade.  Por padrão, o <code>systemd</code> irá exibir resultados no horário local.</p>

<p>Por conta disso, antes de começarmos com o diário, vamos garantir que o fuso horário esteja configurado corretamente.  O pacote do <code>systemd</code> vem com uma ferramenta chamada <code>timedatectl</code> que pode ajudar com isso.</p>

<p>Primeiramente, consulte quais fusos horários estão disponíveis com a opção <code>list-timezones</code>:</p>
<pre class="code-pre "><code>timedatectl list-timezones
</code></pre>
<p>Isso irá listar os fusos horários disponíveis no seu sistema.  Depois de encontrar aquele que corresponde ao local do seu servidor, defina-o usando a opção <code>set-timezone</code>:</p>
<pre class="code-pre "><code>sudo timedatectl set-timezone <span class="highlight">zone</span>
</code></pre>
<p>Para garantir que sua máquina esteja usando o horário correto neste momento, use o comando <code>timedatectl</code> sozinho, ou com a opção <code>status</code>.  O resultado será o mesmo:</p>
<pre class="code-pre "><code>timedatectl status
</code></pre><pre class="code-pre "><code>      <span class="highlight">Local time: Thu 2015-02-05 14:08:06 EST</span>
  Universal time: Thu 2015-02-05 19:08:06 UTC
        RTC time: Thu 2015-02-05 19:08:06
       Time zone: America/New_York (EST, -0500)
     NTP enabled: no
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
</code></pre>
<p>A primeira linha deve exibir o horário correto.</p>

<h2 id="visualização-básica-de-registros">Visualização básica de registros</h2>

<p>Para ver os registros que o daemon do <code>journald</code> coletou, use o comando <code>journalctl</code>.</p>

<p>Quando usado sozinho, todas as entradas no diário que estão no sistema serão exibidas dentro de um pager (geralmente <code>less</code> (menos)) para você navegar.  As entradas mais antigas estarão no topo:</p>
<pre class="code-pre "><code>journalctl
</code></pre><pre class="code-pre "><code>-- Logs begin at Tue 2015-02-03 21:48:52 UTC, end at Tue 2015-02-03 22:29:38 UTC. --
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journal[243]: Runtime journal is using 6.2M (max allowed 49.
Feb 03 21:48:52 localhost.localdomain systemd-journald[139]: Received SIGTERM from PID 1 (systemd).
Feb 03 21:48:52 localhost.localdomain kernel: audit: type=1404 audit(1423000132.274:2): enforcing=1 old_en
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: SELinux: 2048 avtab hash slots, 104131 rules.
Feb 03 21:48:52 localhost.localdomain kernel: input: ImExPS/2 Generic Explorer Mouse as /devices/platform/
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  8 users, 102 roles, 4976 types, 294 bools, 1 sens,
Feb 03 21:48:52 localhost.localdomain kernel: SELinux:  83 classes, 104131 rules

. . .
</code></pre>
<p>É provável que você tenha páginas e páginas de dados para percorrer, que podem ser dezenas ou centenas de milhares de linhas se o <code>systemd</code> estiver em seu sistema há bastante tempo.  Isso demonstra a quantidade de dados que está disponível no banco de dados do diário.</p>

<p>O formato será conhecido por aqueles que estão acostumados com o registro padrão do <code>syslog</code>.  No entanto, este processo coleta dados de mais fontes do que as implementações tradicionais do <code>syslog</code> são capazes de fazer.  Ele inclui registros do processo de inicialização inicial, do kernel, do initrd e do erro padrão de aplicativo e mais.  Todos eles estão disponíveis no diário.</p>

<p>Note que todos os carimbos de data/hora estão sendo exibidos no horário local.  Isso está disponível para toda entrada de registro agora que temos nosso horário local configurado corretamente em nosso sistema.  Todos os registros são exibidos usando essas novas informações.</p>

<p>Se quiser exibir os carimbos de data/hora em UTC, use o sinalizador <code>--utc</code>:</p>
<pre class="code-pre "><code>journalctl --utc
</code></pre>
<h2 id="filtrar-o-diário-pela-hora">Filtrar o diário pela hora</h2>

<p>Embora ter acesso a uma grande coleção de dados seja definitivamente útil, essa grande quantidade de informações pode ser difícil ou impossível de ser inspecionada e processada mentalmente.  Por conta disso, uma das características mais importantes do <code>journalctl</code> é suas opções de filtragem.</p>

<h3 id="exibir-registros-da-inicialização-atual">Exibir registros da inicialização atual</h3>

<p>A opção mais básica existente que pode ser usada diariamente, é o sinalizador <code>-b</code>. Ele irá mostrar todas as entradas do diário que foram coletadas desde a reinicialização mais recente.</p>
<pre class="code-pre "><code>journalctl -b
</code></pre>
<p>Isso ajudará você a identificar e gerenciar informações pertinentes ao seu ambiente atual.</p>

<p>Nos casos em que você não estiver usando esse recurso e estiver exibindo mais de um dia de inicializações, verá que o <code>journalctl</code> insere uma linha que se parece com esta, sempre que o sistema foi desligado:</p>
<pre class="code-pre "><code>. . .

-- Reboot --

. . .
</code></pre>
<p>Isso pode ser usado para ajudar a separar as informações em sessões de inicialização de maneira lógica.</p>

<h3 id="inicializações-passadas">Inicializações passadas</h3>

<p>Embora seja comum querer exibir as informações da inicialização atual, certamente existem momentos em que as inicializações passadas também podem ser úteis.  O diário pode salvar informações de várias inicializações anteriores, de modo que o <code>journalctl</code> pode ser usado para exibir essas informações facilmente.</p>

<p>Algumas distribuições habilitam o salvamento de informações de inicializações anteriores por padrão, enquanto outras desativam esse recurso.  Para habilitar as informações de inicialização persistentes, crie o diretório para armazenar o diário digitando:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir -p /var/log/journal
</li></ul></code></pre>
<p>Ou edite o arquivo de configuração do diário:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/systemd/journald.conf
</li></ul></code></pre>
<p>Na seção <code>[Journal]</code>, defina a opção <code>Storage=</code> como &ldquo;persistent&rdquo; para habilitar o registro persistente:</p>
<div class="code-label " title="/etc/systemd/journald.conf">/etc/systemd/journald.conf</div><pre class="code-pre "><code class="code-highlight language-bash">. . .
[Journal]
Storage=<span class="highlight">persistent</span>
</code></pre>
<p>Quando o salvamento de inicializações anteriores está habilitado no seu servidor, o <code>journalctl</code> fornece alguns comandos para ajudar a trabalhar com as inicializações como uma unidade de divisão.  Para ver as inicializações das quais o <code>journald</code> tem conhecimento, use a opção <code>--list-boots</code> com o <code>journalctl</code>:</p>
<pre class="code-pre "><code>journalctl --list-boots
</code></pre><pre class="code-pre "><code>-2 caf0524a1d394ce0bdbcff75b94444fe Tue 2015-02-03 21:48:52 UTC—Tue 2015-02-03 22:17:00 UTC
-1 13883d180dc0420db0abcb5fa26d6198 Tue 2015-02-03 22:17:03 UTC—Tue 2015-02-03 22:19:08 UTC
 0 bed718b17a73415fade0e4e7f4bea609 Tue 2015-02-03 22:19:12 UTC—Tue 2015-02-03 23:01:01 UTC
</code></pre>
<p>Isso irá exibir uma linha para cada inicialização.  A primeira coluna é o deslocamento da inicialização em relação à atual que pode ser usado para identificá-la com facilidade com o <code>journalctl</code>.  Se precisar de uma referência absoluta, o ID de inicialização está na segunda coluna.  É possível identificar o tempo da sessão com as duas especificações de data/hora no final da linha.</p>

<p>Para exibir informações dessas inicializações, use as informações da primeira ou segunda coluna.</p>

<p>Por exemplo, para ver o diário da inicialização anterior, use o ponteiro relativo <code>-1</code> com o sinalizador <code>-b</code>:</p>
<pre class="code-pre "><code>journalctl -b -1
</code></pre>
<p>Também é possível usar o ID de inicialização para retornar os dados de uma inicialização específica:</p>
<pre class="code-pre "><code>journalctl -b caf0524a1d394ce0bdbcff75b94444fe
</code></pre>
<h3 id="intervalos-de-tempo">Intervalos de tempo</h3>

<p>Embora a visualização de entradas de registro com base na inicialização seja incrivelmente útil, muitas vezes é desejável solicitar intervalos de tempo que não se alinham com as inicializações do sistema.  Isso pode ser especialmente válido ao lidar com servidores de execução prolongada com um tempo de atividade significativo.</p>

<p>É possível filtrar por limites arbitrários de data/hora usando as opções <code>--since</code> e <code>--until</code>, que restringem as entradas exibidas a aquelas que sucedem ou antecedem um determinado tempo, respectivamente.</p>

<p>Os valores de data/hora podem ser usados em uma variedade de formatos.  Para valores de data/hora absolutos, use o seguinte formato:</p>
<pre class="code-pre "><code>YYYY-MM-DD HH:MM:SS
</code></pre>
<p>Por exemplo, podemos ver todas as entradas desde 10 de janeiro de 2015 às 5:15 PM digitando:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10 17:15:00"
</code></pre>
<p>Se algum componente do formato acima for deixado de fora, o padrão será aplicado.  Por exemplo, se a data for omitida, a data atual será empregada.  Se o componente de hora estiver faltando, &ldquo;00:00:00&rdquo; (meia-noite) será utilizado.  O campo de segundos também pode ser deixado de fora e o padrão &ldquo;00&rdquo; é empregado:</p>
<pre class="code-pre "><code>journalctl --since "2015-01-10" --until "2015-01-11 03:00"
</code></pre>
<p>O diário também compreende alguns valores relativos e seus atalhos nomeados.  Por exemplo, é possível usar as palavras &ldquo;yesterday&rdquo; (ontem), &ldquo;today&rdquo; (hoje), &ldquo;tomorrow&rdquo; (amanhã) ou &ldquo;now&rdquo; (agora).  É possível criar datas/horas relativas prefixando &ldquo;-&rdquo; ou &ldquo;+&rdquo; a um valor numerado ou usando palavras como &ldquo;ago&rdquo; (atrás) em uma construção de sentenças.</p>

<p>Para obter os dados de ontem, pode-se digitar:</p>
<pre class="code-pre "><code>journalctl --since yesterday
</code></pre>
<p>Se tiver recebido relatórios de uma interrupção de serviço iniciada às 9:00 AM que durou até uma hora atrás, você pode digitar:</p>
<pre class="code-pre "><code>journalctl --since 09:00 --until "1 hour ago"
</code></pre>
<p>Como se vê, é relativamente fácil definir intervalos flexíveis de tempo para filtrar as entradas que você deseja visualizar.</p>

<h2 id="filtrar-por-interesse-de-mensagens">Filtrar por interesse de mensagens</h2>

<p>Aprendemos acima algumas maneiras de filtrar os dados do diário usando restrições de tempo.  Nesta seção, vamos discutir como filtrar com base em qual serviço ou componente você está interessado.  O diário do <code>systemd</code> oferece diversas maneiras de fazer isso.</p>

<h3 id="por-unidade">Por unidade</h3>

<p>Talvez a maneira mais útil de filtrar seja pela unidade na qual você está interessado.  Podemos usar a opção <code>-u</code> para filtrar dessa maneira.</p>

<p>Por exemplo, para ver todos os registros de uma unidade Nginx em nosso sistema, digitamos:</p>
<pre class="code-pre "><code>journalctl -u nginx.service
</code></pre>
<p>Normalmente, também seria interessante filtrar pela data/hora para exibir as linhas nas quais você está interessado.  Por exemplo, para verificar como o serviço está funcionando hoje, digite:</p>
<pre class="code-pre "><code>journalctl -u nginx.service --since today
</code></pre>
<p>Esse tipo de foco torna-se extremamente útil quando se aproveita da capacidade do diário de intercalar os registros de várias unidades.  Por exemplo, se seu processo Nginx estiver conectado a uma unidade PHP-FPM para processar conteúdo dinâmico, é possível fundir as entradas de ambos em ordem cronológica especificando ambas as unidades:</p>
<pre class="code-pre "><code>journalctl -u nginx.service -u php-fpm.service --since today
</code></pre>
<p>Isso pode facilitar a detecção de interações entre diferentes programas e sistemas de depuração, em vez de processos individuais.</p>

<h3 id="por-processo-usuário-ou-id-de-grupo">Por processo, usuário ou ID de grupo</h3>

<p>Alguns serviços geram uma variedade de processos filhos para funcionar.  Se você tiver pesquisado o PID exato do processo em que está interessado, também é possível filtrar por ele.</p>

<p>Para fazer isso, especificamos o campo <code>_PID</code>.  Por exemplo, se o PID em que estivermos interessados for 8088, digitamos:</p>
<pre class="code-pre "><code>journalctl _PID=8088
</code></pre>
<p>Em outros momentos, pode ser desejável exibir todas as entradas registradas a partir de um usuário ou grupo específico.  Isso pode ser feito com os filtros <code>_UID</code> ou <code>_GID</code>.  Por exemplo, se seu servidor Web estiver sendo executado sob o usuário <code>www-data</code>, é possível encontrar o ID do usuário digitando:</p>
<pre class="code-pre "><code>id -u www-data
</code></pre><pre class="code-pre "><code>33
</code></pre>
<p>Depois disso, use o ID retornado para filtrar os resultados do diário:</p>
<pre class="code-pre "><code>journalctl _UID=<span class="highlight">33</span> --since today
</code></pre>
<p>O diário do <code>systemd</code> possui muitos campos que podem ser usados para a filtragem.  Alguns deles são passados do processo que está sendo registrado e alguns são aplicados pelo <code>journald</code> usando informações que ele coleta do sistema no momento do registro.</p>

<p>O sublinhado inicial indica que o campo <code>_PID</code> pertence ao segundo tipo.  O diário registra e classifica automaticamente o PID do processo que está sendo registrado para a filtragem posterior.  É possível descobrir todos os campos de diário disponíveis digitando:</p>
<pre class="code-pre "><code>man systemd.journal-fields
</code></pre>
<p>Vamos discutir alguns deles neste guia.  Por enquanto, vamos analisar mais uma opção útil relacionada com a filtragem por esses campos.  A opção <code>-F</code> pode ser usada para mostrar todos os valores disponíveis para um campo de diário específico.</p>

<p>Por exemplo, para ver quais entradas para IDs de grupo o diário do <code>systemd</code> possui, digite:</p>
<pre class="code-pre "><code>journalctl -F _GID
</code></pre><pre class="code-pre "><code>32
99
102
133
81
84
100
0
124
87
</code></pre>
<p>Isso irá mostrar todos os valores que o diário armazenou para o campo ID de grupo.  Isso pode ajudar a construir seus filtros.</p>

<h3 id="por-caminho-de-componente">Por caminho de componente</h3>

<p>Também podemos filtrar fornecendo um caminho de pesquisa.</p>

<p>Se o caminho levar a um executável, o <code>journalctl</code> irá exibir todas as entradas relacionadas ao executável em questão.  Por exemplo, para encontrar essas entradas que estão relacionadas ao executável <code>bash</code>, digite:</p>
<pre class="code-pre "><code>journalctl /usr/bin/bash
</code></pre>
<p>Normalmente, se uma unidade estiver disponível para o executável, esse método é mais organizado e fornece informações mais completas (entradas de processos filhos associados, etc).  Às vezes, no entanto, isso não é possível.</p>

<h3 id="exibir-mensagens-de-kernel">Exibir mensagens de kernel</h3>

<p>As mensagens de kernel, que são aquelas geralmente encontradas na saída do <code>dmesg</code>, também podem ser recuperadas do diário.</p>

<p>Para exibir apenas essas mensagens, podemos adicionar os sinalizadores <code>-k</code> ou <code>--dmesg</code> ao nosso comando:</p>
<pre class="code-pre "><code>journalctl -k
</code></pre>
<p>Por padrão, isso irá exibir as mensagens do kernel da inicialização atual.  É possível especificar uma outra inicialização usando os sinalizadores de seleção de inicialização discutidos anteriormente.  Por exemplo, para obter as mensagens de cinco inicializações atrás, digite:</p>
<pre class="code-pre "><code>journalctl -k -b -5
</code></pre>
<h3 id="por-prioridade">Por prioridade</h3>

<p>Um filtro no qual os administradores de sistema estão geralmente interessados é a prioridade de mensagem.  Embora seja muitas vezes útil registrar informações em um nível bastante detalhado, ao resumir as informações disponíveis, o registro de baixa prioridade pode ser confuso.</p>

<p>É possível usar o <code>journalctl</code> para exibir apenas mensagens de uma prioridade especificada ou superior usando a opção <code>-p</code>.  Isso permite filtrar mensagens com níveis de prioridade inferiores.</p>

<p>Por exemplo, para mostrar apenas entradas registradas no nível de erro ou acima, digite:</p>
<pre class="code-pre "><code>journalctl -p err -b
</code></pre>
<p>Isso irá exibir todas as mensagens marcadas como erro, crítico, alerta ou emergência.  O diário implementa os níveis de mensagem padrão do <code>syslog</code>.  É possível usar o nome da prioridade ou seu valor numérico correspondente.  As prioridades, ordenadas da maior para a menor, são:</p>

<ul>
<li>0: emerg</li>
<li>1: alert</li>
<li>2: crit</li>
<li>3: err</li>
<li>4: warning</li>
<li>5: notice</li>
<li>6: info</li>
<li>7: debug</li>
</ul>

<p>Os números ou nomes acima podem ser usados de maneira intercambiável com a <code>-p</code> opção.  A seleção de uma prioridade irá exibir mensagens marcadas no nível especificado e acima.</p>

<h2 id="modificar-a-exibição-do-diário">Modificar a exibição do diário</h2>

<p>Acima, demonstramos a seleção de entradas através da filtragem.  No entanto, existem outras maneiras com as quais podemos modificar o resultado.  Podemos ajustar a exibição do <code>journalctl</code> para atender diversas necessidades.</p>

<h3 id="truncar-ou-expandir-o-resultado">Truncar ou expandir o resultado</h3>

<p>Podemos ajustar como o <code>journalctl</code> exibe os dados, dizendo-lhe para reduzir ou expandir o resultado.</p>

<p>Por padrão, o <code>journalctl</code> irá mostrar a entrada inteira no pager, permitindo que as entradas se expandam à direita da tela.  Essa informação pode ser acessada pressionando a tecla de seta para a direita.</p>

<p>Se preferir o resultado truncado, inserindo reticências onde as informações foram removidas, use a opção <code>--no-full</code>:</p>
<pre class="code-pre "><code>journalctl --no-full
</code></pre><pre class="code-pre "><code>. . .

Feb 04 20:54:13 journalme sshd[937]: Failed password for root from 83.234.207.60...h2
Feb 04 20:54:13 journalme sshd[937]: Connection closed by 83.234.207.60 [preauth]
Feb 04 20:54:13 journalme sshd[937]: PAM 2 more authentication failures; logname...ot
</code></pre>
<p>Também é possível ir na direção oposta e dizer ao <code>journalctl</code> para exibir todas as suas informações, mesmo se forem incluídos caracteres não imprimíveis.  Podemos fazer isso com o sinalizador <code>-a</code>:</p>
<pre class="code-pre "><code>journalctl -a
</code></pre>
<h3 id="resultados-em-formato-padrão">Resultados em formato padrão</h3>

<p>Por padrão, o <code>journalctl</code> exibe o resultado em um pager para um consumo mais simples.  No entanto, se você estiver planejando processar os dados com ferramentas de manipulação de texto, irá querer ser capaz de gerar resultados no formato padrão.</p>

<p>Faça isso com a opção <code>--no-pager</code>:</p>
<pre class="code-pre "><code>journalctl --no-pager
</code></pre>
<p>Isso pode ser canalizado imediatamente em um utilitário de processamento ou redirecionado para um arquivo no disco, dependendo das suas necessidades.</p>

<h3 id="formatos-de-saída">Formatos de saída</h3>

<p>Se você estiver processando entradas de diário, como mencionado acima, provavelmente será mais fácil analisar os dados se eles estiverem em um formato mais consumível.  Felizmente, o diário pode ser exibido em uma variedade de formatos conforme a necessidade.  Faça isso usando a opção <code>-o</code> com um especificador de formato.</p>

<p>Por exemplo, gere um arquivo JSON a partir de entradas no diário digitando:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json
</code></pre><pre class="code-pre "><code>{ "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635", "__REALTIME_TIMESTAMP" : "1422990364739502", "__MONOTONIC_TIMESTAMP" : "27200938", "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d", "PRIORITY" : "6", "_UID" : "0", "_GID" : "0", "_CAP_EFFECTIVE" : "3fffffffff", "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee", "_HOSTNAME" : "desktop", "SYSLOG_FACILITY" : "3", "CODE_FILE" : "src/core/unit.c", "CODE_LINE" : "1402", "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading", "SYSLOG_IDENTIFIER" : "systemd", "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5", "_TRANSPORT" : "journal", "_PID" : "1", "_COMM" : "systemd", "_EXE" : "/usr/lib/systemd/systemd", "_CMDLINE" : "/usr/lib/systemd/systemd", "_SYSTEMD_CGROUP" : "/", "UNIT" : "nginx.service", "MESSAGE" : "Starting A high performance web server and a reverse proxy server...", "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973" }

. . .
</code></pre>
<p>Isso é útil para análise com utilitários.  Você poderia usar o formato <code>json-pretty</code> para ter uma melhor ideia melhor da estrutura de dados antes de passá-lo para o consumidor de JSON:</p>
<pre class="code-pre "><code>journalctl -b -u nginx -o json-pretty
</code></pre><pre class="code-pre "><code>{
    "__CURSOR" : "s=13a21661cf4948289c63075db6c25c00;i=116f1;b=81b58db8fd9046ab9f847ddb82a2fa2d;m=19f0daa;t=50e33c33587ae;x=e307daadb4858635",
    "__REALTIME_TIMESTAMP" : "1422990364739502",
    "__MONOTONIC_TIMESTAMP" : "27200938",
    "_BOOT_ID" : "81b58db8fd9046ab9f847ddb82a2fa2d",
    "PRIORITY" : "6",
    "_UID" : "0",
    "_GID" : "0",
    "_CAP_EFFECTIVE" : "3fffffffff",
    "_MACHINE_ID" : "752737531a9d1a9c1e3cb52a4ab967ee",
    "_HOSTNAME" : "desktop",
    "SYSLOG_FACILITY" : "3",
    "CODE_FILE" : "src/core/unit.c",
    "CODE_LINE" : "1402",
    "CODE_FUNCTION" : "unit_status_log_starting_stopping_reloading",
    "SYSLOG_IDENTIFIER" : "systemd",
    "MESSAGE_ID" : "7d4958e842da4a758f6c1cdc7b36dcc5",
    "_TRANSPORT" : "journal",
    "_PID" : "1",
    "_COMM" : "systemd",
    "_EXE" : "/usr/lib/systemd/systemd",
    "_CMDLINE" : "/usr/lib/systemd/systemd",
    "_SYSTEMD_CGROUP" : "/",
    "UNIT" : "nginx.service",
    "MESSAGE" : "Starting A high performance web server and a reverse proxy server...",
    "_SOURCE_REALTIME_TIMESTAMP" : "1422990364737973"
}

. . .
</code></pre>
<p>Os formatos a seguir podem ser usados para exibição:</p>

<ul>
<li><strong>cat</strong>: exibe apenas o campo de mensagem em si.</li>
<li><strong>export</strong>: um formato binário adequado para transferir e fazer um backup.</li>
<li><strong>json</strong>: JSON padrão com uma entrada por linha.</li>
<li><strong>json-pretty</strong>: JSON formatado para uma melhor legibilidade humana</li>
<li><strong>json-sse</strong>: saída formatada em JSON agrupada para tornar um evento enviado ao servidor compatível</li>
<li><strong>short</strong>: o estilo de saída padrão do <code>syslog</code></li>
<li><strong>short-iso</strong>: o formato padrão aumentado para mostrar as carimbos de data/hora da ISO 8601.</li>
<li><strong>short-monotonic</strong>: o formato padrão com carimbos de data/hora monotônicos.</li>
<li><strong>short-precise</strong>: o formato padrão com precisão de microssegundos</li>
<li><strong>verbose</strong>: exibe todas os campos de diário disponíveis para a entrada, incluindo aqueles que geralmente estão escondidos internamente.</li>
</ul>

<p>Essas opções permitem exibir as entradas do diário no formato que melhor atende às suas necessidades atuais.</p>

<h2 id="monitoramento-de-processo-ativo">Monitoramento de processo ativo</h2>

<p>O comando <code>journalctl</code> imita a forma como muitos administradores usam <code>tail</code> para monitorar atividades ativas ou recentes.  Essa funcionalidade está embutida no <code>journalctl</code>, permitindo acessar esses recursos sem precisar utilizar uma outra ferramenta.</p>

<h3 id="exibir-registros-recentes">Exibir registros recentes</h3>

<p>Para exibir uma quantidade definida de registros, use a opção <code>-n</code>, que funciona exatamente como <code>tail -n</code>.</p>

<p>Por padrão, ele irá exibir as 10 entradas mais recentes:</p>
<pre class="code-pre "><code>journalctl -n
</code></pre>
<p>Especifique o número de entradas que quer ver com um número após o <code>-n</code>:</p>
<pre class="code-pre "><code>journalctl -n 20
</code></pre>
<h3 id="acompanhar-registros">Acompanhar registros</h3>

<p>Para acompanhar ativamente os registros à medida que são escritos, use o sinalizador <code>-f</code>.  Novamente, isso funciona como o esperado, caso você tenha experiência usando o <code>tail -f</code>:</p>
<pre class="code-pre "><code>journalctl -f
</code></pre>
<h2 id="manutenção-do-diário">Manutenção do diário</h2>

<p>Você deve estar se perguntando sobre o custo do armazenamento de todos os dados que vimos até agora.  Além disso, você pode estar interessado em limpar alguns registros mais antigos e liberar o espaço.</p>

<h3 id="descobrir-o-uso-atual-em-disco">Descobrir o uso atual em disco</h3>

<p>É possível descobrir a quantidade de espaço que diário está ocupando no disco usando o sinalizador <code>--disk-usage</code>:</p>
<pre class="code-pre "><code>journalctl --disk-usage
</code></pre><pre class="code-pre "><code>Journals take up 8.0M on disk.
</code></pre>
<h3 id="deletar-registros-antigos">Deletar registros antigos</h3>

<p>Se quiser reduzir o tamanho do seu diário, você pode fazer isso de duas maneiras diferentes (disponível com a versão 218 do <code>systemd</code> ou superior).</p>

<p>Usando a opção <code>--vacuum-size</code>, você pode reduzir o tamanho do diário indicando um tamanho.  Isso irá remover entradas antigas até o espaço total que o diário ocupa em disco esteja no tamanho solicitado:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-size=1G
</code></pre>
<p>Outra maneira para reduzir o tamanho do diário é fornecendo um tempo de corte com a opção <code>--vacuum-time</code>.  Todas as entradas além daquele momento são excluídas.  Isso permite manter as entradas que foram criadas após um tempo específico.</p>

<p>Por exemplo, para manter as entradas do último ano, digite:</p>
<pre class="code-pre "><code>sudo journalctl --vacuum-time=1years
</code></pre>
<h3 id="limitar-a-expansão-do-diário">Limitar a expansão do diário</h3>

<p>É possível configurar seu servidor para colocar limites sobre a quantidade de espaço que diário pode ocupar.  Isso pode ser feito editando o arquivo <code>/etc/systemd/journald.conf</code>.</p>

<p>Os itens a seguir podem ser usados para limitar o crescimento do diário:</p>

<ul>
<li><strong><code>SystemMaxUse=</code></strong>: especifica o espaço máximo em disco que pode ser usado pelo diário em armazenamento persistente.</li>
<li><strong><code>SystemKeepFree=</code></strong>: especifica a quantidade de espaço que o diário deve deixar livre no armazenamento persistente ao adicionar entradas.</li>
<li><strong><code>SystemMaxFileSize=</code></strong>: controla o tamanho máximo até o qual os arquivos de diário individuais podem crescer em armazenamento persistente antes de serem girados.</li>
<li><strong><code>RuntimeMaxUse=</code></strong>: especifica o espaço máximo em disco que pode ser usado como armazenamento volátil (dentro do sistema de arquivos <code>/run</code>).</li>
<li><strong><code>RuntimeKeepFree=</code></strong>: especifica a quantidade de espaço a ser reservada para outros usos ao escrever dados no armazenamento volátil (dentro do sistema de arquivos <code>/run</code>).</li>
<li><strong><code>RuntimeMaxFileSize=</code></strong>: especifica a quantidade de espaço que um arquivo de diário individual pode ocupar em armazenamento volátil (dentro do sistema de arquivos <code>/run</code>) antes de ser girado.</li>
</ul>

<p>Ao definir esses valores, você pode controlar como o <code>journald</code> consome e preserva o espaço no seu servidor.  Tenha em mente que o <strong><code>SystemMaxFileSize</code></strong> e o <strong><code>RuntimeMaxFileSize</code></strong> irão mirar em arquivos arquivados para alcançar os limites declarados. Isso é importante de lembrar ao interpretar contagens de arquivos após uma operação de limpeza.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Como foi visto, o diário do <code>systemd</code> é incrivelmente útil para coletar e gerenciar seus dados de sistema e de aplicativo.  A maior parte da sua flexibilidade vem da grande quantidade de metadados que são registrados automaticamente e da natureza centralizada do registro.  O comando <code>journalctl</code> torna mais fácil aproveitar as funcionalidades avançadas do diário e fazer análises extensas e depuração relacional de diferentes componentes de aplicativos.</p>
