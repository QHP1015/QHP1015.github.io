---
title: JS语句
author: 从此在回忆里
date: 2021-10-10 13:49:51
tags: 前端学习
categories: JavaScript
mathjax: true
---

JavaScript 程序即一系列待执行的语句

<!-- more-->

JavaScript语句语法

| 语句              | 用途                                                  |
| ----------------- | ----------------------------------------------------- |
| break             | 退出最内部循环、switch 或有名字的闭合语句             |
| case              | 在 switch 中标记一条语句                              |
| class             | 声明一个类                                            |
| const             | 声明并初始化一个或多个常量                            |
| continue          | 开始最内部循环或命名循环的下一次迭代                  |
| debugger          | 调试器断点                                            |
| default           | 在 switch 中标记默认语句                              |
| do/while          | 替代 while 循环的一种结构                             |
| export            | 声明可以被导入其他模块的值                            |
| for               | 一种方便好用的循环                                    |
| for/await         | 异步迭代异步迭代器的值                                |
| for/in            | 枚举对象的属性名                                      |
| for/of            | 枚举可迭代对象（如数组）的值                          |
| function          | 声明一个函数                                          |
| if/else           | 根据某个条件执行一个或另一个语句                      |
| import            | 为在其他模块中定义的值声明名字                        |
| label             | 标签名，为语句起名，以便与 break 和 continue 一起使用 |
| let               | 声明并初始化一个或多个块作用域的变量（新语法）        |
| return            | 从函数中返回一个值                                    |
| switch            | 包含 case 或 default 标签的多分支结构                 |
| throw             | 抛出一个异常                                          |
| try/catch/finally | 处理异常和代码清理                                    |
| ”use strict“      | 对脚本或函数应用严格模式                              |
| var               | 声明并初始化一个或多个变量（老语法）                  |
| while             | 一种基本的循环结构                                    |
| with              | 扩展作用域链（在严格模式下被废弃并禁止）              |
| yield             | 提供一个被迭代的；只用在生成器函数中                  |



## 条件语句

条件语句根据指定表达式的值执行或跳过执行某些语句

**`if` 语句格式**

```js
if (expression)
    statement1
else
    statement2
```



**`else if` 语句格式**

```js
if (expression1){
    statement1
} else if(expression2){
    statement2
} else{
    statement3
}
```



**`switch` 语句格式**

```js
switch(expression){
    case value1 :
        statement1
        break;
    case value2 :
        statement2
        break;
    default :
        statement3
        break;
}
```

`switch` 语句实例：根据值得类型决定怎么转换字符串

```js
function convert(x){
    switch(typeof x){
        case "number":
            return x.toString(16);
        case "string":
            return '"'+ x + '"';
        default:
            return String(x);
    }
}
```





 

## 循环语句

循环语句可以重复执行代码中得某些部分

**`while` 语句格式**

```js
while (expression)
    statement
```



**`do/while` 语句格式**

```js
do
    statement
while (expression)
```

`do/while` 保证循环体至少执行一次，`do/while` 语句实例

```js
function printArray(a){
    let len = a.length, i = 0;
    if(len === 0){
        console.log("Empty Array");
    } else{
        do{
            console.log(a[i]);
        }while(++i < len)
    }
}
```



**`for` 语句格式**

```js
for(initialize ; test ; increment){
    statement
}
```

- `initialize`：初始化
- `test`：测试
- `increment`：递增循环变量

`for` 语句实例：遍历链表，返回列表最后一个对象

```js
function tail(o){
    for(; o.next; o = o.next);   // 空循环体，o.next 为真值时遍历
    return o;
}
```



**`for/of` 语句**

ES6 新增循环语句，专门用于可迭代对象

- `for/of` 与对象

  对象（默认）不可迭代，运行 `for/of` 语句会抛出 `TypeError`，若想迭代对象属性，可以使用 `for/in` 循环，或者基于 `Object.keys()` 方法

  > `Object.keys()` 返回一个对象属性名得数组

  ```js
  let o = {x: 1, y: 2, z: 3};
  let keys = "";
  fot(let k of Object.keys(o)){
      keys += k;
  }
  keys    // => "xyz"
  ```

  若不考虑对象的键，可以调用`Object.values()` 方法迭代每个键对应得值

  ```js
  let sum = 0;
  for(let v of Object.values(o)){
      sum += v;
  }
  sum     // => 6
  ```

  若及想要对象属性的键，也想要属性的值，可以基于`Object.entries()` 方法和解构赋值得

  > `Object.entries()` 返回一个数组的数组，每个内部数组表示对象的一个属性键值对

  ```js
  let pairs = "";
  for(let [k,v] of Object.entries(o)){
      pairs += k + v;
  }
  pairs    // => "x1y2z3"
  ```

