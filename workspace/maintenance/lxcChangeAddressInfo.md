---
title: Измение подсети LXC
description: 
published: true
date: 2022-11-29T06:29:24.982Z
tags: workspace
editor: markdown
dateCreated: 2022-11-29T06:29:24.982Z
---

# Инструкция как изменить подсеть LXC

В случае если у нас уже имееется рабочийц воркспейс, то первое, что нужно сделать это остановить работу воркспейса. Это можно сделать при помощи команды (более подробная информация по остановке воркспейса смотрите [здесь](refreshWs.md)):
                                                           
```bash
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json shutdown
```
![sshputty3.jpg](/maintenance/sshputty3.jpg)
## Изменения настроек LXC
После чего мы должны отредактировать файл `lxc-net` по пути `/etc/default/`. Для этого воспользуемся командой:
```bash
nano /etc/default/lxc-net
```
![nanolxcnet.jpg](/maintenance/nanolxcnet.jpg)


После чего нам будет доступен файл для редактирования:
![nanolxcopen.jpg](/maintenance/nanolxcopen.jpg)

Редактируем поля `LXC_ADDR`, `LXC_NETWORK`, `LXC_DHCP_RANGE` изменяем адреса на те которые нам необходимы. Выглядеть это будет примерно так: 
 ![editedlxcnet.jpg](/maintenance/editedlxcnet.jpg)

Сохраняем изменения. После чего нам нужно последовательно воспользоваться командами:

```bash
systemctl restart lxc-net
systemctl restart lxc
```
После этого вводим команду:
```bash
ifconfig
```
И в выводе смотрим на то, как у нас выглядит lxc bridge
![lxcbridgeoutput.jpg](/maintenance/lxcbridgeoutput.jpg)

IP адрес должен быть таким каким мы указали в поле `LXC_ADDR`.

## Настройки воркспейса
После этого нам необзодимо открыть манифест файл воркспейса и изменить в нём адрес в самом верхнее поле.

Открываем файл манифеста дял редактирования при помощи команды `nano /om/workspace1/manifest.json` и редактируем:

![changecontainerip.jpg](/maintenance/changecontainerip.jpg)
После этого сохраняем изменения и затем запускаем работу воркспейса. Используем команду: 

```bash 
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json up
```
![sshputty7.jpg](/maintenance/sshputty7.jpg)

Дожидаемся такого вывода терминала:
![sshputty8.jpg](/maintenance/sshputty8.jpg)


Далее вводим команду `lxc-ls -f`

![lxclsf.jpg](/maintenance/lxclsf.jpg)
Видим что у контейнера IP адреса соответствующие.

## Изменение настроек логин центра
Затем нам нужно ищзменить ip адрес воркспейса в Логин Центра в .ENV файле. Вводим команду:

```bash
nano /om/login-center/.env
```

В файлике .ENV изменяем IP адрес в поле `WORKSPACE_PROXY_URL:` в нашем случае на `192.168.0.15` и созраняем изменения.

И перезагружаем Логин Центр, введя команды последовательно:

```bash
cd /om/login-center/
./om stop web
./om start web`
```

