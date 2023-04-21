---
title: Экспертиза по проблеме доступа из контейнера lxc на внешние ресурсы.
description: Ситуация, когда доступ из контейнера на внешние ресурсы, ограничен. 
published: true
date: 2023-04-21T13:55:16.668Z
tags: 
editor: markdown
dateCreated: 2023-04-21T13:55:16.668Z
---

# 
В случае если из контейнера Воркспейса отсутсвует доступ на до внешних ресурсов (например Базы Данных), но при этом из хоста, на котором установлен Воркспейс, ограничений нет.

Проверить соответствие кейса можно командой на хосте
```
iptables -L -n -t nat
```
команда выводит 
```
target     prot opt source               destination         

Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         

Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination         
```
Кейс совпадает если
в цепочке POSTROUTING отсутствует правило masquerade:
```
MASQUERADE  all  --  10.0.3.0/24         !10.0.3.0/24
```

Правило настраивается службой lxc-net.
Поскольку lxc 5 по умолчанию работает с nftables
а минимальная ubuntu 20.04 по умолчанию использует iptables
Для синхронизации необходимо принудительно указать lxc-net не использовать nftables
для этого правим файл
`/etc/default/lxc-net`
добавляем строку
```
LXC_USE_NFT="false"
```
и перезапускаем службу `lxc-net`
```
systemctl restart lxc-net
```
проверяем что правила маскарада появились в таблицах iptables
```
iptables -L -n -t nat
```

