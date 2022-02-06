---
title: JavaScript语言精粹-总结
date: 2022-02-01 22:21:36
tags:
  - JavaScript
categories:
  - FrontEnd
---

**因为这本书出版的时候ES6还没有问世，所以书中不涉及ES6的知识点**。因此下面的总结不会使用ES6的语法。

## Data Types

**共8种数据类型**

**原始类型(基本类型):**

1. Number
2. String
3. Boolean
4. null
5. undefined
6. Symbol
7. BigInt

**引用类型**

8. Object

#### 检测数据类型

javascript 提供了 typeof 结构用来检测数据的类型。但是对于引用类型，函数则会返回'function'，其他的引用类型和null都会得到'object'。

```javascript
console.log(typeof Math.floor) // 'function' 
console.log(typeof null)       // 'object'
console.log(typeof [])         // 'object'
console.log(typeof {})         // 'object'
```

可以看出，typeof 有诸多限制，无法区分 null 以及除 function 之外的其他引用类型。  
既然 typeof 走不通，那就使用 javascript 提供的另一种检测方法，
**Object.prototype.toString**，它可以检测出所有的数据类型，非常方便

```javascript
var protoToString = Object.prototype.toString

console.log(protoToString.call(1))         // [object Number]
console.log(protoToString.call('1'))       // [object String]
console.log(protoToString.call(undefined)) // [object Undefined]
console.log(protoToString.call(null))      // [object Null]
console.log(protoToString.call([]))        // [object Array]
console.log(protoToString.call({}))        // [object Object]
console.log(protoToString.call(Math.floor)) // [object Function]
```


## Object

**Note that: 对象通过引用来传递，他们永远不会被复制**

对象字面量创建对象:
```javascript
var obj = {
  name: 'w2xi',
  age: 26,
  'profile-info': {
    phone: '13027330803',
    address: 'ChangSha.China',  
  },
  sayHello: function(){
    console.log('hi')
  }
}
```

检索属性。如果该对象没有相应的属性，则会沿着原型链查找，找到则返回相应的值，否则为undefined。
```javascript
obj.name
obj['profile-info']
```

赋值。只会在该对象上操作，不会影响原型链。

```javascript
obj.name = 'wiki'
obj['profile-info']['address'] = 'ShangHai.China'
```

删除对象的属性。只会删除该对象上的属性，不会影响原型链上的属性。
```javascript
delete obj.age
```

对象枚举。
```javascript
for ( var key in obj ){
  console.log(key, obj[key])
}
```

判断对象上是否有某个属性
```javascript
Object.prototype.hasOwnProperty('prop') // 目标对象上有这个属性，则返回true，否则false
'prop' in Object // 属性在原型链上，则返回true，否则false

obj.hasOwnProperty('name') // true
obj.hasOwnProperty('money') // false
```

判断对象上没有constructor属性，但是原型链上有。
```javascript
!obj.hasOwnProperty('constructor') && 'constructor' in obj // true
```


## Function

**函数是 javascript 的一等公民。**  

函数的组成: function关键字，函数名(标识符)，紧跟函数名的参数列表以及由花括号({})扩起来的函数体。

创建函数:
+ 普通函数
```javascript
function sum(a ,b)
{
  return a + b
}
```
+ 通过字面量的方式
```javascript
var sum = function(a, b){
  return a + b
}
```

+ 构造函数(首字母大写，默认约定)，在继承部分会详细介绍
```javascript
function Foo(){}
```

调用:
```javascript
sum(1, 2) // 3
```
IIFE(Immediately Invoked Function Expression)立即调用的函数表达式:
```javascript
(function(){
  // todo something.
}())
```

#### Arguments 

**arguments** 是函数内部特有的一个变量，保存了调用函数时传递的所有参数。  

**描述**:  
arguments 是一个**类数组(array-like)**对象，它有一个length属性并且属性从下标0开始，但是它没有数组内置的一些方法，像 forEach(), map()

将类数组转换为数组:
```javascript
function foo()
{
  var args = Array.prototype.slice.call(arguments)
  // Using an array literal is shorter than above but allocates an empty array
  var args = [].slice.call(arguments)

  console.log(args)
}

foo(1, 2, 3) // [1, 2, 3]
```
求和，不限定参数个数:
```javascript
function sum()
{
  var total = 0

  for ( var i = 0; i < arguments.length; i++ ){
    total += arguments[i]
  }

  return total
}

sum(1, 2， 3, 4) // 10
```

#### Scope

在javascript中，用var声明的变量是函数作用域的。是的，不是块级作用域，这一特性非常糟糕，可能不小心就会导致一些问题。
外部函数的变量对内部函数来说都是可见的；内部函数的变量对外部来说是不可见的。

```javascript
var color = 'red'

function outer(){
  var outerName = 'outer'

  function inner(){
    var innerName = 'inner'
    // color, outerName is seen
  }

  // innerName is not seen

  inner()
}

// outerName, innerName is not seen

outer()
```

**变量提升**  
用 var 声明的变量会被提升到该变量所在词法环境(lexical environment)的顶部。  
例子:
```javascript
function test()
{
  console.log(a) // undefined

  var a = 'test'
}

test()
```

#### Closure

让我们来看一个例子，很简单，就是一个计数器:
```javascript
var counter = (function(){
  var count = 0

  return {
    add: function(inc){
      return count += typeof inc === 'number' ? inc : 1
    },

    getValue: function(){
      return count
    }
  }
}())

counter.add() // 1
counter.add() // 2
console.log(counter.getValue()) // 2
```
这里使用了iife。函数里定义了一个count变量，
该变量对 add 和 getValue 方法总是可用的，但函数的作用域使用它对其他的程序来说是不可见的。  

该函数将返回一个包含两个方法的对象，并且这些方法继续享有访问count变量的特权。  
像这样，函数可以访问它被创建时所处的上下文环境。这被成为闭包。


#### Currying

[柯里化](https://zh.javascript.info/currying-partials)

柯里化是一种函数的转换，它是指将一个函数从可调用的 f(a, b, c) 转换为可调用的 f(a)(b)(c)。  
柯里化不会调用函数，它只是对函数进行转换。

举个例子:
```javascript
// 实现如这种形式的函数调用

sum(1, 2, 3) // 6
sum(1, 2)(3) // 6
sum(1)(2)(3) // 6
```

```javascript
var protoSlice = Array.prototype.slice

function f(a, b ,c)
{
  // Converts array-like to an array 
  // var arr = protoSlice.call(arguments)

  // return arr.reduce(function(a, b){ return a + b }, 0)

  return a + b + c
}

function curry(func)
{
  return function curried(){
    var args = protoSlice.call(arguments)

    if ( args.length >= func.length ){
      return func.apply(this, args)
    }else {
      return function(){ 
        return curried.apply(this, args.concat(protoSlice.call(arguments)))
      }
    }
  }
}

var sum = curry(f)

sum(1, 2, 3) // 6
sum(1, 2)(3) // 6
sum(1)(2)(3) // 6
```

## Inheritance ( extends )

#### 模拟 new 操作符的执行过程

## Array

## Regex( Regular Expression )

