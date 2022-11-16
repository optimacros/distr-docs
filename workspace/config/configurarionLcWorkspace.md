---
title: Настройка Логин Центра и воркспейса, установленных на одном сервере
description: 
published: true
date: 2022-11-16T03:52:47.523Z
tags: install, workspace, login center
editor: markdown
dateCreated: 2022-11-11T06:08:10.669Z
---

# Зависимости

При одновременной работе LC и WS, установленный в LC nginx выступает как Reverse Proxy для установленного WS

Сначала устанавливаем Логин Центр и воркспейс, по соответствующим инструкциям в Содержании.

- [Чистая установка Логин Центра](/ru/login-center/cleaninstall)
- [Чистая установка дистрибутива воркспейса Optimacros](/ru/workspace/cleaninstallation)
{.links-list}
 


# Настройки в .env файле логин центра

После того как мы установили Логин Центра и Воркспейс.

Для начала нам необходимо в Логин Центре в файлике под названием .env, добавить переменную `WORKSPACE_PROXY_URL` и 
указать ей значение: `http://10.0.3.15` (Это тот самый container.ip который мы указывали в файлике manifest.json при 
его заполнении)
![addedvariable.png](/workspace/addedvariable.png)


# Настройка Reverse Proxy для Воркспейса.

Нам необходимо залить на сервер шаблоны nginx в папку `/om/login-center/data/nginx/templates`

Для работы с воркспейсом версии >=2.3.2

[ws80.conf.template](wsProxyTemplates/ws80template)

[ws443.conf.template](wsProxyTemplates/ws443.conf.new.template)

для версии < 2.3.2

[ws80.conf.template](wsProxyTemplates/ws80.conf.template)

[ws443.conf.template](wsProxyTemplates/ws443.conf.template)

[ws8081.conf.template](wsProxyTemplates/ws8081.conf.template)

# Установка сертификатов
Нужно добавить сертификат в `/om/login-center/data/nginx/cert/workspace1/bundle.crt` и его ключ в `/om/login-center/data/nginx/cert/workspace1/crt.key`


# Применение изменений
Необходимо сделать стоп\старт Логин Центра, перейдя в его root директорию и воспользовавшись утилитой `om`, используя команды 
```bash
./om stop
```
и затем 
```bash
./om start
```

[Настройка DNS Воркспейса](changeWorkspaceDns.md)