- `for/of` 与字符串

  字符串在 ES6 中是可以逐个字符迭代的

  ```js
  let frequency = {};
  for(let letter of "mississippi"){
      if(frequency[letter]){
          frequency[letter]++;
      } else{
          frequency[letter] = 1;
      }
  }
  frequency    // => {m: 1, i: 4, s: 4, p: 2}
  ```

  字符串迭代是按照 `Unicode` 码点而不是 `UTF-16` 字符迭代

- `for/of` 与 `Set` 和 `Map`

  ES6 内置的  `Set`（集合） 和 `Map`（映射）类是可迭代的

  迭代 `Set` 时，循环体对集合中的每个元素都运行一次

  ```js
  let text = "Na na na na na na na na Batman!";
  let wordSet = new Set(text.split(" "));
  let unique = [];
  for(let word of wordSet){
      unique.push(word);
  }
  unique    // => ["Na", "na", "Batman!"]
  ```

   `Map` 对象迭代器迭代的是键值对，每次迭代，迭代器返回一个数组，其第一个元素是键，第一个元素是对应的值

  ```js
  let m = new Map([[1, "one"]]);
  for(let [key, value] of m){
      key        // => 1
      value      // => "one"
  }
  ```

- `for/await` 与异步迭代

  ES2018 新增异步迭代器和使用异步迭代器的 `for/await` 循环

  ```js
  // 从异步可迭代流中读取数据块并将其打印出来
  async function printStream(stream){
      for await (let chunk of stream){
          console.log(chunk);
      }
  }
  ```



**`for/in` 语句格式**

```js
for(variable in object)
    statement
```

- `variable`：通常是一个变量名，也可以是左值
- `object`：求值为对象的表达式，若求值为 `null` 或 `undefined` 解释器跳过循环
- `statement`：语句块

`for/in` 循环的 `in` 后面可以是任意对象，`for/in` 语句实例：把一个对象的所有属性复制到数组中

```js
let o = {x: 1, y: 2, z: 3};
let a = [], i = 0;
for(a[i++] in o)
a       // [ 'x', 'y', 'z' ]
```

`for/in` 循环不会枚举对象的所有属性，如名字为符号的属性







## 跳转语句

跳转语句会导致 JavaScript 解释器跳转到源代码的新位置

**语义标签格式**

```js
identifier: statement
```

通常前置一个标识符和一个冒号，为语句添加标签，语义标签实例

```js
mainloop: while(token !== null){
    // 省略的代码
    continue mainloop;    // 跳到命名循环的下一次迭代
    // 省略的其他代码
}
```

`identifier` 可以是任何合法的 JavaScript 标识符（非保留字）

这些标签与变量和函数不在同一命名空间，则同一标识符既可以作为标签，也可以作为变量名或函数名

若两条语句有嵌套关系，不能使用相同的标签

任何语句都可以有多个标签



**`break` 语句**

`break` 语句用于退出某个语句块，如包含它的循环或 `switch` 语句立即退出

JavaScript 允许`break` 关键字后面跟一个语句标签 `break labelname;`，当后面跟标签后，跳转到指定标签的包含语句的末尾或终止该语句

`break` 语句示例

```js
let matrix = getData();    // 从某个地方取得一个数值的二维数组
// 计算矩阵中所有数值之和
let sum = 0, success = false;
// 从一个加标签的语句开始，出错就中断
computeSum: if(matrix){
    for(let x = 0; x < matrix.length; x++){
        let row = matrix[x];
        if(!row) break computeSum;
        for(let y = 0; y < row.length; y++){
            let cell = row[y];
            if(isNaN(cell)) break computeSum;
            sum += cell;
        }
    }
    success = true;
}
// break 语句跳转到这，若此时 success == false
// 表明得到的 matrix 有问题
// 否则，sum 包含矩阵所有元素之和
```



**`continue` 语句**

`continue` 语句不会退出循环，而是从头开始执行循环的下一次迭代

JavaScript 允许`continue` 关键字后面跟一个语句标签 `continue labelname;`，无论带不带标签，`continue` 语句只能在循环体内使用



**`return` 语句**

`return` 语句指定函数调用的返回值



**`yield` 语句**

`yield` 语句类似于 `return` 语句，但只用在 ES6 新增的生成器函数中，以回送生成的值序列中的下一个值，同时又不会真正返回

