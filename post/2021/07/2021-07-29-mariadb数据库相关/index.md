---
title: "mariaDB数据库相关"
date: "2021-07-29"
---

### 创建用户

命令:

```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

  
说明:  
username - 你将创建的用户名,  
host - 指定该用户在哪个主机上可以登陆,如果是本地用户可用localhost, 如果想让该用户可以从任意远程主机登陆,可以使用通配符%.  
password - 该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登陆服务器.  
例子:

```sql
CREATE USER 'm23100'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'm23100'@'192.168.1.101' IDENDIFIED BY '123456';
CREATE USER 'm23100'@'%' IDENTIFIED BY '123456';
CREATE USER 'm23100'@'%' IDENTIFIED BY '';
CREATE USER 'm23100'@'%';
```

### 授权:

命令:\*\*GRANT privileges ON databasename.tablename TO 'username'@'host' \*\*  
说明: privileges - 用户的操作权限,如SELECT , INSERT , UPDATE 等.如果要授予所的权限则使用ALL.;  
databasename - 数据库名,  
tablename-表名,  
如果要授予该用户对所有数据库和表的相应操作权限则可用 \* 表示, 如 \*.\*  
例子:

```sql
GRANT SELECT, INSERT ON test.user TO 'm23100'@'%';
GRANT ALL ON *.* TO 'm23100'@'%';
flush privileges; /*刷新权限：*/
```

注意:用以上命令授权的用户不能给其它用户授权,如果想让该用户可以授权,用以下命令:

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
```

mariadb数据库的相关命令是：

```bash
systemctl start mariadb #启动MariaDB
systemctl stop mariadb #停止MariaDB
systemctl restart mariadb #重启MariaDB
systemctl enable mariadb #设置开机启动
```

```sql
-- 查看用户列表
SELECT User, Host, plugin FROM mysql.user;

-- 改host
use mysql
update user set host = '%' where user = 'root';

-- 改密码
use mysql;  
UPDATE user SET password=password('newpassword') WHERE user='root';  
flush privileges;  
```

作者：m23100  
链接：[https://www.jianshu.com/p/4466e0cd0bd1](https://www.jianshu.com/p/4466e0cd0bd1)  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
