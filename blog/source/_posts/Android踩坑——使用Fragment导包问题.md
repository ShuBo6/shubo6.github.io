---
title: Android踩坑——使用Fragment导包问题
date: 2019-06-19 11:37:44
tags: [Java,Android]
---
<br>

> 今天使用fragment时，抛出了ClassCastException异常。顺手记录下解决方法。
{% asset_img 1.png %}

## `android.support.v4.app.Fragment`和`android.app.Fragment`区别,原因是因为使用的`module`中的`Fragment`与我自己创建的`Fragment`导入的包不一致。
## 解决方法是导入相同的包。


以下摘自CSDN博客作者：巨大蟹 
原文：https://blog.csdn.net/lin353809836/article/details/53717153 
### 1.最低支持版本不同

`android.app.Fragment` 兼容的最低版本是`android:minSdkVersion="11"` 即3.0版

`android.support.v4.app.Fragment` 兼容的最低版本是`android:minSdkVersion="4"` 即1.6版
### 2.需要导jar包

fragment 在定义的时候，要导入的包不同

`android.support.v4.app.Fragment` 需要引入包`android-support-v4.jar`

`android.app.Fragment` 需要导入的是`android-app.jar`

### 3.继承的父类不同，在`fragmentManager`和在`Activity`中取的方法不同

`android.support.v4.app.Fragment`使用  `fragmentManager=getSupportFragmentManager()`获得 ，并且当前的类必须继`FragmentActivity`

`android.app.Fragment`使用 `fragmentManager=getFragmentManager()` 获得  ，继承`Activity`即可。

### 4.<fragment>标签的使用情况（这点最重要了，也是决定你到底使用`v4`包中的`Fragment`还是`app`包的`fragment`）

`v4`包中的`Fragment`在`Activity`的布局中是可以使用`<fragment>`标签的，有些博客中也叫静态地载入`fragment`。

android.app.Fragment在`Activity`布局中是不可以使用`<fragment>`标签的，需要在程序中通过`add`或者`replace`的方式添加。

总结起来就是：当这个`Activity`的布局中有`fragment`标签的时候，这个`Activity`必须继承`FragmentActivity`，也就是使用`V4`包的`fragment`，否则就会抛出`android.view.InflateException: Binary XML file line #69: Error inflating class fragment`异常。

题外话：

我们使用`Fragment`的时候，选择哪个包下的`Fragment`呢？

到底是用`Android.app`下的`Fragment`还是用的`android.support.v4.app`包下的`Fragment`?

我们都知道`Fragment`是`3.0(API 11)`后引入的，那么如果开发的app需要在3.0以下的版本运行呢?比如还有一点点市场份额的2.3!

于是乎,v4包就这样应运而生了,而最低可以兼容到1.6版本!

至于使用哪个包看你的需求了,现在3.0下手机市场份额其实已经不多了,随街都是4.0以上的,所以这个时候,你可以直接使用`app`包下的`Fragment`,然后调用相关的方法
通常都是不会有什么问题的;如果你`Fragment`用了`app`包的,FragmentManager和`FragmentTransaction`都必须是app包的

