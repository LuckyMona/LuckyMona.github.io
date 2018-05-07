---
title: npm ERR! shasum check failed for报错，解决办法
date: 2016-6-2 13:30:00
categories: NodeJS
tags: [踩坑,npm,nodeJS] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---

使用npm install的时候，死活报错：
```
npm ERR! shasum check failed for C:\Users\MARY~1.TIE\AppData\Local\Temp\npm-1212
-7fb4bd00\registry.cnpmjs.org\iconv-lite\download\iconv-lite-0.4.11.tgz
```
<!-- More -->
网上这个问题搜到挺多答案的，但是试了好多都不行，
### 现在列出我试过的,这些试过来都不行： ###
来自[这个链接的方案](http://www.ithao123.cn/content-41081.html)
1. 通过config命令
```
npm config set registry http://registry.cnpmjs.org 
npm info underscore （如果上面配置正确这个命令会有字符串response）
```
2. 命令行指定
`npm --registry http://registry.cnpmjs.org` info underscore
3. 编辑 ~/.npmrc 加入下面内容
`registry = http://registry.cnpmjs.org`
搜索镜像: http://cnpmjs.org

最后在starkoverflow的这个[链接](http://stackoverflow.com/questions/20633898/shasum-check-failed-error-while-installing-phonegap)，试到了正解：
### 可行的方案 ###
1. Try this first
`npm set registry https://registry.npmjs.org/`
2. if failed then try to use the npm mirror:
`npm set registry http://ec2-46-137-149-160.eu-west-1.compute.amazonaws.com`
3. then use it normally:
`npm install express`

我只用了第一句就不再报错了，之前使用的cnpm.js，这个小叛徒，给我造成这么多err，还是原版、官方靠谱