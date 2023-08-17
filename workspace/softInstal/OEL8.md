---
title: Oracle Linux 8
description: Для работы воркспейса на операционной системе Oracle Linux 8
published: true
date: 2023-08-17T07:12:12.622Z
tags: 
editor: markdown
dateCreated: 2023-08-17T07:12:12.622Z
---

Тело bash скрипта для установки пакетов на OEL8:
```
#!/bin/bash

Инструкция тестировалась на Oracle Linux Server 8.8
Требует запуск под root пользователем
Версия: 1
set -ex

cd /tmp

Должен быть репозиторий Epel
yum -y install epel-release

yum -y install tar curl wget dnsmasq redir lxc lxc-templates

Устанавливаем vagrant
dnf install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
vagrant --version

Включить USE_LXC_BRIDGE в /etc/sysconfig/lxc
sed -E 's|(USE_LXC_BRIDGE=")false(")|\1true\2|' -i /etc/sysconfig/lxc

Запуск LXC
systemctl restart lxc-net
systemctl enable lxc-net

Устанавливаем LXC плагин для vagrant
wget -c https://github.com/optimacros/vagrant-lxc/releases/download/v1.4.5/vagrant-lxc.tar.gz
tar -zxvf vagrant-lxc.tar.gz
vagrant plugin install  --plugin-clean-sources vagrant-lxc.gem
```