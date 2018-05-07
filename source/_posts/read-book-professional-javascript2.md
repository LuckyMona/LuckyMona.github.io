---
title: Javascript基础2
date: 2016-09-17 15:00:00
categories: Javascript
tags: [Javascript,红皮书,读书笔记] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：

1. `<script>`的属性defer、async、src、type
2. MINE类型、load事件、DOMContentLoaded事件
3. 文档模式
4. 报错"Identifier Expected"是因为标识符命名使用了关键字
5. 用var定义变量和省略var造成的影响
6. 5种基本数据类型，一种复杂数据类型，使用typeof分别返回什么值
7. null和undefined的区别和联系，各自的使用场景，二者相等(==)，分别使用Number()和parseInt/parseFloat得到的值
8. typeof可以使用未定义变量而不报错
9. 会转换为false的五个值
10. NaN的两个特性，isNaN函数判断是不是可以转换为数字
11. 使用时一般用Number方法转换除string外的值，parseInt/parseFloat只能解析string值
12. parseInt/parseFloat解析字符串规则是，第一位非空格值必须是数字，否则结果就是NaN
13. 使用parseInt最好指明进制，parseInt('070',10)
14. parseFloat只能解析十进制
15. string字符字面量、转义序列，\ntrbf,`\\` ,\",\',\xnn,\unnnn
16. string的length对双字节字符不准
17. null和undefined没有toString()方法，所有类型都有String()方法
18. number类型的toString()方法，可以传参，2、8、10、16，代表进制

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript2](http://luckymona.github.io/2016/09/17/read-book-professional-javascript2)

# 一、使用Javascript #

## 1.使用Javascript的两种方式 ##
使用`<script>`内嵌或者引入外部脚本文件，不能既内嵌又引入，例如这样：
```
<script src="xxx.js">
console.log('js');
</script
```
此时浏览器不解析内嵌的JS代码，只解析引入的代码。
最佳实践是使用外部文件，而非嵌入，优点有：易维护、可缓存（同一个文件可以多除引用）、适应未来
## 2.`<script>`标签的常用属性有：src, defer, async,type ##

- type, 描述引入文件的MIME类型
- src，有src属性的标签都可以跨域，这样的标签有`<script>`, `<img>`,`<iframe>`
- defer, 只能在外部脚本文件上使用，表示延迟到文档完全被解析和显示后再加载，照HTML5规定，会在DOMContentLoaded事件触发前执行(浏览器实现不一定会在此之前执行)，即遇到`</html>`标签在执行，用法：`<script defer scr="xx.js"></script>`
- async，HTML5定义的属性，只能在外部脚本文件上使用，表示开始执行JS脚本，但是不阻塞后面脚本的执行。标记为async的多个`<script>`标签，并不一定按照前后顺序执行。async脚本一定会在load事件之前执行，但可能会在DOMContentLoaded事件触发之前或之后执行

把`<script>`引入文件放在body标签的最后，防止阻塞页面展现

**MIME：**定义数据格式，前面是数据的大类型，后面是具体类型，如text/html、img/gif等
**load事件：**在页面中的一切都加载完毕时触发，包括JS脚本、CSS、图片等
**DOMContentLoaded事件：**DOM节点树映射完成时触发，高程P408

## 3.文档模式 ##
通过DOCTYPE区分文档模式，分为混杂模式、标准模式和准标准模式，混杂模式会让IE表现出与IE5相同的行为，如果不加DOCTYPE会让浏览器按混杂模式解析。标准模式和准标准模式有很小的差别，**标准模式**(不探讨XHTML)：
```
<!-- HTML 4.01 严格型 -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
"http://www.w3.org/TR/html4/strict.dtd">

<!-- HTML 5 -->
<!DOCTYPE html>
```
**准标准模式**：
```
<!-- HTML 4.01 过渡型 -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">

<!-- HTML 4.01 框架集型 -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD HTML 4.01 Frameset//EN"
"http://www.w3.org/TR/html4/frameset.dtd">
```
现在一般使用html5的DOCTYPE声明

# 二、基本概念 #

1. JS中的一切都 **区分大小写**
2. 标识符的第一位可以是字母、下划线、美元符$: aa、 _a、 $a；还可以是中文，但是不推荐
3. 严格模式`use strict`，IE10+
4. 关键字和保留字都不能做标识符，关键字做，会报错 **"Identifier Expected"**
5. JS中的变量是松散类型的，也就是可以保存任何类型，**变量只是一个占位符**
6. **用var定义的变量，会成为所在作用域的局部变量**，与外界无关，当函数运行完毕，会被销毁![var](http://o798x2hdw.bkt.clouddn.com/var.png)
7. **在函数中省略var，会创建一个全局变量**，非常不建议这样做，因为在局部作用域定义的全局变量很难维护

# 三、数据类型 #
这一部分在if else控制流程时很重要的。

JS中共6种数据类型，5种基本(简单)数据类型：Null、Undefined、String、Number、Boolean
1种复杂数据类型：Object

## 3.1检测数据类型使用typeof ##
对应的可以检测出来的值有：还是六种类型，null会被检测为"object", function不是一种数据类型，但是会被检测为"function"
|  数据类型 | typeof 检测值 |
|-----------|---------------|
| null      | "object"      |
| undefined | "undefined"   |
| string    | "string"      |
| number    | "number"      |
| boolean   | "boolean"     |
| object    | "object" / "function" |
例外:正则表达式，在Safari 5和Chrome 7前版本浏览器中，typeof值为"function"，新版本为"object"

## 3.2 null与undefined区别 ##
### 1. undefined ###

1. 定义：已经定义但没有赋值，即没有初始化的变量 `var a; // a === undefined`
2. 没必要显式赋值，变量未初始化时默认是undefined
3. 使用场景：

 - `var a`定义变量而不初始化，默认值为undefined
 - `if (a=== undefined)`流程控制时做判断条件
 - 对象属性没有赋值，默认值为`undefined`^1
`var a={bb:"xx"}; console.log(a.aa); `
 - 函数不显式定义返回值，默认返回`undefined`
```
function aa(){
    var a=1;
    a++;
}
console.log(aa()); // undefined
```
 - 调用函数时，该提供的参数没有提供，参数值默认为`undefined`
 ```
 function aa(bb){
            console.log(bb);
        }
        aa();
 ```

### 2.null ###

1. 定义: 空对象指针
2. 应该显式赋值  初始化一个未来保存object变量时，初始化为null
3. 使用场景：

- 作为对象初始化的值`var timer = null;`
- 作为函数的参数，表示该函数的参数不是对象（这一条的用法不是很理解，谁知道吗？告诉我一下）
- null不是对象，但是是对象原型链的终点`Object.prototype.__proto__； // null`而null没有prototype；
- `var dom = document.getElementById('domId');`如果domId不存在，dom变量为null

### 3. 其他 ###
1.  undefined 值是派生自 null 值的，所以`null == undefined；//true`但是`null === undefined //false`
2. `Number(undefined) = NaN; Number(null) = 0`，但是使用parseInt和parseFloat都是NaN
3. 
```
var aa;
console.log(aa); //undefined
console.log(bb); // 报错
console.log(typeof bb) //undefined
```

## 3.3 Number类型 ##
1. Boolean()函数，作为if条件，都会自动转换为Boolean值，转换为false的值：null、undefined、""、0和NaN、
2. 八进制在严格模式下失效，会报错，十六进制以'0x'开头
3. `1.`和`1.0`都会被解析成1，浮点数计算不准，不能作为判断条件例如0.1+0.2并不等于0.3
4. 科学计数法3.123e7
5. `Number.MIN_VALUE>0`是最接近0的正数，isFinite(Number.MAX_VALUE)，Infinity也是一个number
6. `typeof -Infinity //'number'`

### 1.NaN ###

1. NaN，即非数值（Not a Number）是一个特殊的数值，这个数值用于表示一个本来要返回数值的操作数未返回数值的情况（这样就不会抛出错误了）。
2. `0/0 = NaN; 1/0 = Infinity; -1/0 = -Infinity`
3. NaN的两个特性：

- 任何涉及NaN的计算，结果都是NaN
- NaN与谁都不相等，`NaN==NaN;//false`
4. isNaN() 

- 可以转化为数值的返回false，包括：null（转化为0），boolean，number类型（不含NaN）,"","10";
- 不可以转化为数值的返回true，包括：undefined，NaN，Object，"10aa"

### 2. 数值转换Number、parseFloat、parseInt ###
三种方法:

1. Nubmer()适用于各种数据类型，但是字符串转换规则比较复杂，所以 **Number一般用来转换除字符串的其他类型**
2. **parseFloat和parseInt专门用于字符串转数值**，转换非字符串类型时，都返回NaN
3.parseInt转换字符串的规则是:**查看字符串第一位非空格值，如果是数字或者负号，结果就是数值，否则就是NaN**，所以`parseInt("") = NaN`

- parseInt('22.5')=22,因为小数点不是有效数字字符
- 第一个字符是数字，那么可以识别以0开头后跟数字的八进制和0x开头后跟数字的16进制：
```
parseInt('070')=70; 
parseInt('070',8) = 56; 
parseInt('0x11') = 17; 
parseInt('1f') = 1; 
parseInt('1f',16)=31
```
- **建议`parseInt`无论在什么情况下都明确指定基数**:`parseInt('070',10)`

4. parseFloat也是从首个非空格字符开始解析，直到第二个小数点，后面的都忽略，与parseIn的区别：

- 首个小数点有效，但是小数点后都是0，将解析为整数
- 只能解析十进制，首位0都忽略

## 3.4 String ##

1. 字符字面量，又叫转义序列: `\ntbrf, \\, \', \",  \xnn, \unnnn`
 
- '\xnn'以16进制代码表示一个字符，"\x41" 输出结果为"A"
- '\unnnn'16进制代码表示一个unicode字符，为unicode码，'\u7530'输出结果为汉字"田"
2. string的length属性，在计算包含双字节字符时可能会不准，
 
- 字节知识：最小单位为bit比特/位，8bit = 1byte 字节，英文字母单字节8bit，汉字双字节16bit

3. 字符串不可变，改变一个字符串值，就是销毁重建一个，IE6-效率很低
4. 除了null和undefined，其他数据类型都有toString()方法，数值的toString方法有参数，2、8、10、16，表示转成什么进制表示的字符串
```
var num = 2;
console.log(num.toString(2)); //'10'
console.log(NaN.toString()); //'NaN'
```
但是不能写作：`2.toString(2)`，会报错
5. 比toString()更包容的String(), 也可以把null和undefined转换为字符串，`String(null)="null"; String(070)="56"; String(0x1f)="31"`
6. 转换为字符串还有一种方法：`var b = 1+""; var a = null + "";`




---
> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript2](http://luckymona.github.io/2016/09/17/read-book-professional-javascript2)

Reference：

^1:[undefined与null的区别](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html)





