---
title: 中国联通家庭智能网关 EPON/4+1+WiFi(2.4G) 管理员密码破解
date: 2020-02-14 20:24:11
tags: [家庭组网,JavaScript]
---

坐标lylzad ，今天刚从联通公司办理了千兆光猫更换，200M的宽带用百兆光猫怎么也说不过去啊，于是今日便得到了这台新的光猫。
{% asset_img 1.jpg %}
刚接上路由器，令我头大的事情就发生了，这个新的光猫是光猫路由器二合一，自动给我完成了拨号，这肯定不能忍啊，我的ddns啊 ！！！
于是走上了光猫管理员密码破解之路。

可以看到光猫的背面是提供了一个 普通用户的登录账号和密码的
{% asset_img 1.1.jpg %}
在朋友的帮助下，看了很多帖子，整体来说有两种方法来破解：
## 1.通过telnet:

可以看这个贴子:[山东联通 kd-yun-811e 光猫超级密码获取](https://www.v2ex.com/amp/t/598856)，不过我没有成功。而后又看了这个工具：[天邑光猫配置工具](http://www.downcc.com/soft/316901.html)貌似这个工具是适用于电信的。

## 2.通过修改网页js:

通过修改js绕过表单验证实现密码修改。受到[一篇帖子](https://www.emengweb.com/p/11e4745f54c4)的启发，他遇见的情况是，网页限制没有提供CUAdmin这个用户名登陆的入口，然而通过修改js的表单值，实现了使用用户名和密码都是CUAdmin的方式实现了管理账户登录。

而我也试图使用这种方法发现我的情况并不相同，如图，有两个入口，我的版本给我了CUAdmin的这个登录入口，并且我使用CUAdmin登录并没有成功。
{% asset_img 2.png %}
惊喜便来了，当我使用默认的user账户登录之后发现了这么一个修改user用户密码的页面看了一会这个修改密码的提交代码后发现，改修改代码就是简单的一个post表单并且仅仅使用了几行js来验证表单数据。
{% asset_img 4.png %}
{% asset_img 5.png %}
``` JavaScript
function submit_pwd() 
	{
		var docForm = document.getElementById('form_telacccfg');
		with(docForm) 
		{
			if ( window.parent.authLevel == 1)
			{
				if ( UserOldPassword.value != userPassword)
				{
					alert("旧密码输入错误!");
					return;
				}				
			}
			
			if (UserPassword.value != AdminPassword.value)
			{
				alert("密码两次输入不一致!");
				return;
			}	
			
			var str = new String();
			str = UserPassword.value;
			if (str.length > 64) 
			{
				alert('密码长度不能超过64个字符!');
				return;
			}
			if (str.indexOf(' ') != -1) 
			{
				alert('密码不能包含空格');
				return;
			}
			if ( parent.authLevel == USER && str.length >= 16)
			{
				alert('用户密码长度不能超过15个字符!');
				return;
			}
			if ( window.parent.authLevel == 1) //user
			{
				widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_CU_Function.Web.UserPassword', UserPassword);				

			}
			else{
				if( $("Username_text").options[0].selected == true) //admin
				{
					//widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_BROADCOM_COM_LoginCfg.AdminPassword', UserPassword);
					//对‘#’特殊字符进行转义 add by:wh 2017.9.28
//					if(str.indexOf('#')>-1){
//						str=str.split("#");
//						var passwordStr=str[0]+'%23'+str[1];
//						loc = "userpasswd.htm?a=set&x=InternetGatewayDevice.X_CU_Function.Web.AdminPassword&AdminPassword=" + passwordStr;
//					}
//					else{
//						loc = "userpasswd.htm?a=set&x=InternetGatewayDevice.X_CU_Function.Web.AdminPassword&AdminPassword=" + str;
//					}
//					var code = 'location="' + loc + '"';
//					eval(code);
					widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_CU_Function.Web.AdminPassword', AdminPassword);
				}
				else //user
				{
					widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_CU_Function.Web.UserPassword', UserPassword);				
				}
			}
		}
		return true;
    }
    

代码修改为：

function submit_pwd() 
{
    var docForm = document.getElementById('form_telacccfg');
    with(docForm) 
    {
        
            if( $("Username_text").options[0].selected == true) //admin
            {
                widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_CU_Function.Web.AdminPassword', AdminPassword);
            }
            else //user
            {
                widget.submitElemIndep('userpasswd.htm', 'set', 'InternetGatewayDevice.X_CU_Function.Web.UserPassword', UserPassword);				
            }
        
    }
    return true;
}

```
具体操作不再上图，学过一点点前端js的朋友相信都能做得到。

所以我实际做的事情就是，删除js中的所有的验证代码，以及修改选择器中的值为CUAdmin
如图
{% asset_img 8.png %}
直接输入想要设置的密码然后点击保存就可以了。