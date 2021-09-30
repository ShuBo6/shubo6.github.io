---
title: MongoDB的使用记录(windows环境下的配置)
date: 2019-05-24 08:33:12
tags: [MongoDB]
---

<br>

#	Window 下配置MongoDB
## 运行安装程序将MongoDB安装到d:\mongodb目录下
### 创建文件夹d:\mongodb\data\db、d:\mongodb\data\log，分别用来安装db和日志文件，在log文件夹下创建一个日志文件MongoDB.log，即d:\mongodb\data\log\MongoDB.log

{% asset_img 1.png %}

{% asset_img 2.png %}

### 在D:\MongoDB\bin文件处打开命令行窗口（按住shift点击鼠标右键，选择打开）
输入： `mongod -dbpath "d:\mongodb\data\db"` 初始化启动服务并指定db数据存放在`d:\mongodb\data\db`下

{% asset_img 3.png %}

### 这个时候不要关闭窗口，默认MongoDB监听的端口是27017，此窗口是MongoDB临时启动的一个进程。
此时再打开一个窗口 执行`mongo.exe` 即可进入MongoDB的交互模式

{% asset_img 4.png %}

### 当刚才的mongod窗口被关闭时，mongo.exe 就无法连接到数据库了，因此每次想使用mongodb数据库都要开启mongod.exe程序，所以比较麻烦，此时我们可以将MongoDB安装为windows服务
　还是运行cmd，进入bin文件夹，执行下列命令
``` bash
d:\mongodb\bin>
mongod --dbpath "d:\mongodb\data\db" --logpath "d:\mongodb\data\log\MongoDB.log" --install --serviceName "MongoDB"
```
　这里MongoDB.log就是开始建立的日志文件，--serviceName "MongoDB" 服务名为MongoDB

### 启动、暂停、卸载服务
#### 启动 `net start MongoDB`
#### 停止 `net stop MongoDB`
#### 卸载服务
``` bash
    d:\mongodb\bin>mongod --dbpath "d:\mongodb\data\db" --logpath "d:\mongodb\data\log\MongoDB.log" --remove --serviceName "MongoDB"      
```    
 
### MongoDB导入数据
#### 例:
`mongorestore -h 127.0.0.1 -d employ --dir D:\dbbackup\employ\`

{% asset_img 5.png %}
 
