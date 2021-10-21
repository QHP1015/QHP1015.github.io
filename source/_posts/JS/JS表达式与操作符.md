---
title: JS表达式与操作符
author: 从此在回忆里
date: 2021-10-10 08:37:28
tags: 前端学习
categories: JavaScript
mathjax: true
---

## 属性访问

属性访问表达式求值为对象属性或数组元素的值，JavaScript 定义了两种访问属性的语法

- `expression.identifier`

  > 表达式后跟一个句点和一个标识符，表达式指定对象，标识符指定属性名

- `expression [expression]`

  > 表达式指定属性名或数组元素的索引

<!--more-->

```js
let o = {x: 1, y: {z: 3}};            // 示例对象
let a = [o, 4, [5, 6]];               // 包含前面对象的示例数组
o.x                                   // => 1
o.y.z                                 // => 3
o["x"]                                // => 1
a[1]                                  // => 4
a[2]["1"]                             // => 6
a[0].x                                // => 1
```

若求值结果为 `null` 或 `undefined`，则表达式会抛出 `TypeError`

ES2020 新增条件式属性访问

- `expression?.identifier`
- `expression?.[expression]`

使用条件式属性访问可以防止因结果为 `null` 或 `undefined` 而引起的 `TypeError`

若 `?.` 左侧的子表达式求值为 `null` 或 `undefined` ，整个表达式立即求值为 `undefined` ，不再进一步尝试访问属性

```js
let a;                         // 未初始化
let index = 0;
try{
    a[index++];                // 抛出 TypeError
} catch(e){
    index                      // 抛出 TypeError 之前发生了递增
}
a?.[index++]                   // => undefined
index                          // => 1：?.[] 短路所以没有发生递增
a[index++]                     // !TypeError：不能索引 undefined
```







## 操作符

JavaScript 操作符

| 操作符         | 操作                         | 结合性 | 操作数 | 类型               | 优先级 |
| -------------- | ---------------------------- | ------ | ------ | ------------------ | ------ |
| ++             | 先或后递增                   | 右     | 1      | lval→num           | 1      |
| --             | 先或后递减                   | 右     | 1      | lval→num           | 1      |
| -              | 负值                         | 右     | 1      | num→num            | 1      |
| +              | 转换未数值                   | 右     | 1      | any→num            | 1      |
| ~              | 反转二进制位                 | 右     | 1      | int→int            | 1      |
| !              | 反转布尔值                   | 右     | 1      | bool→bool          | 1      |
| delete         | 删除属性                     | 右     | 1      | lval→bool          | 1      |
| typeof         | 确定操作数类型               | 右     | 1      | any→str            | 1      |
| void           | 返回 undefined               | 右     | 1      | any→undef          | 1      |
| **             | 幂                           | 右     | 2      | num，num→num       | 2      |
| *、/、%        | 乘、除、取余                 | 左     | 2      | num，num→num       | 3      |
| +、-           | 加、减                       | 左     | 2      | num，num→num       | 4      |
| +              | 拼接字符串                   | 左     | 2      | str，str→str       | 4      |
| <<             | 左移位                       | 左     | 2      | int，int→int       | 5      |
| >>             | 右移位以符号填充             | 左     | 2      | int，int→int       | 5      |
| >>>            | 右移位以零填充               | 左     | 2      | int，int→int       | 5      |
| <、<=、>、>=   | 按数值顺序比较               | 左     | 2      | num，num→bool      | 6      |
| <、<=、>、>=   | 按字母表顺序比较             | 左     | 2      | str，str→bool      | 6      |
| instanceof     | 测试对象类                   | 左     | 2      | obj，func→bool     | 6      |
| in             | 测试属性是否存在             | 左     | 2      | any，obj→bool      | 6      |
| ==             | 非严格相等测试               | 左     | 2      | any，any→bool      | 7      |
| !=             | 非严格不相等测试             | 左     | 2      | any，any→bool      | 7      |
| ===            | 严格相等测试                 | 左     | 2      | any，any→bool      | 7      |
| !==            | 严格不相等测试               | 左     | 2      | any，any→bool      | 8      |
| &              | 计算按位与                   | 左     | 2      | int，int→int       | 9      |
| ^              | 计算按位异或                 | 左     | 2      | int，int→int       | 10     |
| \|             | 计算按位或                   | 左     | 2      | int，int→int       | 11     |
| &&             | 计算逻辑与                   | 左     | 2      | any，any→any       | 12     |
| \|\|           | 计算逻辑或                   | 左     | 2      | any，any→any       | 13     |
| ? ?            | 选择第一个有定义的操作数     | 左     | 2      | any，any→any       | 14     |
| ? :            | 选择第二或第三个操作数       | 右     | 3      | bool，any，any→any | 15     |
| =              | 为变量或属性赋值             | 右     | 2      | lval，any→any      | 16     |
| **=、/=、%= …… | 操作并赋值                   | 右     | 2      | lval，any→any      | 16     |
| ,              | 丢弃第一个操作数，返回第二个 | 左     | 2      | any，any→any       | 17     |

