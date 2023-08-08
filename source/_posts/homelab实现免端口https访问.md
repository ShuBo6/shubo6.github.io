---
title: homelab实现免端口http/https访问
date: 2023-08-08 15:58:46
tags:
---

了解到cloudflare强大的转发功能很长时间了。前段时间脑子一热就入了一个cloudflare的域名。这次再来补一下关于https的免端口访问步骤。

前提：
* 有公网IP
* 已经购买cloudflare域名


> 免端口访问，顾名思义就是使用http/https默认的80和443端口访问指定的服务。
而已知家用宽带运营商肯定是禁用这两个端口的，因此无域名隐式转发的情况下，就只能使用一些其他没被禁用的端口访问了。
而cloudflare刚好具备这个功能，这次就是依靠隐式转发实现。也就是说外网端口映射本质上还是非80和443，只是在cloudflare这一层会帮助你隐藏掉而已。

# Cloudflare 配置

{% asset_img cloudflare.png %}


首先设置cname解析并开启cloudflare的代理

```
selefra-apiserver.shubolab.com.	1	IN	CNAME	home.shubolab.com.
selefra-baseapi.shubolab.com.	1	IN	CNAME	home.shubolab.com.
```


在Origin Rules中新建规则:

```
(http.host eq "selefra-baseapi.shubolab.com") or (http.host eq "selefra-apiserver.shubolab.com")
```

重写到58080

# nginx 转发配置

{% asset_img nginx.png %}


|转发描述|来源主机名|来源协议|来源端口|目标协议|目标主机名|目标端口|
|--:|--:|--:|--:|--:|--:|--:|
|[cloudflare] selefra-apiserver |selefra-apiserver.shubolab.com|http|58080|http|192.168.123.3|58018|
|[cloudflare] selefra-baseapi|selefra-baseapi.shubolab.com|http|58080|http|192.168.123.3|58008|



 
