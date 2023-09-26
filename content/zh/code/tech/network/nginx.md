
---
title: "Nginx"
---

## config

```nginx
server {
    listen 80;

    access_log /var/log/nginx/api_v2.access.log main;
    underscores_in_headers on; # 支持读取非标准头部，格式示例：$http_a_b_c

    location /api/v2/ {
        proxy_pass http://0.0.0.0:8080;
        proxy_set_header Host $proxy_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Request-Id $http_x_request_id;
        proxy_connect_timeout 60; # nginx与upstream server的连接超时时间, 默认60s
        proxy_read_timeout 60; # nginx接收upstream server数据超时, 默认60s
        proxy_send_timeout 60; # nginx发送数据至upstream server超时, 默认60s
    }
}
```