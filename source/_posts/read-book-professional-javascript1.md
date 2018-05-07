---
title: Javascript基础
date: 2016-09-11 19:10:00
categories: Javascript
tags: [Javascript,红皮书] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：

0. JS三个组成部分：ECMAScript 、DOM、BOM；
1. DOM三级分别定义了什么；
2. BOM主要定义了什么；
3. BOM对象location、navigator、screen各自的属性和方法；
4. If-Modified-Since；
5. replace方法的特殊之处；
6. screen、window、document概念区分及宽高属性获取；
7. clientHeight / offsetHeight / scrollHeight 三者区分；
8. 怎么判断是否滚到底； 

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://supermaryy.com)
> 本文地址：[http://supermaryy.com/2016/09/11/read-book-professional-javascript1](http://luckymona.github.io/2016/09/11/read-book-professional-javascript1)

# 一、Javascript基础 #
## 1. Javascript由ECMAScript\DOM\BOM 组成##
ECMAScript规定了这门语言的基础语法，Web浏览器只是ECMAScript的宿主环境之一，其他实现还有NodeJS、AS。
## 2. DOM##
Document Object Model，文档对象模型，定义了访问和操作网页内容的接口和方法

- DOM一级，它把html文档映射为节点树，
- DOM二级定义了操作节点的方法范围和遍历、鼠标和用户界面事件、操纵css的方法：

    - DOM视图（DOM Views）
    - DOM事件（DOM Events）
    - DOM样式（DOM Styles）
    - DOM范围和遍历（DOM Traversal and Range）

- DOM三级进一步扩展，新增DOM加载和保存模块（DOM Load and save），DOM验证模块（DOM Validation）

## 3.BOM ##
Browser Object Model，定义与浏览器交互的接口方法。主要包括：

- 弹出新窗口`window.open();`
- 对窗口进行移动、缩放、关闭 `window.moveTo(); window.resizeTo(); window.close();`
- location对象，所加载网页的信息
- navigator对象，浏览器版本信息
- screen对象，提供屏幕分辨率信息

### 3.1 BOM之location对象 ###

- 访问方式window.location，
- 属性：hash（得到URL从#开始的字符串），host，hostname，href，pathname，port，protocol，search（得到URL从?开始的字符串）
- 方法：assign、reload、replace

#### assign方法： ####
window.location.assign(URL),会在当前窗口加载另一个文档
```
<script type="text/javascript">
    function assign(){
        window.location.assign('anotherDoc.html');
    }
        
</script>

<p onclick="assign()">hello</p>
```
#### reload方法： ####
window.location.reload(flase||force),可以模拟浏览器的刷新按钮
- 参数值为false或不填，效果与浏览器刷新按钮一样，使用http头If-Modified-Since检测服务器上的文档有没有改变，没有改变就把浏览器缓存文件显示到浏览器中，如果改变了从服务器加载
- 参数为force，效果与shift+浏览器刷新按钮，相同，强制从服务器加载
#### http头If-Modified-Since实现浏览器缓存 ####
发送请求时，写在http request header里面，如果页面是第一次加载则不写这个参数，第一次请求回来后，服务器返回200，并在response header中会返回一个Last-Modified，这是一个时间戳，代表请求的文件在服务器中最后修改的时间。浏览器会把这个时间戳和请求来的资源缓存下来，这时，按浏览器的刷新按钮之后，发起第二次请求，会在request header中加上If-Modified-Since这个字段，值为刚才的Last-Modified值，服务器收到请求就把IMS值与服务器上的Last-Modified值做对比，如果相同，证明文件没有发生改变，就返回304(Not Modified)，浏览器就从本地缓存加载文件，如果不相同，证明服务器上的文件已经改变，就返回200，浏览器显示请求回的新内容。
#### replace方法： ####
window.location.replace(newURL)，加载新URL的文档内容，新URL将覆盖History对象中的当前记录，也就是不会产生新历史记录。想不到什么场景会需要这个功能，查了一下，有一种场景是^1，严格限制用户操作时，为防止后退后重复提交，可以在跳转时使用replace，这样就不能后退，防止重复提交。

#### 重定向方法总结 ####
window.location.href = newURL;
window.location.assign(newURL);
window.location.replace(newURL); 附加作用：不产生新History记录

### 3.2 BOM之Navigator对象 ###
1. 自认为比较有用的属性：plugins，userAgent，cookieEnabled，appVersion,onLine,都是只读的属性，使用方法：
`window.navigator.plugins`
2. 一个迷思：我系统是win7 64位，然后我用window.navigator.platform，出来是"win32"
查到`With windows xp64-bit, it depends if you have Internet Explorer 64-bit. If it's the case: Win64. If it's the normal edition: Win32.`^2
3. platform这个属性不是很可靠，定义经常变动^3，这个属性使用场景是，修改它以模拟不同系统^4

### 3.3 BOM之Screen对象 ###
使用方法：`window.screen.height`，window可以省略不写
重要属性：
`screen.availHeight`、`screen.availWidth`、`screen.height`、`screen.width`
#### screen、window、document概念区分及宽高属性获取： ####

**1. screen**：指屏幕，屏幕的概念就是显示器、显示屏，所以这里`screen.width\screen.height`指的是整个屏幕的宽高，就是显示器的发光的这块宽高，而`screen.availWidth\screen.availHeight`指的是window可获取的宽高，具体来说，一般情况下，availHeight是屏幕高度减去windows系统任务栏的高度（如下图），如果你把这个任务栏放在了左右某一侧，那么screen.availHeight===screen.height，这时候availWidth等于screen.width减去任务栏占去的宽度
![availHeight](http://o798x2hdw.bkt.clouddn.com/screen_availH.png?imageView/2/w/500/q/90)

**2. window**：指窗口，一个屏幕上可以有多个窗口,关于宽高有两个属性:`window.innerHeight / window.outerHeight`:![window_innerW](http://o798x2hdw.bkt.clouddn.com/window_innerW.png?imageView/2/w/500/q/90)

jquery中$(window)[0] ===window, 
`$(window).width() / $(window).height();`不包括滚动条宽度/高度，
`window.innerWidth / window.innerheight`包括滚动条宽度/高度

![jquery_window_width](http://o798x2hdw.bkt.clouddn.com/jquery_window_width.png)

**3. document**:指文档，

document.body.clientHeight：body元素的宽高
document.documentElement.clientHeight：视口，不包含滚动条，等于window.innerHeight减去滚动条高度

#### 几种size辨析 ####
**1. client size**^5
>Element.clientHeight read-only property is zero for elements with no CSS or inline layout boxes, otherwise it's  the inner height of an element in pixels, including padding but not the horizontal scrollbar height, border, or margin.

凡是client size，包含padding，不包含滚动条、边框、margin:
![Dimensions-client](http://o798x2hdw.bkt.clouddn.com/Dimensions-client.png)

**2.offset size**

>The HTMLElement.offsetHeight read-only property is the height of the element including vertical padding and borders, in pixels, as an integer.

凡是offset size，包含border和padding
![Dimensions-offset](http://o798x2hdw.bkt.clouddn.com/Dimensions-offset.png)

**3.scroll size**

>The Element.scrollHeight read-only attribute is a measurement of the height of an element's content, including content not visible on the screen due to overflow.

Element.scrollHeight：包含因滚动而不可见部分的整体高度
![Dimensions-scroll](http://o798x2hdw.bkt.clouddn.com/Dimensions-scroll.png)

用这个公式判断是否滚到底部了，使用场景是“我已经阅读了xxx”：
`element.scrollHeight - element.scrollTop === element.clientHeight`




> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：本文地址：[http://supermaryy.com/2016/09/11/read-book-professional-javascript1](http://luckymona.github.io/2016/09/11/read-book-professional-javascript1)

参考链接：

^1:[通过location.replace禁止浏览器后退防止重复提交](http://www.jb51.net/article/54781.htm)
^2:[Don’t forget navigator.platform](https://www.nczonline.net/blog/2007/12/17/don-t-forget-navigator-platform/)
^3:[What is the list of possible values for navigator.platform as of today?](http://stackoverflow.com/questions/19877924/what-is-the-list-of-possible-values-for-navigator-platform-as-of-today)
^4:[如何修改游览器的navigator.platform属性？](https://www.zhihu.com/question/36609103)
^5:[MDN Element.clientHeight](https://developer.mozilla.org/en-US/docs/Web/API/Element/clientHeight)

