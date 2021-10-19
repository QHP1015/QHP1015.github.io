---
title: JS数组
author: 从此在回忆里
mathjax: true
date: 2021-10-17 11:23:36
tags: 前端学习
categories: JavaScript
---

## 创建数组

- 数组字面量

- 对可迭代对象使用 `...` 扩展操作符

- `Array()` 构造函数

- 工厂方法 `Array.of()` 和 `Array.from()`

  ```js
  // 使用参数作为数组元素来创建并返回新数组
  Array.of(1,2,3)    // => [1,2,3]
  // 将类数组对象创建真正的数组副本
  let truearray = Array.from(arraylike)
  ```





## 迭代数组

通过 `for/of` 循环迭代，使用内置数组迭代器按照升序返回数组元素，对于稀疏数组，不存在的元素返回 `undefined`

```js
let letters = [..."Hello world"];
let string = "";
for(let letter of letters){
    string += letter;
}
string        // => "Hello world"
```

若通过 `for/of` 循环且想知道每个数组元素的索引，可以使用数组的 `entries()` 方法和解构赋值

```js
let everyother = "";
for(let [index, letter] of letters.entries()){
    if(index % 2 === 0) everyother += letter;  // 偶书索引的字母
}
everyother        // => "Hlowrd"
```



乘法表

```js
// 创建一个多维数组
let table = new Array(10);
for(let i = 0; i < table.length; i++){
    table[i] = new Array[10];
}

// 初始化数组
for(let row = 0; row < table.length; row++){
    for(let col = 0; col < table[row].length; col++){
        table[row][col] = row*col;
    }
}
```







## 数组方法

`Array` 类定义的方法

### 数组迭代器方法

这些方法都接收一个函数作为第一个参数，且对数组的每个元素（或某些元素）都调用一次这个函数，若是稀疏数组，不会对不存在的数组调用传入的这个函数

这些方法都不会修改调用它们的数组，但是，传入的参数可能会修改这个数组

- `forEach()`

  `forEach()` 在调用第一个参数的函数时会给还函数传入 3 个参数：数组元素的值、数组元素的索引和数组本身

  ```js
  let data = [1, 2, 3, 4, 5], sum = 0;
  // 计算数组元素和
  data.forEach(value => {sum += value; });
  
  // 递增每个元素的值
  data.forEach(function(v, i, a){
      a[i] = v + 1;
  })
  ```

  `forEach()` 不提供提前终止迭代的方式

- `map()`

  `map()` 把调用它的数组的每个元素分别传给指定的函数，返回这个函数的返回值构成的数组

  ```js
  let  a = [1 ,2,3 ];
  a.map(x => x*x)        // => [1, 4, 9]
  ```

  `map()` 返回一个新数组，若原数组是稀疏的，缺失元素不调用函数，但返回的数组会与原数组一样稀疏

- `filter()`

  `filter()` 返回一个包含调用此方法的数组的子数组，传给此方法的函数是断言函数

  ```js
  let a = [5, 4, 3, 2, 1];
  a.filter(x => x < 3)           // => [2, 1]
  a.filter((x,i) => i%2 === 0)   // => [5, 3, 1]
  ```

  `filter()` 会跳过稀疏数组中缺失的元素，返回的是稠密数组，可以利用此方法清理稀疏数组的空隙

  ```js
  let dense = sparse.filter(() => true);
  ```

  若即要清理稀疏数组的空隙，也要删除值为 `undefined` 和 `null` 的元素

  ```js
  a = a.filter(x => x !== undefined && x !== niull);
  ```

- `find()` 与 `findIndex()`

  `find()` 与 `findIndex()` 和 `filter()` 类似，不同在于，这两个方法在断言函数找到第一个元素时停止迭代，`find()`  返回匹配元素，`findIndex()` 返回匹配元素的索引，若没有找到匹配元素，`find()`  返回 `undefined`，`findIndex()` 返回 -1

