---
title: Совместная установка Application Manager и Workspace на один сервер
description: 
published: true
date: 2023-04-26T12:49:48.573Z
tags: workspace, appmanager, application manager, dual install
editor: markdown
dateCreated: 2023-04-26T11:19:15.418Z
---

# Совместная установка Application Manager и Workspace на один сервер

## Предварительная подготовка к настройке совместной работы

Первым делом требуется выполнить шаги для предварительной установки софта. Для application Manager нам требуется установленный docker. Для работы воркспейса требуется lxc

Docker ставим по аналогии с логин центром по этой инструкции: [предварительная установка ПО для работы логин центра](/ru/login-center/installPackages)
По для работы воркспейса ставим по этой инструкции: [softInstal](/ru/workspace/softInstal)

При необходимости выполняем перенос директорий Docker и LXC на раздел для установки OM [Как изменить директорию для LXC и Docker](/ru/workspace/maintenance/dockerAndLxcChangeDataFolder)

Далее ставим Application Manager и выполняем его присоединение к логин центру.
- [Установка Application Manager](/ru/app_manager/cleanInstallationAm)
{.links-list}


## Настройка Application Manager

Имеем рабочий Application Manager соединенный с логин центром стоящим на другом сервере. Для совместной работы с воркспейсом требуется выполнить настройку docker контейнера nginx в качестве прокси сервера для запросов к воркспейсу.

### Настройка Reverse Proxy для Воркспейса.

Нам необходимо залить на сервер шаблоны nginx в папку внутри каталога application manager'а `data/nginx/templates`

[ws80.conf.template](wsProxyTemplates/ws80template)

[ws443.conf.template](wsProxyTemplates/ws443template)

### Установка сертификатов
Нужно добавить сертификат в `data/nginx/cert/workspace1/bundle.crt` и его ключ в `data/nginx/cert/workspace1/crt.key`

> Имя сертификата и ключа не следует менять так как они жестко прописаны в шаблонах nginx
{.is-info}

### Переменные окружения 

Внутри каталога application manager'а требуется внести переменные окружения для работы воркспейса. В файл .env дописываем следующие переменные:

```ini
WORKSPACE_HOSTNAME=ws.optimacros.com
WORKSPACE_PROXY_URL=http://10.0.3.15
```

### Правка docker-compose файла

В каталоге application manager'а вносим переменные окружения для работы nginx docker контейнера. Открываем файл `10.docker-compose-nginx.yml` и вносим переменные в разделе `environment` В итоге этот раздел примет такой вид


```yaml
...
    environment:
      NGINX_HOST: "${HOSTNAME}"
      BACKEND_SERVER_URI: "am-runner:18788"
      WEB_FRONTEND_SERVER_URI: "am-webui:18789"
      NGINX_WORKSPACE_HOST: "${WORKSPACE_HOSTNAME}"
      NGINX_WORKSPACE_PROXY_URL: "${WORKSPACE_PROXY_URL}"
      
...
```
Здесь указывается соответствие между переменными указанными в `.env` файле и переменными которые используются в файлах nginx шаблонов `ws80.conf.template` и `ws443.conf.template`

### Применение конфигурации

Для применения сделанных изменений требуется выполнить перезапуск application manager'а. Выполняем команды

```bash
./stop.sh
./start.sh
```

## Установка воркспейса

Далее выполняем установку воркспейса по инструкции ниже с учетом пары замечаний. 

1. В файле `manifest.json` не должно быть настроек для проброса портов с 80го и 443го порта хоста. Они заняты работающим application manager'ом, а он в свою очередь перенаправляет запросы к lxc контейнеру по ip адресу указанному в .env файле
2. В файле `manifest.json` не должно быть секции `web`.`ssl` в параметрах `workspace`. SSL соединение так же работает через application manager


Инструкция
- [Чистая установка воркспейса](/ru/workspace/cleaninstallation)
{.links-list}
