---
title: "数据库、nginx"
date: "2022-04-27"
---

```
mariadb: /etc/mysql/mariadb.conf.d/50-server.cnf
redis: /etc/redis/redis.conf
nginx: /etc/nginx/sites-enabled/default
redis设置密码：config set requirepass 123456
```

## nginx返回ip

```
location /ipaddr{
    default_type application/json;
    return 200 "{\"IP\":\"$remote_addr\",\"Port\":$remote_port}";
}
```