- `every()` 与 `some()`

  `every()` 与 `some()` 是数组断言方法，`every()` 只在断言函数对数组的所有元素都为 `true` 才返回 `true`，`some()` 在断言函数对数组的一个元素为 `true` 返回 `true`

  ```js
  let a = [1, 2];
  a.every(x => x < 10)    // => true
  a.some(x => x%2 ===0)   // => true
  ```

  在空数组上调用，`every()` 返回 `true`，`some()` 返回 `false`

- `reduce()` 与 `reduceRight()`

  `reduce()` 与 `reduceRight()` 方法使用指定函数归并数组元素，最终产生一个值

  ```js
  let a = [5, 4, 3, 2, 1];
  a.reduce((x,y) => x+y, 0)             // => 15
  a.reduce((x,y) => x*y, 1)             // => 120
  a.reduce((x,y) => (x > y) ? x : y)    // => 5：最大值
  ```

  `reduce()` 接收两个参数

  - 执行归并的函数
  - 传给归并函数的初始值（可选）

  `reduceRight()` 与 `reduce()` 类似，只是从高索引引向低索引处理索引



### flat() 和 flatMap() 打平数组

ES2019 新定义方法，falt() 方法用于创建并返回一个新数组，返回元素是数组的会被打平填充到返回的数组中

```js
[1, [2, 3]].flat()     // => [1, 2, 3]
[1, [2, [3]]].flat()   // => [1, 2, [3]]
[1, [2, [3]]].flat(2)  // => [1, 2, 3]
```

`flatMap()` 方法与 `map()` 相似，但返回的数组会自动打平

```js
let phrases = ["hello world", "the definitive guide"];
let words = phrases.flatMap(phrase => phrase.split(" "));
words        // => ["hello", "world", "the", "definitive", "guide"];
```



### 栈和队列操作

- `push()`：在数组末尾添加一个或多个新元素，返回数组的新长度

  `push()` 不会打平传入的数组，若想打平，可以使用扩展操作符 `a.push(...values)`

- `pop()`：在数组末尾删除一个或多个新元素，返回删除的元素

- `shift()`：在数组开头删除一个或多个新元素，返回删除的元素

- `unshift()`：在数组开头添加一个或多个新元素，返回数组的新长度

`pop()` 与 `push()` 组合实现先进后出的栈；`shift()` 与 `push()` 组合实现先进先出的队列



### 数组操作

- `slice()` ：返回一个数组的切片或子数组，不会修改调用它的数组

  `slice()` 接收两个参数，分别用于指定要返回切片的起止位置

  ```js
  let a = [1, 2, 3, 4, 5];
  a.slice(0,2);        // 返回 [1,2,3]
  a.slice(3);          // 返回 [4,5]
  a.slice(1,-1);       // 返回 [2,3.4]
  a.slice(-3,-2);      // 返回 [3]
  ```

- `splice()` ：对数组进行插入和删除的通用方法，会修改调用它的数组，返回删除的子数组

  `splice()` 接收两个参数，第一个参数指定插入或删除的起点位置，第二个参数指定删除的元素个数，这两个参数后跟任意多个参数表示在第一个参数指定的位置插入到数组的元素

- `fill()` ：将数组的元素或切片设置为指定的值，会修改调用它的数组，返回修改后的数组

  ```js
  let a = new Array(5);
  a.fill(0)        // => [0,0,0,0,0]：用 0 填充数组
  a.fill(9,1)      // => [0,9,9,9,9]：从索引 1 开始填充 9
  a.fill(8,2,-1)   // => [0,9,8,8,9]：在索引 2、3 填充 8
  ```

- `copyWithin()` ：将数组切片复制到数组中的新位置，会修改调用它的数组，返回修改后的数组，不会改变数组长度

  `copyWithin()` 第一个参数指定要把第一个元素复制到的目的索引，第二个参数指定要复制的第一个元素的索引，第三个参数指定要复制的元素切片的终止索引

  ```js
  let a = [1, 2, 3, 4, 5];
  a.copyWithin(1)        // => [1,1,2,3,4]：把数组元素复制到索引 1 及之后
  a.copyWithin(2,3,5)    // => [1,1,3,4,4]：把最后两个元素复制到索引 2
  a.copyWithin(0,-2)     // => [4,4,3,4,4]
  ```



