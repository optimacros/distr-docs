---
title: Изменение параметров установленного Логин Центра Optimacros
description: Изменение параметров установленного Логин Центра Optimacros
published: true
date: 2023-04-18T05:16:37.286Z
tags: 
editor: markdown
dateCreated: 2023-02-16T21:23:58.462Z
---

# Изменение параметров установленного Логин Центра Optimacros:

В случае необходимости изменения адреса Логин Центра, для начала следует остановить работу воркспейса. Это можно сделать 
при помощи команды:

```
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json shutdown
```

![sshputty3.jpg](/login-center/change_lc_params/sshputty3.jpg)

Далее нам необходимо изменить данные в .ENV файле для правильной работы nginx, за которым стоит сам Login Center. Для этого перейдём в директорию с Логин Центром с помощью команды:

````
cd /om/login-center/
````

Затем остановим работу Логин Центра с помощью команды:

```
./om stop app
```

В директории с Логин Центром находится файл .ENV, можем посмотреть его содержимое с помощью команды:

```
cat .env
```

![catenv.png](/login-center/change_lc_params/catenv.png)

Теперь для изменения данных Логин Центра, нам нужно изменить соответствующие поля, инструкция с обозначениями полей ниже:

Более подробное описание полей можно посмотреть тут: [Описание полей .env](/ru/login-center/envDescription)
Чтобы отредактировать файл .ENV воспользуйтесь командой:

```
nano ./env
```

Затем сохраняем изменения.

Далее в случае смены адреса Логин Центра нам необходимо изменить manifest файл воркспейса, переходим в директорию с файлом манифеста при помощи команды:

```
cd /om/worspace1/
```

Находясь в этой директории, мы можем открыть для редактирования файл манифеста с помощью команды:

```
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
      "url": "https://om.test.workspace.ru"
    },
    "loginCenter": {
      "url": "https://lc.company.ru/", // <= Интересующее нас поле в случае смены адреса Логин Центра
      "token": "4aed337a0ac34dd13716c476a4c7",
      "apiUrl": "wss://lc.company.ru/api/ws/v1/" // <= Интересующее нас поле в случае смены адреса Логин Центра
    },
    "admin": {
      "email": "admin@optimacros.com"
    }
  }
}
```

Нам нужно отредактировать поля Логин Центра, в частности `url` и `apiUrl`
после редактирования, файл манифеста будет иметь примерно такой вид: 

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
      "url": "https://om.test.workspace.ru"
    },
    "loginCenter": {
      "url": "https://новыйАдрес/",
      "token": "4aed337a0ac34dd13716c476a4c7",
      "apiUrl": "wss://новыйАдрес/api/ws/v1/"
    },
    "admin": {
      "email": "admin@optimacros.com"
    }
  }
}
```

После этого нам остаётся вновь запустить Логин Центр и возобновить работу воркспейса. Переходим в директорию Логин Центра с помощью команды:

```
cd /om/login-center/
```

Затем запускаем Логин Центр командой:

```
./om start app
```

Затем запускаем работу воркспейса командой:

```
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json up
```

![sshputty7.jpg](/login-center/change_lc_params/sshputty7.jpg)

Дожидаемся такого вывода терминала:

![sshputty8.jpg](/login-center/change_lc_params/sshputty8.jpg)

И так же в случае смены адреса Логин Центра, обязательно нужно изменить параметр `webroot` на странице конфигурации Login Center (по адресу http://.../admin/config/urls) для правильных ссылок, отправляемых в email письмах о сбросе пароля и приглашении новых пользователей.

![configurls.jpg](/login-center/change_lc_params/configurls.jpg)

Так же важно учесть, что при смене адреса Логин Центра, возможно потребуется изменение ssl сертификата.