---
title : "Установка MongoDB из репозиториев APT по умолчанию в Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-from-the-default-apt-repositories-on-ubuntu-20-04-ru
image: "https://sergio.afanou.com/assets/images/image-midres-7.jpg"
---

<h3 id="Введение">Введение</h3>

<p><a href="https://www.mongodb.com/">MongoDB</a> — бесплатная база данных документов NoSQL с открытым исходным кодом, часто используемая в современных веб-приложениях.</p>

<p>В этом учебном модуле мы установим MongoDB, будем управлять ее сервисами и включим опцию удаленного доступа.</p>

<p><span class='note'><strong>Примечание</strong>. На момент составления данный учебный модуль устанавливает версию MongoDB <span class="highlight">3.6</span>, доступную для репозиториев Ubuntu по умолчанию. Однако обычно вместо этого мы рекомендуем установить последнюю версию MongoDB. На момент написания это версия <span class="highlight">4.4</span>. Если вы хотите установить последнюю версию MongoDB, мы рекомендуем следовать указаниям этого руководства <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Установка MongoDB в Ubuntu 20.04 из источника</a>.<br></span></p>

<h2 id="Предварительные-требования">Предварительные требования</h2>

<p>Для выполнения этого учебного модуля вам потребуется следующее:</p>

<ul>
<li>Один сервер Ubuntu 20.04, настроенный в соответствии с <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">указаниями учебного модуля по начальной настройке сервера</a>, имеющий пользователя без привилегий root с административными привилегиями и брандмауэр, настроенный с помощью UFW.</li>
</ul>

<h2 id="Шаг-1-—-Установка-mongodb">Шаг 1 — Установка MongoDB</h2>

<p>MongoDB входит в официальные репозитории пакетов Ubuntu, т. е. мы можем устанавливать необходимые пакеты с помощью <code>apt</code>. Как уже было отмечено во введении, по умолчанию в репозиториях доступна не самая последняя версия. Чтобы установить последнюю версию Mongo, воспользуйтесь <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">этим учебным модулем</a>.</p>

<p>Вначале следует обновить пакеты, чтобы получить последнюю версию списков репозитория:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt update
</li></ul></code></pre>
<p>Затем необходимо установить сам пакет MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo apt install mongodb
</li></ul></code></pre>
<p>Эта команда попросит подтвердить, что вы хотите установить пакет <code>mongodb</code> и его зависимости. Чтобы это сделать, нажмите <code>Y</code>, а затем <code>ENTER</code>.</p>

<p>Эта команда устанавливает несколько пакетов, содержащих стабильную версию MongoDB, а также полезные инструменты для управления сервером MongoDB. Сервер базы данных автоматически запускается после установки.</p>

<p>Затем нужно убедиться, что сервер запущен и работает корректно.</p>

<h2 id="Шаг-2-—-Проверка-службы-и-базы-данных">Шаг 2 — Проверка службы и базы данных</h2>

<p>Запуск MongoDB был автоматически выполнен в процессе установки, но теперь нужно убедиться, что служба запущена и база данных работает.</p>

<p>Вначале проверим состояние службы:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Вы увидите следующий результат:</p>
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
<p>Согласно данному выводу, сервер MongoDB запущен и работает.</p>

<p>Мы можем дополнительно подтвердить это, фактически подключившись к серверу базы данных и запустив следующую диагностическую команду. Команда выведет текущую версию базы данных, адрес и порт сервера, а также результаты выполнения команды status:</p>
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
<p>Значение <code>1</code> поля <code>ok</code> в ответе означает, что сервер работает нормально.</p>

<p>Теперь мы рассмотрим, как управлять экземпляром сервера.</p>

<h2 id="Шаг-3-—-Управление-службой-mongodb">Шаг 3 — Управление службой MongoDB</h2>

<p>Процесс установки, описанный в шаге 1, настраивает MongoDB как службу <code>systemd</code>, и это означает, что вы можете управлять этим процессом с помощью стандартных команд <code>systemctl</code> наряду со всеми другими системными сервисами в Ubuntu.</p>

<p>Чтобы проверить состояние службы, введите:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl status mongodb
</li></ul></code></pre>
<p>Вы можете остановить сервер в любое время, используя следующую команду:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl stop mongodb
</li></ul></code></pre>
<p>Чтобы запустить остановленный сервер, введите:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl start mongodb
</li></ul></code></pre>
<p>Также вы можете перезапустить сервер с помощью следующей команды:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>По умолчанию MongoDB настроена для автоматического запуска вместе с сервером. Если вы хотите отключить автоматический запуск, введите:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl disable mongodb
</li></ul></code></pre>
<p>Вы можете повторно активировать автоматический запуск в любое время с помощью следующей команды:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl enable mongodb
</li></ul></code></pre>
<p>Теперь изменим настройки параметров брандмауэра для нашей системы MongoDB.</p>

