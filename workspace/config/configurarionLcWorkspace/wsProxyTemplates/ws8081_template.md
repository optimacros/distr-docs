---
title: ws8081.conf.template
description: 
published: true
date: 2022-11-16T04:01:31.355Z
tags: 
editor: markdown
dateCreated: 2022-11-16T04:01:31.355Z
---

# ws8081.conf.template
```nginx
server {
    listen 8081 ssl;

    server_name ${NGINX_WORKSPACE_HOST};

    ssl_certificate /etc/nginx/ssl/workspace1/bundle.crt;
    ssl_certificate_key /etc/nginx/ssl/workspace1/crt.key;

    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    location / {
        proxy_pass "${NGINX_WORKSPACE_PROXY_URL}:8080";
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
    }
}
```