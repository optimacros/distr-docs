---
title: Подключение и настройка OLTP на воркспейсе 
description: 
published: true
date: 2022-11-16T04:42:26.366Z
tags: 
editor: markdown
dateCreated: 2022-11-16T04:42:26.366Z
---

# Подключение OLTP на воркспейсе 
для этого требуется внести изменения в файл настройки воркспейса `manifest.json` и перезапустить воркспейс.

В файле `manifest.json` в блоке `workspace` добавляется секция oltp c настройками подключения требующегося oltp. Более подробно можно посмотреть в [описании манифест файла Воркспейсаt](/ru/workspace/config/manifest)

> Важно использовать достаточно сложные и длинные пароли. Для этого рекомендуется использовать любой онлайн генератор и достаточно длинные пароли в 20 символов. 
Если для настройки требуется указать несколько паролей для разных учетных записей, генерируем несколько паролей.
{.is-warning}

# Настройка OLTP
Для доступа к интерфейсам различных баз требуется внести соответствующие параметры в секции `oltp`. На воркспейсе может работать несколько `oltp`. Для этого соответствующие настройки вносятся последовательно. 

## Maria DB
Пример подключения

```json
		...
		"oltp": {
      "mysql": {
        "userPasswords": {
          "root": "ABGx6z0AQ8GUBGDgfTo2",
          "admin": "i7KtCnUu5biTWFuAg6v5",
          "reader": "txQtsjG6t8ooNbh20Yn4",
          "writer": "OLehofSb3HI3PGgyjiK0"
        },
        "web": {
            "status": true
        }
      }
    },
	...
```
После перезагрузки воркспейса, oltp будет доступно в браузере по адресу: `https://workspace.com/oltp/mysql`
где `workspace.com` это адрес вашего воркспейса.
Аналитикам для пользования oltp следует выдать пароли от ролей reader и writer.
## Click House
Пример подключения
```json
...
 "oltp": {
      "clickhouse": {
          "service": {
              "status": true
          },
          "web": {
              "status": true
          },
          "userPasswords": {
              "default": "vq0xnnmzJittJsday1"
          }
      }
    },
		...
```
После перезагрузки воркспейса, oltp будет доступно в браузере по адресу: `https://workspace.com/oltp/clickhouse/play`
где `workspace.com` это адрес вашего воркспейса.
Для входа в интерфейсе clickHouse продублируйте адрес oltp из адресной строки браузера.
![oltpclickhouse.jpg](/workspace/oltpclickhouse.jpg)


