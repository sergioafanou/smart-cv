---
title : "Como usar o servidor SMTP do Google"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server-pt
image: "https://sergio.afanou.com/assets/images/image-midres-54.jpg"
---

<p><em>O autor selecionou a <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> para receber uma doação como parte do programa <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introdução">Introdução</h3>

<p>Um recurso pouco conhecido sobre o Gmail e o e-mail do Google Apps é o servidor SMTP portátil do Google. Ao invés de ter que gerenciar seu próprio servidor de e-mail de saída em seu <a href="https://www.digitalocean.com/products/droplets/">Droplet da DigitalOcean</a> ou <a href="https://try.digitalocean.com/kubernetes-in-minutes/?utm_campaign=amer_brand_kw_en_cpc&amp;utm_adgroup=digitalocean_kubernetes_exact&amp;_keyword=digitalocean%20kubernetes&amp;_device=c&amp;_adposition=&amp;utm_medium=cpc&amp;utm_source=google&amp;gclid=EAIaIQobChMImcKuus2e7AIVhY5bCh1otQGSEAAYAiAAEgJEu_D_BwE">Cluster Kubernetes</a>, é possível definir as configurações do servidor SMTP do Google em qualquer script ou programa do qual você queira enviar e-mails. Tudo o que você precisa é (i) uma conta Gmail gratuita, ou (ii) uma conta G Suite paga.</p>

<h2 id="benefícios">Benefícios</h2>

<p>Você tem a opção de deixar o Google lidar com a armazenagem e indexação dos e-mails enviados através do seu servidor SMTP, de forma que todos os seus e-mails enviados sejam pesquisáveis e tenham cópias de backup salvas nos servidores do Google. Se optar por também usar sua conta Gmail ou G Suite para os e-mails de entrada, você terá todos os seus e-mails em um lugar conveniente. Além disso, como o servidor SMTP do Google não usa a porta 25, a probabilidade de um ISP bloquear algum e-mail seu ou sinalizá-lo como spam será reduzida.</p>

<h2 id="configurações">Configurações</h2>

<p>O servidor SMTP do Google exige autenticação, então aqui está como configurá-la em seu cliente ou aplicativo de e-mail:</p>

<p><span class='note'><strong>Nota:</strong> Antes de começar, considere investigar a classificação de segurança do seu cliente ou aplicativo de e-mail, de acordo com o Google. Se você estiver usando um programa que o Google não considera seguro, seu uso será bloqueado a menos que você habilite aplicativos menos seguros (uma configuração de segurança que o Google não recomenda) ou gere uma senha de aplicativo específica do aplicativo. <a href="https://support.google.com/accounts/answer/6010255?hl=en">Para obter mais informações de segurança, consulte este link</a> para determinar a melhor abordagem para seu cliente ou aplicativo de e-mail.<br></span></p>

<ol>
<li>Servidor SMTP (ou seja, servidor de e-mail de saída): <strong><a href="http://smtp.gmail.com">smtp.gmail.com</a></strong></li>
<li>Nome de usuário SMTP: <strong>seu endereço de e-mail completo do Gmail ou G Suite</strong> (por exemplo, <code><span class="highlight">example@gmail.com</span></code> ou <code><span class="highlight">example@your_domain</span></code>)</li>
<li>Senha SMTP: <code><span class="highlight">A senha do seu e-mail Gmail ou G Suite </span></code></li>
<li>Porta SMTP: <code>465</code></li>
<li><strong>TLS/SSL necessário</strong>: <strong>sim</strong> do SMTP</li>
</ol>

<p>Para que o Google copie automaticamente seus e-mails enviados para a pasta enviados, também é necessário verificar se o acesso IMAP está habilitado para sua conta.</p>

<p>Para fazer isso, vá para as configurações do Gmail e clique na guia <strong>Encaminhamento e POP/IMAP</strong>. Role para baixo até a seção <strong>Acesso IMAP</strong> e certifique-se de que o acesso IMAP está habilitado para sua conta.</p>

<span class='note'><p>
<strong>Nota:</strong> o Google irá substituir automaticamente a linha <strong>De</strong> de qualquer e-mail que você enviar através do seu servidor SMTP pelo endereço de e-mail padrão associado à conta se o endereço usado não estiver na lista de endereços <strong>Enviar e-mail como</strong> nas configurações do Gmail ou G Suite. Verifique a lista indo até a guia <strong>Contas e importação</strong> na tela de configurações.</p>

<p>Esteja ciente desse detalhe, pois ele afeta a apresentação do seu e-mail do ponto de vista do destinatário, e pode também afetar a configuração <strong>Responder a</strong> de alguns programas.<br></p></span>

<h2 id="limites-de-envio">Limites de envio</h2>

<p>O Google limita a quantidade de e-mails que um usuário pode enviar através do seu servidor SMTP portátil. Esse limite restringe o número de mensagens enviadas por dia para até 99 e-mails; a restrição é removida automaticamente 24 horas após você atingir o limite.</p>

<h2 id="conclusão">Conclusão</h2>

<p>Agora, você tem a opção de usar o servidor SMTP do Google. Se essa opção leve não for suficiente, considere <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04">instalar e configurar o postfix como um servidor SMTP apenas para envio</a>.</p>
