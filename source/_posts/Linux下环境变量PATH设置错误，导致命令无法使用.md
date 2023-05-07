---
title: Linux下环境变量PATH设置错误，导致命令无法使用
date: 2019-03-05 18:20:23
tags: Linux
---

<br>
### 今天在配置jdk过程中遇见了，java命令能用，其他命令全部失效的情况。
{% asset_img p2.png p2 %}

### 下图是我的 /etc/profile文件中的环境变量配置

{% asset_img p3.png p3 %}

### 可以看到 图中`PATH=$JAVA_HOME/bin:$JRE_HOME/bin `中，只指定了jdk的环境变量，并没有引入原有的PATH，故分析原因为PATH被覆盖导致命令失效。

# 解决方法：

### 1.使用export 手动导入以下目录后手动将path纠正。
 ``` bash
 export PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
 ```
 ### 2.进入vi或vim的安装目录，再使用命令，手动将path中的$path添加进去。
  ``` bash
 [root@master bin]# pwd
/usr/bin
[root@master bin]# ./vim
vim       vimdiff   vimtutor  
[root@master bin]# ./vi
vi        view      vim       vimdiff   vimtutor  
[root@master bin]# ./vi /etc/profile
或者
[root@master bin]# ./vim /etc/profile
 ``` 

 {% asset_img p4.png p4 %}
 ### 3.正确的PATH填写方式
  {% asset_img p1.png p1 %}
### 4.使用` source ` 命令更新配置文件。
<br>
---
#####  开学第二天。