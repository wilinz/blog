---
title: "wordpress搭建"
date: "2021-07-29"
---

## 安装并配置mariaDB数据库

```bash
# 安装mariaDB数据库
apt update
apt install mariadb-server

#进入数据库
mariadb
```

```sql
--创建用户
create user 'wordpress'@'locathost' identified by 'password';
--授权用户
grant all on *.* TO 'wordpress'@'localhost';
--创建数据库
create database wordpress;
--刷新权限
flush privileges; 
--退出数据库
exit;
```

## 安装wordpress

```bash
#下载
wget http://wordpress.org/latest.tar.gz
#解压
tar -xzvf latest.tar.gz
```

## 配置 wordpress

```bash
#重命名配置文件
mv /root/wordpress/wp-config-sample.php /root/wordpress/wp-config.php
#编辑配置文件,修改文件中DB_NAME，DB_USER，DB_PASSWORD以及下面的唯一key
vim /root/wordpress/wp-config.php
```

```bash
#拷贝wordpress文件到web目录下
cp -r /root/wordpress/* /var/www/html
#修改权限（这些文件都是root的，而Nginx默认是www-data用户来运行，所以没有权限写入root的文件）
chown -R www-data:www-data /var/www/html
```

## 配置Nginx+PHP7

```bash
apt install nginx
apt install php-fpm php-mysql

# 编辑nginx配置文件
vim /etc/nginx/sites-enabled/default
```

```nginx
server {
# 将 index.php 添加到列表中
        index index.html index.htm index.nginx-debian.html index.php;

# 将 PHP 脚本传递给 FastCGI 服务器
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                # 使用 php-fpm（或其他 unix 套接字）：
                fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;               
        }
}
```

```bash
# 启动php服务
/etc/init.d/php7.4-fpm start
# 重启nginx
/etc/init.d/nginx restart

# 测试php
# 编辑下面文件
vim /var/www/html/info.php
```

#### 输入下面代码

```php
<?php 
phpinfo();
```

访问 http://你的网站地址/info.php，出现下面内容则配置nginx+php成功

![](images/VX0IKX7WYMWIHKEXTWKK.png)
