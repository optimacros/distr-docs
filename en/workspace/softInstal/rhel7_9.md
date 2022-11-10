---
title: Установка на на операционную систему RHEL
description: 
published: true
date: 2022-11-10T14:38:33.249Z
tags: workspace, rhel
editor: markdown
dateCreated: 2022-11-10T08:54:13.042Z
---

# Тело bash скрипта для установки пакетов на RHEL 7.9

```bash
#!/bin/bash

# Инструкция тестировалась на RHEL 7.9
# Требует запуск под root пользователем
# Версия: 1

set -ex

# добавить epel репозиторий с lxc
cat <<\EOT > /etc/yum.repos.d/thm-lxc3.0-epel-7.repo
[copr:copr.fedorainfracloud.org:thm:lxc3.0]
name=Copr repo for lxc3.0 owned by thm
baseurl=https://download.copr.fedorainfracloud.org/results/thm/lxc3.0/epel-7-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://download.copr.fedorainfracloud.org/results/thm/lxc3.0/pubkey.gpg
repo_gpgcheck=0
enabled=1
enabled_metadata=1
EOT

cd /tmp

yum -y install nano tar zip unzip curl wget lxc lxc-templates dnsmasq

# Устанавливаем vagrant
yum install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
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
wget https://nextcloud.optimacros.com/s/Q4g4SyLkR3ZDepz/download/redir_3.3_centos8_x86_64.zip
unzip redir_3.3_centos8_x86_64.zip
cp redir /usr/bin/redir
chmod +x /usr/bin/redir
```