---
title: 前端常见知识点整理 ---- 继承类型
date: 2020-01-13 11:18:22
categories: 
  - 前端技术
  - 前端常见知识点整理
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处

继承的操作需要有一个父类，这里使用构造函数外加原型来创建一个：
有下面两个类，下面实现Child继承Father：
``` js
// js代码

function Father() {
    this.type = 'prople'
}

Father.prototype.eat = function () {
    console.log('吃东西啦');
}

function Child(name) {
    this.name = name;
    this.color = 'black';
}
```
## 原型继承（认贼作父）
> ***关键点***：子类原型等于父类的实例 `Child.prototype = new Person()`（将父类指向子类的原型）。
``` js
// js代码
Child.prototype = new Father();

```
特点：
实例可继承的属性有：
- 实例的构造函数的属性
- 父类构造函数的属性 
- 父类原型上的属性
***新实例不会继承父类实例的属性***

缺点：
- 新实例无法向父类构造函数传参
- 继承单一
- 所有新实例都会共享父类实例的属性。--- 原型上的属性是共享的，一个实例修改了原型属性，另一个实例的原型属性也会被修改

## 构造继承（借腹生子）
> 在子类构造函数中调用父类构造函数
``` js
// js代码
function Child(name) {
    Father.call(this);
}
```
***关键点***：用call或apply将父类构造函数引入子类函数（在子类函数中做了父类函数的自执行（复制））Person.call(this, 'reng')
特点：
- 只继承了父类构造函数的属性，没有继承父类原型的属性
- 解决了原型链继承的注意事项（缺点）1，2，3
- 可以继承多个构造函数的属性（call可以多个）
- 在子实例中可以向父实例传参
缺点：
- 只能继承父类构造函数的属性
- 无法实现构造函数的复用。（每次用每次都要重新调用）
- 每个新实例都有构造函数的副本，臃肿
（不能继承父类原型，函数在构造函数中，每个子类实例不能共享函数，浪费内存。）

## 组合继承（原型继承+构造继承）
> 使用构造继承继承父类参数，使用原型继承继承父类函数
``` js
// js代码
function Child(name) { // 构造继承
    Father.call(this);
}

Child.prototype = Father.prototype; // Child.prototype = new Person(); // 原型继承
```
***关键点***：结合了两种模式的优点--向父类传参（call）和复用（prototype）
特点：
- 可以继承父类原型上的属性，可以传参，可复用
- 每个新实例引入的构造函数属性是私有的

缺点：
- 父类原型和子类原型是同一个对象，无法区分子类真正是由谁构造。
- 调用了两次父类的构造函数（耗内存）
- 子类的构造函数会代替原型上的那个父类构造函数（call相当于拿到了父类构造函数的副本）

## 原型式继承(复制降级)
``` js
// 先封装一个函数容器，用来承载继承的原型和输出对象
function object(obj) { // 寄生
    function F() {}
    F.prototype = obj;
    return new F();
}
var father = new Father();
var child = object(father);
console.log(child instanceof Father); // true
console.log(child.job); // frontend
```
***关键点***：用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了可以随意增添属性的实例或对象。Object.create()就是这个原理。

特点：
- 类似于复制一个对象，用函数来包装

注意事项：
- 所有的实例都会继承原型上的属性
- 无法实现复用。（新实例属性都是后面添加的）
`Object.create()`方法规范了原型式继承。这个方法接收两个参数，一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。
``` js
// 传一个参数的时候
var child = Object.create(new Father());
console.log(child.job); // frontend
console.log(child instanceof Father); // true
// 传两个参数的时候
var child = Object.create(new Father(), {
    name: {
        value: 'come on'
    }
});
child.sayHello(); // Hello come on
```

## 寄生组合继承
它跟`组合继承`一样，都比较常用。
***寄生***：在函数内返回对象然后调用
***组合***：
- 函数的原型等于另一个实例
- 在函数中用apply或call引入另一个构造函数，可传参
``` js
// 寄生
function object(obj) {
    function F(){}
    F.prototype = obj;
    return new F();
}
// object是F实例的另一种表示方法
var obj = object(Father.prototype);
// obj实例（F实例）的原型继承了父类函数的原型
// 上述更像是原型链继承，只不过只继承了原型属性

// 组合
function Child() { // 构造
    this.age = 100;
    Father.call(this); // 这个继承了父类构造函数的属性
} // 解决了组合式两次调用构造函数属性的特点

// 重点
Child.prototype = obj; // 原型

console.log(Child.prototype.constructor); // Father
obj.constructor = Child; // 一定要修复实例
console.log(Child.prototype.constructor); // Child
var child = new Child();
// Child实例就继承了构造函数属性，父类实例，object的函数属性
console.log(child.job); // frontend
console.log(child instanceof Father); // true
```
***重点***：修复了组合继承的问题

在上面的问题中，你可能发现了这么一个注释`obj.constructor = Sub; // 一定要修复实例。`为什么要修正子类的构造函数的指向呢？

因为在不修正这个指向的时候，在获取构造函数返回的时候，在调用同名属性或方法取值上可能造成混乱。比如下面：
``` js
function Car() { }
Car.prototype.orderOneLikeThis = function() {  // Clone producing function
    return new this.constructor();
}
Car.prototype.advertise = function () {
    console.log("I am a generic car.");
}

function BMW() { }
BMW.prototype = Object.create(Car.prototype);
BMW.prototype.constructor = BMW;              // Resetting the constructor property
BMW.prototype.advertise = function () {
    console.log("I am BMW with lots of uber features.");
}

var x5 = new BMW();

var myNewToy = x5.orderOneLikeThis();

myNewToy.advertise(); // => "I am BMW ..." if `BMW.prototype.constructor = BMW;` is not 
                      // commented; "I am a generic car." otherwise.
```

`object.create` 在组合继承的基础上，子类继承一个由父类原型生成的空对象。

``` js
// js代码
function Child(name) {
    Father.call(this);
}

Child.prototype = Object.create(Father.prototype, {
    constructor: {
        value: Child
    }
})
```
## inherits函数
``` js
// js代码

function inherits(ctor, superCtor) {
  ctor.super_ = superCtor;
  ctor.prototype = Object.create(superCtor.prototype, {
    constructor: {
      value: ctor,
      enumerable: false,
      writable: true,
      configurable: true
    }
  });
}; 

// 使用
function Child() {
  Father.call(this);
  //...
}
inherits(Child, Father);

Child.prototype.fun = ...
```