### 数组索引和排序

- `indexOf()` 和 `lastIndexOf()` ：从数组中搜索指定值并返回第一个找到的元素的索引，未找到返回 -1

  `indexOf()` 从前往后，`lastIndexOf()` 从后往前搜索

  ```js
  // 利用 indexOf() 的第二个参数找除第一个以外的匹配值
  function findall(a,x){
      let results = [],
          len = a.length,
          pos = 0;
      while(pos < len){
          pos = a.indexOf(x, pos);
          if(pos === -1) break;
          result.push(pos);
          pos = pos++;
      }
      return results;
  }
  ```

- `includes()` ：ES2016新增，接收一个参数，若数组包含该值返回 `true`，否则返回 `false`

- `sort()` ：对数组元素就地排序并返回排序后的数组，若数组包含未定义元素，排到数组末尾

  对数组元素执行非字母顺序的排序，给此方法传一个比较函数作为参数

  ```js
  let a = [33, 4, 1111, 222];
  a.sort();                // => [1111, 222, 33, 4]，字母顺序
  a.sort(function(a,b){    // 传入一个比较函数
      return a-b;
  });                      // => [4, 33, 222, 1111]，数值顺序
  a.sort((a,b) => b-a);    // => [1111, 222, 33, 4]，相反的数值顺序
  ```

  对字符串数组做不区分大小写的字母排序

  ```js
  let a = ["ant", "Bug", "cat", "Dog"];
  a.sort();               // => ["Bug", "Dog", "ant", "cat"]，区分大小写的排序
  a.sort(function(s,t){
      let a = s.toLowerCase();
      let b = t.toLowerCase();
      if(a < b) return -1;
      if(a > b) return 1;
      return 0;
  });                     // => ["ant", "Bug", "cat", "Dog"]
  ```

- `reverse()` ：反转数组元素的顺序



### 数组到字符串转换

- `join()` ：将数组的所有元素转换为字符串，拼接，可以指定一个参数，用于分隔字符串的元素

  ```
  let a = [1, 2, 3];
  a.join()           // => "1,2,3"
  a.join(" ")        // => "1 2 3"
  a.join("")         // => "123"
  let b = new Array(10);
  b.join("-")        // => "---------"
  ```

- `toString()` ：与无参数的 `join()` 方法一样

- `toLocaleString()` 



### 静态数组函数

- `Array.of()`
- `Array.from()`
- `Array.isArray()` ：用于确定一个未知值是不是数组







## 类数组对象

数组不同于对象的特殊特性

- 数组的 `length` 属性会在新元素假如时自动更新
- 设置 `length` 为更小的值会截断数组
- 数组从 `Array.prototype` 继承有用的方法
- `Array.isArray()` 对数组返回 `true`

当对象有一个数值属性 `length`，且有相应的非负整数属性，即为类数组对象

```js
let a = {};
// 添加属性使其成为类数组对象
let i = 0;
while(i < 10){
    a[i] = i * i;
    i++;
}
a.length = i;

// 像数组一样遍历这个对象
let total = 0;
for(let j =0; j < a.length; j++){
    total += a[j];
}
```

在客户端中，很多操作HTML文档的方法（如 `document.querySelectorAll()`）都返回类数组对象

```js
// 测试对象是不是类数组对象
function isArrayLike(o){
    if(o &&                           // o 不是 null、undefined 等假值
       typeof o === "object" &&       // o 是对象
       Number.isFinite(o.length) &&   // o.length 是有限数值
       o.length >= 0 &&               // o.length 是非负数值
       Number.isInteger(o.length) &&  // o.length 是整数
       o.length < 4294967295){        // o.length < 2^32 - 1
        return true;                  // o 是类数组对象
    }else{
        return false;
    }
}
```

类数组对象不继承 `Array.prototype` 可以使用 `Function.call()` 方法来调用数组方法

```js
let a = {"0": "a", "1": "b", length: 3};
Array.prototype.join.call(a, "+")    // => "a+b"
Array.from(a)                        // => ["a","b"]
```

