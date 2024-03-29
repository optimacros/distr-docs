---
title: Изменение адреса/имени Воркспейса
description: 
published: true
date: 2023-04-19T07:14:42.076Z
tags: workspace
editor: markdown
dateCreated: 2022-11-29T06:00:50.687Z
---

# Изменение адреса/имени Воркспейса:

В случае необходимости изменения адреса Воркспейса, для начала следует остановить работу воркспейса. Это можно сделать при помощи команды (более подробная информация по остановке воркспейса смотрите [здесь](/ru/workspace/maintenance/update):

```bash
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json shutdown
```
![sshputty3.jpg](/maintenance/sshputty3.jpg)

## Изменение имени воркспейса в .env файле
Далее нам необходимо изменить данные в .ENV файле для правильной работы nginx, за которым стоит сам Login Center. Для 
этого перейдём в директорию с Логин Центром с помощью команды:
```bash
cd /om/login-center/
```
В директории с Логин Центром находится файл .ENV, можем посмотреть его содержимое с помощью команды:
```bash
cat .env
```
![catenv.png](/maintenance/catenv.png)
Теперь для изменения данных Логин Центра, нам нужно изменить соответствующие поля, инструкция с обозначениями полей ниже:

```text
VERSION - изменять нельзя
HOSTNAME - при смене адреса Login Center
DB_USERNAME - при смене имени пользователя MongoDB
DB_PASSWORD - при смене пароля MongoDB
GRAYLOG_PASSWORD - достаточно для смены пароля администратора http://HOSTNAME/logs
GRAYLOG_PASSWORD_SHA2 - всегда должен соответвовать SHA256 параметра GRAYLOG_PASSWORD
ADMIN_USERNAME - не используется после первого запуска Login Center
ADMIN_PASSWORD - не используется после первого запуска Login Center
WORKSPACE_NAME - не используется после первого запуска Login Center
WORKSPACE_HOSTNAME - при смене адреса воркспейса
```

Чтобы отредактировать файл .ENV воспользуйтесь командой:
```bash
nano ./env
```
Нас интересует переменная WORKSPACE_HOSTNAME, изменяем её значение на новый адрес воркпейса.
> При добавлении любого кириллического домена (оптимакрос.рф) в файл .env его нужно добавлять в формате Punycode. Можно воспользоваться любым из онлайн сервисов, допустим: https://hb.by/punycode-converter.aspx 
В конечном итоге домен будет выглядеть примерно вот так: `еxn--80aphgiecmkd.xn--p1ai`
 {.is-warning}
 
> В самых последних версиях ЛЦ при установке, допускается в .env файле указывать кириллический домен. После инсталляции ЛЦ сконвертирует адрес в БД в punycode. Ссылка на экспертизу по добавлению кириллических доменов

[Ссылка на экспертизу по кириллическим доменам](/ru/solution/cyrillicUrl)
## Внесение изменений в базу данных
> Неосторожное обращение с базами данных, может привести к необратимым последствиям!!!
 {.is-warning}
 
Следующим этапом, нам понадобится сделать обновление записи в базе mongodb Логин Центра. К сожалению на данный момент, визуальных инструментов для редактирования данных воркспейса или добавления новых связей с
 воркспейсами в Логин Центре нет. Поэтому при необходимости внести изменения, а они вносятся напрямую в базу данных Логин Центра, воспользуемся командами:
 
```bash
docker exec -it optimacros_db /bin/bash
```

Затем введём команду:
```bash
mongo --port 27017 -u DB_USERNAME -p  --authenticationDatabase 'admin'
```
Где DB_USERNAME это имя пользователя базы данных, затем нужно будет ввести пароль к базе данных. После чего вводим команду:
```bash
use logincenter
```
Вся информация о подключаемых воркспейсах хранится в коллекции workspace, одной связи соответствует один документ. Для просмотра всех документов коллекции workspace, используйте команду:

```bash
db.workspace.find({})
```

Формат документа:

```json
{
    "_id": сгенерировано базой данных автоматически
    "id": "8522aedecc6b4219ee87ee28", случайное 16-ричное число длиной не менее 24 знаков, используется для проверки подключения воркспейса. !Не путать с полем _id!
    "name": "TEST", используется для навигации в UI Логин Центра и воркспейсов
    "description": "Тестовый воркспейс", описание показывается в UI Логин Центра
    "authenticationRedirectUrl": "https://om.test.workspace.ru/auth?token={userToken}", url воркспейса, путь /auth?token={userToken} всегда постоянный, домен (имя или IP адрес) должен соотв. добавляемому воркспейсу
    "domains": ["om.test.workspace.ru"], домен воркспейса без протокола http:// или https://
    "token": "4aed337a0ac34dd13716c476a4c7" СЕКРЕТНОЕ!!! случайное 16-ричное число длиной не менее 24 знаков, используется для проверки подключения воркспейса. 
}
```

При изменении адреса воркспейса в Логин Центре нужно изменить поля domains, authenticationRedirectUrl и, при необходимости, description в соответствующем документе.
При необходимости добавить новую связь создается новый документ и заполняется по схеме выше.

Пример команды обновления документа:

```json
db.workspace.updateOne({"id": "4aed337a0ac34dd13716c476a4c7"}, {$set: {"name": "PROD", "description": "Главный воркспейс", "authenticationRedirectUrl": "https://om.prod.workspace.ru/auth?token={userToken}", "domains": ["om.prod.workspace.ru"]}})
```

После всех манипуляций, с базой вводим:
```bash
exit
```

После добавления или изменения связи с воркспейсом необходимо перезапустить Логин Центр. Для этого введём последовательно команды:

```bash
./om stop app
./om start app
```
## Изменения в manifest.json файле
Далее нам необходимо внести изменения в файл манифеста самого воркспейса. Перейдём в директорию воркспейса, для этого вводим команду:
```bash
cd /om/worspace1/
```
Находясь в этой директории, мы можем открыть для редактирования файл манифеста с помощью команды:
```bash
nano manifest.json
```
Этот файл выглядит примерно вот так:

```json
{
  "container": {
    "ip": "10.0.3.15",
    "cpu": 6,
    "memory": 33685016576,
    "ports": {},
    "hosts": {},
  },
  "workspace": {
    "id": "8522aedecc6b4219ee87ee28",
    "name": "TEST",
    "web": {
      "url": "https://om.test.workspace.ru" // <= Интересующее нас поле в случае смены адреса Логин Центра
    },
    "loginCenter": {
      "url": "https://lc.company.ru/",
      "token": "4aed337a0ac34dd13716c476a4c7",
      "apiUrl": "wss://lc.company.ru/api/ws/v1/"
    },
    "admin": {
      "email": "admin@optimacros.com"
    }
  }
}
```

Нам нужно отредактировать одно поле для Workspace, в частности `workspace.web.url` после редактирования, файл манифеста будет иметь примерно такой вид: 

```json
{
  "container": {
    "ip": "10.0.3.15",
    "cpu": 6,
    "memory": 33685016576,
    "ports": {},
    "hosts": {},
  },
  "workspace": {
    "id": "8522aedecc6b4219ee87ee28",
    "name": "PROD",
    "web": {
      "url": "https://om.prod.workspace.ru"
    },
    "loginCenter": {
      "url": "https://lc.company.ru/",
      "token": "4aed337a0ac34dd13716c476a4c7",
      "apiUrl": "wss://lc.company.ru/api/ws/v1/"
    },
    "admin": {
      "email": "admin@optimacros.com"
    }
  }
}
```

> При добавлении любого кириллического домена (оптимакрос.рф) в файл .env его нужно добавлять в формате Punycode. Можно воспользоваться любым из онлайн сервисов, допустим: https://hb.by/punycode-converter.aspx 
В конечном итоге домен будет выглядеть примерно вот так: `еxn--80aphgiecmkd.xn--p1ai`
 {.is-warning}

Затем запускаем работу воркспейса командой:

```bash
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json up
```

![sshputty7.jpg](/maintenance/sshputty7.jpg)

Дожидаемся такого вывода терминала:
![sshputty8.jpg](/maintenance/sshputty8.jpg)


# Дополнительная информация:
- При добавлении нового воркспейса в админке Логин Центра на странице воркспейса `http://.../admin/workspace/<ID>` 
подтвердить связь кнопкой `Grant Approval`, после чего пользователям можно будет давать к нему доступ. 

- ID и токен воркспейса должны быть скопированы в конфигурационный файлы самого воркспейса.

- В случае компроментации токена воркспейса его можно отключить кнопкой `Revoke Approval` на странице воркспейса в Логин 
Центре.

- Для генерации 16-ричных чисел для значений полей id и token можно использовать онлайн сервисы, например: 
https://onlinehextools.com/generate-random-hex-numbers
  
