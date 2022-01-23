---
title: Javascript数组常用的操作方法
date: 2022-01-15 13:29:45
tags:
  - JavaScript
categories: 
  - FrontEnd
---

## 数组去重

### 数组中的元素是原始数据类型

```javascript
const numbers = [1, 2, 3, 1]

console.log([...new Set(numbers)])
console.log(Array.from(new Set(numbers))

// 输出结果：
[1, 2, 3]

```

### 数组中的元素是对象类型

```javascript
const todos = [
  { id: 1, title: 'Vue' },
  { id: 2, title: 'React' },
  { id: 3, title: 'Angular' },
  { id: 1, title: 'Vue' },
]

console.log(arrayUnique(todos, 'id'))

// 输出结果:
[
  { id: 1, title: 'Vue' },
  { id: 2, title: 'React' },
  { id: 3, title: 'Angular' }
]

function arrayUnique(arr, on)
{
  const ret = {}

  arr.forEach(item => {
    if ( !ret[item[on]] ){
      ret[item[on]] = item
    }
  })

  return Object.values(ret)
}
```


## 数组的排序

```javascript
const d = [2, 10, 1, -1]
const arr = [
  { id: 1, title: 'Learn Vue' }, 
  { id: 3, title: 'Learn React' }, 
  { id: 2, title: 'Learn Angular' }, 
]

d.sort(buildSorter())
arr.sort(buildSorter('id')) // 升序
// arr.sort(buildSorter('id'， 'DESC')) // 降序

console.log(d)
console.log(arr)

// 输出结果：

[-1, 1, 2, 10]

[
  { id: 1, title: 'Learn Vue' }, 
  { id: 2, title: 'Learn Angular' }, 
  { id: 3, title: 'Learn React' }, 
]

function buildSorter(on, SORT = 'ASC')
{
  SORT = SORT.toUpperCase()

  const sign = SORT === 'ASC' ? 1 : -1

  if ( on ) {
    return (a, b) => sign * (a[on] - b[on])
  }else {
    return (a, b) => sign * (a - b)
  }
}

```

## 数组列(column)

```javascript
const todos = [
  { id: 1, title: 'Vue' },
  { id: 2, title: 'React' },
  { id: 3, title: 'Angular' },
]

console.log(arrayColumn(todos, 'id'))

// 输出结果:
[1, 2, 3]

function arrayColumn(arr, on)
{
  return arr.map(item => item[on])
}
```


## 数组的交集, 差集, 并集, 补集

```javascript
const d1 = [1, 2, 3]
const d2 = [2, 3, 4]

console.log(arrayIntersect(d1, d2)) // [2, 3]
console.log(arrayMinus(d1, d2)) // [1]
console.log(arrayMinus(d2, d1)) // [4]
console.log(arrayUnion(d1, d2)) // [1, 2, 3, 4]
console.log(arrayComplement(d1, d2)) // [1, 4]

// 交集
function arrayIntersect(a, b)
{
  return a.filter(val => b.indexOf(val) !== -1)
}

// 差集
function arrayMinus(a, b)
{
  return a.filter(val => b.indexOf(val) === -1)
}

// 并集
function arrayUnion(a, b)
{
  return a.concat(arrayMinus(b, a))
}

// 补集
function arrayComplement(a, b)
{
  return arrayMinus(a, b).concat(arrayMinus(b, a))
}

```