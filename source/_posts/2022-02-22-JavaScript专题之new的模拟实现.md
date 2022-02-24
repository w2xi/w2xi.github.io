---
title: JavaScript专题之new的模拟实现
date: 2022-02-22 14:50:35
tags:
  - JavaScript专题系列
  - JavaScript
categories:
  - FrontEnd
---

一句话介绍new:

> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。

这句话似乎有点不是很好理解。在模拟 <code>new</code> 之前，我们先看看 <code>new</code> 做了什么事情。

举个栗子:

```javascript
// 前端开发
function FrontEndDeveloper(name, age) {
  this.name = name
  this.age = age
  this.skills = ['HTML', 'CSS', 'JavaScript', 'Vue', 'React']
}

// 我们知道，程序员一般没有女朋友，而且发量较少
FrontEndDeveloper.prototype.hasGirlfriend = false
FrontEndDeveloper.prototype.isLessHair = true
// 口头禅:
FrontEndDeveloper.prototype.say = function(){
  console.log('996是福报', '我要做群里最卷的那个仔')
}
// 理想的工作:
FrontEndDeveloper.prototype.dream = function(){
  console.log('钱多事少离家近')
}

const augest = new FrontEndDeveloper('八月'， 20)

console.log(augest.name)          // 八月
console.log(augest.age)           // 20
console.log(augest.hasGirlfriend) // false
console.log(augest.say())         // 996是福报 我要做群里最卷的那个仔
console.log(augest.dream())       // 钱多事少离家近
console.log(augest instanceof FrontEndDeveloper) // true
console.log(augest.__proto === FrontEndDeveloper.prototype) // true
```

从上面的栗子，我们可以看出，augest:

+ 可以访问 FrontEndDeveloper 构造函数中的属性 ( 其实是 augest 本身的属性 )
+ 可以访问 FrontEndDeveloper.prototype 上的属性
+ 是 FrontEndDeveloper 的实例

**注意**: 
> 虽然 augest 是 FrontEndDeveloper 的实例，但是 augest 并不是继承 FrontEndDeveloper，而是 继承 FrontEndDeveloper.prototype

> JavaScript 并不像其它OOP语言，它采用的是对象继承对象的形式(原型)，而且还不是直接的继承，而是使用了一个间接量: 用构造函数创建对象


## 实现

接下来，让我们来模拟 new 运算符。

当你 <code>new</code> 一个构造函数的时候，<code>new</code> 替我们做了很多事情，它的执行步骤如下:

1. 创建一个对象
2. 将新创建的对象的[[prototype]]特性指向构造函数的原型对象
3. 将新创建的对象绑定到构造函数中的 this 并传入对应的参数，调用构造函数
4. 如果调用构造函数的返回结果是一个对象，则返回该对象；否则返回新创建的对象

```javascript
function mockNew(){
  var constructFunc = arguments[0]
  var args = Array.prototype.slice.call(arguments, 1)

  var target = Object.create(constructFunc.prototype)
  // or 
  // var target = new Object()
  // target.__proto__ = constructFunc.prototype

  var ret = constructFunc.apply(target, args)

  return (typeof ret === 'object' && ret) || target
}

// 验证:

function Person(name, age){
  this.name = name
  this.age = age
}

var xiaoming = mockNew(Person, 'xiaoming', 21)

console.log(xiaoming)                               // Person {name: "xiaoming", age: 21}
console.log(xiaoming.__proto__ === Person.prototype)  // true
```

## 参考

[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)

[https://github.com/mqyqingfeng/Blog/issues/13](https://github.com/mqyqingfeng/Blog/issues/13)