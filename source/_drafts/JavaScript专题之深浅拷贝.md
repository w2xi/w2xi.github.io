---
title: JavaScript专题之深浅拷贝
tags:
---

深浅拷贝应该算是面试的高频考点之一，而且在工作中也会经常使用到。

## 浅拷贝

简单点说，浅拷贝: "如果一个对象或数组最外层元素的值是基本类型，那么拷贝的是该值，否则拷贝的是该引用。"

JavaScript 本身就提供了一些方法或操作来浅拷贝数组或对象。

比如 ES5 的 <code>Object.assign()</code>，<code>Array.prototype.slice()</code>，<code>Array.prototype.concat()</code>

ES6 的 <code>Array.from()</code> 和 <code>...</code> 扩展符

> :information_source: 建议:  
> 除非是为了兼容旧浏览器，否则建议使用 ES6 语法，因为现在所有的主流浏览器都支持它。  
> 而且现在的编码风格指南和最佳实践都推荐使用ES6，更用 Good和Bad来区分代码的好坏了:joy:。  
> 更重要的是，ES6 的语法更加简洁，可读性更高:smiley:。

如果是浅拷贝一个对象或数组，直接使用 ES6的 [展开运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax) <code>...</code>，它更简洁，优雅。

### 测试

```javascript
const xiaoming = { name: '小明', profile: { address: 'ChansSha.China' } }
const xiaomingCopy = { ...xiaoming }

console.log(xiaoming === xiaomingCopy)                 // false
console.log(xiaoming.profile === xiaomingCopy.profile) // true
```

```javascript
const users = [{ name: '小明' }]
const usersCopy = [...users]

console.log(users === usersCopy)       // false
console.log(users[0] === usersCopy[0]) // true
```

## 深拷贝






