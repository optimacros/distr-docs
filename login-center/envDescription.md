---
title: Описание свойств .env файла
description: 
published: true
date: 2023-04-18T04:12:06.833Z
tags: login center, config
editor: markdown
dateCreated: 2023-04-18T03:20:01.419Z
---

# Описание свойств .env файла

- VERSION - Версия дистрибутива Логин Центра !изменять нельзя
- HOSTNAME - !изменяется при смене адреса Login Center
- DB_USERNAME - !изменяется при смене имени пользователя MongoDB
- DB_PASSWORD - !изменяется при смене пароля MongoDB
- GRAYLOG_PASSWORD - !достаточно для смены пароля администратора http://HOSTNAME/logs 
- GRAYLOG_PASSWORD_SHA2 - !всегда должен соответвовать SHA256 параметра GRAYLOG_PASSWORD
- ADMIN_USERNAME - !не используется после первого запуска Login Center!!Внимание: Это поле всегда должно быть почтовым адресом
- ADMIN_PASSWORD - !не используется после первого запуска Login Center!!Внимание в этом поле нельзя использовасть символ @
- WORKSPACE_NAME - !не используется после первого запуска Login Center
- WORKSPACE_HOSTNAME - при смене адреса воркспейса

> !поля GRAYLOG в последних версиях Логин Центра более не нужны, но их описания на всякий случай оставлены в документации
{.is-info}