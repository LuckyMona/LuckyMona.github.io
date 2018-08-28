---
title: 继承以及Super
date: 2018-05-10 20:30:00
categories: Javascript #CSS，HTTP，Javascript，NodeJS，框架
tags: [ES6] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
一个小小的总结，主要关注以下三个问题：ES5的继承方式，ES5的继承与ES6的继承的区别，ES6的super的几种使用方式以及其中this的指向。

<!-- more -->

> From  [http://supermaryy.com](http://supermaryy.com)

## 一、ES5的继承

[JS实现继承的几种方式](https://www.cnblogs.com/humin/p/4556820.html)

[MDN | Object.create | 用 `Object.create`实现类式继承](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

继承可以分为对象实例的继承，类的继承

## 二、ES6的继承

Class B extends A { } 中的A可以是个class，还可以是个有prototype属性的函数

## 三、ES5继承与ES6继承的区别

1. **this的区别**

   ES5 的继承，实质是先创造子类的实例对象`this`，然后再将父类的方法添加到`this`上面（`Parent.apply(this)`）。ES6 的继承机制完全不同，实质是先将父类实例对象的属性和方法，加到`this`上面（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

2. **原型链ES6有两条**

   ```javascript
   class A { }
   class B extends A { }
   
   B.__proto__ === A // true
   B.prototype.__proto__ === A.prototype // true
   ```

   这样的结果是因为，类的继承是按照下面的模式实现的。

   ```javascript
   class A { }
   class B { }
   // B 的实例继承 A 的实例
   Object.setPrototypeOf(B.prototype, A.prototype);
   // B 继承 A 的静态属性
   Object.setPrototypeOf(B, A);
   const b = new B();
   
   //setPrototypeOf的内部实现
   Object.setPrototypeOf = function (obj, proto) {
     obj.__proto__ = proto;
     return obj;
   }
   //但是setPrototypeOf会有性能问题，通常推荐使用Object.create
   ```

3. **ES6可以继承原生构造函数，而ES5不能**

   1. 原生构造函数有：Boolean()、Number()、String()、Array()、Date()、Function()、RegExp()、Error()、Object()
   2. 原因：

      - ES5 是先新建子类的实例对象`this`，再将父类的属性添加到子类上，由于父类的内部属性无法获取，导致无法继承原生的构造函数。

      - ES6 允许继承原生构造函数定义子类，因为 ES6 是先新建父类的实例对象`this`，然后再用子类的构造函数修饰`this`，使得父类的所有行为都可以继承。
   3. 继承`Object`的子类，有一个[行为差异](http://stackoverflow.com/questions/36203614/super-does-not-pass-arguments-when-instantiating-a-class-extended-from-object)。

      ```javascript
      class NewObj extends Object{
        constructor(){
       super(...arguments);
        }
      }
      var o = new NewObj({attr: true});
      o.attr === true  // false
      ```

      上面代码中，`NewObj`继承了`Object`，但是无法通过`super`方法向父类`Object`传参。这是因为 ES6 改变了`Object`构造函数的行为，一旦发现`Object`方法不是通过`new Object()`这种形式调用，ES6 规定`Object`构造函数会忽略参数。


## 四、ES6的super及其this

1. **作为函数**：在子类构造函数中调用 `super()` ，super相当于父类constructor

2. **作为对象**：

   - 在子类**构造函数或普通函数**中使用 `super.methodA()` ，super相当于父类原型

     **此时methodA中的this指向子类实例**

   - 在子类**静态方法**中使用 `super.methodB()`，super指向父类而非原型，此时的methodB指的是父类的静态方法methodB

     **此时methodB中的this指向子类**，所以只能通过this访问到子类的静态方法和属性

3. 在子类中使用super**给属性赋值**  `super.father_prop = 1` ，相当于子类的this  `this.father_prop=1` 

   ```javascript
   class A {
     constructor() {
       this.x = 1;
     }
   }
   
   class B extends A {
     constructor() {
       super();
       this.x = 2;
       super.x = 3;
       console.log(super.x); // undefined
       console.log(this.x); // 3
     }
       change(){
           super.x = 4
           console.log(super.x); //undefined
           console.log(this.x);  // 4
       }
   }
   let b = new B();
   b.change()
   ```


4. 使用`super`的时候，必须显式指定是作为函数、还是作为对象使用，否则会报错。

   ```javascript
   class A {}
   
   class B extends A {
     constructor() {
       super();
       console.log(super); // 报错
     }
   }
   ```

## 五、ES6 的class的本质

1. 不存在任何继承时。`A`作为一个基类（即不存在任何继承），就是一个普通函数，所以直接继承`Function.prototype`。

   ```javascript
   class A { }
   A.__proto__ === Function.prototype // true
   A.prototype.__proto__ === Object.prototype // true
   ```

## 参考

1. [Class 的继承](http://es6.ruanyifeng.com/#docs/class-extends)

