---
title: 使用Node.js搭建简单Web服务器
date: 2016-10-22 00:30:00
categories: Node.js
tags: [Node.js,Express,web server,教程] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：
1. Ubuntu下Node.js和Express安装
2. 搭建简单Web服务器

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[developing-a-simple-web-server-with-node-js](http://supermaryy.com/2016/10/22/developing-a-simple-web-server-with-node-js/)
 
# 一、环境搭建：Ubuntu下node和express安装 #
第一次玩ubuntu加上公司网络比较弱鸡限制了好多网站，node官网上的安装包下载不了，一开始装node还费了老大劲，连滚带爬装上了node，现在总结一下怎么安装的，不然以后用到还得重新摸索。如有错漏，欢迎批评。

## 1. Node安装 ##
### 1.1关于node版本 ###
node查看版本使用命令node –v，我装上后查看显示是0.10.x的版本，但是官网上的都是4.x.x和6.x.x的版本了，很明显这应该使用的不同的版本管理体系。查到二者区别说：0.x.x是Joyent公司维护.进展缓慢，但是稳定；4.,5.*都是由网友维护的，相对的进展快，增加和许多新功能，io.js也是指的这个开源版本。偶数开头的是稳定版，奇数开头的开发版就是会不断变化更新，为下一个稳定版本做测试差不多的意思^1。关于node.js和io.js背后还有一段精彩的故事^2。
### 1.2Ubuntu安装node：### 
#### 1.2.1tar.gz安装 ####
^3到node官网上下载安装包，Ubuntu上需要使用.tar.gz结尾的文件进行安装，下载到后，假设放在主文件夹,文件名是name.tar.gz
快捷键ctrl+alt+t打开终端，`tar -zxvf name.tar.gz`进行解压，假设解压为name文件夹:
```
cd name
sudo make
sudo make install
```
等待安装完成即可
#### 1.2.2使用nvm管理安装的node版本 ####
先安装nvm：
`git clone https://github.com/chenxizhang/nvm.git && ./nvm/install.sh && . ~/.nvm/nvm.sh  && rm –rf ./nvm`
装完nvm之后：
1.通过nvm ls查看当前已经安装的node或者iojs版本；
2.通过nvm ls-remote查看当前可以安装的node或者iojs版本；
3.通过nvm install v0.21.7安装制定版本的nodejs；
4.通过nvm use v0.21.7切换使用的nodejs版本；
#### 1.2.3安装npm ####
npm是node的包管理工具，使用npm可以很方便地安装一些框架、工具什么的，百度一下安装方法，比较简单，就不说了。
### 1.2Express安装 ###
^4 Express是nodeJS的应用框架，提供了强大的工具和特性帮助创建Web应用和HTTP工具，其关系就相当于JQuery、AngularJS之于JS吧，安装：
```mkdir myapp
cd myapp
npm init 之后一路回车
npm install express –save
```
# 二、搭建简单Web服务器 #
## 1. Hello world ##
进去myapp根目录，创建index.js文件，这是整个web应用的入口。在index.js中输入：
```
var express = require('express');     //引入express框架
var app = express();                  //实例化一个express app

app.get('/', function (req, res) {  
  res.send('Hello World!');     //当使用get请求根目录时，服务器返回’Hello World！’
});

app.listen(3000, function () {    //监听3000窗口，监听成功后打印log
  console.log('Example app listening on port 3000!');
});
```
至此，最简单的一个服务器已经建好了，启动方法：
```
cd  myapp
node index.js
```
这时在浏览器中打开localhost:3000，页面上有”Hello World！”
## 2. 展示静态文件 ##
假设你重构了一个静态页面login.html，想要放在这个web服务器上进行展示。假设你的静态文件结构是这样的：login文件夹下放着login.html、style.css、login.js和存放图片的img文件夹。那么把login文件夹放到myapp根目录下，并把index.js修改为：

```
var express = require('express');     
var app = express();              
 
app.use(express.static('login'));  //使用express的static方法展示静态文件

app.listen(3000, function () {    
  console.log('Example app listening on port 3000!');
});
```
然后用浏览器访问localhost:3000/login.html就能展示出你的静态页面了
## 3. 配置路由 ##
上面代码中的这行就是配置路由的部分：
```  
  app.use(express.static('login'));  //使用express的static方法展示静态文件
```
其基本结构是：`app.METHOD(PATH, HANDLER)`，这里的method可以是get、post，PATH就是在浏览器中输入的地址路径，在HANDLER函数里面对服务器返回的内容进行设定。通过修改这三项就可以对访问路径和服务器相应内容进行配置了。                                             

---
> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[developing-a-simple-web-server-with-node-js](http://supermaryy.com/2016/10/22/developing-a-simple-web-server-with-node-js/)

Reference：

^1:[ nodejs各版本的区别](http://cnodejs.org/topic/5762549a50312f1107e615d7)
^2:[ Node.js与io.js那些事儿](http://www.infoq.com/cn/articles/node-js-and-io-js/)
^3:[在Linux（ubuntu server）上面安装NodeJS的正确姿势](http://www.cnblogs.com/chenxizhang/p/5222918.html)
^4:[expressjs 官网](http://expressjs.com/en/starter/installing.html)




