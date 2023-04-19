---
title: Не пускает в модель
description: 
published: true
date: 2023-04-19T06:52:45.168Z
tags: solution
editor: markdown
dateCreated: 2023-04-19T06:52:45.168Z
---

# Не пускает в модель.

## Ошибка связанная с тем, что автобекапирование перестало работать:

![errorbackup.png](/solutions/errorbackup.png)
Проблема заключается в том, что на сервере в контейнере упал сервис scheduler-server нужно зайти на сервер в контейнер:

```
cd /op/om/workspace1/container
```

```
vagrant ssh
```

Далее в контейнере нужно перейти под root через команду `sudo su`. Так как на входе в контейнер вы будете под пользователем vagrant
Далее выполняем команды:

```
systemctl start scheduler-server
```

```
systemctl status scheduler-server
```
И проверяем, что сервис имеет активное состояние.

Затем переходим под пользователя optimacros_middlework, используя команду

```
su optimacros_middlework
```

Далее нужно перейти в директорию приложения командой

```
cd ~/optimacros_middlework
```

Далее нужно выполнить команду 

```
console/command backup-models
```

Дождаться результата, после чего проверить вход модель через фронтенд в браузере, должно стать всё заработать.

Далее, идём в админ панель воркспейса /admin/scheduler
И создаём новый заполняя как на скриншоте:

![schedulercreate.png](/solutions/schedulercreate.png)
Теперь автобекапирование включено, после чего было бы не плохо убедиться, что бекапы создаются

Время в разделе scheduler указывается +3 часа от МСК.