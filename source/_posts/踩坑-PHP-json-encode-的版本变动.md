---
title: '踩坑:PHP json_encode()的版本变动'
date: 2019-04-25 13:40:46
tags: PHP
---
<br>

整理一下今天遇见的坑。
故事是这样的，今天因为要使用阿里的短信接口api，所以后台的php版本需要升级到5.5以上，原先是5.4.6。
所以肯定果断 `yum  remove php*`，
然后 `yum install #￥%……&`，
然后 `php -v`，
哎，然后就发现 更换版本完成了，就觉得不可能这么顺利。
然后运行了一下phpinfo，哎还真行。
然后把之前就服务器上的php代码搬过来。
故事就开始了。。。
原先5.4.6能运行的 到了php7就完蛋了？
然后我换成了5.6然而还是不行
这时候我意识到了严重性



## 在网上查到了官方文档：
{% asset_img 1.png  %}

### 可以看到在5.5版本增加了：失败时返回的值从 null 字符串改成 FALSE。
## 原因：
php从mysql数据库中取出数据如果不指定编码方式为utf8那么中文将会变成"?"。因此返回值会变成false。
## 解决方法：
首先要保证从mysql数据库中取出的数据编码方式是正确的，
所以
``` php
$conn = new mysqli($servername, $username, $password,$DBname,$port);
//下边紧跟着执行一句
mysqli_query($conn,"SET NAMES utf8");
```
就可以解决中文乱码的问题
此时解决了中文乱码之后，问题其实就已经解决了。

