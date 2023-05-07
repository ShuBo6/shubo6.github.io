---
title: Jquery JSONstr和JSONobj之间的转换和判断
date: 2019-04-02 22:13:52
tags:  [Jquery,JavaScript]
---

<br>
转自：http://bjlhx.cnblogs.com

# 1：jQuery插件支持的转换方式
代码如下:

### String→Object
`$.parseJSON( jsonstr );                     //jQuery.parseJSON(jsonstr),可以将json字符串转换成json对象`

反过来，使用 serialize 系列方法：如：`var fields = $("select, :radio").serializeArray();` 序列化表单

# 2：浏览器支持的转换方式(Firefox，chrome，opera，safari，ie9，ie8)等浏览器
代码如下:

### String→Object
`JSON.parse(jsonstr); //可以将json字符串转换成json对象`

### Object→String
`JSON.stringify(jsonobj); //可以将json对象转换成json对符串`

注：ie8(兼容模式),ie7和ie6没有JSON对象，需要引入 json.js 或 json2.js。

# 3：Javascript支持的转换方式 
### String→Object

`eval('(' + jsonstr + ')'); //可以将json字符串转换成json对象,注意需要在json字符外包裹一对小括号 `

注：ie8(兼容模式),ie7和ie6也可以使用eval()将字符串转为JSON对象
