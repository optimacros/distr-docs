---
title: Установка на на операционную систему Astra Linux
description: 
published: true
date: 2022-11-11T05:28:34.457Z
tags: workspace, astra linux
editor: markdown
dateCreated: 2022-11-11T05:28:34.457Z
---

# Работа воркспейса на операционной системе Astra Linux

Тело bash скрипта для установки пакетов на Astra Linux:
```bash
# Инструкция тестировалась на Astra Linux 1.7 SE (Смоленск)
# Требует запуск под root пользователем
# Версия: 1

# Флаг для переключения скрипта в strict mode (остановка скрипта при ошибках)
set -ex

# раскомментировать или добавить параметр в /etc/systemd/logind.conf

if [[ $(grep 'KillUserProcesses' /etc/systemd/logind.conf) ]]; then sed -i '/KillUserProcesses/s/^#//' /etc/systemd/logind.conf; sed -i '/KillUserProcesses/s/yes/no/' /etc/systemd/logind.conf; else echo 'KillUserProcesses=no' >> /etc/systemd/logind.conf; fi

# после правки logind.conf необходимо перезапустить сервис
systemctl restart systemd-logind.service

cd /tmp

# Устанавливаем корневые сертификаты

apt-get install ca-certificates

# Устанавливаем пакет управления сетевыми мостами

apt-get install bridge-utils

# Устанавливаем дополнительный пакет для работы DHCP сети LXC

apt-get install dnsmasq-base

# Устанавливаем LXC

wget http://ftp.ru.debian.org/debian/pool/main/l/lxc/liblxc1_3.1.0+really3.0.3-8_amd64.deb
wget http://ftp.ru.debian.org/debian/pool/main/l/lxc/lxc_3.1.0+really3.0.3-8_amd64.deb

dpkg -i liblxc1_3.1.0+really3.0.3-8_amd64.deb
dpkg -i lxc_3.1.0+really3.0.3-8_amd64.deb

# Устанавливаем пакет для проброса портов LXC контейнеров

wget http://ftp.ru.debian.org/debian/pool/main/r/redir/redir_3.2-1_amd64.deb
dpkg -i redir_3.2-1_amd64.deb

# Устанавливаем утилиту Vagrant для управления LXC контейнерами
wget https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.deb
wget https://github.com/optimacros/vagrant-lxc/releases/download/v1.4.5/vagrant-lxc.tar.gz

dpkg -i vagrant_2.2.19_x86_64.deb
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
```