- 结合性：规定相同优先级操作的执行顺序

- 操作数：操作符期待的操作数个数

- 类型：操作数期待的类型 → 结果类型

  > lval：即 lvalue（左值），包括变量、对象属性和数组元素



### 算数表达式

基本的算术操作符：**（幂）、\*（乘）、/（除）、%（模）、+（加）和 -（减）

基本操作符会对操作数转换为数值进行求值，无法转换的转换为 `NaN`，操作结果为 `NaN`

在 JavaScript 中，所有数值都是浮点数，如 `5/2 = 2.5`

#### + 操作符

二元 + 操作符用于计算数值操作数的和或拼接字符串

严格讲，+ 操作符遵循以下行为

- 若一个操作数为对象，则+ 操作符将对象转换为原始值。Date 对象用 `toString()` 方法转换，其他对象通过 `valueOf()` 转换（若此方法返回原始值），无此方法通过 `toString()` 方法转换
- 完成对象转换到原始值，若操作数有字符串，另一个操作数也转成字符串拼接
- 否则，两个操作数转超数值（或 `NaN`），计算加法



#### 一元算术操作符

- 一元加（+）
- 一元减（-）
- 递增（++）
- 递减（--）



#### 位操作符

位操作符将数值当成 32 位整数而不是 64 位浮点数，转换时会丢弃小数部分和 32 位以外的部分

`NaN`、`Infinity`、`-Infinity` 作为位操作符的操作数转换为 0

除了 >>> 的操作符都可用于 `BigInt` 操作数

- 按位与（&）

- 按位或（|）

- 按位异或（^）

- 按位非（~）

  > 因为 JavaScript表示用符号整数的方式，对一个值应用 ~ 操作符等于修改符号并减 1 
  >
  > 如：~0x0f 求值为 0xFFFFFFF0，即 -16

- 左移（<<）

- 有符号右移（>>）

  > 若第一个操作数是正值，结果的高位填充 0
  >
  > 若第一个操作数是负值，结果的高位填充 1
  >
  > 右移 n 位等于这个值除以 $2^n$（丢弃余数）

- 零填充右移（>>>）

  > 无论第一个操作数的符号是什么，始终填充 0 





### 关系表达式

关系操作符测试两个值之间的关系，并依据相应关系是否存在返回 `true` 或 `false`

#### 相等和不相等操作符

严格相等（===）

- 若两个值类型不同，不相等
- 两个值都是 `null` 或 `undefined`，相等
- 两个值都是 `true` 或 `false`，相等
- 一个值或两个值是 `NaN`，不相等
- 两个值都是数值且值相同，相等
- 两个值引用同一个对象、数组或函数，相等

基于类型转换的相等（==）

- 两个值类型相同，按严格相等测试
- 两个值类型不同
  - 一个值为 `null`，另一个值为 `undefined`，相等
  - 一个为数值，一个为字符串，将字符串转为数值，比较
  - 一个值为 `true`，转换为 1，比较；一个值为 `false`，转换为 0，比较
  - 一个值为对象，另一个值为数值或字符串，用对象转原始值，比较



#### in 操作符

in 操作符期待左侧操作数是字符串、符号或可以转换为字符串的值，右侧操作数是对象

若左侧的值是右侧的对象的属性名，则 in 返回 `true`

```js
let point = {x: 1, y: 1};
"x" in point                    // => true
"z" in point                    // => false
"toString" in point             // => true：对象继承了 toString方法

let data = [7,8,9];
"0" in data                     // => true：数组有元素 0
1 in data                       // => true
2 in data                       // => false：没有元素 3
```



#### instanceof 操作符

instanceof 操作符期待左侧操作数是对象，右侧操作数是对象类的标识

左侧对象是右侧类的实例时返回 `true`

```js
let d = new Date();      // 通过 Date() 构造函数创建一个对象
d instanceof Date        // => true：d 是通过 Date() 创建的
d instanceof Object      // => true：所有对象都是 Object 的示例
d instanceof Number      // => false：d 不是 Number 对象
let a = [1,2,3];         // 通过数组字面量语法创建一个数组
a instanceof Array       // => true：a 是个数组
a instanceof Object      // => true：所有数组都是对象
a instanceof RegExp      // => false：a 不是正则表达式
```

若左侧操作数不是对象，返回 `false`

若右侧操作数不是对象的类，抛出 `TypeError`
