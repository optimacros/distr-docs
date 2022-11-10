---
title: Установка на на операционную систему Ubuntu
description: 
published: true
date: 2022-11-10T14:35:08.036Z
tags: workspace, ubuntu
editor: markdown
dateCreated: 2022-11-10T09:09:11.362Z
---

# Для работы воркспейса на ОС Ubuntu требуется предварительная установка пакетов.
Для установки пакетов нужно запустить bash скрипт с содержимым указанным ниже или выполнить эти команды последовательно:

!!! Пакеты vagrant устанавливаются именно тех версий которые указаны в инструкциях.
```bash
#!/bin/bash

# Инструкция тестировалась на Ubuntu 18.04/20.04 LTS
# Требует запуск под root пользователем
# Версия: 4

# Флаг для переключения скрипта в strict mode (остановка скрипта при ошибках)
set -ex

# Обновляем информацию о пакетах
apt-get update

# Устанавливаем новые пакеты
apt-get install -y software-properties-common lxc lxc-templates bridge-utils redir tar zip unzip curl wget

# Устанавливаем vagrant пакет
cd /tmp
wget -c https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.deb
dpkg -i vagrant_2.2.19_x86_64.deb

# Устанавливаем LXC плагин для vagrant
wget -c https://github.com/optimacros/vagrant-lxc/releases/download/v1.4.5/vagrant-lxc.tar.gz
tar -zxvf vagrant-lxc.tar.gz
vagrant plugin install  --plugin-clean-sources vagrant-lxc.gem
```