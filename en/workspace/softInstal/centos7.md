---
title: Установка на на операционную систему Centos 7
description: 
published: true
date: 2022-11-10T14:38:33.249Z
tags: workspace, centos
editor: markdown
dateCreated: 2022-11-10T10:04:27.991Z
---

# Установка для работы воркспейса на операционной системе CentOS 7.
## Добавление репозитория lxc
Создадим новый файл репозитория с помощью команды:

`nano /etc/yum.repos.d/lxc3.0.repo`

Заполним его следующим содержимым и затем сохраним:
```bash
[thm-lxc3.0]
name=Copr repo for lxc3.0 owned by thm
baseurl=https://copr-be.cloud.fedoraproject.org/results/thm/lxc3.0/epel-7-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://copr-be.cloud.fedoraproject.org/results/thm/lxc3.0/pubkey.gpg
repo_gpgcheck=0
enabled=1
enabled_metadata=1
```

## Тело bash скрипта для установки пакетов на CentOS:

```bash
#!/bin/bash

# Инструкция тестировалась на CentOS Linux release 7.9.2009 (Core)
# Требует запуск под root пользователем
# Версия: 2

set -ex

cd /tmp

yum -y install epel-release
yum -y install nano tar zip unzip curl wget lxc lxc-templates  lxc-extra libcap-devel libcgroup

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
wget -c https://nextcloud.optimacros.com/s/j3Xwn2MGzsLRTES/download -O redir_3.3_centos8_x86_64.zip
unzip redir_3.3_centos8_x86_64.zip
cp redir /usr/bin/redir
chmod +x /usr/bin/redir
```

Так же после всего можно проверить готовность lxc к работе с помощью команды: `lxc-checkconfig`

Кроме двух строк после: "User namespace: enabled" параметр у всех строк должен быть enabled.