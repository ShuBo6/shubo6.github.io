---
title: windows下手动搭建wamp环境
date: 2019-02-21 20:12:09
tags: WAMP
---

<br>


## 什么是wamp环境？
- ### 所谓wamp环境就是指在我们常用的windows环境下搭建Apache+Mysql/MariaDB+PHP的集成环境。
## 为什么使用wamp？
- ### wamp集成了php的解析器以及mysql数据库，能够实现一些常用的动态网站需求
## 主要的WAMP集成环境主要有：
### 1、**WampServe** 
### 2、**XAMPP** 
### 3、**AppServ**
# 下面我们来演示如何手动搭建wamp集成环境
## 1.首先下载各环境所需文件：（访问官网建议科学上网，需要其他版本自行到官网下载）
- #### apache2.4 [64位](https://home.apache.org/~steffenal/VC15/binaries/httpd-2.4.38-win64-VC15.zip) [32位](https://home.apache.org/~steffenal/VC15/binaries/httpd-2.4.38-win32-VC15.zip)
- #### mysql5.7.25 [64位](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.25-winx64.zip) [32位](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.25-win32.zip)
- #### php（Thread Safe） [64位](https://windows.php.net/downloads/releases/php-5.6.40-Win32-VC11-x64.zip) [32位](https://windows.php.net/downloads/releases/php-5.6.40-Win32-VC11-x86.zip)
- #### 如果出现无法运行的情况请自行到微软官网下载vc组件 
## 2.开始设置配置文件


#### 1） Apache默认使用80端口，在计算机中一个端口只能被一个软件占用。
####  &nbsp;&nbsp;如果80端口被占用：

#### &nbsp;&nbsp;①停止80端口暂用的软件

#### &nbsp;&nbsp;②修改apache默认端口


#### 2） 使用DOS命令管理Apache
#### 必须要用管理员身份打开dos窗口
#### 安装：`httpd -k install`
#### 卸载：`httpd -k uninstall`
#### 启动/关闭/重启：`httpd -k start/stop/restart`
#### 查看版本：`httpd -v`  	        （version）
#### 查看支持模块：`httpd -m `	 	  （modules）
#### 查看配置文件是否有错误：`httpd -t`  （test）    
#### 修改httpd.conf 文件（此步骤较长且网上教程较多自行google）
#### 3） 测试Apache是否安装成功
##### 访问：http://127.0.0.1   或 http://localhost   

## 安装PHP
#### 1)将php解压
#### 2)让Apache支持PHP
##### 修改httpd.conf 文件
##### 在httpd.conf 文件末尾追加以下内容

``` conf
#声明PHP配置文件所在目录（php.ini）
PHPIniDir D:\wamp\php5
#加载php7模块（目的：让apache有能力处理php文件）
LoadModule php5_module D:\wamp\php5\php5apache2_4.dll
#让apache遇到.php文件交给PHP模块处理
AddType application/x-httpd-php .php
```

## 安装MySQL
### 先把mysql解压

#### 1)修改配置文件（my.ini）

``` conf
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=O:\mysql-8.0.11-winx64
# 设置mysql数据库的数据的存放目录
datadir=O:\mysql-8.0.11-winx64\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```

### 2)使用DOS命令管理MySQL
 #### 前提是把mysql解压好了  ini文件修改好了dos窗口一定要用管理员权限打开
#### 安装服务：`mysqld --install`
#### 卸载服务：`mysqld --remove mysql`
#### 启动/关闭：`net start/stop mysql`