<h2 id="Шаг-4-—-Настройка-брандмауэра-необязательно">Шаг 4 — Настройка брандмауэра (необязательно)</h2>

<p>Если вы следовали указаниям <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04#step-4-%E2%80%94-setting-up-a-basic-firewall">учебного модуля по начальной настройке сервера</a> и включили на сервере брандмауэр, сервер MongoDB будет недоступен из интернета.</p>

<p>Если вы намереваетесь использовать сервер MongoDB только локально с запуском приложений на том же сервере, эту безопасную настройку рекомендуется сохранить. Однако если вы хотите иметь возможность подключения к серверу MongoDB из Интернета, необходимо разрешить входящие подключения, добавив соответствующее правило UFW.</p>

<p>Чтобы разрешить доступ к MongoDB через порт по умолчанию <code>27017</code> из любой точки, используйте команду <code>sudo ufw allow <span class="highlight">27017</span></code>. Однако включение доступа к серверу MongoDB через интернет с параметрами по умолчанию даст кому угодно доступ к серверу базы данных и его содержимому.</p>

<p>В большинстве случаев доступ к MongoDB следует разрешать только из определенных доверенных мест, таких как другой сервер хостинга приложения. Чтобы разрешить только доступ к порту MongoDB по умолчанию со стороны другого доверенного сервера, вы можете указать IP-адрес удаленного сервера в команде <code>ufw</code>. Таким образом, подключение будет явно разрешено только для этой машины:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw allow from <span class="highlight">trusted_server_ip</span>/32 to any port <span class="highlight">27017</span>
</li></ul></code></pre>
<p>Вы можете проверить изменение параметров брандмауэра с помощью <code>ufw</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo ufw status
</li></ul></code></pre>
<p>В результатах вывода должно быть видно, что трафик на порт <code>27017</code> разрешен. Если вы решили разрешить подключение к серверу MongoDB только для одного IP-адреса, этот адрес должен быть указан вместо <code>Anywhere</code> в списке, выводимом при выполнении этой команды:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
<span class="highlight">27017                      ALLOW       Anywhere</span>
OpenSSH (v6)               ALLOW       Anywhere (v6)
<span class="highlight">27017 (v6)                 ALLOW       Anywhere (v6)</span>
</code></pre>
<p>Дополнительные настройки брандмауэра для ограничения доступа к службам можно найти в разделе <a href="https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands">Основы UFW: распространенные правила и команды брандмауэра</a>.</p>

<p>Хотя порт и открыт, MongoDB все равно будет прослушивать только локальный адрес <code>127.0.0.1</code>. Чтобы разрешить удаленные подключения, добавьте публичный маршрутизируемый IP-адрес вашего сервера в файл <code>mongodb.conf</code>.</p>

<p>Откройте файл конфигурации MongoDB в предпочитаемом текстовом редакторе. Команда в этом примере использует <code>nano</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo nano /etc/mongodb.conf
</li></ul></code></pre>
<p>Добавьте IP-адрес вашего сервера MongoDB в значение <code>blindIP</code>. Обязательно поставьте запятую между уже записанным IP-адресом и тем адресом, который вы добавили:</p>
<div class="code-label " title="/etc/mongodb.conf">/etc/mongodb.conf</div><pre class="code-pre "><code class="code-highlight language-bash">...
logappend=true

bind_ip = 127.0.0.1<span class="highlight">,your_server_ip</span>
#port = 27017

...
</code></pre>
<p>Сохраните файл и выйдите из редактора. Если вы использовали <code>nano</code> для редактирования файла, нажмите <code>CTRL + X</code>, <code>Y</code>, а затем <code>ENTER</code>.</p>

<p>Затем перезапустите службу MongoDB:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo systemctl restart mongodb
</li></ul></code></pre>
<p>Теперь MongoDB прослушивает удаленные соединения, но доступ к нему открыт для всех. Следуйте указаниям руководства <a href="https://www.digitalocean.com/community/tutorials/how-to-secure-mongodb-on-ubuntu-20-04">Установка и защита MongoDB в Ubuntu 20.04</a>, чтобы добавить административного пользователя и дополнительные ограничения.</p>

<h2 id="Заключение">Заключение</h2>

<p>Более подробные обучающие модули по настройке и использованию MongoDB можно найти в <a href="https://www.digitalocean.com/community/search?q=mongodb">следующих статьях сообщества DigitalOcean</a>. Официальная <a href="https://docs.mongodb.com/v3.6/">документация по MongoDB</a> также содержит много информации о возможностях, предоставляемых MongoDB.</p>
