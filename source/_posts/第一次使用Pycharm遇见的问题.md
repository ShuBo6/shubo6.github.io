---
title: 第一次使用Pycharm遇见的问题
date: 2019-03-19 00:31:26
tags: Python
---

<br>

# 问题

## 今天第一次使用pycharm时添加第三方库时，遇见了一个错误：`ModuleNotFoundError: No module named 'setuptools'`

## 该问题是由于Python默认是没有安装setuptools这个模块的，这也是一个第三方模块。

## 使用`pip install setuptools`即可解决。

## 然鹅。。。

## pip命令无法使用

{% asset_img 2.png %}

## 首先排除环境变量的问题，安装过程中勾选了add to path。故环境变量是没有问题的。

### 环境变量如图：

{% asset_img 1.png %}

## 此时打开script文件夹发现文件夹是空的，所以pip肯定是无法使用的

{% asset_img 3.png %}

# 解决方法

## 使用 `python -m ensurepip` 命令

{% asset_img 4.png %}

## 可以看到此时已经出现了pip3的可执行exe文件

{% asset_img 6.png %}

## 如果提示版本更新如下图

{% asset_img 5.png %}

## 则执行 `python -m pip install --upgrade pip`即可

{% asset_img 7.png %}