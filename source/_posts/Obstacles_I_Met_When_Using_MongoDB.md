---
title: MongoDB使用过程踩坑小记
date: 2016-6-2 13:30:00
categories: NodeJS
tags: [踩坑,mongoDB,] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---

在windows下使用Mongodb貌似有好多坑，快搞完了才想到要记录下来，于是只记了最后几个坑坑，有的还记得不太清楚，Anyway，能帮助到需要的人就好~ Why so serious？
<!-- More -->
一开始报错rc=56，有个插件没装，参考了好多教程，最后按这个[链接](http://www.tuicool.com/articles/ruqU32 'win7 安装 mongodb 绝对正确')装下来比较顺利：

装好以后启动服务时，会出现这个提示：
```
Hotfix KB2731284 or later update is installed, no need to zero-out data files?
```
找到[stackoverflow上一个问题](http://stackoverflow.com/questions/30246428/hotfix-kb2731284-or-later-update-is-installed-no-need-to-zero-out-data-files?s=1|3.2969)，最终指向[这个地址](http://mongodb.2344371.n4.nabble.com/mongod-don-t-start-after-server-crash-td1096.html)

说是windows的服务超时时间太短，修改注册表增加到2min后就可以了，
这部分没看懂：Here is a snippet of Powershell code that I use to make the edits to all of my MongoDB servers remotely.
```
Invoke-Command -ConnectionUri $uri -Credential $localCredentials `
        -ScriptBlock {set-itemproperty -path HKLM:\SYSTEM\CurrentControlSet\Control -name WaitToKillServiceTimeout -value 120000}
```
修改注册表： Windows服务的启动超时时间默认是30秒，但这并对对所有服务都有效，有些服务的时间可能超过30秒，这时候需要修改注册表来解决这个问题。注册表项为`HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Control/ServicesPipeTimeout`,这个值有可能不存在，如果不存在需要添加。类型为DWORD，单位是毫秒。引用自[这里](http://blog.csdn.net/hanyu1980/article/details/2197455)

