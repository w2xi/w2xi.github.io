---
title: JavaScript专题之深浅拷贝
date: 2022-03-22 22:56:09
tags:
  - JavaScript
  - JavaScript专题系列
categories:
  - FrontEnd
---

深浅拷贝应该算是面试的高频考点之一，而且在工作中也会经常使用到。

## 浅拷贝

简单点说，浅拷贝: "如果一个对象或数组最外层元素的值是基本类型，那么拷贝的是该值，否则拷贝的是该引用。"

JavaScript 本身就提供了一些方法或操作来浅拷贝数组或对象。

比如 ES5 的 <code>Object.assign()</code>，<code>Array.prototype.slice()</code>，<code>Array.prototype.concat()</code>

ES6+ 的 <code>Array.from()</code> 和 <code>...</code> 扩展符

> :information_source: 建议:  
> 除非是为了兼容旧浏览器，否则建议使用 ES6 语法，因为现在所有的主流浏览器都支持它。  
> 而且现在的编码风格指南和最佳实践都推荐使用ES6，更用 Good和Bad来区分代码的好坏了。
> 更重要的是，ES6 的语法更加简洁，可读性更高。

如果是浅拷贝一个对象或数组，我们更多的是使用 ES6的 [展开运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax) <code>...</code>

### 测试

浅拷贝一个对象

```javascript
const xiaoming = { name: '小明', profile: { address: 'ChansSha.China' } }
const xiaomingCopy = { ...xiaoming }
```

浅拷贝一个数组

```javascript
const arr = [{ name: '小明' }, 24]
const newArr = [...arr]

arr[0].name = '花花'
arr[1] = 30

console.log(arr)    // [{ name: '花花' }, 30]
console.log(newArr) // [{ name: '花花' }, 24]
```

我们发现，如果数组的元素是基本类型，就会直接拷贝元素的值；如果数组元素是引用类型，就会拷贝其引用；如果修改引用类型属性的值，则两者都会发生变化。

### 浅拷贝的实现

<code>...</code> 展开符只是一个语法糖，和 <code>Array.from()</code>, <code>Object.assing()</code> 一样，只是 JavaScript 语言本身提供的便捷方法。

接下来我们思考一下应该如何实现对数组或对象的浅拷贝。

其实并不难，遍历对象，把对应的属性和值放到一个新的对象就行了。

让我们开始吧～

```javascript
function shallowCopy(obj){
  const hasOwnProperty = Object.prototype.hasOwnProperty

  const isObject = function(val){
    return !!(typeof val === 'object' && val)
  }

  // 其实这里还可以检测的更严谨一些，比如只有obj是数组或者是对象字面量时才通过，
  // 否则直接返回对应的值，或者抛出一个错误。
  
  if ( !isObject(obj) ){
    return obj
  }

  // 根据obj的类型判断是新建一个对象还是数组
  const newObj = Array.isArray(obj) ? [] : {}

  // 遍历obj，并且只有key是obj的属性时才拷贝
  for ( let key in obj ){
    if ( hasOwnProperty.call(obj, key) ){
      newObj[key] = obj[key]
    }
  }

  return newObj
}
```

## 深拷贝

顾名思义，深拷贝就是对一个对象的完全复制，无论它嵌套的层级有多深，新得到的对象与原来的对象彼此独立，互不影响。

那如何深拷贝一个对象呢？其实这里有一个小技巧，不仅适用于对象还适用于数组，额，其实它们都是对象子类型，真拗口～。

```javascript
const arr = [{ id: 1, name: 'xiaoming' }, { id: 2, name: 'huahua' }]

const newArr = JSON.parse(JSON.stringify(arr))

console.log(newArr)
```

我们轻而易举地深拷贝了一个数组，是不是很方便？

但是请小心，这种方法只适用于 <code>JSON</code> 安全的数据。

> :information_source: 像 <code>undefined，function, symbol 以及 循环引用(对象之间互相引用)</code> 都不符合 JSON 结构标准，支持 JSON 的语言无法处理它们。

**<code>JSON.stringify(..)</code>在对象中遇到 <code>undefined<code> 、<code>function</code> 和 <code>symbol</code> 时会自动将其忽略，在数组中则会返回 null (以保证单元位置不变)。**  

举个栗子:

```javascript
console.log(JSON.parse(JSON.stringify([undefined, () => {}, Symbol(), 1]))) 
// [null, null, null, 1]

console.log(JSON.parse(JSON.stringify({ name: undefined, say: function(){}, age: 24 }))) // { age: 24 } 
```

### 深拷贝的实现

那如何实现一个深拷贝呢？其实原理也很简单，和浅拷贝类似，只是多了一些步骤，我们在拷贝的时候判断一下属性值的类型，如果是引用类型，就递归地调用的深拷贝函数就行了。

```javascript
function deepCopy(obj){
  const hasOwnProperty = Object.prototype.hasOwnProperty

  const isObject = function(val){
    return !!(typeof val === 'object' && val)
  }

  // 这里这么做是出于严谨性的考虑，原因同上。

  if ( !isObject(obj) ){
    return obj
  }

  const newObj = Array.isArray(obj) ? [] : {}

  for ( let key in obj ){
    if ( hasOwnProperty.call(obj, key) ){
      const curr = obj[key]
      newObj[key] = isObject(curr) ? deepCopy(curr) : curr
    }
  }

  return newObj
}
```

不知道你发现了没有，我们似乎刻意忽略了函数，要知道函数也是一个对象子类型啊。

```javascript
const arr = { 
  name: 'xiaoming', 
  profile: { 
    address: 'China' 
  }, 
  say: function(){ 
    console.log('xiaoming') 
  },
}
const newArr = deepCopy(arr)

console.log(arr.profile === newArr.profile) // false
console.log(arr.say === newArr.say)         // true
```

记得在《你不知道的JavaScript(上卷)》中这样说道：

> 我们还不确定“复制”一个函数意味着什么。有些人会通过 <code>toString()</code> 来序列化一个函数的源代码(但是结果取决与JavaScript的具体实现，而且不同的引擎对于不同类型的函数处理方式不完全相同)...(P109)

## structuredClone()

在新的浏览器上已经可以使用内置的API <code>structuredClone()</code> 深拷贝JavaScript值了，

浏览器开始提供原生能力了，这是个令人振奋的消息。

浏览器兼容:

![Chrome](https://raw.github.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.github.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![Edge](https://raw.github.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Safari](https://raw.github.com/alrra/browser-logos/master/src/safari/safari_48x48.png) | ![Opera](https://raw.github.com/alrra/browser-logos/master/src/opera/opera_48x48.png)
:---: | :---: | :---: | :---: | :---: | 
 98 ✔ |  94 ✔ |  98 ✔ | 15.4 ✔ | 84 ✔ |


是的，这个 API 只能在新的浏览器中使用，如果你要应用在实际项目中，则需要做一些兼容性处理。

对于这个 <code>structuredClone</code> 解决了什么问题，有什么不足，我这里就不赘述了，可以参考下面的链接。

## 参考

[https://github.com/mqyqingfeng/Blog/issues/33](https://github.com/mqyqingfeng/Blog/issues/33)

[https://web.dev/structured-clone/](https://web.dev/structured-clone/)

[https://developer.mozilla.org/en-US/docs/Web/API/structuredClone](https://developer.mozilla.org/en-US/docs/Web/API/structuredClone)





