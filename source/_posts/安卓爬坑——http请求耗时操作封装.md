---
title: 安卓爬坑（一）——http请求耗时操作封装
date: 2019-06-19 16:06:57
tags: [Java,Android]
---
<br>

学习安卓有一段时间了。。。菜的难受，一直不知道怎么封装耗时操作的方法。只会现用现开线程。下面记录今天学会的方法：

## 场景重现：
 当我们频繁需要网络耗时操作的时候，例如http请求json数据，由于安卓不允许主线程耗时操作的机制，导致我们每次请求数据的时候都需要开启子线程，并且还要考虑线程之间通信、同步、异步等问题。非常繁琐。
## 解决方法：
 由于`Http`请求`Json`的形式比较固定。
 ### 封装思路为：

    类似`JQuery`中的`ajax`的使用方法，传入参数为`url`和`Json`字符串。返回值则是从`Http`中请求下来的`Json`字符串。

## 注意事项：
### 首先不要忘记在`AndroidManifest.xml`中添加网络权限
`<uses-permission  android:name="android.permission.INTERNET"/>`


### 我使用okhttp3的这个http框架。相比原生的请求方式代码可以短很多。

{% asset_img 1.png %}


## 代码：
###  MyHandler:
``` Java
package com.example.a81418.countrymap.HttpRequestUtils;

import android.content.Context;
import android.os.Handler;
import android.os.Message;
import android.widget.Toast;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class MyHandler extends Handler {
    private Context context;
    private DataCallBack callBack;
    public MyHandler() {
    }

    public MyHandler(Context context, DataCallBack callBack) {
        this.context = context;
        this.callBack = callBack;
    }

    public void getDataFromServer(Context context, RequestBean bean, DataCallBack callBack){
        MyHandler handler=new MyHandler(context,callBack);
        MyTask task=new MyTask(bean,handler);
        //获取CPU数量
        int cpunum = Runtime.getRuntime().availableProcessors();
        //线程池实例化
        ExecutorService service = Executors.newScheduledThreadPool(cpunum + 1);
        //将子线程放入线程池执行；
        service.execute(task);
    }
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        int what = msg.what;
        if(what==200){
            String json= (String) msg.obj;
            callBack.prosseData(json);
        } else {
            Toast.makeText(context, "请求失败！"+what, Toast.LENGTH_SHORT).show();
        }
    }



    public abstract interface DataCallBack{
        public abstract void prosseData(String json);
    }

}

```

### handler主要负责接受来自线程发送的message，并开启线程池。


### MyTask:

``` java
package com.example.a81418.countrymap.HttpRequestUtils;

import android.os.Message;
import android.util.Log;

import java.io.IOException;
import okhttp3.Call;
import okhttp3.Callback;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

public class MyTask extends Thread {
    private RequestBean requestBean;

    private MyHandler handler;
    public MyTask(RequestBean requestBean, MyHandler handler) {
        this.requestBean = requestBean;
        this.handler = handler;
    }

    @Override
    public void run() {
        super.run();
        OkHttpClient client=new OkHttpClient();
        final Request request = new Request.Builder()
                .url(requestBean.url)
                .build();
        Call call = client.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                String htmlStr = response.body().string();
                Message message=Message.obtain();

                if (response.code()==200){
                    message.what=response.code();
                    message.obj=htmlStr;
                    handler.sendMessage(message);
                }else {
                    message.what=response.code();
                    handler.sendMessage(message);
                }
                Log.e("MyTask", "onResponse: "+htmlStr );
            }
        });
    }
}
```
### MyTask继承了thread，将http请求的具体操作，以及请求成功或失败后返回结果的操作，放在继承的run()方法中。
### RequestBean：
``` java
package com.example.a81418.countrymap.HttpRequestUtils;

public class RequestBean {
    public String url;
    public String json;
    public String method="POST";

    public RequestBean(String url, String json) {
        this.url = url;
        this.json = json;
    }

    public RequestBean() {
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getJson() {
        return json;
    }

    public void setJson(String json) {
        this.json = json;
    }

    public String getMethod() {
        return method;
    }

    public void setMethod(String method) {
        this.method = method;
    }
}
```

### RequestBean自定义的请求bean类。一目了然。没的说。

## 运行截图
{% asset_img 2.png %}

纪念一下success的喜悦。