---
title: 前端常见知识点整理 ---- 原型
date: 2020-01-02 19:22:22
categories: 
	- 前端技术
	- 前端常见知识点整理
tags: 
	- 面试
	- 知识点
---
> 作者：李旭光
> 引用请标明出处


# 原型
![yuanxing.png](https://i.loli.net/2020/01/02/7ov8I4YDiBkjJyr.png)
每个***函数***都有 `prototype` 属性，除了 `Function.prototype.bind()`，该属性指向`原型对象`，简称`原型`，`prototype`原型对象里的`constructor`指向构造函数本身。
每个***对象***都有 `__proto__` 属性，指向了创建该对象的***构造函数的原型***。其实这个属性指向了 `[[prototype]]`，但是 `[[prototype]]` 是内部属性，我们并不能访问到，所以使用 `_proto_` 来访问。
对象可以通过 `__proto__` 来寻找不属于该对象的属性，`__proto__` 将对象连接起来组成了`原型链`。

<!-- more -->
![总结](https://pic2.zhimg.com/v2-e722d5325f7d4215169f1d04296e0f89_r.jpg)
# `prototype` 原型对象
## 什么是原型？
每个函数都具有 `prototype` 属性，它被默认成一个对象，即原型对象
首先来介绍下 `prototype` 属性。这是一个`显式`原型属性，只有`函数`才拥有该属性。基本上所有函数都有这个属性，但是也有一个例外
```
// js代码
let fun = Function.prototype.bind()
```
如果你以上述方法创建一个函数，那么可以发现这个函数是不具有 prototype 属性的。

## `prototype` 如何产生的
当我们声明一个函数时，这个属性就被自动创建了。
``` js

function Foo() {}
```
并且这个属性的值是一个对象（也就是`原型`），只有一个属性 `constructor`
`constructor` 对应着构造函数，也就是 `Foo`。
## 什么是原型链？
当对象使用属性时，先在自身找，有就直接用，没有就沿着__proto__这条链往上找，直到 Object 原型的位置，有就返回相应的值，没有就返回 underfined。

## `constructor` 构造函数
### 什么是构造函数？
任何一个函数，只要被 new 操作符使用，就可以是一个构造函数（构造函数建议以大写开头）
另外，在 JavaScript 的内置对象中，所有的函数对象都是 Function 构造函数的实例，比如：Object、Array等

`constructor` 是一个公有且不可枚举的属性。一旦我们改变了函数的 `prototype` ，那么新对象就没有这个属性了（当然可以通过原型链取到 `constructor`）。

那么你肯定也有一个疑问，这个属性到底有什么用呢？其实这个属性可以说是一个历史遗留问题，在大部分情况下是没用的，在我的理解里，我认为他有两个作用：

1. 让实例对象知道是什么函数构造了它
2. 如果想给某些类库中的构造函数增加一些自定义的方法，就可以通过 `xx.constructor.method` 来扩展

## `_proto_`
这是每个对象都有的`隐式原型属性`，指向了创建该对象的`构造函数`的原型。其实这个属性指向了 `[[prototype]]`，但是 `[[prototype]]` 是内部属性，我们并不能访问到，所以使用 `_proto_` 来访问。

因为在 JS 中是没有类的概念的，为了实现类似继承的方式，通过 `_proto_` 将对象和原型联系起来组成原型链，得以让对象可以访问到不属于自己的属性。

### 实例对象的 `_proto_` 如何产生的
从上图可知，当我们使用 `new` 操作符时，生成的实例对象拥有了 `_proto_` 属性。
``` js
function Foo() {}
// 这个函数是 Function 的实例对象
// function 就是一个语法糖
// 内部调用了 new Function(...)
```
所以可以说，在 `new` 的过程中，新对象被添加了 `_proto_` 并且链接到构造函数的原型上。

### `new` 的过程
1. 新生成了一个对象
2. 链接到原型
3. 绑定 `this`
4. 返回新对象

在调用 `new` 的过程中会发生以上四件事情，我们也可以试着来自己实现一个 `new`
``` js
function Person( name ){ 
 this.name = name; 
}; 
Person.prototype.getName = function(){ 
 return this.name; 
};

function create() {
	let obj = new Object() // 从 Object.prototype 上克隆一个空的对象
	let Con = [].shift.call(arguments) // 获取外部传入的构造器，此例是 Person 
	obj.__proto__ = Con.prototype	// 指向正确的原型,链接到原型
	let result = Con.apply(obj, arguments) // 绑定 this，执行构造函数，借用外部传入的构造器给 obj 设置属性
	return typeof result === 'object' ? result : obj // 确保 new 出来的是个对象
}

create(Person,'lixg')
```
对于实例对象来说，都是通过 `new` 产生的，无论是 `function Foo()` 还是 `let a = { b : 1 }` 。

对于创建一个对象来说，更推荐使用字面量的方式创建对象。因为你使用 `new Object()` 的方式创建对象需要通过作用域链一层层找到 `Object` ，但是你使用字面量的方式就没这个问题。

``` js
function Foo() {} // function 就是个语法糖,内部等同于 new Function()
let a = { b: 1 } // 这个字面量内部也是使用了 new Object()
```

## Function.__proto__ === Function.prototype
对于对象来说，`xx.__proto__.contrcutor` 是该对象的构造函数，但是在图中我们可以发现 `Function.__proto__` === `Function.prototype`，难道这代表着 `Function` 自己产生了自己?

答案肯定是否认的，要说明这个问题我们先从 `Object` 说起。

从图中我们可以发现，所有对象都可以通过原型链最终找到 `Object.prototype` ，虽然 `Object.prototype` 也是一个对象，但是这个对象却不是 `Object` 创造的，而是引擎自己创建了 `Object.prototype` 。所以可以这样说，所有实例都是对象，但是对象不一定都是实例。

接下来我们来看 `Function.prototype` 这个特殊的对象，如果你在浏览器将这个对象打印出来，会发现这个对象其实是一个函数。

我们知道函数都是通过 `new Function()` 生成的，难道 `Function.prototype` 也是通过 `new Function()` 产生的吗？答案也是否定的，这个函数也是引擎自己创建的。首先引擎创建了 `Object.prototype` ，然后创建了 `Function.prototype` ，并且通过 `__proto__` 将两者联系了起来。这里也很好的解释了上面的一个问题，为什么 `let fun = Function.prototype.bind()` 没有 prototype 属性。因为 `Function.prototype` 是引擎创建出来的对象，引擎认为不需要给这个对象添加 `prototype` 属性。

***所以我们又可以得出一个结论，不是所有函数都是 `new Function()` 产生的。***
有了 `Function.prototype` 以后才有了 `function Function()` ，然后其他的构造函数都是 `function Function()` 生成的。

现在可以来解释 `Function.__proto__ === Function.prototype` 这个问题了。因为先有的 `Function.prototype` 以后才有的 `function Function()` ，所以也就不存在鸡生蛋蛋生鸡的悖论问题了。对于为什么 `Function.__proto__` 会等于 `Function.prototype` ，个人的理解是：其他所有的构造函数都可以通过原型链找到 `Function.prototype` ，并且 `function Function()` 本质也是一个函数，为了不产生混乱就将 `function Function()` 的 `__proto__` 联系到了 `Function.prototype` 上。

## 总结
- `Object` 是所有对象的爸爸，所有对象都可以通过 `__proto__` 找到它
- `Function` 是所有函数的爸爸，所有函数都可以通过 `__proto__` 找到它
- `Function.prototype` 和 `Object.prototype` 是两个特殊的对象，他们由引擎来创建
- 除了以上两个特殊对象，其他对象都是通过构造器 `new` 出来的
- 函数的 `prototype` 是一个对象，也就是原型
- 对象的 `__proto__` 指向原型， `__proto__` 将对象和原型连接起来组成了原型链
![总结](https://img2018.cnblogs.com/blog/1731684/201907/1731684-20190704181143591-516719297.png)

## 归纳
`ES` 把对象定义为：“无序属性的集合，其属性可以包含基本值，对象和函数”。
严格来讲，这就相当于说对象是一组没有特定顺序的值。ES 中的构造函数可以用来创建特定类型的对象，用来在创建对象时初始化对象。它的特点是，一般为大写字母开头，使用 `new` 操作符来实例化对象，比如：

``` js
function Person() {}
var person = new Person();
person.name = "Kevin";
console.log(person.name); // Kevin
```

`Person` 就是构造函数， `person` 就是对象。对于对象而言，每个 `JS` 对象一定对应一个原型对象，并从原型对象继承属性和方法。对象 `__proto__` 属性的值就是它所对应的原型对象。对象的 `__proto__` 指向自己构造函数的 `prototype` 。所以对象的原型链就是 `obj.__proto__.proto__....` 。对于函数而言，只有函数才有 `prototype` 属性， `Person.prototype`  是一个对象，并且有两个属性， 一个是 `constructor` 指向其构造函数 `Person` ， 一个是 `__proto__`  属性：是一个对象，指向上一层的原型。原型链的尽头是 `Object.prototype` 。所有对象均从 `Object.prototype` 继承属性。`Function.prototype` 和 `Function.__proto__` 为同一对象。`Object/Array/String` 等等构造函数本质上和 `Function` 一样，均继承于 `Function.prototype` 。`Function.prototype` 直接继承 `Object.prototype` 。这里的 `Object` 和 `Function` 有点鸡和蛋的问题，总结：先有 `Object.prototype`（原型链顶端），`Function.prototype` 继承 `Object.prototype` `而产生，最后，Function` 和 `Object` 和其它构造函数继承 `Function.prototype` 而产生。属性查找时，先在对象自己上找，找不到才会一步步根据原型链往上找。
[继承](https://pic2.zhimg.com/v2-2e8ec703287854d174483ba5f9f937cf_1200x500.jpg)
## 关联阅读
[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)