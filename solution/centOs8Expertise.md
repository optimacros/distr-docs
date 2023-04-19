---
title: Особенности касающиеся CentOS 8
description: 
published: true
date: 2023-04-19T06:33:44.366Z
tags: solution
editor: markdown
dateCreated: 2023-04-19T06:33:44.366Z
---

# Экспертиза: Особенности касающиеся CentOS 8.

## CentOS 8:
### Перед установкой системы контейнеризации Docker на Centos 8, проверьте следующие пункты:
Если ваша ОС управляется firewalld, то проверьте наличие включенного маскарада. В случае включенного маскарада, 
результат команды 
```bash
firewall-cmd --zone=public --query-masquerade
```
должен быть `yes`. 
Для включения маскарада используйте команду 
```bash
firewall-cmd --zone=public --add-masquerade --permanent
```