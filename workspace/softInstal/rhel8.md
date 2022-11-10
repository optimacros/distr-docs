---
title: Установка на на операционную систему RHEL 8
description: 
published: true
date: 2022-11-10T14:39:40.540Z
tags: workspace, rhel
editor: markdown
dateCreated: 2022-11-10T09:11:13.145Z
---

# Для работы воркспейса на операционной системе RHEL.

Тело bash скрипта для установки пакетов на RHEL:
```bash
#!/bin/bash

# Инструкция тестировалась на RHEL 8
# Требует запуск под root пользователем
# Версия: 2

set -ex

cd /tmp

# Должен быть предустановлен репозиторий Epel (Проверьте его наличие командой `yum repolist`)

yum -y install nano tar zip unzip curl wget lxc lxc-templates

# Устанавливаем vagrant
dnf install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
vagrant --version

# Включить USE_LXC_BRIDGE в /etc/sysconfig/lxc
sed -E 's|(USE_LXC_BRIDGE=")false(")|\1true\2|' -i /etc/sysconfig/lxc

# Запуск LXC
systemctl restart lxc-net
systemctl enable lxc-net

# Устанавливаем LXC плагин для vagrant
wget -c https://github.com/optimacros/vagrant-lxc/releases/download/v1.4.5/vagrant-lxc.tar.gz
tar -zxvf vagrant-lxc.tar.gz
vagrant plugin install  --plugin-clean-sources vagrant-lxc.gem

# Устанавливаем redir
wget -c https://nextcloud.optimacros.com/s/j3Xwn2MGzsLRTES/download -O redir_3.3_centos8_x86_64.zip
unzip redir_3.3_centos8_x86_64.zip
cp redir /usr/bin/redir
chmod +x /usr/bin/redir
```