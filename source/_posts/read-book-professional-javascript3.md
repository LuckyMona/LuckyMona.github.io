---
title: Javascript基础3
date: 2016-09-17 16:00:00
categories: Javascript
tags: [Javascript,红皮书,读书笔记] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：

1. 一切对象都继承自Object，但不包括宿主对象，如DOM对象、BOM对象，宿主环境定义宿主对象
2. 所有对象都有的方法有：constructor、isPrototypeOf、hasOwnProperty、toString; valueOf、toLocalString、propertyIsEnumberable
3. 对象使用toString()一般得到"[object Object]"
4. 除null和undefined的数据类型可以使用操作符；
5. Object类型使用操作符时会先使用toString或者valueOf转换为字符串，如果需要的是数值，再使用Number转换成数值，之后进行操作
6. ++i与i++的区别：i值改变，与所在语句求值，这两个顺序的不同
7. 加性操作、乘性操作中，涉及NaN、0、Infinity的特殊状况
8. 逻辑与&&或||，短路操作，当有一个值是对象时，结果怎样，当有一个值null、undefined、NaN时，结果怎样
9. 位操作~ & | ^ << >> >>>非与或，异或，左移，有符号右移，无符号右移
10. 位操作常用场景：~与indexOf配合、求整、判奇偶、判正负、随机颜色

<!-- more -->

> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript3](http://luckymona.github.io/2016/09/17/read-book-professional-javascript3)


## 3.5 Object ##

1. 对象就是一组数据和功能的组合；
2. Object类型是所有对象的祖先，但不是宿主对象的祖先，例如宿主环境浏览器中的BOM和DOM对象是由宿主实现和定义的
3. 除了宿主对象，其他对象全都拥有有这些方法：
```
var o = new Object; //不传参时，可以不用圆括号，但不推荐这种写法
console.log(); //打印以下值：

o.constructor();
o.hasOwnProperty(propertyName);
o.isPrototypeOf(object);

o.toString();       // 返回[object NativeConstructorName]，可以用于对象类型检测
o.toLocalString();  // 基础功能类似toString，返回的字符串与执行环境的地理地区对应，可用于Date对象，输出以当地时间格式表示的字符串
o.valueOf();        //


o.propertyIsEnumberable(propertyName); //不常用
```
4. 对Object使用toString()方法，通常会得到'[object Object]'，也可以这样用：
```
var o = { toString: function(){
        return -1;
    }
}
console.log(o.toString()); //-1
```

## 3.6 操作符 ##
包括：算数操作符、位操作符、关系操作符、相等操作符，这些操作符均可以应用于除null和undefined之外的数据类型，操作符应用于Object时，会先调用toString()或valueOf()转换成字符串，以取得可以操作的值

1. 使用+、-操作时会先转换数据类型，`1+ "" = "1"; 1-"" = 1;`
2. ++i和i++的区别在于，所在语句求值之前改变i，还是在语句求值之后，--i和i--同理：
```
var i=0;
console.log(i++) // 0 

var i= 0;
console.log(++i); // 1

var j = 20;
var res = j++ + 1; // res = 21

var m = 20;
var res = ++m +1 // res = 22

```

3. 逻辑与&&
有一个操作数不是布尔值的时候：
对象：

- 第一个值为对象，返回第二个值
- 第二个值为对象，第一个值计算结果为true时返回第二个数
- 两个值都是对象，返回第二个值

其他特殊：

- 有一个为NaN、undefined、null，则结果分别为NaN、undefined、null，这三个值相互&&，则返回第一个值：
```
NaN && undefined;  // NaN
undefined && NaN;  // undefined
null && NaN;       // null
NaN && null;       //NaN
```

逻辑与是短路操作，如果第一个值为false，就不对第二个值求值

4. 逻辑或 ||
有一个操作数不是布尔值时：
对象：

- 第一个值为对象，返回第一个值
- 第二个值为对象，如果第一个值为false，返回第二个值
- 两值都为对象，返回第一个

其他特殊：

- 两个值都是null、undefined、NaN，那么分别返回null、undefined、NaN
- null、undefined、NaN之间相互或||运算，返回第二个值
```
NaN || null; // null
null || NaN; // NaN
```

5. 乘性操作符
乘*、除/、取余%，如果操作数不是数值，会先用 **Number()**转换成数值
乘法特殊点的：

- Infinity * 0 = NaN
- Infinity乘以非0，是Infinity或者-Infinity

除法特殊点的：

- Infinity / Infinity = NaN
- 0 / 0 = NaN
取余特殊的太多，不记了

6. 加性操作符

- Infinity+ (-Infinity) = NaN
- -Infinity - (-Infinity) = NaN
- Infinity - Infinity = NaN

7. 比较操作符>、<、==

- NaN与任何值进行比较大小，结果都是false：
```
NaN > "a"; //false
NaN <= "a"; //false
```

相等操作符:

- null 和undefined
```
null == undefined； // true
null == 0;          // false
undefined == 0;     // false
```

- NaN与任何值比较相等，都是false，比较不等都是true：
```
NaN == null // false
NaN != null // true
NaN != NaN  // true
```

- 如果两个都是对象，如果这两个指向同一个对象，返回true，否则false


## 3.7 位操作 ##

### 3.7.1 位操作基础知识 ###
1. JS的数字都使用64位格式存储，所以一个数字是4Byte，此外一个英文字母是1Byte，一个汉字是2Byte，基本数据类型变量8Byte
2. 机器层面是64位，使用JS实际操作时，按照32位操作，有符号整数，左起第一位是符号位，其余为数值位，无符号整数，32位都是数值位，因此数值更大
3. 负数存储使用二进制补码，求二进制补码的过程：

- 求这个数绝对值的二进制码
- 求二进制码的反码(0变1,1变0)
- 将反码加一
4. 负数的二进制补码形式只在机器存储层面表现，使用toString()输出二进制字符串时，对于负数，得到的只是绝对值二进制码前面加符号
5. 机器层面把64位存储转换成32位，JS操作完再把32位转成64位存储，这个过程导致副作用是：对NaN和Infinity应用位操作时，都被当成0处理
6. **位操作都只对整数有效，如果是小数，先舍弃小数，再进行位操作运算**

### 3.7.2 位操作运算 ###

#### 1位操作符列表 ####
| 操作符 |        英文名         |   中文名   |          用法          |
|--------|-----------------------|------------|------------------------|
| ~      | NOT                   | 按位非     | `~i = -(i+1)`，求反码值 |
| &      | AND                   | 按位与     | `var res = 3 & 7`      |
| ^      | XOR                   | 按位异或   | `var res = 25 ^ 3`      |
| <<     | LEFT SHIFT            | 左移       | `var res = 2 << 5`       |
| >>     | RIGHT SHIFT           | 有符号右移 | `var res = 64 >> 5`      |
| >>>    | ZERO-FILL RIGHT SHIFT | 无符号右移 | `var res = 64 >>> 5`     |

按位或的符号与MD表格冲突，列在表格外：
`|`   OR    按位或    `var res = 3 | 7`  

#### 2位操作符应用场景^2^3^4： ####
列举常用的比较简单的使用场景，尽量少用，会使阅读者理解困难，主要是当别人装13时，自己不至于蒙圈

1. ~-1 = 0，配合利用arr.indexOf(xxx) = -1代表arr中不存在xxx
```
var arr = ['aa', 'bb'] ;
if(~arr.indexOf(xxx)) {}  // 相当于if(0)，即if(arr中不存在xxx)
```
2. 求整，相当于Math.floor：
```
var num1 = ~~1.1; // 1 
var num2 = ~~1.9; // 1 
var num3 = 1.9|0; // 1
```
3. & 判断奇偶：
```
function isOdd(n){
    return n & 1?true:false;
}
isOdd(3); // true
isOdd(4); // false
```
4. <<和>>2的幂运算
```
function power(n){
    return 1 << n; // 求2的n次方
}
power(5); //32

console.log(32>>1); //求32的一半
```
5. `>>>`判断数的正负
```
function isPos(n){
   return n === (n>>>0) ? true:false;
}
isPos(-1); // false
isPos(1); // true
```
-1>>>0虽然没有向右移动位数，但-1的二进制码已经变成了正数的二进制码：
所以-1>>>0的值为4294967295。
6. 或者随机颜色色值，其实用到位运算的就是一个取整，使用Math.floor是一样的
`'#'+(Math.random()*0xFFFFFF|0).toString(16)`



---
> 本文为原创文章，尊重辛勤劳动，可以免费摘要、推荐或聚合，但完整转载需经过本人同意，本人邮箱 miao.hnlk@gmail.com
> 本文地址：[http://supermaryy.com/2016/09/17/read-book-professional-javascript3](http://luckymona.github.io/2016/09/17/read-book-professional-javascript3)

Reference：

^2:[js中位运算的运用](http://www.tuicool.com/articles/VJryYb)
^3:[JS中位操作符有什么特殊作用](http://www.kqiqi.com/knowledge/program/1079.html)
^4:[小代码大学问之JavaScript位运算](http://www.mmfei.com/?p=299)




