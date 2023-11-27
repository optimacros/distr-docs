---
title: Настройка sudoers для работы Воркспейса.
description: Файл sudoers описывает команды и их правила для управления Воркспейсом через sudo
published: false
date: 2023-11-27T21:44:17.340Z
tags: 
editor: markdown
dateCreated: 2023-11-27T21:41:54.135Z
---

# SUDOERS
> При необходимости ограничить пользовательские возможности несколькими, заранее определёнными командами, в таком случае команды описываются в файле sudoers и выполняются с командой sudo.

## workspace:sudoers
В инсталлятор Воркспейса добавлена команда workspace:sudoers, которая 
 выводит контент для файла sudoers для текущего пользователя:
```
/om/workspace-installer/install workspace:sudoers --path /om/workspace1/manifest.json
```
Пример, вывода на экран, результата команды `install workspace:sudoers`:
![workspace_sudoers_stdout.png](/workspace_sudoers_stdout.png)

Вывод команды можно скопировать и поместить в файл `/etc/sudoers.d/om-ws-installer`
или прямо в `/etc/sudoers`
> :warning: Обязательно нужно использовать команду `visudo`
> команда `visudo` помогает отладить файл sudoers,
не потеряв доступ к терминалу в случае ошибки.
Для этого выполняем команду
```
visudo /etc/sudoers.d/om-ws-installer
```
Вставляем скопированный вывод команды `install workspace:sudoers`
и сохраняем файл.
- Пользователь готов к управлению Воркспейсом через команду `sudo`
- В случае ошибки при правке `sudoers` через команду `visudo`
`e` - вернуться в файл и исправить ошибку.
`x` - отмена правок файла `sudoers`.
Пример, вывода на экран, в случае ошибочного заполнения файла `sudoers` командой `visudo`:
![visudo_error_example_stdout.png](/visudo_error_example_stdout.png)