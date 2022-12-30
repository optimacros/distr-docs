---
title: Чистая установка Логин Центра на клиентский сервер
description: Чистая установка Логин Центра на клиентский сервер
published: true
date: 2022-12-30T18:16:31.674Z
tags: 
editor: markdown
dateCreated: 2022-12-29T21:31:39.342Z
---

# Чистая установка Логин Центра на клиентский сервер:

Подключаемся к серверу через ssh протокол.

Переходим в аккаунт root с помощью команды 
```
sudo su
```

Затем скачиваем дистрибутив Логин Центра через терминал с помощью команды: 

`wget ссылкуНеобходимоЗапроситьУТехническогоОтделаOptimacros/download -O ИМЯ_ФАЙЛА.С_РАСШИРЕНИЕМ`

![wget.png](/login-center/wget.png)

>:warning:В случае, если на сервере уже установлен воркспейс,
то необходимо завершить его работу. Предварительно открыв файл 
manifest.json, чтобы убедиться, что мы находимся на нужном сервере. 

Переходим в директории с файлом, как правило, это:
`/om/workspace1` и выполняем команду `cat manifest.json`

После того как мы удостоверились что находимся на нужном сервере (TEST или PROD). Завершаем работу воркспейса.
![shutdownws.png](/login-center/shutdownws.png)

Далее нам необходимо установить на сервере docker.
Устанавливаем его, следуя инструкции c официального сайта,
для соответсвующей Операционной Системы:

https://docs.docker.com/engine/install/#server

Версия docker не имеет принципиального значения (в отличие от версии vagrant необходимой для Воркспейса), устанавливаем 
самую актуальную на момент установки.

>:warning:Установка Docker в системе РЭД ОС имеет нюансы.
[Смотрите подробнее... ](softInstallLc.md) 

Далее мы создаём директорию на сервере под Логин Центр по пути: `/om/login-center`

Для этого переходим в папку om с помощью команды: `cd /om/` и создаём там директорию login-center с помощью команды
`mkdir login-center`

![mkdirlogincenter.png](/login-center/mkdirlogincenter.png)

Теперь когда мы подготовили директорию для установки Логин Центра и скачали дистрибутив, нам необходимо будет установить его. Для этого переходим в директорию со скачанным ранее дистрибутивом с помощью команды `cd /home/имяПользователя/`. 
Выдаём файлу права с помощью команды `chmod +x login-center-версия.sh` и запускаем инсталлятор с помощью команды 
 `./login-center-версия.sh` во время установки инсталлятор попросит подтвердить установку Логин Центра в дефолтную 
 директорию по пути, которому мы как раз и подготовили директории. Текущая версия инсталлятора устанавливает Логин Центр с
  дефолтными настройками подключения, которые нам предстоит настроить вручную, далее нынешняя версия инсталлятора, выдаёт 
  ошибку со следующим содержимым:
 
![errorinstaller.png](/login-center/errorinstaller.png)Которая нам говорит о том, что по заданным путям отсутствуют файлы сертификатов. Затем мы переходим в директорию, где 
лежит Логин Центр `cd /om/login-center`

![](./pictures/goToDirectoryAfterError.png)

Затем мы смотрим содержимое файла .env с помощью команды `cat .env` и видим что там есть только одно свойство VERSION.

Видим, что Логин Центр прервал установку на самом начальном этапе, до установки базы данных. Теперь мы можем смело 
редактировать .env файл, в соответствии с нашими требованиями(открываем редактирование с помощью команды `nano .env`). В частности установить пароли к аккаунту админа, пароли к базе 
данных и информацию о web части. 

![](./pictures/settingsEnv.png)

```
VERSION=1.1.9
HOSTNAME=profile.optimacros.com
DB_USERNAME=login-center
DB_PASSWORD=
ADMIN_USERNAME=admin@optimacros.com
ADMIN_PASSWORD=
WORKSPACE_NAME=workspace1
WORKSPACE_HOSTNAME=workspace1.optimacros.com
```

Более подробное описание полей можно посмотреть тут:
<a href="https://github.com/optimacros/distribution_documentation/blob/main/envDescription.md" target="_blank">ссылка на описание полей .env</a>

Для пароля базы данных (DB_PASSWORD) воспользуемся любым генератором паролей в интернете (http://www.onlinepasswordgenerator.ru/), сгенерировав надёжный пароль длиной более 
20 символов (ОЧЕНЬ важно: пароль не должен содержать символов `@:;/\%` это приведёт к багу установки). 

После настройки всех пунктов, сохраняем наши изменения в 
.env файл.

Затем возвращаемся к нашей проблеме во время установки Логин Центра. Переходим в папку с сертификатами с помощью команды
`cd /om/login-center/data/nginx/cert` После чего, находясь в папке для сертификатов, мы генерируем сами сертификаты 
с помощью команды 

`openssl req -newkey rsa:2048 -nodes -keyout crt.key -x509 -days 1095 -out bundle.crt`

Отвечаем на вопросы, которые задаст программа генерации. На этом с генерацией сертификатов мы заканчиваем.

Затем мы переходим снова в папку с инсталлятором Логин Центра с помощью команды: `cd /home/user/` и запускаем инсталлятор 
`./login-center-версия.sh` 

Во время установки на вопрос, нужно ли перезаписывать env файл конечно же отвечаем нет. Ожидаем процесс установки.

![](./pictures/installationLC.png)

Затем увидим вот такую консоль, это значит что Логин Центр успешно установлен.

![](./pictures/installComplete.png)

Можно проверять результат установки в браузере введя имя хоста нашего логин центра.

[Вернуться к содержанию <](contents.md)

[Вернуться к оглавлению <<](index.md)