---
title: Настройка sudoers для работы Воркспейса.
description: Файл sudoers описывает команды и их правила для управления Воркспейсом через sudo
published: false
date: 2023-11-27T21:55:55.597Z
tags: 
editor: markdown
dateCreated: 2023-11-27T21:41:54.135Z
---

# SUDOERS
> В случае необходимости ограничить пользовательские возможности заранее определёнными командами, создают список допустимых команд в файле sudoers, для последующего выполнения их через sudo.

## workspace:sudoers
В инсталлятор Воркспейса добавлена команда workspace:sudoers, которая 
 выводит контент для файла sudoers для текущего пользователя:
```
/om/workspace-installer/install workspace:sudoers --path /om/workspace1/manifest.json
```
Пример вывода результата команды `install workspace:sudoers`:
![workspace_sudoers_stdout.png](/workspace_sudoers_stdout.png)

Вывод команды можно скопировать и поместить в файл `/etc/sudoers.d/om-ws-installer`
или прямо в `/etc/sudoers`
> :warning: Обязательно нужно использовать команду `visudo`
> команда `visudo` помогает отладить файл `sudoers`,
не потеряв доступ к терминалу в случае ошибки.

Для правки файла `sudoers` выполняем команду:
```
visudo /etc/sudoers.d/om-ws-installer
```
**Вставляем скопированный вывод команды `install workspace:sudoers`
и сохраняем файл.**
- При успешном сохранении файла `sudoers` - пользователь готов к управлению Воркспейсом через команду `sudo`
- В случае ошибки, команда `visudo` укажет на это примерным выводом на экран:
![visudo_error_example_stdout.png](/visudo_error_example_stdout.png)
в таком случае можно нажать
`e` - вернуться в файл и исправить ошибку.
`x` - отмена правок файла `sudoers`.