```js
// 回送一系列整数的生成器函数
function* range(from, to){
    for(let i = from; i <= to; i++){
        yield i;
    }
}
```



**`throw` 语句格式**

```js
throw expression;
```

- `expression`：可以为任何类型的值，可以抛出一个表示错误码的数值或者包含可读的错误消息的字符串

异常是一种信号，表示发生了某种意外情形或错误

抛出（throw）异常是为了表明发生了这种错误或意外情形

捕获（catch）异常则是要处理它，采取必要或对应的措施以从异常中恢复

JavaScript 解释器在抛出错误时会使用 `Error` 类及其子类，`Error` 对象有一个 `name` 属性和 `message` 属性，分别用于指定错误类型和保存传入构造函数的字符串

`throw` 语句示例：收到无效参数时抛出一个 `Error` 对象

```js
function factorial(x){
    // 如果收到的参数无效，则抛出异常
    if(x < 0) throw new Error("x must not be negatice");
    // 否则，计算一个值并正常返回
    let f;
    for(f = 1; x > 1; f *= x, x--)  /* 空语句 */;
    return f;
}
factorial(4);    // => 24
```



**`try/catch/finally` 语句格式**

```js
try{
    // 正常情况下，这里的代码顺序执行
    // 遇到异常，通过 throw 抛出异常
    // 或由于调用一个抛出异常的方法而抛出
}
catch(e){
    // 当且仅当 try 块抛出异常时，才会执行catch语句
    // 可以使用局部变量 e 引用被抛出的 Error 对象
    // 也可以忽略异常
    // 还可以通过 throw 重新抛出异常
}
finally{
    // 无论 try 块中发生什么，这里的代码都会执行
    // 无论 try 块是否终止，这些语句都会执行
    //    1）正常情况下，在到达 try 块底部时执行
    //    2）由于 break、continue 或 return 语句而执行
    //    3）由于上面的 catch 子句处理了异常而执行
    //    4）由于异常未被处理而继续传播而执行
}
```

`try/catch/finally` 语句是 JavaScript 的异常处理机制

`catch` 关键字跟着个标识符 e。这个标识符类似函数的参数。捕获异常时，与异常关联的值（如 `Error` 对象）会被赋给这个参数







## 其他语句

**`debugger` 语句**

使用浏览器并且打开开发者控制台，可以使用`debugger` 语句设置断点

```js
function f(o){
    if(o === undefined) debugger;    // 仅为调试添加
    ...
}
```

如上述例子，调用 `f()` 而不传参数，执行会停止，使用调试器检查调用栈，可以找到这个错误的调用来源



**`use strict` 语句**

`use strict` 语句开启严格模式

严格模式与非严格模式的区别

- 严格模式下不允许使用 `with` 语句
- 在严格模式下，所有变量必须声明，若不声明会抛出 `ReferenceError` （非严格模式下，给全局对象的属性赋值会隐式声明一个全局变量，即给全局对象添加一个新属性）
- 在严格模式下，函数若作为函数（非方法）被调用，其 `this` 值为 `undefined`（非严格模式下，作为函数调用的函数始终以全局对象作为 `this` 的值），并且，若函数通过 `cell()` 或 `apply()` 调用，则 `this` 值就是作为第一个参数传给 `cell()` 或 `apply()` 的值（非严格模式下，`null` 或 `undefined` 值会被替换为全局对象，而非对象值会被转换为对象）
- 在严格模式下，给不可写的属性赋值或尝试在不可扩展的对象上创建新属性会抛出 `TypeError`（非严格模式下，静默失败）
- 在严格模式下，传给 `eval()` 的代码不能再调用者的作用域声明变量或定义函数，会存在于一个为 `eval()` 创建的新作用域，这个作用域在 `eval()` 返回时销毁
- 在严格模式下，函数中的 `Arguments()` 对象保存着以跟传给函数的值的静态副本
- 在严格模式下，若 `delete` 后跟变量、函数或函数参数，会导致抛出 `SyntaxError`（非严格模式下，返回 `false`）
- 在严格模式下，尝试删除一个不可配置的属性会导致抛出 `TypeError`
- 在严格模式下，对象字面量定义两个或多个同名属性是语法错误
- 在严格模式下，函数声明中有两个或多个同名参数是语法错误
- 在严格模式下，不允许使用八进制整数字面量
- 在严格模式下，标识符 `eval` 和 `arguments` 被当作关键字，不允许修改
- 在严格模式下，检查调用栈的能力受限
