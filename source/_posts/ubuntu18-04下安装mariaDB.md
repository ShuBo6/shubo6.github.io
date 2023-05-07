---
title: ubuntu18.04下安装mariaDB
date: 2018-11-18 15:09:37
tags: Ubuntu
---

<br>

{% asset_img Ubuntu18.04logo.png ubuntu logo %}

## 1.安装服务

``` bash
apt-get install mariadb-server
```

## 2.启用启动服务


``` bash
sudo systemctl start mariadb
sudo systemctl enable mariad
```

## 3.后续设置命令


``` bash
mysql_secure_installation
```

## 4.登入数据库后，设置所有用户使用root连接，并更新设置。

``` bash
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '000000' WITH GRANT OPTION;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> flush privileges;
```

## 5.使用navicate远程连接时出现 "10061error"

### 解决方法:

#### (1）    修改/etc/mysql/my.cnf文件。打开文件，找到下面内容：
 
``` bash
Instead of skip-networking the default is now to listen only on
localhost which is more compatible and is not less secure.
bind-address  = 127.0.0.1
```

####   把上面这一行注释掉或者把127.0.0.1换成合适的IP，建议注释掉。

#### (2）但现在最新版的mariaDB 已将配置文件拆分此时my.cnf文件里面显示如下


``` bash
!includedir /etc/mysqql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
```

####  这两句话的意思是配置文件包含了上面两个文件夹所有的文件，那么现在一一查找 bind-address  = 127.0.0.1这句话写在哪了。
#### 之后在/etc/mysql/mariadb.conf.d/50-server.cnf
#### 此文件下找到bind-address = 127.0.0.1这句话，注释掉就行了。