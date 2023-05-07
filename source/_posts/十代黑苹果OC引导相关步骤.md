---
title: 十代黑苹果OC引导相关步骤
date: 2020-09-29 13:34:11
tags: [Hackintosh]
---
# 最近上了十代Intel
配置清单如下：
```
    CPU:            i5-10400F
    显卡:            蓝宝石RX580 2304 4G
    主板:            技嘉B460M AORUS ELITE
    硬盘:            SN750 500G
    无线网卡：        bcm943602cs
```

存在的问题：

```
睡眠问题CFG LOCK 问题
iMessage 不会搞
Facetime 不会搞



iCloud 以及AppStore 使用正常不闪退。
```

# 先上图

{% asset_img 1.png %}
{% asset_img 2.png %}
{% asset_img 3.png %}

# 我的EFI 下载地址

```
https://github.com/allcando/10th-Gen-Intel-Hackintoch-EFI
```

# 基础部分（镜像以及OC/KEXT搜集）
```
BiliBili 良心UP：司波图
https://www.bilibili.com/video/BV1uf4y1X7MT?p=2&t=48

```

# 部分Kexts内容解释

| 文件名 | 功能   |
| ---- | ------ |
| USBPorts.kext | usb补丁 |
| IntelMausiEthernet.kext | 板载千兆网卡i219-v补丁 |
| RadeonBoost.kext | RX系列显卡适配补丁   |
|  XHCI-unsupported.kext | 主板不支持XHCI需要添加此补丁 |
--------------------- 

# kexts 文件夹截图
{% asset_img 4.png %}


# 安装步骤
```
1.根据视频教程，直接刷写BASE版本的苹果OC镜像到U盘

2.根据视频步骤，添加或删除相关的kexts文件，主要是显卡、板载网卡的相关补丁。

3.重启进入OC，然后使用从互联网上安装macos。

  Tips：由于在宿舍搞，没有直接可以使用的互联网（在BASE的macos中无法认证校园网。。。。），因此使用了与视频不一样的另一种骚操作：插入另一个刷写有从[黑果小兵](https://blog.daliansky.net/)那里下载的最新镜像的u盘，此时BASE系统将会自动识别，并且可以直接安装。

```