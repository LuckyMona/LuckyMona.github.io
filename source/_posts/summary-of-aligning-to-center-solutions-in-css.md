---
title: 总结向↗CSS居中常用套路
date: 2016-09-08 21:00:00
categories: CSS
tags: [CSS, 垂直居中, 水平居中, 方案总结, 总结] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
> by MaryTien from [http://luckymona.github.io](http://luckymona.github.io)
> 本文地址：[http://luckymona.github.io/2016/09/08/summary-of-aligning-to-center-solutions-in-css](http://luckymona.github.io/2016/09/08/summary-of-aligning-to-center-solutions-in-css)

之前差不多有小半年时间做切图仔，虽然居中的方法来来去去常用的就那么几个，但是由于没有认真总结过每种方案适用的场景，有时遇到居中需求还是不能一下就用对方法，试来试去浪费时间。本文就对CSS中的常用的居中方案来个总结，本想来个最强总结，但是CSS套路太多，写着写着就不想写了==!，于是变成了常用方案的总结。（文中例子均为以chrome测试结果）
<!-- More -->

# 常用元素分类--按照块元素、行内元素 #
居中的需求常常涉及这些因素：内联元素居中、块元素居中，块元素居中又分为定宽度、不定宽度，还有各种定位、浮动、父元素宽高，等等。于是我先把常用元素按照display值分一下类。根据二八原则，仅举常用的比较重要的小朋友，且不包括HTML5那些新标签（多了人家记不住嘛，脑细胞要想更重要的事 ~~嗯，一个借口~~）

## 常用block元素： ##
文档类：head \ html \ body 
文章类：h \ h1~h6 \ p 
布局类：div \ br \ hr
列表类：ol \ ul \ li \ dl \ dt \ dd
表单类：form
需注意的：**hr是块元素，可以设定宽高**

## 常用inline元素： ##
a \ span \ strong \ i \ em \ del \ q 

## 常用的inline-block元素： ##
img \ input \ button \ select \ textarea

## 其他元素： ##

1. 与select搭配使用的option、optgroup，虽然是display:block的元素，但是却不能设定宽高
2. 起配置作用的元素：meta、script、style
3. table元素：（markdown中写表格很费劲，推荐一个插件TableEditor）

| table元素名   | display值         | 
| :-----------: | :---------------: |
| table         |   table           |
| thead         | table-header-group| 
| tbody         | table-row-group   | 
| tfoot         | table-footer-group| 
| tr            | table-row         |   
| th / td       | table-cell        | 

inline元素不存在宽高，也就不存在居不居中的问题。所以讨论居中主要是讨论block / inline-block元素的居中。

# 居中主要用到的技术 #
## line-height--垂直居中 ##
block / inline-block元素，通过使height值等于line-height值，使 **单行文本内容** 或者 **单行display:inline的元素**垂直居中:
```
.block_center { height:200px; line-height:200px; border:1px solid red; }
.inlineB_center { display:inline-block; height:200px; line-height:200px; border:1px solid red; }

<p class="block_Center">dfasdfadfa</p>
<p class="inlineB_center">dfasdfadfa</p>

.origin_inlineB_center { height:200px; line-height:200px; border:1px solid red; }
<input class="origin_inlineB_center" type="text" value="aaaaa">
<button class="origin_inlineB_center" >aaaaa</button>
<textarea sclass="origin_inlineB_center" >aaaaa</textarea>
```

**需注意:**

1. 子元素为inline-block元素，父级加line-height等于height，并不能使子元素居中：
```
.father { height: 200px; width:100px; line-height: 200px; border:1px solid red; }
.child { height: 100px; width:100px; background: red; display: inline-block; }
<div class="father">
        <div class="child"></div>
</div>

<!-- 或者原生inline-block元素也不行,class内容同上，.child 去掉display:inline-block -->

<div class="father">
    <input class="child" type="button" name="">
</div>

<!-- or -->
<div class="father">
    <img src="1.jpg" class="child">
</div>
```
都是这样的结果：
![align/1](http://o798x2hdw.bkt.clouddn.com/align/1.png) ![align/2](http://o798x2hdw.bkt.clouddn.com/align/2.png)

## text-align:center--水平居中 ##
text-align顾名思义，其主要作用是对齐text的，具体说来：

1. 是使block / inline-block元素的内容文本水平居中，但对inline-block元素select无效
```
<!-- div元素 -->
.center { text-align:center }
<div class="center">This text will be in the center</div>
<p class="center">dsfadfadfafgdfs</p>

<!-- inline-block元素 -->
.inlineB_center { text-align:center; width:100px; }
<input class="inlineB_center" value="aaaaa" type="text">
<button class="inlineB_center">aaaaa</button>
<textarea class="inlineB_center">aaaaa</textarea>
```
2. 此外，还可以使作为子元素的inline / inline-block元素在父元素内水平居中
```
.center { text-align:center; width: 200px; height:100px; border:1px solid red; }
.centerChild { width:100px; height:100px; }

<div class="center">
    <div style="display:inline">aaa</div>
</div>

<div class="center">
    <div style="display:inline-block">aaa</div>
</div>

<!-- or 原生inline-block元素 -->
<div class="center">
    <img src="1.jpg" class="centerChild">
</div>
```

## padding--水平垂直居中 ##
通过在内容的上下左右添加同样的距离，来反向达到内容居中的效果。通过这种方法实现居中，限制条件比较多。这种居中相当于在模拟标准盒模型，父级相当于border，子级相当于content，二者的宽高通过padding相互制约。所以一般不使用padding来做居中，它经常的使用场景是，在一块内容分区的边线内的四面留白。如下图红线框起来的内容区和外边框之间的留白
![align/3](http://o798x2hdw.bkt.clouddn.com/align/3.png)

## margin 0 auto--水平居中 ##
最常使用的是margin:xx auto，可以令子级水平居中，但是在上下margin上设定auto却不能令其垂直居中，这是由CSS2规范的规定决定的^1。

原理是，CSS2规范规定：
>块级元素在普通流内布局按以下公式：左m+左b+左p+w+右p+右b+右m = 包含块宽度
>如果宽度w不是auto，并且左margin和右margin都是auto的话，两者相等。

然而纵向margin没有规定，于是就不能垂直居中。

**限制条件是：**

1. 父级子级均为block元素
2. 父级子级宽度固定
3. 并且处于正常文档流中

**失效情况：**

1. 如果子级是inline元素或者inline-block元素，居中会失效；
2. 如果没有设定父级宽度，居中会失效；
3. 如果子级宽度为auto那就整行撑满父级，宽度等于父级了，也就没有什么居中不居中了；
4. 子级设定了绝对定位或者固定定位，脱离了正常文档流，居中会失效；
5. 另外如果不写DOCTYPE或者写的不正确也会导致失效。

## absolute + left/top 50% + margin为宽/高一半的负值--水平垂直居中 ##
**限制条件：**

1. 父级定位relative，子级定位absolute,
2. 子级宽度确定
```
.father { border:2px solid #f77; height:100px; width:300px; position:relative; }
.child { width:100px; height:100px; background:#7c7; position:absolute; left:50%;  margin-left:-50px;}

<div class="father">
    <div class="child"></div>
</div>
```
![align/4](http://o798x2hdw.bkt.clouddn.com/align/4.png)

margin负值的重要作用有^2：

1. 相对自身偏移，达到的视觉效果相当于`position：relative; top：-xx; left:-xx; right:-xx; bottom:-xx`，但与之的区别在于，relative偏移后仍占据原来的位置，margin负值的偏移连原来占据的位置也会偏移，常利用这点，设置左上的margin负值，遮盖左边或者上边的border，如果设置右下的，则会被后面的内容遮住。

2. 扩展宽度例如

```
div { width:432px; background:#999; }
ul { margin-right: -10px; height:200px; border:1px solid #f77; padding:0;}
li { width:100px; margin-right:10px; float:left; height:100px; background:#7c7; list-style: none;}

<div>
    <ul>
        <li></li>
        <li></li>
        <li></li>
        <li></li>
    </ul>
</div>
```
![align/5](http://o798x2hdw.bkt.clouddn.com/align/5.png)

## absolute + left/top 50% + transform translate--水平垂直居中 ##
上面的居中方法有一个限制是必须知道子级的宽高，CSS3中的transform的translate可以基于自身尺寸设定百分数，这样就不用知道子级的宽高，也能做到居中了。此方法从张鑫旭大神博客学来^3，感谢大神。
缺点： IE9(-ms-), IE10+以及其他现代浏览器才支持
```
    .father { position:relative; 
              width:300px; height: 300px; border:1px solid red;
            }
    .child { position:absolute; 
             left:50%; 
             top:50%;
             transform: translate(-50%, -50%);

             width:100px; height: 100px; background: #7c7;
            }
    <div class="father">
        <div class="child"></div>
    </div>
```
![align/6](http://o798x2hdw.bkt.clouddn.com/align/6.png)

## margin：auto + absolute 0 0 0 0--水平垂直居中 ##
比起上面的方法，可以兼容IE8+浏览器，同样来自张鑫旭大神^3，出来效果同上

```
    .father { position:relative; 
              width:300px; height: 300px; border:1px solid red;
            }
    .child { position:absolute; 
             left:0;
             top:0; 
             right: 0; 
             bottom: 0;

             margin:auto;

             width:100px; height: 100px; background: #7c7; 
            }

    <div class="father">
        <div class="child"></div>
    </div>
```

简单翻译下原理 ^4：

1. 正常文档流内，对于top和bottom，margin为auto，等同于margin等于零；
2. absolute使之脱离正常文档流，此时的margin auto相当于不起作用了；
3. 设定四面都为0后，浏览器会在设定了relative的父元素内，贴近父元素的border设置一个边界盒子（bound box），此时，边界盒子占满父元素所有空间；
4. 给子元素设定了宽或者高，就阻止其占满父元素所有空间，迫使浏览器基于边界盒子重新计算margin：auto；
5. 现在元素是绝对定位，非正常文档流，浏览器计算margin：auto时，就让上下margin相等（基于规范规定），这样元素就居中于边界盒子（bound box），也就居中于父元素了。

## table-cell + vertical-align --水平居中##
如果是在td元素中，可以直接使用属性valign="middle"做垂直居中，align="center"做水平居中
```
td { width:300px; height: 300px; border:1px solid red; }
.tdChild { width:100px; height: 100px; background: #7c7; }

<td valign="middle" align="center">
    <div class="tdChild"></div>
</td>

```

如果没有table，没有td元素，可以用css这样写：
这种方法，子级不需要大小固定，可以用于：多行文字居中、大小不固定图片的居中
有一个疑问，许多博客写这种方法的时候，都要给.father再加一个父级，让这个父级display:table,我不知道为什么要这么加，因为不加的时候就可以居中。
```
    .father {
        display: table-cell;
        vertical-align: middle;
         
        width:300px; 
        height: 300px; 
        border:1px solid red;
    }
    .child { display: inline-block; background: #7c7; }

    <div class="father">
        <div class="child">dafsdf sda sdf sdfas sdf<br>dsfasd sdf dd </div>
    </div>
```

## css3 flex居中--水平垂直居中 ##
兼容性不太好，适用于手机端开发，水平垂直居中：
```
    .father { display:-webkit-box;
        -webkit-box-align:center;
        -webkit-box-pack:center;
        
        width:300px; 
        height: 300px; 
        border:1px solid red; 
    } 
    .child { display:-webkit-flex; 

        width: 100px; 
        height:100px; 
        background: #7c7; 
        margin-bottom:10px;
    }
    <div class="father">
        <div class="child"></div>
    </div>
```
![align/7](http://o798x2hdw.bkt.clouddn.com/align/7.png)

本人常用的就是这些居中方法了，还有一些居中方式，本人比较不常用，由于精力有限就不加了。另外小可才疏学浅，时间仓促，如有错漏处，请大力批评！另外的另外，写博客真累，但是也真的很有成就感呀，赫赫~~

> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[http://luckymona.github.io/2016/09/08/summary-of-aligning-to-center-solutions-in-css](http://luckymona.github.io/2016/09/08/summary-of-aligning-to-center-solutions-in-css)

参考链接：

^1:[为什么「margin:auto」可以让块级元素水平居中？](https://www.zhihu.com/question/21644198)
^2:[CSS布局奇淫巧计之-强大的负边距](http://www.cnblogs.com/2050/archive/2012/08/13/2636467.html)
^3:[小tip: margin:auto实现绝对定位元素的水平垂直居中](http://www.zhangxinxu.com/wordpress/2013/11/margin-auto-absolute-%E7%BB%9D%E5%AF%B9%E5%AE%9A%E4%BD%8D-%E6%B0%B4%E5%B9%B3%E5%9E%82%E7%9B%B4%E5%B1%85%E4%B8%AD/)
^4:[Absolute Horizontal And Vertical Centering In CSS](https://www.smashingmagazine.com/2013/08/absolute-horizontal-vertical-centering-css)

