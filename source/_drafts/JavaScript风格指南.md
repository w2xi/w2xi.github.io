---
title: JavaScript风格指南
tags:
  - JavaScript
categories:
  - FrontEnd
---

## 前言

### 强类型校验

**使用 <code>===</code>，而不是 <code>==</code>**

你知道下面的真假吗？
```javascript
null == 0;    // ?
0 == [];      // ?
1 == [1];     // ?
true == [1];  // ?
```
当你使用 **==** 弱类型比较操作符时，你就要小心了，因为它可能在你不明所以的情况下做了类型转换而且你可能还不知道原因。

这里面有大量的隐式转换规则，如果你想掌握它，那就去熟读 ECMAScript 标准规范吧。

我敢打赌，就算你花了大量时间记住了所有的隐式转换规则，但是一段时间后你肯定会忘记很多。

### 变量

我们知道，使用 <code>var</code> 声明的变量是 **函数作用域的**；
使用 <code>const</code> or <code>let</code> 声明的变量是 **块级作用域的**。

使用 <code>var</code> 来声明变量有一些不好的地方。

+ 总是使用 <code>const</code> or <code>let</code> 来声明变量
```javascript
// bad
var hasApples = true;

// good
const hasApples = true;
```

#### 变量命名


### 全局变量

**全局变量是魔鬼，我们不应该使用它。**

## 类型的包装对象

**不要使用 包装类型**，我们没有任何理由使用它。

我们知道: Number, String, Boolean 基本类型都有其对应的包装类型。

对包装类型使用 typeof 操作符得到结果是 'object'，它们都是引用类型。

```javascript
typeof new Number(1);     // 'object'
typeof new String('a');   // 'object'
typeof new Boolean(true); // 'object'
```

### 缺少块的语句

**不要省略花括号**

我们知道，像 if,for 这样的代码块内只有一条语句的，可以省略后面的花括号来简写。

**但是，不要这样做，没有任何好处，反而牺牲了代码的可读性且容易出错。**

**Bad:**
```javascript
if ( hasProp ) doSomething();
```

**Good:**
```javascript
if ( hasProp ){
  doSomething();
}
```

我们的本意可能是这样的:
```javascript
if ( hasProp ){
  taskOne();
  taskTwo();
}
```
省略花括号后:
```javascript
if ( hasProp )
  taskOne();
  taskTwo();
```

### 构造函数或类的命名首字母大写

