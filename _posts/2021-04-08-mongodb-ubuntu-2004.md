---
title : "Создание резервных копий, восстановление и миграция базы данных MongoDB в Ubuntu 20.04"
layout: post
tags: tutorial labnol
post_inspiration: https://www.digitalocean.com/community/tutorials/how-to-back-up-restore-and-migrate-a-mongodb-database-on-ubuntu-20-04-ru
image: "https://sergio.afanou.com/assets/images/image-midres-18.jpg"
---

<p><em>Автор выбрал <a href="https://www.brightfunds.org/funds/write-for-donations-covid-19-relief-fund">COVID-19 Relief Fund</a> для получения пожертвования в рамках программы <a href="https://do.co/w4do-cta">Write for DOnations</a>.</em></p>

<h2 id="Введение">Введение</h2>

<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">MongoDB</a> — одна из самых популярных систем управления базами данных NoSQL. Она отличается масштабируемостью, отказоустойчивостью, надежностью и удобством в использовании. В этом учебном модуле вы создадите резервную копию, восстановите и перенесете образец базы данных MongoDB.</p>

<p>Импортирование и экспорт базы данных подразумевают работу с данными в формате, приемлемом для чтения, который также совместим с другими программными продуктами. Операции резервного копирования и восстановления MongoDB создают или используют специальные двоичные данные MongoDB, в результате чего не только обеспечиваются согласованность и целостность данных, но и сохраняются специальные атрибуты MongoDB. Таким образом, для переноса данных предпочтительнее использовать процесс резервного копирования и восстановления, если исходные и целевые системы совместимы друг с другом.</p>

<h2 id="Предварительные-требования">Предварительные требования</h2>

<p>Перед выполнением этого учебного модуля необходимо следующее:</p>

<ul>
<li><a href="https://www.digitalocean.com/products/droplets/">Один дроплет Ubuntu 20.04</a>, настроенный в соответствии с <a href="https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04">руководством по начальной настройке сервера Ubuntu 20.04</a>, в том числе пользователь без привилегий root с привилегиями sudo и брандмауэр.</li>
<li>СУБД MongoDB, установленная и настроенная согласно указаниям статьи <a href="https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04">Установка MongoDB в Ubuntu 20.04</a>.</li>
<li>Пример базы данных MongoDB, импортированной в соответствии с инструкциями учебного модуля <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-20-04">Импорт и экспорт базы данных MongoDB</a>.</li>
</ul>

<p>Если не указано иное, все команды в этом учебном модуле, для которых требуются привилегии root, должны выполняться пользователем без привилегий root с привилегиями sudo.</p>

<h2 id="Шаг-1-—-Использование-json-и-bson-в-mongodb">Шаг 1 — Использование JSON и BSON в MongoDB</h2>

<p>Прежде чем продолжить прохождение этого учебного модуля, важно понять некоторые базовые моменты. Если у вас есть опыт работы с другими системами баз данных NoSQL, такими как Redis, вы можете найти некоторое сходство с ними при работе с MongoDB.</p>

<p>MongoDB использует для хранения информации форматы <a href="http://json.org/">JSON</a> и BSON (двоичный JSON). JSON - это удобный для прочтения человеком формат, идеально подходящий для экспорта и импорта данных. Вы сможете управлять экспортированными данными в этом формате с помощью любого инструмента, поддерживающего JSON, включая простой текстовый редактор.</p>

<p>Пример документа JSON:</p>
<div class="code-label " title="Example of JSON Format">Example of JSON Format</div><pre class="code-pre "><code class="code-highlight language-bash">{"address":[
    {"building":"1007", "street":"Park Ave"},
    {"building":"1008", "street":"New Ave"},
]}
</code></pre>
<p>С JSON удобно работать, но этот формат поддерживает не все типы данных, доступные в BSON. Это означает, что при использовании JSON возможна так называемая потеря корректности информации. Для создания резервных копий и восстановления, лучше использовать двоичный формат BSON.</p>

<p>Во-вторых, вам не нужно беспокоиться о явном создании базы данных MongoDB. Если база данных, которую вы указываете для импорта, еще не существует, она создается автоматически. Еще лучше обстоят дела со структурой коллекций (таблицы базы данных). В отличие от других СУБД, в MongoDB структура создается автоматически при вставке первого документа (строка базы данных).</p>

