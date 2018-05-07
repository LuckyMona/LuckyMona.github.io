---
title: 使用Hexo踩坑小记
date: 2016-7-2 13:30:00
categories: 框架
tags: [Hexo,踩坑,博客,七牛云] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---

之前就装好了Hexo，但仍有好多未完成的部分，所有的细节堆起来就是一个大工程啦，记录一下坑坑们，那都是我逝去的青春π__π
<!-- More -->
## 1、语言 ##
Hexo语言设置在hexo项目根文件夹下的_config.yml中配置，找到language，不是chinese，不是zh-CN，我之前设置为zh-CN，会导致语言变为德文，文章标题下面的时间戳前面会变成“Veröffentlicht am”，要设为
`language: zh-Hans`
如果设置之后还不起作用，请到theme/next/languages/目录下查看是否有zh-Hans.yml（zh-EN.yml)文件，如果没有，请直接到next的Github下载相应文件添加即可。
## 2、文章折叠 ##
按照一般的Hexo教程进行安装，安装好后，首页文章没有折叠起来，全部是默认打开的，而我们希望每篇文章只显示部分内容，用户点击more的时候，再展开到详情页面。要做到这样，只需在.md文章里，在需要截取那段展示出来文字的地方添加 `<!-- More -->`，这样后面的文字都默认不显示，并出现一个More按钮
更详细的说明，可以看这个[链接](http://www.zhihu.com/question/40674614 'hexo 文章无法折叠？')
## 3、图片以及头像 ##
github page页的容量限制是300M，如果图片比较少，可以放在本地路径，我现在就是。
使用方法：
（注意下面1和2不可混用，用1就要卸载2的插件，用2就不要用1的语法，别问我怎么知道的）

### 1. Hexo官方插入图片方法

1. config.yml 文件中的 post_asset_folder 选项设为 true
2. Hexo将会在你通过 hexo new [layout] <title> 命令创建新文章时自动创建一个文件夹，这个资源文件夹将会有与这个 markdown 文件一样的名字，然后你就可以使用相对路径来引用图片了
3. 当你打开文章资源文件夹功能后，你把一个 example.jpg 图片放在了你的资源文件夹中，如果通过使用相对路径的常规 markdown 语法 `![](/example.jpg)` ，它将不会出现在首页上。（但是它会在文章中按你期待的方式工作）
正确的引用图片方式是使用下列的标签插件而不是 markdown ：
```
{% asset_img example.jpg This is an example image %}
```

### 2. 使用hexo-asset-image插件插入图片

1. 首先确认 _config.yml 中有 post_asset_folder:true 。
2. 在 hexo 目录，执行
`npm install https://github.com/CodeFalling/hexo-asset-image --save`
假设在
```
MacGesture2-Publish
├── apppicker.jpg
├── logo.jpg
└── rules.jpg
MacGesture2-Publish.md
```
这样的目录结构（目录名和文章名一致），只要使用 `![logo](MacGesture2-Publish/logo.jpg) `就可以插入图片。

### 3. 使用千牛做图床 ###
可以参考这个链接[如何使用七牛云做为图床？](http://cnfeat.com/blog/2015/11/30/cli-qiniu/#section-7)
我采用他说的第一种方法，也就是七牛云的上传插件[qiniu upload files](https://chrome.google.com/webstore/detail/qiniu-upload-files/emmfkgdgapbjphdolealbojmcmnphdcc)，遇到了一个小障碍，就是在填写配置表格的时候，这个域名:
![hexo/qiniu](http://o798x2hdw.bkt.clouddn.com/hexo_qiniu.png)
不是你博客的域名，而是七牛生成的外链的域名，在七牛云----->marys存储空间(marys是我的存储空间名字)---->内容管理，里面可以看到你通过插件上传的图片，那个外链默认域名才是你需要填进去的域名，通过操作->复制外链可以直接复制：
![hexo/qiniu](http://o798x2hdw.bkt.clouddn.com/setdomain.png)
keyword:七牛云 图床 图片404 上传插件
### 4. 修改头像 ###
进去themes/next/_config.yml，搜索avatar，去掉#注释，avatar: 后接你的头像图片URL。头像图片放next主题下的images文件夹，url就写成/images/avatar.jpg

## 4、markdown标题不解析 ##
解析出来后，标题的#号仍然显示，并且标题没有加粗等这些格式：
markdown标题标准写法需要在"#"和后面字符之间加一个空格，如果不加空格，有些引擎就解析不了

## 5、百度统计 ##
直接甩链接系列：[NexT主题文档之百度统计](http://theme-next.iissnan.com/getting-started.html#analysis-system-baidu)
## 6、评论 ##
评论可以用多说评论框，但是用了多说评论框，你的文章就会自动被扒走，然后放到推酷这个网站上，你还啥都不知道（囧不懂脸）。所以我用的是国外的disqus，直接甩链接系列:

1. [告别多说，拥抱 Disqus](https://blog.jamespan.me/2015/04/18/goodbye-duoshuo/)
2. [解決 Hexo Comment !](http://morris821028.github.io/2014/04/12/web/hexo-comment/)

## 7、文章阅读量 ##

直接甩链接系列：[NexT主题文档之阅读次数统计（LeanCloud) ](http://theme-next.iissnan.com/getting-started.html#leanclound-page-views)

## 8、Rss ##
1. 安装生成插件
`npm install hexo-generator-feed --save`
2. 修改配置文件，在hexo根目录下的_config.yml中添加以下内容：
```
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
```
3. 修改主题配置文件_config.yml，添加以下内容，以在页面中显示rss按钮：
`rss: /atom.xml`
---
参考链接：

1. [Hexo的next主题变成德语](http://www.zhihu.com/question/41625825)
2. [Hexo文章无法折叠？](http://www.zhihu.com/question/40674614)
3. [Hexo官方文档-资源文件夹](https://hexo.io/zh-cn/docs/asset-folders.html)
4. [在Hexo中无痛使用本地图片](http://www.tuicool.com/articles/umEBVfI)
5. [hexo博客 markdown解析不了标题](http://www.thinksaas.cn/ask/question/22799/)
6. [你还敢用“多说评论框”吗？](http://tieba.baidu.com/p/1683464316)