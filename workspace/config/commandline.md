---
title: Параметры коммандной строки
description: 
published: true
date: 2023-02-14T10:48:40.614Z
tags: workspace, command line
editor: markdown
dateCreated: 2023-02-14T09:40:54.718Z
---

> Функционал позволяющий делать запросы состояния воркспейса, управлять режимом обслуживания и обновлять ssl сертификат, доступен для использования начиная с версии 2.18.0
{.is-info}

# workspace

## up

Запуск воркспейса. При использовании инсталлятора версии отличной от версии установленного воркспейса производится обновление воркспейса. 

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json up
```


## shutdown

Остановка воркспейса.

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json shutdown
```

## info

Отобразить информацию о состоянии воркспейса.

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json info
```

**Example output**
```json
{
  "status": "RUNNING",
  "maintenance": true,
  "version": "2.19.2",
  "requests": {
    "count": 1,
    "items": [
      {
        "id": "7AE7C11E12104D0589D374DAEED55BD2-0",
        "timePassed": "02:36:48",
        "timeStart": "2023-02-10 04:03:45 UTC",
        "status": "IN_PROGRESS",
        "user": "admin@optimacros.com",
        "message": "In progress",
        "modelName": "ОП для оптимакроса",
        "modelId": 233,
        "process": "PID #8655"
      }
    ]
  }
}
```
> Данное представление приведено для наглядности информации. Команда выводит на экран `json` в сыром виде. Для форматирования вывода требуется использовать парсер json. Например `jq`
>
> ```
> ./current/install workspace -- path /om/workspace1/manifest.json info | jq
>```
{.is-info}

## status

Текущее состояние воркспейса

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json status
```

Варианты состояния:
- **RUNNING** - воркспейс запущен

## maintenance

Выводит информацию и управляет режимом обслуживания воркспейса. В данном режиме отключается возможность работы с воркспейсом и доступен только раздел администратора воркспейса. 
Без параметров команда выводит текущее состояние данного режима в виде логической переменной `true` или `false`

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json maintenance
```

Для управления данным режимом есть два параметра. Для его включения и выключения, соответственно.

- **--enable**    Enable maintenance mode
- **--disable**   Disable maintenance mode 

## ssl-update

Данная команда вызывает обновление воркспейсом информации об используемых ssl сертификатах без его полной остановки и последующего запуска.
Для практического применения:
- Смотрим указанные пути в файле `manifest.json` до файла ssl сертификата и закрытого ключа
- Заменяем файлы при необходимости обновления в заданном каталоге
- Вызываем команду для обновления информации запущенным воркспейсом

> Данная команда используется только для обновления информации о сертификатах шифрования внутри lxc контейнера. В случае совместной установки воркспейса и логин центра, сертификаты обновляются через перезапуск логин центра. 
{.is-warning}

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json ssl-update
```

## version

Информация о версии запущенного дистрибутива. 

**Example:**
```bash
./current/install workspace --path /om/workspace1/manifest.json version
```

# help

Вывод справки по командам и допустимым параметрам

**Examples:**
```bash
./current/install help
./current/install help workspace
./current/install help up
```

# Опции

## --path

Обязательный параметр для команды `workspace` В параметре указывается абсолютный путь до файла `manifest.json`

## --version

Версия дистрибутива установщика. Важно не путать этот параметр с командой `version` используемой совместно с командой `workspace`

## --verbose

Подробный режим вывода для диагностики в случае ошибок


## --force

Форсировать применение команды. Преимущественно для ускорения остановки воркспейса командой `shutdown`


## --help 

Справка по командам используемым совместно с командой `workspace`

