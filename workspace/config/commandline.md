---
title: Параметры коммандной строки
description: 
published: true
date: 2023-02-14T09:40:54.718Z
tags: workspace, command line
editor: markdown
dateCreated: 2023-02-14T09:40:54.718Z
---

# Команды

## workspace

### up

Запуск воркспейса

**Example:**
```bash
./current/install workspace -- path /om/workspace1/manifest.json up
```


### shutdown

Остановка воркспейса

**Example:**
```bash
./current/install workspace -- path /om/workspace1/manifest.json shutdown
```

### info

Отобразить информацию о состоянии воркспейса.

**Example:**
```bash
./current/install workspace -- path /om/workspace1/manifest.json info
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
> Данное представление приведено для наглядности информации. Комманда выводит на экран `json` в сыром виде. Для форматирования вывода требуется использовать парсер json. Например `jq`
>
> ```
> ./current/install workspace -- path /om/workspace1/manifest.json info | jq
>```
{.is-info}

### status

Текущее состояние воркспейса

**Example:**
```bash
./current/install workspace -- path /om/workspace1/manifest.json status
```

Варианты состояния:
- **RUNNING** - воркспейс запущен

### maintenance








# Команды управления состоянием воркспейса

## Options

### -v,--version         

Вывести версию инсталятора.

### -h, --help

Отобразить справку