<p>В-третьих, в MongoDB операции чтения или вставки больших объемов данных, таких как в задачах этого учебного модуля, могут потреблять много ресурсов процессора, памяти и дискового пространства. Это крайне важно, поскольку MongoDB часто используется для крупных баз данных и больших данных. Самое простое решение этой проблемы — выполнять экспорт и создавать резервные копии в ночное время или в периоды низкой нагрузки.</p>

<p>В-четвертых, согласованность информации может представлять проблему, если у вас загруженный сервер MongoDB, где информация может изменяться при экспорте или резервном копировании базы данных. Одно из возможных решений этой проблемы — <a href="https://docs.mongodb.com/manual/faq/replica-sets/">репликация</a>, и вы можете использовать его, когда лучше освоитесь с MongoDB.</p>

<p>Хотя вы можете использовать <a href="https://www.digitalocean.com/community/tutorials/how-to-import-and-export-a-mongodb-database-on-ubuntu-14-04">функции импорта и экспорта</a> для резервного копирования и восстановления данных, существуют и лучшие способы обеспечения полной целостности данных в базах данных MongoDB. Чтобы создать резервную копию данных, вам следует использовать команду <code>mongodump</code>. Для восстановления используйте команду <code>mongorestore</code>. Давайте посмотрим, как они работают.</p>

<h2 id="Шаг-2-—-Использование-mongodump-для-резервного-копирования-базы-данных-mongodb">Шаг 2 — Использование <code>mongodump</code> для резервного копирования базы данных MongoDB</h2>

<p>Вначале поговорим о резервном копировании базы данных MongoDB.</p>

<p>Одним из самых важных аргументов команды <code>mongodump</code> является аргумент <code>--db</code>, указывающий имя базы данных, резервную копию которой вы хотите создать. Если вы не укажете имя базы данных, <code>mongodump</code> создаст резервные копии всех ваших баз данных. Второй важный аргумент — это аргумент <code>--out</code>, определяющий каталог, в котором будут сохранены данные. Давайте создадим резервную копию базы данных <code>newdb</code> и сохраним ее в каталоге <code>/var/backups/mongobackups</code>. В идеальной ситуации все наши резервные копии будут содержаться в каталоге с текущей датой <code>/var/backups/mongobackups/10-29-20</code>.</p>

<p>Вначале создайте каталог <code>/var/backups/mongobackups</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mkdir /var/backups/mongobackups
</li></ul></code></pre>
<p>Затем запустите команду <code>mongodump</code>:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongodump --db newdb --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</li></ul></code></pre>
<p>Результат должен будет выглядеть следующим образом:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:22:36.886+0000    writing newdb.restaurants to
2020-10-29T19:22:36.969+0000    done dumping newdb.restaurants (25359 documents)
</code></pre>
<p>Обратите внимание, что в вышеуказанном пути каталога мы использовали формат <code>date +"%m-%d-%y"</code>, который автоматически получает текущую дату. Это позволяет нам размещать резервные копии в каталогах вида <code>/var/backups/<span class="highlight">10-29-20</span>/</code>. Это особенно удобно для автоматизации резервного копирования.</p>

<p>Теперь у вас имеется полная резервная копия базы данных <code>newdb</code> в каталоге <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>. В этой резервной копии есть все необходимое для правильного восстановления <code>newdb</code> и сохранения так называемой «корректности».</p>

<p>Как правило, резервное копирование выполняется регулярно, и обычно это делается в периоды наименьшей нагрузки на сервер. Вы можете задать команду <code>mongodump</code> как задачу планировщика (cron), чтобы она выполнялась регулярно, например, каждый день в 03:03.</p>

<p>Для этого откройте редактор crontab:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Обратите внимание, что при запуске <code>sudo crontab</code> вы будете редактировать кроны пользователя root. Рекомендуется сделать именно это, потому что если вы настроите кроны для своего пользователя, они могут выполняться неправильно, особенно если для вашего профиля sudo требуется проверка пароля.</p>

<p>Вставьте в командную строку crontab следующую команду <code>mongodump</code>:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">3 3 * * * mongodump --out /var/backups/mongobackups/`date +"%m-%d-%y"`
</code></pre>
<p>В приведенной выше команде мы целенаправленно опускаем аргумент <code>--db</code>, потому что обычно мы хотим создать резервные копии всех наших баз данных.</p>

