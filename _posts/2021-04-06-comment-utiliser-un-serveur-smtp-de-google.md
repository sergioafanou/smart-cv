---
title : "Comment utiliser un serveur SMTP de Google"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server-fr
image: "https://sergio.afanou.com/assets/images/image-midres-4.jpg"
---

<p><em>L'auteur a choisi le <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> pour recevoir un don dans le cadre du programme <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="introduction">Introduction</h3>

<p>Le serveur SMTP portatif de Google est une fonctionnalité peu connue qu'intègrent les courriels de Gmail et des applications Google. Plutôt que d'avoir à gérer votre propre serveur de courriels sortants sur votre <a href="https://www.digitalocean.com/products/droplets/">DigitalOcean Droplet</a> ou <a href="https://try.digitalocean.com/kubernetes-in-minutes/?utm_campaign=amer_brand_kw_en_cpc&amp;utm_adgroup=digitalocean_kubernetes_exact&amp;_keyword=digitalocean%20kubernetes&amp;_device=c&amp;_adposition=&amp;utm_medium=cpc&amp;utm_source=google&amp;gclid=EAIaIQobChMImcKuus2e7AIVhY5bCh1otQGSEAAYAiAAEgJEu_D_BwE">Kubernetes Cluster</a>, vous pouvez configurer les paramètres du serveur SMTP de Google avec n'importe quel script ou programme avec lequel vous souhaitez envoyer des courriels. Tout ce dont vous avez besoin est soit (i) un compte gratuit Gmail ou (ii) un compte payant G Suite.</p>

<h2 id="avantages">Avantages</h2>

<p>Vous avez la possibilité d'avoir Google Store et d'indexer les e-mails que vous envoyez via son serveur SMTP, de sorte que tous les e-mails que vous envoyez puissent être consultés et sauvegardés sur les serveurs de Google. Si vous décidez d'utiliser également votre compte Gmail ou G Suite pour vos courriels entrants, tous vos courriels se retrouveront alors dans un endroit pratique. De plus, étant donné que le serveur SMTP de Google n'utilise pas le Port 25, vous réduirez la probabilité qu'un ISP puisse bloquer votre courriel ou le baliser comme un pourriel.</p>

<h2 id="paramètres">Paramètres</h2>

<p>Le serveur SMTP de Google nécessite une authentification. Voici donc de quelle manière le configurer dans votre client ou application de courriel :</p>

<p><span class='note'><strong>Remarque :</strong> avant de commencer, envisagez de trouver la cote de sécurité de votre client de courrier ou de l'application selon Google. Si vous utilisez un programme que Google ne considère pas comme sécurisé, votre utilisation sera bloquée, à moins que vous n'activiez les applications moins sécurisées (un paramètre de sécurité que Google ne recommande pas) ou que vous génériez un mot de passe App spécifique à une application. <a href="https://support.google.com/accounts/answer/6010255?hl=en">Pour plus d'informations sur la sécurité, suivez ce lien</a> pour déterminer la meilleure approche pour votre client ou application de courrier électronique.<br></span></p>

<ol>
<li>Serveur SMTP (c'est-à-dire le serveur des courriels sortants) : <strong>[smtp.gmail.com (<a href="http://smtp.gmail.com">http://smtp.gmail.com</a>)</strong></li>
<li>SMTP username : <strong>votre adresse électronique de Gmail ou G Suite complète</strong> (par exemple, <code><span class="highlight">example@gmail.com</span></code> ou <code><span class="highlight">example@your_domain</span></code>)</li>
<li>SMTP password : <code><span class="highlight">votre mot de passe de courriel Gmail ou G Suite</span></code></li>
<li>SMTP port : <code>465</code></li>
<li>SMTP <strong>TLS/SSL required</strong> : <strong>oui</strong></li>
</ol>

<p>Afin que Google puisse automatiquement copier les e-mails envoyés dans le dossier Messages envoyés, vous devez également vérifier que l'accès IMAP est activé pour votre compte.</p>

<p>Pour ce faire, allez dans les paramètres de Gmail et cliquez sur l'onglet <strong>Transfert et POP/IMAP</strong>. Descendez à la section <strong>Accès IMAP</strong> et assurez-vous que l'accès IMAP est bien activé pour votre compte.</p>

<span class='note'><p>
<strong>Remarque :</strong> Google réécrira automatiquement la ligne <strong>From</strong> de tout courriel que vous envoyez via son serveur SMTP à l'adresse de courrier électronique par défaut associée au compte si celle utilisée n'est pas sur la liste d'adresses <strong>Send mail as</strong> dans les paramètres de Gmail ou G Suite. Vous pouvez vérifier la liste en allant sur l'onglet <strong>Comptes et Importation</strong> de l'écran des paramètres.</p>

<p>Vous devez être conscient de cette nuance car elle affecte la présentation de votre courrier électronique au destinataire et cela peut également affecter la configuration de <strong>Répondre à</strong> de certains programmes.<br></p></span>

<h2 id="limites-d-39-envoi">Limites d'envoi</h2>

<p>Google limite la quantité de courriels qu'un utilisateur peut envoyer via son serveur SMTP portatif. Cette limite restreint le nombre de messages envoyés par jour à 99 courriels. La restriction est automatiquement levée 24 heures après que la limite est atteinte.</p>

<h2 id="conclusion">Conclusion</h2>

<p>Vous avez maintenant la possibilité d'utiliser le serveur SMTP de Google. Si cette option légère n'est pas suffisante, vous pouvez envisager <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04">d'installer et de configurer postfix comme un serveur SMTP pour envois seulement</a>.</p>
