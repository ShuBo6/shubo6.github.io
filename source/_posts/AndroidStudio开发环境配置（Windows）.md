---
title: AndroidStudio开发环境配置（Windows）
date: 2018-12-23 18:18:00  
tags: [Android,Java,Windows]
---
<br>
 

 

 

 

AndroidStudio是一个非常主流好用的安卓app开发ide。

由于AndroidStudio很多配置文件都是在国外的一些网站上才能下载到，所以很多初学者在配置安卓环境的时候会浪费很多时间。在这里整理一下自己最近的配置环境步骤。

# 一、java环境配置
java环境配置,这里就不在仔细介绍了，请参照我之前的教程：

## [win10下配置java jdk jre环境变量](https://www.cnblogs.com/lzwangshubo/p/9959214.html)：https://www.cnblogs.com/lzwangshubo/p/9959214.html

# 二、环境准备
## 1.AndroidStudio下载：
推荐到[AndroidStudio中文社区](http://www.android-studio.org/)下载：

{% asset_img 1.png %}

## 2.sdk下载：
AndroidStudio有自带的sdkmanager，但是可能是我比较菜吧，每次下载找不到最新的sdk信息以及一些虚拟机工具。

所以这里sdk我推荐使用一个独立的sdk manager工具：下载地址：http://tools.android-studio.org/index.php/sdk/

{% asset_img 2.png %}

下载后打开：

{% asset_img 3.png %}

 

 

 点击下一步：（这里建立在本地有jdk环境的基础上的）
{% asset_img 4.png %}


继续下一步：（在这里指定sdk的存放目录）
{% asset_img 5.png %}


继续下一步开始安装即可

{% asset_img 6.png %}

安装后打开sdk manager（如何打开后没有出现下列的sdk信息可以在optaion中配置http代理

  推荐使用东软的代理：mirrors.neusoft.edu.cn 端口是80，在这里也感谢东软 ）

{% asset_img 7.png %}

在sdk manager中勾选要使用的sdk版本，在这里我选了最新的api28


{% asset_img 8.png %}
{% asset_img 9.png %}

 

 

还有额外的一些组件

{% asset_img 10.png %}

 

 然后点击开始安装

{% asset_img 11.png %}
{% asset_img 12.png %}


等待下载完成

{% asset_img 13.png %}

 

 

# 三、安装AndroidStudio
打开刚才下载的AndroidStudio安装包
{% asset_img 14.png %}
{% asset_img 15.png %}

 {% asset_img 16.png %}
{% asset_img 17.png %}


 

 

安装完成后打开刚刚安装的AndroidStudio

然后看我操作！

选Do not import setttings，点ok
{% asset_img 18.png %}


 点取消
{% asset_img 19.png %}


下一步

{% asset_img 20.png %}

 

 选第二个 下一步
{% asset_img 21.png %}


 

选一个你喜欢的主题颜色
{% asset_img 22.png %}


 选择你刚才安装sdk的目录
{% asset_img 23.png %}
 

然后下一步

 
{% asset_img 24.png %}
 

 点击finish并耐心等待下载

{% asset_img 25.png %}

完成后点击 finish
{% asset_img 26.png %}


这时候不要着急新建！！

 首先点击configure打开settings检查sdk路径是否正常
{% asset_img 27.png %}


 

然后 点击


{% asset_img 28.png %}
 

将默认的recommend的勾选去掉
{% asset_img 29.png %}


填入你本地jdk的路径 点击ok就可以了。

下面新建一个项目，可以看到新建项目后 他会自动下载gradle4.6   因为我是3.2版本的Androidstudio，，之前用的3.1.2对应的gradle是4.4。总之这个下载的过程还是需要大家耐心等待的。下次新建项目就不会出现这个情况啦。

{% asset_img 30.png %}

 这里gradle下载完成后  报出了一个错误说缺少28.0.2的组件，，可能是之前sdkmanager那一步勾选的不够全面 不过没关系 点击下边的蓝字

 {% asset_img 31.png %}

接受 然后等待下载完成后 点击finish就可以了 

{% asset_img 32.png %}

 

随后  还是耐心等待
{% asset_img 33.png %}


这个时候  三角号也绿了 下边的也绿了 我们的 安卓环境基本上就没有 问题了  

剩下的就是虚拟了了  或者你也可以打开你手机的usb调试  使用手机调试app了

# 四、虚拟机安装
AndroidStudio自带的虚拟机目前支持Intel处理器和比较新版本amd的而且必须开启bios的vt虚拟化技术，开启过程 非常简单，大家可以自行百度。

也可以使用第三方的安卓模拟器来作为虚拟机调试（例如  海马玩、夜神模拟器等等）

下面只给大家简单介绍 自带的模拟器

{% asset_img 34.png %}

新建虚拟机

然后下一步下一步完成就可以了  期间会让你安装一个intelhaxm的驱动

# 五、HelloWorld！

{% asset_img 35.png %}
 

 

 

感谢大家！！！

如果有不正确的地方欢迎大家指正



### 加油吧 