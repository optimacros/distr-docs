---
title: Описание манифест файла Воркспейса
description: 
published: true
date: 2022-11-14T09:07:59.015Z
tags: workspace, manifest
editor: markdown
dateCreated: 2022-11-14T09:07:59.015Z
---

Для работы Воркспейса используется манифест файл в формате JSON, 
который описывает основные настройки воркспейса, требуемые для его работы.

Манифест воркспейса может иметь любое имя, по умолчанию используется имя `manifest.json`.

Расположение манифеста определяет директорию, которая содержит файловую 
подсистему для работы воркспейса в Linux контейнере.

## Пример базового варианта манифест файла

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

## Блок `container`

Блок `container` позволяет настроить работу Linux контейнера.

```json
{
    "ip": "10.0.3.15",
    "cpu": 6,
    "memory": 33685016576,
    "ports": {},
    "hosts": {},
}
```

### IPv4 адрес контейнера

`required|string`

Для работы Linux контейнера используется LXC подсеть 10.0.3.0/24, 
которая автоматически назначает IPv4 адрес контейнеру. 
Для эмуляции статической привязки адреса, при поднятии контейнера 
мы дополнительно привязываем IPv4 адрес, выбор которого за вами.

Обычно мы выбираем адреса начиная с 10.0.3.15, 
а LXC обычно назначает автоматически контейнерам адреса начиная с 10.0.3.100. 
Но бывают конфликты, для их исключания смотрите вывод команды `sudo lxc-ls -f`, 
так вы можете понять какие адреса уже заняты и обойти их

```json
{
    "ip": "10.0.3.15",
    ...
}
```

### Gateway

`string`

Адрес шлюза контейнера в формате IPv4. Значение по умолчанию - 10.0.3.1

### CPU

`required|integer`

Ограничивает количество воркеров служб Optimacros.\
Не ограничивает память Linux контейнера.

Обычно указываем количество ядер хост сервера.

```json
{
    ...
    "cpu": 6,
    ...
}
```

### Memory

`required|integer`

Память в байтах.\
Используется для некоторых ограничений служб Optimacros.\
Используется для формирования счетчика памяти Optimacros расположенного на Drive Landing.\
Не используется для ограничения Linux контейнера.

При работе одного воркспейса на сервере, обычно указывается значение из вывода команды `free -b`.\
При работе нескольких воркспейсов, указывается в нужных пропорциях между всеми воркспейсами

```json
{
    ...
    "memory": 33685016576,
    ...
}
```

### Ports

`object`

Параметр позволяет настроить переадресацию портов. При этом можно гибко указать конкретный адрес интерфейса хоста.

При использовании воркспейса с Логин Центром на одном хосте, обычно мы не используем переадресацию 
портов на воркспейсе контейнере, так как используется Nginx Логин Центра в качестве reverse proxy.

Обычно переадресация портов воркспейса используем при установке воркспейса отдельно от Логин Центра.

Для внешнего взаимодействия с воркспейсом импользуются порты: 80, 443, 8080, 8081.

```json
{
    ...
    "ports": {
        "0.0.0.0:80": 80,
        "0.0.0.0:443": 443,
        "0.0.0.0:8080": 8080,
        "0.0.0.0:8081": 8081
    },
    ...
}
```

Для работы на конкретном интерфейсе, замените `0.0.0.0` на соответствующий IPv4 адрес.

