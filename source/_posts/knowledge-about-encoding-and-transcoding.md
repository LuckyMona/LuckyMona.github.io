---
title: 前端中关于编码和转码的知识小总结
date: 2016-10-30 14:30:00
categories: Javascript
tags: [编码,转码,总结] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：

1. url编码%uxxxx
2. base64
3. unicode转义序列\uxxxx
4. 字符编码知识：ASCII,Unicode,Utf-8,GB2312,GBK

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[http://supermaryy.com/2016/10/30/knowledge-about-encoding-and-transcoding/](http://supermaryy.com/2016/10/30/knowledge-about-encoding-and-transcoding/)
 
# 一、前言 #
自从学前端以来，关于编码和转码这一块的知识，不论是书还是博客，从来没有看到过作系统介绍的文章，自己也都是三不五时零碎地遇到这方面的问题，加上这块的知识也挺复杂琐碎的，所以一直都比较糊涂，于是在这个深圳刚刚降温，秋高气爽的下午，无比地需要写上这么一篇总结啦啦啦。总结来自于网上资料整理和自己的浅薄经验，比较肤浅，等我把肤浅的东西弄熟之后再添加深入的东西(ง •̀_•́ )ง

# 二、url编码%uxxxx #

1. %uxxxx是url编码的结果

2. 为什么要进行URL编码?
【文档标准】
RFC3986文档规定，Url中只允许包含英文字母（a-zA-Z）、数字（0-9）、-_.~4个特殊字符以及所有保留字符。RFC3986文档对Url的编解码问题做出了详细的建议，指出了哪些字符需要被编码才不会引起Url语义的转变，以及对为什么这些字符需要编码做出了相应的解释。
HTTP协议中通过URL传参是通过键值对形式进行的，格式上是以？、&和=为特征标识进行解析，如果键或者值的内容中包含这些符号，就会造成解析错误，所以要进行编码，用不会造成歧义的符号代替有歧义的符号。

3. 编码方式：
在特殊字符前加上%，例如“name1=value1”,其中value1的值是“va&lu=e1”，对其进行URL编码后：“name1=va%26lu%3D”，这样服务端会把紧跟在“%”后的字节当成普通的字节，就是不会把它当成各个参数或键值对的分隔符。因此，Url编码通常也被称为百分号编码

4. 为什么汉字也要进行编码：URL中的传参字符串是ASCII编码而非unicode，所以URL中不能包含任何非ASCII字符，否则可能出问题。

5. JS中的escape()和encodeURI()的区别：
escape()是把非ASCII字符先进行unicode编码，然后把四位十六进制字符前面加%，这种方式已经被W3C废弃，而后个是把非ASCII字符先进行UTF-8编码，然后加百分号，这是RFC推荐的，所以现在对url编码都是使用encodeURI了。
escape()不能直接用于URL编码，它的真正作用是返回一个字符的Unicode编码值。比如“春节”的返回结果是%u6625%u8282，也就是说在Unicode字符集中，“春”是第6625个（十六进制）字符，“节”是第8282个（十六进制）字符。

6. encodeURI和encodeURIComponent的区别：
encodeURI()对完整的URI编码，后者对URI的一个组件进行编码。这样分隔URI组件的那些特殊符号不会被encodeURI编码，但如果使用encodeURIComponent()，就会进行编码。需要注意的是，encodeURI不会对单引号进行编码

7. 什么时候使用encodeURI()？四种涉及URL编码的场景，及该场景下的编码方式^1：
    - 网址路径中包含汉字【UTF-8】
    - 网址的查询字符串包含汉字【操作系统编码】
    - Get方法生成的URL包含汉字，在已打开的网页上，直接用Get或Post方法发出HTTP请求，例form方式：【meta标签的charset属性定义的编码】
    - Ajax调用的URL包含汉字:【不同浏览器的编码方式不同】
对包含中文的URL的处理问题，不同浏览器表现不同，很混乱，保证客户端只用一种编码方法向服务器发出请求的解决方案：使用Javascript先对URL编码，然后再向服务器提交，不要给浏览器插手的机会。因为Javascript的输出总是一致的，所以就保证了服务器得到的数据是格式统一的。

8. 出现乱码问题时的排查：
    - 检查编辑器保存文件的字符集，和<meta>标签中指定的字符集是否一致
    - 两次encodeURI后提交给后台，解决后台收到乱码的问题^5
    - 服务器返回的响应头Content-Type没有指明字符编码

9. win7操作系统默认编码是gbk，winXP是gb2312

# 三、base64编码 #

1. base64是什么？^6
base是二进制到文本字符串的转换方法，常用于在URL、Cookie、网页中传输少量二进制数据。

2. 为什么要进行base64编码：
用记事本打开pdf、exe、jpg等文件时，会显示乱码，因为二进制有许多不能显示的字符，如果想让记事本可以打开二进制文件，就需要把二进制转换成字符串，base64是常见的二进制转换方法。

3. 什么时候进行base64编码：
 - 上传图片、文件时
 - 拼URL的时候，base64编码后，肉眼看不出信息

4. 其他：
 - Base64编码会把3字节的二进制数据编码为4字节的文本数据，长度增加33%，好处是编码后的文本数据可以在邮件正文、网页等直接显示。
 - Base64是一种通过查表的编码方法，不能用于加密，即使使用自定义的编码表也不行

5. JS自带的base64转换方法：btoa()和atob()
负责ASCII字符串或二进制数据与base64编码的相互转换。命名与实际作用相反：
 - btoa：按名字理解是base64到ASCII字符串或二进制数据呗，但实际作用是反过来。该方法不能直接作用于Unicode字符串，所以如果btoa('你好')，会报错。
 - atob：对base64码进行解码。
 - 如果要对中文进行base64编码，只需要将中文进行encodeURIComponent进行编码之后再进行 base64编码即可。

# 四、\unnnn是什么 #
\unnnn有两种含义：
1. \unnnn字符串的字符字面量，又叫转义序列，还包括\ntbrf、\\、\'、\"、\xnn，nnnn是四位16进制数表示的unicode字符，
2. 正则表达式元字符:
```
var str = "Visit W3Schools. Hello World!";
var patt1 = /\u0057/g;
var result = str.match(patt1);   //W,W
```

可以利用nnnn是unicode编码，和escape(),unescape()来进行编码和解码^7

# 五、总结以上 #

 1. URL中的传参字符串必须是ASCII编码
 2. escape()：是把非ASCII字符先进行unicode编码，加%，已废弃。不能直接用于URL编码，它的真正作用是返回一个字符的Unicode编码值
 3. encodeURI：把非ASCII字符先进行UTF-8编码，然后加百分号，推荐！
 4. btoa()和atob()负责ASCII字符串或二进制数据转换成一个base64，不能对unicode字符（如中文）使用，会报错

# 六、字符编码知识总结 #

1. 所有字符集源于ASCII字符集，是单字节字符集，最开始有128个，主要包括26个英文字符和数字，及其他符号和控制符^8
2. ASCII字符集后来扩展到256个，主要是添加了其他一些符号
3. 计算机传到亚洲，发展出多字节字符集。
4. 中文字符集：

 - GB2312较常用，涵盖了所有简体字符以及一部分其他字符；
 - GBK(k是扩展的意思)，在GB2312的基础上添加了繁体字
 - GB18030 不是双字节字符集

5. 更多ASCII衍生字符集出现后，世界各个组织进行标准化流程，ANSI组织提出ANSI标准字符编码，我们现在通常说到ANSI编码，通常指的是平台的默认编码，例如英文操作系统中是ISO-8859-1，中文系统是GBK。然后各个国家也都编写自己语言的字符集
6. 新的需求出现了：**在一份文档中显示所有字符。**此时需要一个全人类达成共识的巨大的字符集，这就是 **Unicode字符集**，![sticker](http://o798x2hdw.bkt.clouddn.com/stickers/11.jpeg?imageView2/1/w/100/h/100/q/100)
7. 在Unicode出现之前，所有的字符集都是和具体编码方案绑定在一起的，Unicode将字符集和字符编码方案分离开。所以产生了多种不同的Unicode编码集，**UTF-8是目前应用最广泛的一种Unicode编码方案**。中国的GB18030编码，覆盖了Unicode所有的字符，因此也算是一种Unicode编码。
8. Unicode字符集只是定义了字符的集合和唯一编号，Unicode编码，则是对UTF-8、UCS-2/UTF-16等具体编码方案的统称而已，并不是具体的编码方案。所以当需要用到字符编码的时候，你可以写gb2312，codepage936，utf-8，utf-16，但请不要写unicode（看过别人在网页的meta标签里头写charset=unicode，有感而发）
9. 造成 **乱码**的原因就是因为使用了错误的字符编码去解码字节流，因此当我们在思考任何跟文本显示有关的问题时，请时刻保持清醒：当前使用的字符编码是什么。
10. **术语**：编码的过程是将字符转换成字节流。 解码的过程是将字节流解析为字符。


---
> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[http://supermaryy.com/2016/10/30/knowledge-about-encoding-and-transcoding/](http://supermaryy.com/2016/10/30/knowledge-about-encoding-and-transcoding/)

Reference：

^1: [URL编码为什么中文加%原因](http://fengqing888.blog.163.com/blog/static/330114162013101522549676/)
^2: [Unicode转义(\uXXXX)的编码和解码](http://blog.csdn.net/java2009cgh/article/details/11214081)
^3: [为什么要进行URL编码](http://www.cnblogs.com/jerrysion/p/5522673.html)
^4: [encodeURI、encodeURIComponent、btoa及其应用场景](http://www.cnblogs.com/shytong/p/5102256.html)
^5: [encodeURI来解决URL传递时的中文问题](http://www.cnblogs.com/jx270/p/4829589.html)
^6: [base64](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001399413803339f4bbda5c01fc479cbea98b1387390748000)
^7: [Unicode转义(\uXXXX)的编码和解码](http://netwjx.github.io/blog/2012/07/07/encode-and-decode-unicode-escape-string/)
^8: [关于字符编码，你所需要知道的（ASCII,Unicode,Utf-8,GB2312…）](http://www.imkevinyang.com/2010/06/%E5%85%B3%E4%BA%8E%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81%EF%BC%8C%E4%BD%A0%E6%89%80%E9%9C%80%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84.html)




