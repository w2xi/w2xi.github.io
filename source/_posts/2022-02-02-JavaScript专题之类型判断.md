---
title: 2022-02-02-JavaScript专题之类型判断
date: 2022-02-02 15:24:40
tags:
  - JavaScript专题
categories:
  - FrontEnd
---

## 前言

类型判断，在我们平时的工作中经常会使用到。比如，判断给定值是否是数字，字符串，布尔值，数组等类型。

## typeof

typeof 应该是我们最常用地用于判断数据类型的一种方式了。比如:

```javascript
typeof 1 // 'number'
```

引用 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof) 对typeof的介绍:
> typeof 操作符返回一个字符串，表示未经计算的操作数的类型。

最新的 ECMAScript 标准定义了8种[数据类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Grammar_and_Types#%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E5%92%8C%E7%B1%BB%E5%9E%8B):

+ 7种基本数据类型
  + Number
  + String
  + Boolean
  + null
  + undefined
  + Symbol
  + BigInt
+ 以及对象 Object

对这8种数据类型分别使用 typeof:
```javascript
typeof 1            // 'number'
typeof 'a'          // 'string'
typeof true         // 'boolean'
typeof null         // 'object'
typeof undefined    // 'undefined'
typeof Symbol('s')  // 'symbol'
typeof BigInt(2)    // 'bigint'
typeof {}           // 'object'
```
可以看到，得到的结果与它们的类型并非完全一一对应。null 和 {} 都返回了 object 字符串。

在 javascript 中，可以说一切皆对象。数组是对象，函数是对象，但是 typeof 却可以检测出函数类型:
```javascript
typeof function(){} // 'function'
```

现在我们知道了，typeof 可以检测出: Number, String, Boolean, undefined, Symbol, BigInt, Function 类型。
但是，对于 Array, Object, Error, Math 等对象似乎无能为力。
```javascript
typeof []         // 'object'
typeof {}         // 'object'
typeof new Error  // 'object'
typeof Math       // 'object'
```

难道我们真的没有办法了吗？

## Object.prototype.toString

**toString()方法返回一个表示该对象的字符串，格式: '[object type]'，type 表示对象的类型**

**来自MDN:**
> 为了每个对象都能通过 Object.prototype.toString() 的检测，需要以 Function.prototype.call() 或者 Function.prototype.apply() 的形式来调用，传递要检查的对象作为第一个参数，称为 thisArg。

看个例子:
```javascript
const protoToString = Object.prototype.toString

protoToString.call(1)                   // '[object Number]'
protoToString.call('a')                 // '[object String]'
protoToString.call(true)                // '[object Boolean]'
protoToString.call(null)                // '[object Null]'
protoToString.call(undefined)           // '[object Undefined]'
protoToString.call(Symbol('s'))         // '[object Symbol]'
protoToString.call(BigInt(2))           // '[object BigInt]'
protoToString.call([])                  // '[object Array]'
protoToString.call({})                  // '[Object Object]'
protoToString.call(new Error)           // '[object Error]'
protoToString.call(Math)                // '[object Math]'
protoToString.call(new Date)            // '[object Date]'
protoToString.call(/abc/)               // '[object RegExp]'
protoToString.call(new Promise(()=>{})) // '[object Promise]'
protoToString.call(function(){})        // '[object Fuction]'
protoToString.call(function*(){})       // '[object GeneratorFunction]'
protoToString.call(async function(){})  // '[object AsyncFunction]'
```

Object.prototype.toString 方法可以检测出多少种类型呢？ '几乎'所有的类型。这里用几乎是因为我发现，
```javascript
protoToString.call(new Proxy({},()=>{}))// '[object Object]'
```
和预期的 '[object Proxy]' 不一致，以后找到原因了再更新。


## type API

既然通过 Object.prototype.toString 可以检测出几乎所有的类型，那么我们可以对其进行封装。

我的设想：

写一个 type 函数能检测各种类型的值，如果是基本类型，就使用 typeof，引用类型就使用 toString。此外鉴于 typeof 的结果是小写，我也希望所有的结果都是小写。

```javascript
/**
 * 获取要检测的数据类型
 * @param {*} value The Value to check
 * @returns {string} Returns the type of data
 */
function type(value)
{
  const protoToString = Object.prototype.toString
  const isReference = typeof value === 'object' || typeof value === 'function'

  return isReference ? protoToString.call(value).slice(8, -1).toLowerCase() : typeof value
}
```

## 封装常用的 API

### isNumber

```javascript
function isNumber(value)
{
  return type(value) === 'number'
}
```

### isString

```javascript
function isString(value)
{
  return type(value) === 'string'
}
```

### isNull

```javascript
function isNull(value)
{
  return type(value) === 'null'
}
```

### isUndefined

```javascript
function isUndefined(value)
{
  return type(value) === 'undefined'
}
```

### isBoolean

```javascript
function isBoolean(value)
{
  return type(value) === 'boolean'
}
```

### isSymbol

```javascript
function isSymbol(value)
{
  return type(value) === 'symbol'
}
```

### isObject

```javascript
function isObject(value)
{
  return value !== null && type(value) === 'object'
}
```

#### isArray

对于数组的检测，ES6定义了Array.isArray API，用来检测数组类型。目前，所有主流浏览器都已经支持该接口。

```javascript
function isArray(value)
{
  return Array.isArray(value)
}
```

#### isFunction

```javascript
function isFunction(value)
{
  return type(value) === 'function'
}
```

#### isArguments

```javascript
function isArguments(value)
{
  return type(value) === 'arguments'
}
```

#### isArrayLike

类数组(array-like)

```javascript
function isArrayLike(value)
{
  return isObject(value) && !isFunction(value) && isNumber(value.length)
}
```

#### isPlainObject

```javascript
function isPlainObject(value)
{
  const hasOwnProperty = Object.prototype.hasOwnProperty
  const funcToString = Function.prototype.toString

  if ( !isObject(value) ){
    return false
  }

  const proto = Object.getPrototypeOf(value)

  if ( proto === null ){
    return true
  }

  const Ctor = hasOwnProperty.call(proto, 'constructor') && proto.constructor

  return typeof Ctor === 'function' && Ctor instanceof Ctor && 
      funcToString.call(Ctor) === funcToString.call(Object)
}
```

做一下验证:

```javascript
isPlainObject([])               // false
isPlainObject(new function(){}) // false
isPlainObject({})               // true
isPlainObject(Object.prototype) // true
```