---
title: ubuntu18.04下配置jdk1.8
date: 2018-11-18 03:01:00
tags: Ubuntu
---

<br>

{% asset_img Ubuntu18.04logo.png ubuntu logo %}

# ubuntu下配置jdk第一步与win10下相同  首先来到 [官网下载jdk](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)：
https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

{% asset_img p1.png p1 %}

### 在这里下载 压缩包的版本。

{% asset_img p3.png p3 %}

## 1.下载完成后，解压文件 使用命令：


``` bash
tar -zxvf jdk-8u191-linux-x64.tar.gz 
```

## 2.解压完成后 将文件移动到 /usr/lib


``` bash
mv jdk1.8.0_191 /usr/lib/jdk1.8
```

## 3.修改环境变量

### （1）使用全局设置方法，它是所有用户的共用的环境变量


``` bash
sudo gedit ~/.bashrc
```

### （2）在文件末尾追加路径


``` bash
export JAVA_HOME=/usr/local/jdk1.8
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=.:${JAVA_HOME}/bin:$PATH
```
{% asset_img p2.png p2 %}

### （3）生效~/.bashrc文件


``` bash
source ~./bashrc
```

### （4）最后执行这几条命令

``` bash
sudo update-alternatives --install /usr/bin/java java /usr/lib/jdk1.8/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jdk1.8/bin/javac 300
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jdk1.8/bin/jar 300
sudo update-alternatives --install /usr/bin/javah javah /usr/lib/jdk1.8/bin/javah 300
sudo update-alternatives --install /usr/bin/javap javap /usr/lib/jdk1.8/bin/javap 300
```

## 4.测试是否成功：
``` bash
java javac java -version
```
