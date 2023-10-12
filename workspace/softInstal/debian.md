---
title: Установка на на операционную систему Debian
description: 
published: true
date: 2023-10-12T13:06:25.120Z
tags: workspace, debian
editor: markdown
dateCreated: 2022-11-10T09:04:42.654Z
---

# Для работы воркспейса на ОС Debian требуется предварительная установка пакетов.

Для установки пакетов нужно запустить bash скрипт с содержимым указанным ниже или выполнить эти команды последовательно:

> Версии пакетов vagrant устанавливаются именно те, что указаны в скрипте.
{.is-warning}
```bash
#!/bin/bash

# Инструкция тестировалась на Debian 10
# Требуются права root пользователя
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

# Настраиваем сеть lxc-net

cat <<EOT > /etc/lxc/default.conf
lxc.net.0.type = veth
lxc.net.0.link = lxcbr0
lxc.net.0.flags = up
lxc.net.0.hwaddr = 00:16:3e:xx:xx:xx
EOT

cat <<EOT > /etc/default/lxc-net
USE_LXC_BRIDGE="true"
LXC_BRIDGE="lxcbr0"
LXC_ADDR="10.0.3.1"
LXC_NETMASK="255.255.255.0"
LXC_NETWORK="10.0.3.0/24"
LXC_DHCP_RANGE="10.0.3.2,10.0.3.254"
LXC_DHCP_MAX="253"
EOT

systemctl enable lxc-net
systemctl restart lxc-net
systemctl start lxc
```