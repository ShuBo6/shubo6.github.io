---
title: ubuntu18.04下安装eclipse-jee
date: 2019-02-17 08:05:13
tags:  [Ubuntu,Linux]
---

<br>

{% asset_img Ubuntu18.04logo.png ubuntu logo %}

## 1.要使用eclipse首先配置jdk以及jre的java环境变量。前边已经写过了这里就不写了[详情点击](http://blog.shubo6.cn/2019/02/17/ubuntu18-04%E4%B8%8B%E9%85%8D%E7%BD%AEjdk1-8/)

## 2.到[eclipse官网下载](http://www.eclipse.org/downloads/packages/) eclipse jee。

{% asset_img p1.png p1 %}

### 在这里我下载了linux64位压缩包版本的

## 3.下载完成后将文件移动到 /opt/eclipse-jee文件夹

{% asset_img p2.png p2 %}

## 4.解压压缩包


``` bash
tar xzvf eclipse-jee-2018-09-linux-gtk-x86_64.tar.gz
```

## 5.创建eclipse桌面快捷图标

### （1）编辑eclipse.desktop


``` bash
cd /usr/share/applications
vim eclipse.desktop
```

### （2）在文件中写入(其中“Exec=”后面为eclipse安装目录下的eclipse程序的位置路径，“Icon=”后面为eclipse安装目录下的图标图片的路径)

``` bash
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse
Exec=/opt/eclipse-jee/eclipse/eclipse
Icon=/opt/eclipse-jee/eclipse/icon.xpm
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
```

{% asset_img p3.png p3 %}

## 6.将eclipse变为可执行文件


``` bash
chmod u+x eclipse.desktop
```

## 7.在/usr/share/applications目录下即可打开eclipse（也可以复制到桌面打开）
{% asset_img p4.png p4 %}
{% asset_img p5.png p5 %}