---
title: Изменение пароля mongodb для Логин Центра
description: Изменение пароля mongodb для Логин Центра
published: true
date: 2023-04-18T05:12:24.822Z
tags: mongo, mongo password
editor: markdown
dateCreated: 2023-02-16T16:56:52.385Z
---

# Изменению пароля mongodb для Логин Центра:

Для изменения пароля mongodb, для начала перейдём в директорию Логин Центра с помощью команды:

```
cd /om/login-center/
```

Затем выполняем следующие команды:

```
docker exec -it optimacros_db /bin/bash
```

```
mongo --port 27017 -u OLD_USERNAME -p  --authenticationDatabase 'admin'
```

Где OLD_USERNAME это старое (текущее) имя пользователя. Затем нужно будет ввести старый пароль.

После этого вводим команду:

```
use admin
```

Затем для смены только пароля вводим:

```
db.updateUser("OLD_USERNAME", { pwd: "NEW_PASSWORD" })
```

Где OLD_USERNAME это старое (текущее) имя пользователя, а NEW_PASSWORD новый пароль.

для смены и имени пользователя и пароля вводим:

```
db.createUser("NEW_USERNAME", { pwd: "NEW_PASSWORD", roles: [{role:"root", db:"admin"}] })
```

после чего вводим команду:

```
exit
```

Затем т.к. мы находимся в директории Логин Центра, мы можем прямо в этой директории обновить файл .ENV, вводим команду:

```
nano .env
```

Изменяем на новые значения DB_USERNAME в случае смены имени пользователя и DB_PASSWORD в случае смены пароля
соответственно.

После чего нам остаётся только перезапустить Логин Центр, с помощью последовательно введённых команд:

```
./om stop app
```

```
./om start app
```

На этом процедура окончена.


![controlcheck.jpg](/login-center/change_mongo_pass/controlcheck.jpg)

На этом процедура изменения пароля mongodb для Логин Центра завершена.
