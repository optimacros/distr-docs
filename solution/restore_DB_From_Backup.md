---
title: Восстановление Базы Данных
description: 
published: true
date: 2023-05-26T10:05:17.669Z
tags: solution, mysql
editor: markdown
dateCreated: 2023-05-26T10:05:17.669Z
---

# Восстановление структуры таблицы в базе данных

Восстановление структуры базы данных может понадобится в двух ситуациях. 
1. При запуске выдает ошибку и не выполняется восстановление моделей. Веб интерфейс недоступен. Контейнер lxc после сбоя работает и доступен для входа.
![restoredb_ssherror.png](/workspace/restoredb_ssherror.png)
1. На работающем воркспейсе в веб интерфейсе вылазит ошибка при попытке восстановить модель из резервной копии. 
![restoredb_weberror.png](/workspace/restoredb_weberror.png)


В обоих случаях мы знаем id нужной модели для дальнейшей работы. id это последние цифры в имени таблицы из сообщения об ошибке. 
![restoredb_id.png](/workspace/restoredb_id.png)
Или мы получили от пользователей имя проблемной модели. Тогда id мы узнаем далее

> Хотя все равно если админка доступна лучше сходить и запустить восстановление что бы убедится в наличии проблемы. Если подтвердится в сообщении об ошибке мы увидим id.
{.is-warning}


## Порядок действий

Идем в каталог воркспейса в папку `container` и выполняем вход в него через команду:
```bash
vagrant ssh
```
Повышаем уровень привилегий до рута
```bash
sudo su
```
И входим в базу данных
```
mysql
```
Делаем запрос к базе данных
```sql
SELECT id,uid,name FROM corplan_main.models;
```
Нам нужен uid соответствующий id. Или если мы знаем только имя модели мы получим id и uid

![restoredb_sql.png](/workspace/restoredb_sql.png)

Выходим из mysql
```
quit;
```

Переходим в каталог \tmp и создаем временную директорию. Дальнейшие действия будем выполнять там. 

```bash
cd /tmp
mkdir db_tmp
cd db_tmp
```

Резервные копии базы данных хранятся в каталоге
`/home/optimacros_middlework/.optimacros_middlework/modelBackups/`

Далее идет имя каталога которое соответствует `uid` модели полученном на предыдущем шаге. Внутри этого каталога лежат архивы резервных копий. Имя архива содержит дату время создания резервных копий. 

Выведем список файлов архива отсортированных по времени в обратном порядке
```bash
 ls -latr /home/optimacros_middlework/.optimacros_middlework/modelBackups/0bd5069efd48cbb612eb139d36eff2e3/
```
где `0bd5069efd48cbb612eb139d36eff2e3` это `uid` модели

![restoredb_ls.png](/workspace/restoredb_ls.png)

Выполняем распаковку найденного архива

```bash
/home/optimacros_middlework/.optimacros_middlework/modelBackups/0bd5069efd48cbb612eb139d36eff2e3/2023-05-25_10-26-52_5414.zip
```
И восстановление базы
```bash
mysql corplan_model_193 < /root/tmp_backup_1889_193/dump.sql
```

:warning: здесь 193 это id таблицы

Выходим из контейнера по <kbd>ctrl</kbd> + <kbd>d</kbd> два раза. 

После этого есть два варианта действий. 

1. Если админка воркспейса не доступна и ошибка была в консоли делаем остановку воркспейса с ключом --force и последующий запуск.

```bash
cd /om/workspace-install
./current/install workspace --path /om/workspace1/manifest.json shutdown --force
./current/install workspace --path /om/workspace1/manifest.json up
```
2. Если веб интерфейс администратора доступен идем в админку. Заходим в модели. Находим нужную по ее имени. И нажимаем `open` В верхнем меню `Backups` находим последний и кликнем по его номеру 

![restoredb_admin.png](/workspace/restoredb_admin.png)
Далее нажимаем на Restore Model и ждем восстановления модели.


![restoredb_restorebackup.png](/workspace/restoredb_restorebackup.png)
