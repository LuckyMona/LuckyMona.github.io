---
title: ionic + angularJS开发Hybrid App开发环境配置及打包踩坑记
date: 2016-6-14 13:30:00
categories: 框架
tags: [ionic,angularJS2,开发环境, webapp,] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---

公司之前使用appcan进行Hybrid App开发，其中遇到过许多问题，最严重的是性能差，所以新项目希望转向ionic + ng进行移动app开发。公司里之前没有人用过，于是领导就把这一光荣的任务交给了我，于是我就开始了~~自虐~~开心的踩坑过程！本文主要集中于开发环境的配置。
<!-- More -->
开始之前请先安装nodeJS。我首先是去[官网](http://ionicframework.com/getting-started/ "ionic 官网")看文档：
# 1、安装Ionic #
打开nodeJS的shell窗口，（曾试过直接在cmd窗口中输npm命令，会有奇怪的问题）输入
`npm install -g cordova ionic`
# 2、开始一个项目 #
`ionic start myApp tabs`
这句里的tabs还有两种选择：blank、sidemenu，分别对应不同种类的app界面。找到你的myApp文件夹，下面就是一个seed project，你可以在这个项目基础上进行开发你自己的APP。可以说到这里你已经完成了开发环境的配置，下面就属于把项目打包成手机安装包的过程了。
# 3、打包 / 在手机上跑起来(android) #
按照官网上的教程，建ios的APP命令如下：
```
cd myApp
ionic platform add ios
ionic build ios
ionic emulate ios
```
推测建安卓APP的时候，就应该把上面命令中的ios换成android：
```
cd myApp
ionic platform add android
ionic build android
```
当时走到这一步时看了别的教程，我直接用的下面的命令：
```
cd myApp
ionic platform add android
ionic run android
```
# 报错1 #
输入之后报错了，错误信息：
![ionic/7](http://o798x2hdw.bkt.clouddn.com/ionic/7.jpg)
```
Error: Failed to find 'ANDROID_HOME' environment variable. Try setting setting it manually.
Failed to find 'android' command in your 'PATH'. Try update your 'PATH' to include path to valid SDK directory.
```
把错误提示扔到谷歌里，找到StackOverflow上说**要安装android studio**，[这里是官网安装包下载地址](https://developer.android.com/studio/install.html "android studio")，需要翻墙且文件很大，1.2G，我用的[梯子](https://do1.glbproxy.tk/?r=1/?r=1&name=miao.hnlk@foxmail.com)，（通过这个链接购买的，你的账户可以增加10天），实在不想翻墙也可以找下国内的下载链接，只是版本都比较低。下载到一半，发现有同事现成下好的，大喜！
# 报错2 #
如果你输入platform那句之后，运行的这个命令：`ionic build android`，有这样的报错：
那么建议你可以装一下android studio试试。
![ionic/8](http://o798x2hdw.bkt.clouddn.com/ionic/8.jpg)
```
Error: Please install Android target: "android-23".
Hint: Open the SDK manager by running: "C:\Users\mary.tien\AppData\Local\Android
\sdk\tools\android.bat"
You will require:
1. "SDK Platform" for android-23
2. "Android SDK Platform-tools (latest)
3. "Android SDK Build-tools" (latest)
```
装好后，运行命令：`ionic build android`
如果看到许多点点点，就是成功了，接下来是漫长等待……
中间会出现很卧槽的东西：
![ionic/9](http://o798x2hdw.bkt.clouddn.com/ionic/9.jpg)
# Build成功，安装包打包完成 #
但憋害怕，最终你会看到一个"BUILD SUCCESSFUL"
![ionic/10](http://o798x2hdw.bkt.clouddn.com/ionic/10.jpg)
其实到这里已经打包完成了，你可以在上图中的那个路径找到build好的apk文件，然后就可以发到手机上安装了。但我当时看教程，还有一句命令`ionic emulate android`，于是就继续往下走了。这个是在电脑上模拟手机上的效果的，可以有很多替代方案，所以如果在这个步骤被卡住了，完全可以先放下，之后采用别的方式。

---


# 额外知识1：电脑上模拟手机效果 #
我在build successful之后输入`ionic emulate android`
## 报错1 ##
出了几行之后，卡住了：
```
Please ensure Intel HAXM is properly installed and usable.
CPU acceleration status: HAXM must be updated (version 1.1.1 < 6.0.1).
```
搜索之，找到这个[链接](http://www.cnblogs.com/csulennon/p/4178404.html),
>HAXM作用是管理硬件加速的，估计是用了这个东西模拟器就能告别Eclipse时代的龟速。
>你也可以在Inter官网下载这个HAXM，当然Android SDK已经集成了这个软件，你需要做的就是找到他，然后安装它就是了。
>他的位置放在这个目录下：
>![ionic/19](http://o798x2hdw.bkt.clouddn.com/ionic/19.png)

但是里面的haxm链接打开是404，你得使用这个~~[链接](https://software.intel.com/zh-cn/android/articles/intel-hardware-accelerated-execution-manager-end-user-license-agreement)~~
[链接](http://download.csdn.net/detail/xfortune/9462367)
## 报错2 ##
安装的时候会报错，我安装的时候报错，还跟po主的错误不一样：
```Failed to configure driver:unknown error. Failed to open driver.
```
搜索之，找到这个[链接](https://software.intel.com/en-us/blogs/2013/04/25/workaround-patch-for-haxm-installation-error-failed-to-configure-driver-unknown)，解决办法如下：
![ionic/13](http://o798x2hdw.bkt.clouddn.com/ionic/13.jpg)
意思就是：

1. 下载上面链接中的zip文件
2. 解压到一个文件夹
3. 复制"hax_extract.cmd"到“IntelHaxm.exe”所在的文件夹，
4. 在复制过来的"hax_extract.cmd"上右键选择“以管理员身份运行”
5. 在弹出框里选yes

之后再安装“IntelHaxm.exe”就可以了。如果不行，这个链接里还有许多别人的评论，可以浏览下看有没有别的解决方案。
## 成功 ##
照这个步骤走完，果然安装成功，bravo！经过漫长的Error岁月，回过头来看我们的ionic窗口，已经卡死在那里了。关了重新打开一个node shell窗口，进去项目文件夹，输入`ionic emulate android`，发现HAXM竟被装了个更低的版本，傻眼！于是又找到这个[链接](http://download.csdn.net/detail/xfortune/9462367)，装上之后，重新打开node shell窗口，输入`ionic emulate android`，就可以啦！有一个手机模拟器，竟然有点激动，想哭有木有。
![ionic/18](http://o798x2hdw.bkt.clouddn.com/ionic/18.jpg)


# 额外知识2：两种打包命令 && 安装哪个apk？ #
`cordova build --release android`
`ionic build android [--debug | --release]`
以下引用自这个[链接](http://blog.csdn.net/yourlin/article/details/48000083)

1. 第二个命令默认不带参数输出为debug版本，配置正确情况下会在myApp/platform/Android/outputs/APK/下面生成，对应的APK文件。
2. debug模式下会输出2个APK，一个是不带签名的，一个是带debug签名的，带debug签名的APK可以在手机上安装测试
3. release模式下会输出1个不带数字签名APK，需要自己对该APK进行签名
###额外知识3：APK数字签名###
进入APK文件所在的目录 

1. 先产生密钥文件 
`keytool -genkey -alias demo.keystore -keyalg RSA -validity 40000 -keystore demo.keystore `
这个-validity 40000，意思是证书有效期40000天 

2. 再给文件签名 
`jarsigner -verbose -keystore demo.keystore -signedjar CoderCalendar.apk android-release-unsigned.apk demo.keystore -digestalg SHA1 -sigalg MD5withRSA`
CoderCalendar.apk 是我们生成的目标文件名 
android-release-unsigned.apk 是需要被签名的APK文件




***
# 结语 #
写出来觉得很少，而在实际操作中，遇到问题、找答案、一个个答案试错、以及下载1.2G安装包的过程，都是很崩溃的，体验了好几次“山重水复”和“柳暗花明”，记下一点经验，希望给同行的朋友一点参考。:)
	
	