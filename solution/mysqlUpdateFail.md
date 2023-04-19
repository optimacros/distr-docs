---
title: Проблема с обновлением Mysql при поднятии воркспейса
description: 
published: true
date: 2023-04-19T07:10:57.360Z
tags: solution
editor: markdown
dateCreated: 2023-04-19T07:10:57.360Z
---

# Ручное обновление данных Mysql.

При поднятии воркспейса может произойти прерывание процесса с ошибкой 
`The database must be manually upgraded`
Это означает что MySQL пакет и директория с данными имеют разные версии, что требует обновления директории с данными до версии mysql пакета. Переходим в директорию с контейнером с помощью команды (директория может быть другой):

```bash
cd /om/workspace1/container
```

Затем подключаемся к вагрант контейнеру с помощью команды:

```bash
vagrant ssh
```

После чего нам нужно перейти под root пользователя, вводим команду:

```
sudo su
```
 
Теперь нам необходимо перезагрузить сервис `mariadb` для этого введём команду: 

```
systemctl restart mariadb
```

Выполняем команду:

```
mysql_upgrade
```
![mysqlupgrade.jpg](/solutions/mysqlupgrade.jpg)


Выполняем повторно команду 
```bash
mysql_upgrade
```

Если сообщение содержит текст `already upgraded`, то обновление успешно выполнено и можно выходить из контейнера и переходить к перезагрузке воркспейса или переходить к обновлению OLTP базы, если нет, то продолжаем обновление далее по инструкции

Перезагружаем сервис `mariadb`, для этого введём команду: 

```bash
systemctl restart mariadb
```

После этого нам нужно зайти в MySQL терминал с помощью команды:

```
mysql
```

И в MySQL терминале нужно выполнить команду:
 
```
FLUSH PRIVILEGES;
```

Затем выполняем команду сброса пользователя root:

```sql
ALTER USER `root`@`localhost` IDENTIFIED VIA mysql_native_password USING PASSWORD("syeAIuL4S5Sw9t9") OR unix_socket;
```

Выходим из MySQL терминала с помощью команды:

```
exit
```

И выполняем команду:

```
mysql_upgrade
```

Если сообщение содержит текст `already upgraded`, то обновление успешно выполнено и можно выходить из контейнера и переходить к перезагрузке воркспейса или переходить к обновлению OLTP базы

Если сообщение не содержит текст `already upgraded`, то выполняем команду `mysql_upgrade` до тех пор, пока его не получим

## Обновление OLTP

Повторяем действия по обновлению из инструкции выше, заменяя сервис `mariadb` на `mariadb@oltp`, например

```bash
systemctl restart mariadb@oltp
```

Команду `mysql_upgrade` выполняем с флагом, который указывает на конфигурацию

```bash
mysql_upgrade --defaults-file=/etc/mysql/debian.oltp.cnf
```

## Перезагрузка

Выходим из контейнера с помощью команды `exit`

![containerexit.jpg](/solutions/containerexit.jpg)

Далее нужно перейти в директорию с нашим инсталлятором воркспейса и выполнить штатную остановку воркспейса, но с флагом
`force` т.е. команда будет выглядеть таким образом:

```bash
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json shutdown --force
```

После этого можно поднимать воркспейс:

```bash
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json up
```

На этом всё, проблема решена.