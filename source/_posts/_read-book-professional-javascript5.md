---
title: Javascript基础5-Date
date: 2016-09-20 22:00:00
categories: Javascript
tags: [Javascript,红皮书,读书笔记] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
摘要：

1. for in 循环如果属性值为null和undefined，ES3报错，ES5终止循环，故使用前需要检测值是

<!-- more -->


> by MaryTien from  [http://supermaryy.com](http://luckymona.github.com)
> 本文地址：[http://supermaryy.com/2016/09/20/read-book-professional-javascript4](http://supermaryy.com/2016/09/20/read-book-professional-javascript4)

# 一、Date类型 #

1. for会自动调用Boolean()函数，把条件语句，转换成布尔值。

2. for(var i=0; i<3; i++){} 循环中定义的变量i，循环外部也可以访问，因为JS不存在块级作用域

3. **for in循环**可以遍历对象的属性，没有一定的顺序，如果属性值为null和undefined，ES3下会抛出错误，ES5中会中断循环，~~但是实际执行时并没有中断~~，所以实际使用前最好检测值是否为null或者是undefined：
```
// 这种写法不会中断：
var x;
var aa = { 
    bb:null,
    cc:x,
    dd:"1"
}

for(var propName in aa){
console.log(aa[propName]);
}
// 结果：null/undefined/1

// 这种写法会中断：
arr = new Array(3); // [undefined × 3]
arr.forEach(()=>console.log('have done'));  // 没有打印，浏览器语句返回值undefined
for(var i in arr) console.log('have done'); // 没有打印，浏览器语句返回值undefined
for(var i of arr) console.log('have done'); //3次"have done"
```

4. **label 语句**
label语句主要是与for循环配合使用，结合break和continue，可以跳出循环。多用在循环嵌套的时候
```
//下面例子结合break，continue同理
outLoop:
for(var i=0; i<4; i++){
    innerLoop:
    for(var j=0; j<4; j++){
        if(i==2 && j==2){
            break outLoop;
        }
        console.log('i:'+i+",j:"+j);
    }
}
// 输出值：
 i:0,j:0
 i:0,j:1
 i:0,j:2
 i:1,j:0
```

5. **with 语句**
简化对同一个对象的多次操作，如：
```
var obj = {a:'11', b:'22'}；
obj.a = 'aa';
obj.b = 'bb';

// 简化后：
var obj = {a:'11', b:'22'}；
with(obj){
    var c = "cc"
    a = 'aa';
    b = 'bb';
}
```
with代码块形成一个作用域，就是obj。JS解析时，会将with代码块中的所有变量a/b/c当做局部变量，如果局部环境中找不到定义a/b，就从obj的属性中查找，如果找到了就覆盖obj中的变量，如果找不到，就从with所在的外部作用域中找。
>这意味着在 with 语句的代码块内部，每个变量首先被认为是一个局部变量，而如果在局部环境中找不到该变量的定义，就会查询location 对象中是否有同名的属性。如果发现了同名属性， 则以 location 对象属性的值作为变量的值。这样会导致性能下降，不推荐使用。

6. **switch**
需要混合的时候，要加注释：
```

switch(i){
    case 12:
        //合并两种情形
    case 13:
        console.log('hhh');
        break;
    case 14:
        console.log('14');
        bresak;
}
```
据JS精粹这本书，贯穿的时候，每个都写break：
```
switch(i){
    case 12:
    case 13:
        break;
}

//上面写法易出错，且很难发现，应该这么写
switch(i){
    case 12:
        break;
    case 13:
        break;
}
```
**switch使用的全等操作符，所以并不会进行数据类型转换**

7. **函数**

    - 函数定义时不必指定是否返回值；
    - 如果加了return，只执行到return语句，后面的永远不会被执行，
    - 例外：try catch语句，如果有finally，即使在try 或catch中写了return，仍然会执行finally里的代码
    - 如果return语句不加返回值，默认将返回undefined

8. **参数**

    + arguments可以与命名参数一起使用
    + arguments与数组相似：可以用arguments[n]来访问值，有length属性。但不是数组，没有数组的方法：slice、splice、shift、unshift、push、pop、concat、join、sort、reverse
    + arguments的值永远与命名参数的值保持同步，其与命名参数并不是访问相同的内存空间，但是值会同步，没有传递值的；命名参数值默认被赋予undefined
    + ** 函数所有传参都是按值传递，不可能通过引用传参 **
    结论：对于数字、字符串等是将它们的值传递给了函数参数，函数参数的改变不会影响函数外部的变量；对于数组和对象等是将对象(数组)变量的值传递给了函数参数，这个值是指向对象(数组)的地址。

    **函数传参的过程是把函数外部的变量的值复制给函数的局部变量，这个局部变量是指arguments的某个值。**
    这句话对于理解引用类型传参时也是按值传递非常重要，检验是否理解引用类型按值传参的一段代码：
    ```
    function setName(obj) {
        obj.name = 'aaa';
        var obj = new Object();
        obj.name = 'ccc';
        return obj;
    }
    var person = new Object();
    person.name = 'bbb';

    var newPerson = setName(person);
    console.log(person.name + ' | ' + newPerson.name); // aaa | ccc
    ```

# 二、变量、作用域和内存问题 #

1. **基本类型和引用类型的值**

    + 定义基本类型值和引用类型值的过程：创建一个变量并为该变量赋值
    + 基本类型值指的是简单的数据段，而引用类型值指可能有多个值构成的对象
    + 5种基本数据类型是按值访问的，因为可以操作保存在变量中的实际的值
    + 引用类型的值是保存在内存中的对象的地址。JavaScript不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间。
    + 操作对象时，实际上是在操作对象的引用而不是实际的对象。为此，引用类型的值是按引用访问的

2. **堆栈**

    堆存储：类似书架与书，存取没有顺序，只要知道书的名字，就可以方便的取出想要的书
    栈存储：类似羽毛球筒与羽毛球，先存后取，后存先取，要拿中间某一个球必须先把它上面的球全拿出来

    栈的优势是存取速度比堆快，缺点是存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。堆的优势是可以动态分配内存大小，生存期也不必事先告诉编译器，垃圾收集器会自动地收走这些不再使用的数据，但是缺点是由于在运行时动态分配内存，所以存取速度较慢。

    基本类型值，一旦赋值，大小就固定了，因此存储在栈内存中。引用类型的值，可以动态的改变，例如定义一个对象之后，还可以给它添加变量和方法，因此大小不固定，所以存储在堆内存中。

    js中的基础类型变量，都是存放在变量对象中，JavaScript属于高级语言，高级语言中严格意义上来说并没有堆栈的区分。但是一些特殊场景会在逻辑上实现栈的存取方式，这才有了堆与栈的不同。

    理解堆栈存储：
    ```
    function Person(id,name,age){ 
        this.id = id; 
        this.name = name; 
        this.age = age; 
    } 
    var num = 10; 
    var bol = true; 
    var str = "abc"; 
    var obj = new Object(); 
    var arr = ['a','b','c']; 
    var person = new Person(100,"jxl",22);
    ```
    [stack-pic](http://o798x2hdw.bkt.clouddn.com/stack.png)

3. **赋值变量值**

    + 从一个变量向另一个变量复制基本类型值，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。
    + 当复制引用类型的值时，会将原变量的值复制一份放到为新变量分配的空间中。不同的是这个值是一个指针，改变其中一个变量会影响到另一个变量
    [copy_variable-pic](http://o798x2hdw.bkt.clouddn.com/copy-variable.png)

4. **传递参数**

    - JS中所有参数都是按值传递的
    - 把函数外部的值复制给函数内部参数，就和把值从一个变量复制到另一个变量一样
    - 传递引用类型值时传递的是指针，因此在函数中对局部变量参数的修改会反映到函数外部
    - 可以把函数参数想象成局部变量

5. **检测类型**
    - 检测基本类型值（number\string\undefined\boolean）用typeof，检测引用类型值用instanceof
    - 如果是对象或**null**，typeof会返回"object"
    - 如果是function，typeof会返回"function"
    - 如果变量是给定引用类型的实例，instanceof 操作符就会返回 true
    ```
    var person = function(){
      this.name = "aa";
    }
    var bill  = new person();
    console.log(bill instanceof person)  //true
    ```
    - 用instanceof检测基本类型值，始终返回false，因为基本类型值不是对象

6. **执行环境及作用域**
    - **执行环境（execution context）**定义了变量或函数有权访问的其他数据
    - 每个函数有自己的执行环境，每个执行环境有一个相关联的**变量对象(variable object)**，保存环境中所有的变量和函数
    - 全局执行环境是最外围的执行环境，在web浏览器中是window对象，所有全局的变量和函数都是作为window的属性和方法
    - 某个执行环境的所有代码执行完毕后，该环境其中的变量和函数全部销毁
    - 而全局执行环境在程序退出时销毁，web浏览器中对应关闭网页时
    - **ECMAScript程序的执行流：**每个函数都有自己的执行环境，当执行流进入一个函数时，函数的环境就被推入一个环境栈中。函数执行后，栈将其环境弹出，把控制权返还给之前的执行环境
    - **作用域链**，作用域链前端，是当前执行的代码所在环境的变量对象，如果环境是函数，就把函数的**活动对象（activation object）**作为变量对象。活动对象最开始只包含一个变量，即arguments对象（全局环境中不包含），作用域链下一个变量对象来自包含环境，这样一直延续到全局环境。
    - **延长作用域链**：with和try catch中的catch块可以延长作用域链，即在作用域链的前端临时添加一个变量对象，该变量对象在代码执行完毕后被移除。
    - 对于catch语句，会创建一个新的变量对象，其中包含对被抛出的错误对象的声明。
    ```
    function withLocation(){
        with(location){
            var url = href;
        }
        reutrn url
    }
    // with语句内部，定义了一个url变量，因而url就成了函数执行环境的一部分，所以可以作为函数的值被返回。
    ```
   
    - 标识符解析会沿着作用域链一级一级进行查找，直到找到声明变量的地方，假设局部环境有`var color = "red"`,此时在局部环境中只有使用`window.color`才可以访问在全局变量中定义的color
    - 变量查询有代价会比较慢，所以访问局部变量会比访问全局变量快，但现在差距几乎可以忽略
    
7. **垃圾收集**
    - JS具有自动垃圾收集机制，执行环境会负责管理代码执行过程中使用的内存，即内存分配和无用内存的回收
    - 这种自动垃圾收集机制的原理是：找出不再使用的变量，然后销毁释放其占用的内存。垃圾收集器会隔段时间就周期性地执行这一操作
    - 浏览器中对垃圾收集的实现方式包含：引用计数和标记清除两种
    - **标记清除**：当变量进入环境时（如在函数中声明一个变量）标记为“进入环境”，当变量离开环境时，标记为“离开环境”
    - **引用计数**：跟踪每个值被引用的次数，当引用次数为0时就在下一次垃圾收集器运行时销毁这个变量。
    - 当循环引用时，引用计数不能清除变量，会造成内存泄漏
    - IE9之前版本中部分DOM和BOM对象不是原生JS对象，而是C++实现的COM对象，涉及到COM对象与原生JS对象的相互引用时，即使页面中的BOM对象\DOM对象从页面移除了，他也不会被回收
    - 为了避免此类问题，在不使用的时候，手动断开DOM元素与原生JS对象间的连接

8. **管理内存**
    - 电脑分配给Web浏览器的内存比分给桌面程序的少，以避免浏览器耗尽内存引发系统崩溃
    - 这个内存限制，会对给变量分配内存造成影响，并影响调用栈以及在一个线程中同时执行的语句数量。
    - 所以为了获得更好的页面性能，需要注意**优化内存使用**，就是不使用的变量就解除引用，局部变量会在离开执行环境时自动解除引用，所以要注意的就是全局变量不使用时就解除引用。

# 三、引用类型 #

1. **Object类型**
    - 创建Object实例包含两种方法：`new Object()`和对象字面量`var obj = {}`，使用对象字面量创建对象，不会调用Object构造函数
    - `var obj = {}`是一个表达式上下文，表达式上下文指的是能够返回一个值；`if(a==b)`是一个语句上下文
    - 在最后一个属性后面添加逗号，会在一些浏览器中导致错误
    - 属性名可以是数值，此时会自动转换为数值字符串
    - 在向函数传递大量可选参数时，最好的做法是对那些必需值使用命名参数，而使用对象字面量来封装多个可选参数
    - 访问对象属性有两种：点和方括号，方括号的优点是可以通过变量来访问属性`var a = "name"; console.log(person[a])`，并且可以兼容比较奇怪的属性名：`person["first name"] = "Nicholas";`通常使用点方法访问

2. **Array类型**
    - 创建Array实例的方式：
    ```
    // Array构造函数方式
    var arr1 = new Array("a","b");
    var arr2 = Array("a", "b");
    var arr3 = Array(3);        

    // 数组字面量方式
    var arr4 = [1,2,3];

    var arrFault = [,,,];  //不推荐!
    var arrFault2 = [1,2,] //不推荐!
    ```
    - 在使用数组字面量表示法时，也不会调用 Array 构造函数【？】
    - 关于length:
        1. 如果索引值超出length，会自动以undefined补全长度
    ```
    var arr=[1,2,3]
    console.log(arr[5])      //undefined
    console.log(arr.length)  //5
    ```
        2. 可以通过设置length值，修改数组，截短或加长
    - 检测数组
        1. instanceof检测法`if(value instanceof Array)`，问题在于它假定只有一个全局执行环境，因为Array是window的属性。如果网页中包含多个frame，那实际上就存在多个window，即两个以上不同的全局执行环境，从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。
        2.  ECMAScript 5 新增了 Array.isArray()方法。这个方法的目的是最终确定某个值到底是不是数组，而不管它是在哪个全局执行环境中创建的。兼容性IE9+
        3.  22.1.1章节中的安全的类型检测的方法，可以解决低版本浏览器的类型检测问题
        ```
            function isArray(value){
                return Object.prototype.toString().call(value)=="[object Array]";
            }
        ```
            原理：在任何值上调用 Object 原生的 toString()方法，都会返回一个[object NativeConstructorName]格式的字符串。用来区分是否是JS原生对象，任何开发人员自定义的构造函数都将返回"[object Object]"。
    - 转换方法：toString、toLocaleString返回由每一项调用toString、toLocaleString得到的字符串组成的数组；valueOf返回原数组；arr.join("")拼接数组成字符串
    - 栈方法（后进先出）与队列方法（先进先出）
      1. push / pop：数组末尾添加/删除，push可添加多项，返回数组长度；pop移除一项，返回被移除的项
      2. shift / unshift：在数组前端移除 / 添加一项，shift返回移除的项，unshift可添加多项，返回新数组长度
      3. shift和pop，都只能移除一项，返回移除的项；unshift和push都可增加多项，并返回新数组长度。
    - 排序：reverse反序和sort排序
      1. sort用法
      ```
        arr.sort(fun)
        function fun(a,b){
            return a-b
            // 返回负值，a排在前面；返回0，顺序不变；返回正值，a排在后面
        }
      ```
    - 操作方法：concat、slice、splice
      1. concat参数为数组时，是把每一项添加到原数组中，可以用来给多维数组降维
      2. slice方法可接受一或两个参数，即要返回项的起始和结束位置，不会影响原始数组，若参数中有一个负数，则用数组长度加上该数来确定相应的位置
      3. splice最强大的数组方法，可以删除、插入和替换，主要用途是向数组的中部插入项，会改变原数组。返回一个数组，包含从原数组中删除的项（如果没有删除任何项，返回空数组）
      ```
      splice(0,2)
        //删除数组中的前两项
      splice(2,0,"red","green")
        //从当前数组的位置 2 开始插入字符串"red"和"green"
      splice (2,1,"red","green")
        //删除当前数组位置 2 的项，然后再从位置 2 开始插入字符串"red"和"green"
      ```
    - 位置方法：indexOf()和 lastIndexOf()，二者的区别在于，从头查和从尾查，在没查到的情况下返回-1。在比较第一个参数
与数组中的每一项时，**使用全等（===）操作符**。
    - 迭代方法：every、filter、forEach、map、some，都不会修改原数组，传入的函数接收三个参数：数组项的值、该项在数组中的位置和数组对象本身。`function(item, index, arr){}`
      1. every()：对数组中的每一项运行给定函数，若该函数对每一项都返回 true，则返回 true
      2. filter()：对数组中的每一项运行给定函数，返回该函数会返回true项组成的数组
      3. forEach()：对数组中的每一项运行给定函数。这个方法没有返回值
      4. map()：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组
      5. some()：对数组中的每一项运行给定函数，如果该函数对任一项返回 true，则返回 true
    - 归并方法ruduce和reduceRight，迭代数组的所有项，然后构建一个最终返回的值，区别是左起遍历还是右起遍历。传给 reduce()和 reduceRight()的函数接收4参数：前一个值、当前值、项的索引和数组对象。该函数返回的值会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。迭代和归并方法是ES5引入的方法，IE9+支持
    ```
    var values = [1,2,3,4,5];
    var sum = values.reduce(function(prev, cur, index, array){
    return prev + cur;
    });
    alert(sum); //15
    ```


---
> 本文地址：[http://supermaryy.com/2016/09/20/read-book-professional-javascript4](http://supermaryy.com/2016/09/20/read-book-professional-javascript4)

Reference：

1. [前端基础进阶（一）：内存空间详细图解](http://www.jianshu.com/p/996671d4dcc4#)
2. [深入理解javascript之内存分配](http://www.2cto.com/kf/201506/409654.html)




