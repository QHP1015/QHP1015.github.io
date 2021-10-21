---
title: JS对象
author: 从此在回忆里
mathjax: true
date: 2021-10-11 20:24:24
tags: 前端学习
categories: JavaScript
---

## 对象

JavaScript 中，任何不是字符串、数值、符号、布尔值、`null`、`undefined` 的值都是对象

JavaScript 的主要特性是“**原型式继承**”，JavaScript 使用术语“自由属性”指代非继承属性

<!--more-->

对象属性除了名字和值之外，还有三个属性特性

- `writable`（可写）特性指定是否可以设置属性的值
- `enumerate`（可枚举）特性指定是否可以在 `for/in` 循环中返回属性的名字
- `configurable`（可配置）特性指定是否可以删除属性，以及是否可修改其特性

默认情况下，创建的对象的所有属性都是可写、可枚举和可配置的

很多 JavaScript 内置对象拥有只读、不可枚举或不可配置的属性



## 创建对象

#### **对象字面量**

```js
let empty = {};                             // 没有属性的对象
let point = {x: 0, y: 0};                   // 包含两个数值属性
let p2 = {x: point.x, y: point.y+1};        // 值比较复杂
let book = {
    "main title": "JavaScript",             // 属性名包含空格
    "sub-title": "The Definitive Guide",    // 和连字符，使用字符串字面量
    for: "all audiences",                   // for 是保留字，不用引号
    author:{                                // 对象
        firstname: "David"，
        surname: "Flanagan",
    }
};
```

#### **`new` 关键字**

`new` 操作符用于创建和初始化一个新对象

`new` 关键字后必须跟一个函数调用，这种方式使用的函数被称为构造函数（constructor）

```js
// JavaScript 内置类型构造函数
let o = new Object();      // 创建一个空对象，与 {} 相同
let a = new Array();       // 创建一个空数组，与 [] 相同
let d = new Date();        // 创建一个表示当前时间的日期对象
let r = new Map();         // 创建一个映射对象，用于存储键/值映射
```

#### **`Object.create()` 函数**

`Object.create()` 用于创建一个对象，使用其第一个参数作为新对象的原型

```js
let o1 = Object.create({x: 1, y: 2});    // o1 继承属性 x 和 y
o1.x + o1.y                              // => 3
```

传入 `null` 可以创建一个没有原型的新对象，不继承任何属性或方法

```js
let o2 = Object.create(null);
```

创建普通的空对象

```js
let o3 = Object.create(Object.prototype);    // o3与 {} 或 new Object{} 类似
```







## 查询对象

查询不存在的属性不会报错，返回 `undefined`

查询不存在对象的属性报错





## 删除属性

`delete` 操作符用于从对象中移出属性

`delete` 操作符只删除自有属性，不删除继承属性（要删除继承属性，需从定义属性的原型对象上删除）





## 测试属性

测试对象属性成员关系，即检查对象是否有一个给定名字的属性

- `in` 操作符

  ```js
  let o = {x: 1};
  "x" in o       // => true
  ```

- `hasOwnProperty()` 方法

  ```js
  let o = {x: 1};
  o.hasOwnProperty("x")             // => true
  o.hasOwnProperty("toString")      // => false
  ```

  此方法对继承的属性返回 `false`

- `propertyIsEnumerable()` 方法

  ```js
  let o = {x: 1};
  o.propertyIsEnumerable("x")                          // => true
  o.propertyIsEnumerable("toString")                   // => false
  Object.prototype.propertyIsEnumerable("toString")    // => false
  ```

  传入的命名属性时自有属性且这个属性可枚举，返回 `true`







## 枚举属性

- 用 `for/in` 循环返回可枚举的自由或继承属性
- 先获取对象所有属性名的数组，再通过 `for/of` 循环遍历该数组
- `Object.keys()` ：返回对象可枚举自有属性名的数组，不包含不可枚举属性、继承属性或名字是符号的属性
- `Object.getOwnPropertyNames()` ：返回自有属性名的数组，只要其名字为字符串
- `Object.getOwnPropertySymbols()` ：返回名字是符号的自由属性，无论是否可枚举
- `Reflect.ownKeys()` ：返回所有属性名



