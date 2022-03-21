---
title: JavaScript专题之深浅拷贝
tags:
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

如果是浅拷贝一个对象或数组，使用 ES6的 [展开运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax) <code>...</code>

### 测试

```javascript
const xiaoming = { name: '小明', profile: { address: 'ChansSha.China' } }
const xiaomingCopy = { ...xiaoming }

console.log(xiaoming === xiaomingCopy)                 // false
console.log(xiaoming.profile === xiaomingCopy.profile) // true
```

```javascript
const arr = [{ name: '小明' }, 24]
const newArr = [...arr]

arr[0].name = '花花'
arr[1] = 30

console.log(arr)    // [{ name: '花花' }, 30]
console.log(newArr) // [{ name: '花花' }, 24]
```

我们发现，如果数组的元素是基本类型，就会直接拷贝元素的值；如果数组元素是对象或者数组(引用类型)，就会拷贝其引用，我们知道，修改引用类型属性的值，两者都会发生变化。

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








