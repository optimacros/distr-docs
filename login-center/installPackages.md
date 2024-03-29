---
title: Предварительные установки ПО необходимые для работы логин центра
description: 
published: true
date: 2023-08-17T07:08:33.678Z
tags: 
editor: markdown
dateCreated: 2022-11-09T04:56:33.478Z
---

Для установки Логин Центра всё что необходимо установить из пакетов это Docker. Иструкция по установке Docker для различных операционных систем: https://docs.docker.com/engine/install/#server

>RHEL и CentOS с 2018 года отсказались от использования Docker в пользу Podman, при попытке установки Docker из официального репозитория ОС, устанавливается Podman. В связи с этим установку Docker нужно производить из официального репозитория Docker: https://docs.docker.com/engine/install/ 
{.is-info}

## РЭД ОС (проверялось под v.7.3.1)

При установке Docker по инструкции для Centos после добавления репозиториев с https://download.docker.com/linux/centos/docker-ce.repo есть нюанс. При попытке обновить пакеты выдает ошибку 404. Дело в том что в РЭД ОС указанная в полученном файле переменная $releasever имеет значение 7.3.1 в то время как в репозитоииях докера есть каталог с именем 7.3. Самое просто заменить в ручную указанную переменную на ближайшее возможное значение. Посмотреть можно перейдя по ссылке указанной в попытке обновления взяв значение до версии пакетов.

Так же под РЭД ОС для работы docker требуется дополнительно поставить

	yum install libxcrypt-compat

Зависимостями он не ставится.

## RHEL 7.9
Предварительно требуется включить репозиторий

	subscription-manager repos --enable=rhel-7-server-extras-rpms
  
После этого установка по иснтрукции https://docs.docker.com/engine/install/rhel/ 

## Oracle Linux 8
```
#!/bin/bash

Инструкция тестировалась на Oracle Linux Server 8.8
Требует запуск под root пользователем
Версия: 1
set -ex

cd /tmp

yum install -y yum-utils

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

yum remove -y runc

yum install -y docker-ce

systemctl enable docker.service
systemctl start docker.service  
```
