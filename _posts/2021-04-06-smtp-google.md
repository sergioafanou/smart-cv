---
title : "Использование сервера SMTP Google"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-use-google-s-smtp-server-ru
image: "https://sergio.afanou.com/assets/images/image-midres-25.jpg"
---

<p><em>Автор выбрал <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> для получения пожертвования в рамках программы <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h3 id="Введение">Введение</h3>

<p>Портативный сервер Google SMTP — один из малоизвестных функциональных компонентов Gmail и Google Apps. Вместо того, чтобы управлять собственным сервером исходящей почты в <a href="https://www.digitalocean.com/products/droplets/">дроплете DigitalOcean</a> или <a href="https://try.digitalocean.com/kubernetes-in-minutes/?utm_campaign=amer_brand_kw_en_cpc&amp;utm_adgroup=digitalocean_kubernetes_exact&amp;_keyword=digitalocean%20kubernetes&amp;_device=c&amp;_adposition=&amp;utm_medium=cpc&amp;utm_source=google&amp;gclid=EAIaIQobChMImcKuus2e7AIVhY5bCh1otQGSEAAYAiAAEgJEu_D_BwE">кластере Kubernetes</a>, вы можете настроить сервер Google SMTP для работы с любым скриптом или с любой программой, которые вы хотите использовать для отправки электронной почты. Для этого потребуется (i) бесплатная учетная запись Gmail или (ii) платная учетная запись G Suite.</p>

<h2 id="Преимущества">Преимущества</h2>

<p>Вы можете использовать службу Google для сохранения и индексации электронных писем, отправляемых через сервер SMTP, чтобы резервные копии всех отправленных электронных писем хранились на серверах Google, и чтобы в них можно было производить поиск. Если вы решите использовать учетную запись Gmail или G Suite и для получения входящей почты, вся ваша почта будет храниться в одном удобном месте. Кроме того, поскольку сервер Google SMTP не использует порт 25, это уменьшит вероятность того, что провайдер заблокирует ваше электронное письмо или пометит его как спам.</p>

<h2 id="Настройки">Настройки</h2>

<p>Для сервера Google SMTP требуется аутентификация, и здесь мы покажем, как настроить ее в почтовом клиенте или приложении:</p>

<p><span class='note'><strong>Примечание.</strong> Прежде чем начать, попробуйте оценить рейтинг безопасности вашего почтового клиента или приложения согласно критериям Google. Если вы используете программу, которую Google не считает безопасной, использование будет заблокировано, если вы не включите менее безопасные приложения (настройка безопасности, которая не рекомендуется Google) или не сгенерируете пароль для конкретного приложения. <a href="https://support.google.com/accounts/answer/6010255?hl=en">Используйте эту ссылку, чтобы получить больше информации по безопасности</a> и определить наилучший подход для вашего почтового клиента или приложения.<br></span></p>

<ol>
<li>Сервер SMTP (т. е. сервер исходящей почты): <strong><a href="http://smtp.gmail.com">smtp.gmail.com</a></strong></li>
<li>Имя пользователя SMTP: <strong>ваш полный адрес электронной почты Gmail или G Suit</strong>e (например, <code><span class="highlight">example@gmail.com</span></code> или <code><span class="highlight">example@your_domain</span></code>)</li>
<li>Пароль SMTP: <code><span class="highlight">ваш пароль Gmail или G Suite</span></code></li>
<li>Порт SMTP: <code>465</code></li>
<li><strong>Требуется SMTP TLS/SSL</strong>: <strong>д</strong>а</li>
</ol>

<p>Чтобы отправленные электронные письма автоматически копировались Google в папку Отправленные, необходимо также убедиться, что для вашей учетной записи включен доступ IMAP.</p>

<p>Для этого следует перейти в настройки Gmail и открыть вкладку <strong>Переадресация и POP/IMAP</strong>. Затем следует перейти в раздел <strong>Доступ IMAP</strong> и убедиться, что доступ IMAP включен для вашей учетной записи.</p>

<span class='note'><p>
<strong>Примечание.</strong> Google будет автоматически перезаписывать строку <strong>От</strong> любого электронного письма, отправляемого через сервер SMTP Google, на связанный с учетной записью электронный адрес по умолчанию, если используемый адрес не содержится в списке адресов <strong>Отправлять почту как</strong> в настройках Gmail или G Suite. Чтобы просмотреть этот список, используйте вкладку <strong>Учетные записи и импорт</strong> на экране настроек.</p>

<p>Этот нюанс необходимо учитывать, поскольку он влияет на то, как получатель видит ваше электронное письмо, и также может влиять на настройку поля адресата в ответе <strong>Кому</strong> в некоторых программах.<br></p></span>

<h2 id="Лимиты-отправки">Лимиты отправки</h2>

<p>Google ограничивает объем почты, который пользователь может отправить через портативный сервер SMTP. Этот лимит ограничивает количество отправляемых сообщений в день 99 электронными сообщениями. Это ограничение автоматически снимается через 24 часа после достижения предела.</p>

<h2 id="Заключение">Заключение</h2>

<p>Теперь у вас есть возможность использовать сервер Google SMTP. Если компактной версии недостаточно, вы можете <a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-18-04">установить и использовать postfix как сервер SMTP, служащий только для отправки сообщений</a>.</p>