<p>Если резервных копий будет слишком много, а базы данных MongoDB будут слишком большими, у вас быстро закончится место на диске. В связи с этим, также рекомендуется регулярно удалять старые резервные копии или сжимать их.</p>

<p>Например, вы можете использовать следующую команду bash для удаления всех резервных копий старше семи дней:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</li></ul></code></pre>
<p>Аналогично предыдущей команде <code>mongodump</code>, вы можете добавить эту команду как задание cron. Его следует запускать непосредственно перед началом следующего резервного копирования, например, в 03:01. Для этого снова откройте crontab:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo crontab -e
</li></ul></code></pre>
<p>Вставьте следующую строку:</p>
<div class="code-label " title="crontab">crontab</div><pre class="code-pre "><code class="code-highlight language-bash">1 3 * * * find /var/backups/mongobackups/ -mtime +7 -exec rm -rf {} \;
</code></pre>
<p>Сохраните и закройте файл.</p>

<p>Выполнение всех задач, описанных на этом шаге, обеспечит надлежащее решение резервного копирования для ваших баз данных MongoDB.</p>

<h2 id="Шаг-3-—-Использование-mongorestore-для-восстановления-и-переноса-базы-данных-mongodb">Шаг 3 — Использование <code>mongorestore</code> для восстановления и переноса базы данных MongoDB</h2>

<p>При восстановлении базы данных MongoDB из предыдущей резервной копии вы получите точную копию информации MongoDB на определенный момент времени, включая все индексы и типы данных. Это особенно полезно, если вы хотите выполнить миграцию ваших баз данных MongoDB. Для восстановления MongoDB мы будем использовать команду <code>mongorestore</code>, которая работает с двоичными резервными копиями, которые производятся <code>mongodump</code>.</p>

<p>Давайте продолжим наши примеры с базой данных <code>newdb</code> и посмотрим, как мы можем восстановить ее с предыдущей резервной копии. Вначале мы укажем имя базы данных с аргументом <code>--nsInclude</code>. Если использовать <code>newdb.*</code>, мы восстановим все коллекции. Для восстановления отдельной коллекции, например <code>restaurants</code>, следует использовать <code>newdb.restaurants</code>.</p>

<p>Используя аргумент <code>--drop</code> мы обеспечим предварительное отбрасывание целевой базы данных так, чтобы резервная копия была восстановлена в чистой базе данных. Как последний аргумент мы зададим каталог последней резервной копии, который будет выглядеть примерно так: <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<p>Когда вы создадите резервную копию с временной меткой, вы можете восстановить ее с помощью следующей команды:</p>
<pre class="code-pre command prefixed"><code class="code-highlight language-bash"><ul class="prefixed"><li class="line" data-prefix="$">sudo mongorestore --db newdb --drop /var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/
</li></ul></code></pre>
<p>Результат должен будет выглядеть следующим образом:</p>
<pre class="code-pre "><code><div class="secondary-code-label " title="Output">Output</div>2020-10-29T19:25:45.825+0000    the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --nsInclude instead
2020-10-29T19:25:45.826+0000    building a list of collections to restore from /var/backups/mongobackups/10-29-20/newdb dir
2020-10-29T19:25:45.829+0000    reading metadata for newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.metadata.json
2020-10-29T19:25:45.834+0000    restoring newdb.restaurants from /var/backups/mongobackups/10-29-20/newdb/restaurants.bson
2020-10-29T19:25:46.130+0000    no indexes to restore
2020-10-29T19:25:46.130+0000    finished restoring newdb.restaurants (25359 documents)
2020-10-29T19:25:46.130+0000    done
</code></pre>
<p>В примере выше мы восстанавливаем данные на том же сервере, на котором создали резервную копию. Если вы хотите перенести данные на другой сервер, используя эту же методику, вам следует скопировать на другой сервер каталог резервной копии, в нашем случае это <code>/var/backups/mongobackups/<span class="highlight">10-29-20</span>/newdb/</code>.</p>

<h2 id="Заключение">Заключение</h2>

<p>Вы выполнили ряд важных задач по резервному копированию, восстановлению и миграции баз данных MongoDB. Производственный сервер MongoDB нельзя использовать без надежной стратегии резервного копирования, такой как описана здесь.</p>
