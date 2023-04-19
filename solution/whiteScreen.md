---
title: Не работает приложение
description: 
published: true
date: 2023-04-19T06:40:00.649Z
tags: solution
editor: markdown
dateCreated: 2023-04-19T06:40:00.649Z
---

## Белый экран бесконечное вращение лоадера:

### Одной из причин проблемы может оказаться падение сервиса WebSocket.

Краткая инструкция для решения данной проблемы:
- Перейти в директорию контейнера воркспейса `cd /om/workspace1/contaner`
- Зайти в SSH терминал контейнера 
```bash
vagrant ssh
```

> ! Далее все пункты и команды осуществляются из командной строки контейнера
{.is-info}

- Повысить свои права до root 
```bash
sudo su
```
- Проверить статус сервиса `ws-server` 
```bash
systemctl status ws-server
```
- Зеленый (active, running) - значит эта инструкция не решит проблему и дальше не нужно следовать инструкции. Нам нужен красный статус (failed).
- Перезагружаем сервис
```bash
systemctl restart ws-server
```
- Смотрим статус сервиса, теперь он должен быть зеленым 
```bash
systemctl status ws-server
```
- Входим под пользователя `optimacros_middlework` 
```bash
su optimacros_middlework
```
- Переходим в директорию приложения 
```bash
cd ~/optimacros_middlework
```
- Выполняем команду 
```bash
console/command workspace-maintenance --stop
```
- Проверяем результат в браузере
- Закрываем терминал
