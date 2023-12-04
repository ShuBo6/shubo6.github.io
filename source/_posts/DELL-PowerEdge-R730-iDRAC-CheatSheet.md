---
title: DELL PowerEdge R730 iDRAC CheatSheet
date: 2023-12-04 09:09:09
tags: [运维]
---

# DELL iDRAC CheatSheet
{% asset_img 1.png %}

## 默认账号密码
root/calvin (忘记密码可以重置)

## DELL iDRAC 远程桌面
{% asset_img 2.png %}
1. windows下使用ie浏览器可以直接在浏览器开启远程桌面
2. 其他操作系统使用java7版本的javaws运行viewer.jnlp，见下文

## java连接iDRAC 可能碰到的问题

由于iDRAC可能版本过老，使用以下方法可以暂时解决

1. 安装java7版本。否则可能出现 `查看器已终止。原因：网络连接中断。`问题 
{% asset_img 3.png %}

2. 进入 java 控制面板-> 高级 -> 高级安全设置 勾选使用TLS1.x,并在在安全选项增加idrac为例外站点。

```
windows 可以控制面板中找到java进入java控制面板
mac/linux 同理也可以在设置中找到java进入java控制面板
或者是在终端输入 javaws -viewer 命令也可以打开java控制面板
```
{% asset_img 4.png %}
{% asset_img 5.png %}
3. 编辑主机hosts文件将idrac的IP与idrac name进行绑定。登录时显示的常用名作为hostname配置到hosts。否则可能出现 `登陆失败,并出现无法访问错误`问题 
{% asset_img 7.png %}
常用名:
{% asset_img 6.png %}
```
# vim /etc/hosts

10.0.0.192 idrac

```

# DELL ipmi CheatSheet

## 风扇控制
（用来在允许的情况下强制降低服务器噪音）
1. 停止服务器的自动风扇控制，最后一位0x00表示停止自动风扇控制，0x01为开启自动风扇控制。
```bash
ipmitool -I lanplus -H idrac控制地址 -U 用户名 -P 密码 raw 0x30 0x30 0x01 0x00
```

2. 手动设置风扇（需要先停止自动风扇），最后一位0x01为设置1%的转速（16进制）。
```bash
ipmitool -I lanplus -H idrac控制地址-U 用户名 -P 密码 raw 0x30 0x30 0x02 0xff 0x01
```

## 传感器

1. 查看温度
```bash
ipmitool -I lanplus -H idrac控制地址-U 用户名 -P 密码 sensor list
```
