---
title: ws80.conf.template
description: 
published: true
date: 2022-11-16T03:54:55.707Z
tags: 
editor: markdown
dateCreated: 2022-11-16T03:54:55.707Z
---

# ws80.conf.template
```nginx
server {
    listen 80;
    server_name ${NGINX_WORKSPACE_HOST};

    return 301 https://${NGINX_WORKSPACE_HOST}$request_uri;
}
```