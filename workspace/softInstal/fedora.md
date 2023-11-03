---
title: Установка на на операционную систему Fedora
description: 
published: true
date: 2023-11-03T07:27:18.114Z
tags: worksapce, fedora
editor: markdown
dateCreated: 2023-10-27T10:19:28.075Z
---

# Тело bash скрипта для установки пакетов на Fedora:
```bash
#!/bin/bash

# Инструкция тестировалась на Fedora 28
# Требует запуск под root пользователем

set -ex

cd /tmp

yum -y install epel-release
yum -y install nano tar zip unzip curl wget lxc lxc-templates dnsmasq bridge-utils

# Устанавливаем vagrant
dnf install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
vagrant --version

# Включить USE_LXC_BRIDGE в /etc/sysconfig/lxc
sed -E 's|(USE_LXC_BRIDGE=")false(")|\1true\2|' -i /etc/sysconfig/lxc

# Отключить использование nftables
echo 'LXC_USE_NFT="false"' >> /etc/sysconfig/lxc-net

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