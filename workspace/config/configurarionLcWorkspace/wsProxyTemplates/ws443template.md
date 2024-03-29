---
title: new ws443.conf.template
description: 
published: true
date: 2023-09-15T10:43:20.889Z
tags: 
editor: markdown
dateCreated: 2022-11-16T03:57:19.050Z
---

# new ws443.conf.template
```nginx
server {
    listen 443 ssl;

    server_name ${NGINX_WORKSPACE_HOST};

    ssl_certificate /etc/nginx/ssl/workspace1/bundle.crt;
    ssl_certificate_key /etc/nginx/ssl/workspace1/crt.key;

    client_max_body_size 1G;

    location / {
        proxy_pass ${NGINX_WORKSPACE_PROXY_URL};
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
        proxy_pass_request_headers on;
        proxy_connect_timeout       30;
        proxy_send_timeout          600;
        proxy_read_timeout          10800;
        send_timeout                600;

        # Web Socket Support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```