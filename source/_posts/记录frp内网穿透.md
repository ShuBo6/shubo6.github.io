---
title: 记录frp内网穿透
date: 2019-01-17 22:29:50
tags: [frp,Linux]
---

<br>

### 最近对内网穿透十分感兴趣。通过内网穿透可以实现使内网的设备被外部访问。

### frp这个工具相当强大。获取地址：https://github.com/fatedier/frp

### 由于手头的设备经常要变动所以，最近重复配置了多次frp。步骤倒是简单，好记性不如烂笔头。下边开始：

<br>

## 1、首先是frpserver端：
### 在git上获取项目通过ftp上传到你的云主机或者使用命令下载你需要的版本：

{% asset_img p1.png p1 %}

### 我的服务端是我的腾讯云主机 centos7 64位系统 所以在这里选择amd64版本


``` bash
wget https://github.com/fatedier/frp/releases/download/v0.23.1/frp_0.23.1_linux_amd64.tar.gz
```

### 下载完成后解压


``` bash
tar -zxvf xxxxx.tar.gz
```

### 进入解压后的目录

{% asset_img p2.png p2 %}

### 可以看到对应的有 frpc 、frps、frpc.ini、frps.ini这几个主要文件

### 在这里作为server端所以我只需要修改frps.ini这个文件

{% asset_img p3.png p3 %}



``` bash
[common]
bind_port = 65534                #frps服务绑定的端口号
dashboard_port = 65533       #frps监视页面的端口号
dashboard_user = admin       #frps监视页面的登陆用户名
dashboard_pwd = admin       #frps监视页面的登陆密码
vhost_http_port = 18080       #http端口
token = 123456                     #frpc连接要对的暗号
max_pool_count = 50
```

### 修改完成后保存执行以下命令生效（看好自己的路径哟）：


``` bash
/root/frp/frp/frps -c /root/frp/frp/frps.ini
```

 
## 2、自定义linux服务：
### 为了方便管理 以及可以开机自启我将frps自定义成了一个服务具体步骤如下：

### 首先写一个启动脚本`frps_start.sh`:(执行刚才那条命令 应用`frps.ini`配置文件的设置 并将日志重定向到`frps_startlog.txt`)


``` bash
#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:~/bin
sleep 30
/root/frp/frp/frps -c /root/frp/frp/frps.ini >>/root/frp/frps_startlog.txt
```

### 而后进入 /usr/lib/systemd/system建立自定义服务文件frps.service


``` bash
cd  /usr/lib/systemd/system
vim frps.service
```

### `frps.service`:



``` bash
[Unit]

Description=frps

[Service]

Type=oneshot

ExecStart=/root/frp/frps_start.sh

[Install]

WantedBy=multi-user.target
```


### 执行 更新`systemctl daemon-reload`
### 而后启动服务`systemctl start frps.service` 
### 开启自启`systemctl enable frps.service`



#### 等待服务启动后即可检查日志或是web监视页面

#### 服务器ip+端口号：

{% asset_img p4.png p4 %}

{% asset_img p5.png p5 %}

### 至此 frp 服务端配置成功

##  3.客户端配置frpclient：
### 客户端配置的操作和服务端思路相同

### 以树莓派为例：

### （1）首先是下载软件包

### 通过  命令查看系统版本和系统位数


``` bash
uname -a
getconf LONG_BIT
```


{% asset_img p6.png p6 %}
 

### 所以下载arm的软件包

{% asset_img p7.png p7 %}

### 然后解压（方法和上边相同），然后进入目录

{% asset_img p8.png p8 %}

### （2）在这里就需要修改frpc.ini这个配置文件了

{% asset_img p9.png p9 %}



``` bash
vim frpc.ini 

[common] 
server_addr = xx.xx.xx.xx #服务端的ip
server_port = 65534       #服务端的端口号
token = 123456　　　　　　  #暗号 哈哈哈哈不知道咋翻译 
 
[ssh] 
type = tcp 　　　　　　     #指定tcp协议
local_ip = 127.0.0.1 　　  #本地ip
local_port = 22  　　　　　 #端口号
remote_port = 65532　　    #穿透使用的端口号
```


### 然后是启动脚本：

{% asset_img p10.png p10 %}

### 然后到`/usr/lib/systemd/system`目录新建服务`frpc.service`文件

``` bash
cd /usr/lib/systemd/system/
root@raspberrypi:/usr/lib/systemd/system# vim frpc.service 

[Unit] 
 
Description=frpc 
 
[Service] 
 
Type=oneshot 
 
ExecStart=/home/pi/frpc_start.sh 
 
[Install] 
 
WantedBy=multi-user.target
```


### 启动服务  开机自启

``` bash
systemctl start frpc.service
systemctl enable frpc.service
```

### 而后等待启动成功后  即可去监视页面查看了

{% asset_img p11.png p11 %}

 

####  如上图 可以看到当前已经存在的。。ssh了。

### （3）测试
#### 下面进行测试

{% asset_img p12.png p12 %} 

{% asset_img p13.png p13 %}

####  成功！！！！！