Для переадресации портов должна быть установлена утилита [redir](https://github.com/troglobit/redir)

### Hosts

Параметр позволяет настроить файл контейнера `/etc/hosts` ([man](https://man7.org/linux/man-pages/man5/hosts.5.html))

```json
{
    ...
    "hosts": {
        "test.example.com": "10.0.5.34",
        "ftp.example.com": "192.168.1.44"
    },
    ...
}
```

### DNS

`object`

Параметр позволяет настроить DNS для контейнера, файл 
`/etc/resolv.conf` ([man](https://man7.org/linux/man-pages/man5/resolv.conf.5.html)).

`dns.search` - `string[]` См. в `man` пункт о `search`.

`dns.nameServers` - `object[]` Каждый объект 
коллекции содержит информацию об одном DNS сервере

`dns.nameServers[n].ip` - `required|string` IPv4 адрес DNS сервера

```json
{
    ...
    "dns": {
        "search": [
            "example.com"
        ],
        "nameServers": [
            {
                "ip": "8.8.8.8"
            },
            {
                "ip": "8.8.4.4"
            }
        ]
    },
    ...
}
```

Можно использовать `dnsmasq` ([man](https://wikipedia.org/wiki/Dnsmasq)) на хосте:

```json
{
    ...
    "dns": {
        "nameServers": [
            {
                "ip": "10.0.3.1"
            }
        ]
    },
    ...
}
```

### Routes

`object[]`

Данный параметр позволяет кастомизировать таблицу маршрутов 
контейнера ([man](https://man7.org/linux/man-pages/man8/ip-route.8.html))

```json
{
    ...
    "routes": [
        {
            "destination": "192.168.1.50/32",
            "gateway": "10.0.3.1"
        }
    ],
    ...
}
```

### FreeTDS

`object[]`

Параметр позволяет настроить программный интерфейс `freetds` ([man](https://www.freetds.org/)) 
для работы Optimacros с MS SQL

Данный интерфейс используется Optimacros макросами при пользовании 
коннектором MS SQL с драйвером DBLIB (Выбран по умолчанию)

`freetds[n].hosts` - `required|string[]` Хост MS SQL. Каждый элемент порождает отдельную запись конфигурации `freetds`

`freetds[n].port` - `integer` Порт MS SQL

`freetds[n].ntlmv2` - `boolean` При использовании Active Directory авторизации,
возможно потребуется включить в true. См. [man](https://www.freetds.org/)

```json
{
    ...
    "freetds": [
        {
            "hosts": [
                "instance1.example.com",
                "instance2.example.com"
            ],
            "port": 1433,
            "ntlmv2": true
        }
    ],
    ...
}
```

Пример сгенерированной конфигурации `freetds` на основе параметров выше:

```ini
...
[instance1.example.com]
host = instance1.example.com
port = 1433
use ntlmv2 = yes

[instance2.example.com]
host = instance2.example.com
port = 1433
use ntlmv2 = yes
```

### Tunnel

`object`

`tunnel.status` - `required|boolean` если включен, система будет использовать vpn, настроенный на хост-машине. 

```json
{
    ...
    "tunnel": {
        "status": true
    }
    ...
}
```

### Limits

`object`

`limits.nofile` - `integer` количество дескрипторов https://www.freedesktop.org/software/systemd/man/systemd.exec.html#Process Properties

`limits.nproc` - `integer` ограничивает количество дочерних systemd процессов https://www.freedesktop.org/software/systemd/man/systemd.exec.html#Process%20Properties

```json
{
    ...
    "limits": {
        "nofile": 123,
        "nproc": 123
    }
    ...
}
```

### Synced Folders

`object`

`syncedFolders.^[a-z0-9_]+$` - Вместо регулярного выражения подставить id встроенной папки, по которому к ней можно будет обращаться.

`syncedFolders.^[a-z0-9_]+$.externalPath` - `required|string` Путь до встраиваемой папки на хост-машине (вне vagrant контейнера).

```json
{
    ...
    "syncedFolders": {
       "^[a-z0-9_]+$": {
           "externalPath": "path/to/folder"
       }
    }
    ...
}
```

### Cgroup

`object`

`cgroup.version` - `enum: ["auto"|"v1"|"v2"]` В этом параметре нужно указать текущую версию cgroup для совместимости с lxc. Если есть затруднения с определением, лучше оставить значение "auto", которое выбирается по умолчанию.

```json
{
    "cgroup": {
        "version": "auto"
    }
}
```

### Proxy

`object`

Параметр позволяет настроить proxy для сервисов. 

```json
{
    ...
    "proxy": {
        "http": "http://test1.com",
        "https": "https://test2.com",
        "ftp": "ftp://test3.com",
        "ignore": [
            "http://test4.com",
            "http://test5.com"
        ]
    }
    ...
}
```