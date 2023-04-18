---
title: Настройка DNS Воркспейса
description: 
published: true
date: 2023-04-18T04:53:55.196Z
tags: workspace, dns
editor: markdown
dateCreated: 2023-04-18T04:53:55.196Z
---

# Настройка DNS Воркспейса

По умолчанию в контейнере работает DNS с адресом 10.0.3.1:53

Обычно на данном адресе висит DNS сервис хостовой ОС (dnsmasq)

Если необходимо переопределить DNS для Воркспейса, то можно использовать свойство манифеста [`container.dns`](/ru/workspace/config/manifest#dns)

Если в DNS нужное доменное имя не резолвится, его можно прописать через свойство манифеста [`container.hosts`](/ru/workspace/config/manifest#hosts)

Например имеем адрес LC `адресЛогинЦентра.ru`, который не прописан в DNS, поэтому его укажем в блок `hosts`. В качестве IPv4 адреса используем адреc сервера на котором LC доступен, например`10.11.12.13`.

```json
"hosts": {
        "адресЛогинЦентра.ru": "10.11.12.13"
 },
```

После данных настроек файла манифеста, если воркспейс уже был запущен, его нужно остановить и запустить заново с помощью последующего выполнения команд:

**остановка работы воркспейса**
```
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json shutdown
```  
**запуск работы воркспейса**

```
/om/workspace-installer/current/install workspace --path /om/workspace1/manifest.json up
```

Если воркспейс не был запущен ранее, то просто запускаем его работу.