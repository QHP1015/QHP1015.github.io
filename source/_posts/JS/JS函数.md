---
title: JS函数
author: 从此在回忆里
mathjax: true
date: 2021-10-18 21:19:55
tags: 前端学习
categories: JavaScript
---

函数是 JavaScript 程序的一个基本组成部分

JavaScript 函数是参数化的，即函数定义可以包含一组标识符，称为参数或形参，函数调用会为这些形参提供值或实参

JavaScript 函数可以嵌套函数，内嵌的函数可以访问定义在函数作用域的任何变量。即 JavaScript 函数是闭包

<!--more-->

## 定义函数

定义函数有以下方法

- function 关键字
- 箭头函数



### 函数声明

函数声明由 function 关键字和函数体组成

```js
// 计算阶乘的递归函数
function factorial(x){
    if(x <= 1) return 1;
    return x * factorial(x-1);
}
```

函数声明语句会被“提升”到包含脚本、函数或代码块的顶部，因此调用代码可以出现在函数定义代码之前

在一个 JavaScript 代码块中声明的所有函数在该块的任何地方都有定义，且在 JavaScript 解释器开始执行该块的任何代码前被定义

在语句块中定义的函数只在该块中有定义，对块的外部不可见



### 函数表达式

函数表达式出现在复杂表达式或语句的上下文中，且函数名是可选的

```js
// 将该函数表达式赋值给一个变量
const square = function(x){ return x*x};

// 函数表达式可以包含名字，对递归有用
const f = function fact(x){ if (x <= 1) return 1; else return x*fact(x-1);};

// 函数表达式也可以用作其他函数的参数
[3,2,1].sort(function(a,b){ return a-b;});

// 函数表达式也可以定义完立即调用
let tensquared = (function(x){ return x*x;}(10));
```



### 箭头函数

在 ES6 中，箭头函数使用”箭头“分隔函数的参数和函数体

```js
const sum = (x, y) => { return x + y};

// 若函数体只有一个 return 语句，可以省略
const sum = (x, y) =>  x + y;
```

箭头函数从定义自己的环境继承 `this` 关键字的值，而不像其他函数那样定义自己的调用上下文、

箭头函数没有 `prototype` 属性，即箭头函数不能作为新类的构造函数







## 调用函数

构成函数体的 JavaScript 代码在调用函数的时候执行，JavaScript 函数可通过 5 种方式调用

- 作为函数
- 作为方法
- 作为构造函数
- 通过 call() 或 apply() 方法间接调用
- 通过 JavaScript 语言特性隐式调用（与常规书函数调用不同）



### 函数调用

对于常规函数调用，函数的返回值会变成调用表达式的值

- 若函数由于解释器到末尾返回，返回值为 `undefined`
- 若函数由于解释器执行到 `return` 语句返回，返回语句表达式值，若没有值返回 `undefined`

作为函数来调用的函数通常不会在定义中使用 `this` 关键字



### 方法调用

方法即对象的属性为函数时的函数

方法调用会调用上下文，属性访问表达式由两部分构成：对象和属性名。对象会成为调用上下文，函数体可以通过 `this` 引用这个对象

```js
let calculator = {
    operand1: 1,
    operand2: 2,
    add(){
        // 使用this关键字引用包含对象
        this.result = this.operand1 + this.operand2;
    }
};
calculator.add();
calculator.result;    // => 2
```

`this` 关键字不具有变量那样的作用域，除了箭头函数，嵌套函数不会继承包含函数的 `this` 值

- 嵌套方法被当作方法调用，`this` 值就是调用它的对象
- 嵌套方法（不是箭头函数）被当作函数调用，`this` 值是全局对象（非严格模式）或 `undefined`（严格模式）

```js
let o = {
    m:function(){
        let self = this;    // 将 this 值保存在变量中
        this === o;         // => true
        f();                // 调用嵌套函数
        
        function f(){
            this === o;     // => false：this 是全局对象或 undefined
            self === o;     // => true
        }
    }
};
```

在嵌套函数内，`this` 关键字不等于对象，解决技巧：

- 如上述代码将 `this` 值 赋给变量，在嵌套函数中，使用变量引用

- ES6 之后，使用箭头函数继承 `this` 值，函数表达式不会被提升，需要将定义放在对象方法调用嵌套函数代码前

  ```js
  const f = () => {
      this ===o;    // true
  };
  ```

- 调用嵌套函数的 `bind()` 函数，定义一个在指定对象上被隐式调用的新函数

  ```js
  const f = (function(){
      this === o    // true，因为将这个函数绑定到外部的 this
  }).bind(this);
  ```



### 构造函数调用

在函数或方法调用前加关键字 `new`，即为构造函数调用

构造函数调用会创建一个新的空对象，这个对象继承构造函数的 `prototype` 属性指定的对象



### 隐式函数调用

一些 JavaScript 语言特性看起来不像函数调用，但实际上会导致某些函数被调用

这些被隐式调用的函数，若产生 bug，副效应和性能问题都比常规函数更难排查

可能导致隐式函数调用的语言特性

- 若对象由获取方法或设置方法，查询或设置其属性值可能会调用这些方法
- 对象在字符串上下文中使用时，会调用对象的 `toString()` 方法，用于数值上下文时，会调用 `valueOf()` 方法
- 遍历可迭代对象的元素时，也会涉及一系列方法调用
- 标签模板字面量是一种伪装的函数调用
- 代理对象的行为完全由函数控制，其上的几乎任何操作都会导致一个函数被调用







## 函数实参与形参

JavaScript 函数定义不会指定函数形参的类型，函数调用也不对传入的实参进行任何类型检查

### 可选形参与默认值

调用函数时传入的实参少于声明的形参时，额外的形参会获得默认值，通常是 `undefined`

```js
// 将对象 o 的可枚举属性名放到数组中，返回数组
// 若不传数组，创建一个新数组
function getPropertyNames(o, a = []){
    for(let property in o) a.push(property);
    return a;
}
```

函数的形参默认值表达式在函数调用时求值，因此每次调用上述函数时若只传一个参数，都创建并传入一个新的空数组

若函数有多个形参，可以使用前面参数的值来定义后面参数的默认值

```js
const rectangle = (width, height = width*2)
rectangle(1)        // => {width: 1, height: 2}
```



### 剩余实参与可变长度实参列表

剩余形参能够编写在调用时传入比形参多任意数量的实参的函数

```js
function max(first=-Infinity, ...rest){
    let maxValue = first;
    // 遍历其他参数，寻找最大数值
    for(let n of rest){
        if(n > maxValue){
            maxValue = n;
        }
    }
    // 返回最大的数值
    return maxValue;
}

max(1, 10, 100, 2, 3, 1000, 4)    // => 1000
```

剩余形参前有三个点，且必须是函数声明中最后一个参数

在函数体内，剩余形参的值始终是数组，数组可能为空，但剩余形参不可能为 `undefined`



### 函数实参解构为形参

调用函数时若传入一个实参列表，则所有参数值都会被赋给函数定义时声明的形参

```js
function vectorAdd(v1, v2){
    return [v1[0] + v2[0], v1[1] + v2[1]];
}
vectorAdd([1,2], [3,4])    // => [4,6]
```

在解构赋值中可以为形参定义默认值

```js
// 用标量乘以向量
function vetorMultiply({x, y, z=0}, scalar){
    return { x: x*scalar, y: y*scalar, z: z*scalar};
}
vectorMultiply({x: 1, y: 2}, 2)    // => {x: 2, y: 4, z: 0}
```

可以通过把对象参数解构为函数参数模拟调用函数时使用 `name=value` 的形式指定实参

```js
function arraycopy({from,to=from,n=from.length,fromIndex=0,toIndex=0}){
    let valuesToCopy = from.slice(fromIndex, fromIndex + n);
    to.splice(toIndex, 0, ...valuesToCopy);
    return to;
}
let a = [1,2,3,4,5], b=[9,8,7,6,5];
arraycopy({from: a, n: 3, to: b, toIndex: 4})    // => [9, 8, 7, 6, 1, 2, 3, 5]
```

ES2018 中，解构对象也可以使用形参

```js
function vetorMultiply({x, y, z=0, ...props}, scalar){
    return { x: x*scalar, y: y*scalar, z: z*scalar, ...props};
}
vectorMultiply({x: 1, y: 2, w: -1}, 2)    // => {x: 2, y: 4, z: 0, w: -1}
```







## 函数作为值

在 JavaScript 中，函数不仅是语法，也是值，即可以把函数赋值给变量、保存为对象的属性或数组的元素、作为参数传给其他函数等操作

函数在 JavaScript 中不是原始值，是一种特殊的对象，即函数也可以有属性。当函数需要一个“静态”变量，且这个变量的值需要在函数每次调用时都能访问到，则通常把这个变量定义为函数自身的一个属性

```js
// 计算阶乘并把结果缓存到函数本身的属性中
function factorial(n){
    if(Number.isInteger && n > 0){              // 仅限于正整数
        if(!(n in factorial)){                  // 若没有缓存
            factorial[n] = n * factorial(n-1);  // 计算并缓存结果
        }
        return factorial[n];                    // 返回缓存结果
    }else{
        return NaN;
    }
}

factorial[1] = 1;         // 初始化缓存
factorial[6]              // => 720
factorial[5]              // => 120
```







## 函数作为命名空间

在函数体内声明的变量在函数外部不可见，可以利用函数作为临时命名空间

假设有一段 JavaScript 代码，想在多个程序或页面中调用，且这段代码定义了存储中间计算结果的变量，则无法知道多个程序创建的变量是否会跟这段代码冲突，利用函数作为命名空间可以解决

```js
function chunkNamespace(){
    // 要复用的代码
    // 在这里定义的任何变量都是函数的局部变量
    // 不会污染全局命名空间
}
chunkNamespace()    // 需要调用函数
```

也可以在一个表达式中定义并调用匿名函数（也称立即调用函数表达式）

```js
(function(){
   // 将 chunkNamespace() 函数重写为一个无名表达式
   // 要复用的代码
});    // 函数定义结束后立即调用
```







## 闭包

JavaScript 使用司法作用域，即函数执行时使用的是定义函数时生效的变量作用域，而不是调用函数时生效的变量作用域

闭包：将函数对象与作用域（即一组绑定）组合起来解析函数变量的机制



闭包在嵌套函数中最为常见，且一个私有变量可以由多个闭包共享

```js
function conter(){
    let n = 0;
    return{
        count: function(){ return n++; },
        reset: function(){ n=0; }
    };
}
let c = counter(), d = counter();    // 创建两个计数器
c.count()
d.count()
c.reset();          // reset() 和 count() 方法共享状态
c.count()           // =>0：因为重置了 c
```

上述代码中 `counter()` 函数返回一个“计数器”对象，有两个方法。每次调用 `counter()` 都会创建一个新作用域，即 c 和 d 的变量不一样，在一个计数器上调用 `count()` 或 `reset()` 不会影响另一个计数器



闭包技术也可以与属性获取方法和设置方法组合使用

```js
function counter(n){
    return{
        // 属性获取方法，返回递增后的私有计数器值
        get count() { return n++; },
        // 属性设置方法，不允许 n 的值减少
        set count(m) {
            if (m > n) n = m;
            else throw Error("count can only be set to a larger value");
        }
    };
}
```



基于闭包技术实现一个通用的共享私有状态的函数，使用闭包的私有属性访问器方法

```js
// 这个函数按照指定的名字为对象 o 添加属性访问器方法
// 方法命名为 get<name> 和 set<name>
// 若提供断言函数，则设置方法用它测试自己的函数
// 在存储前先验证，若断言返回 false，则设置方法抛出异常

// 操作的值没有保存在对象 o 上，保存在这个函数的一个局部变量中
// 获取方法和设置方法也在函数局部定义，即可以访问这个局部变量
// 即该变量对两个访问器方法是私有的，除了设置方法，没有
// 其他途径可以设置或修改这个变量的值
function addPrivateProperty(o, name, predicate){
    let value;    // 属性值
    
    // 获取方法简单地返回属性值
    o[`get${name}`] = function(){ return value; };
    
    // 设置方法保存值或在断言失败时抛出异常
    o[`set${name}`] = function(v){
        if(predicate && !predicate(v)){
            throw new TypeError(`set${name}: invalid value ${v}`);
        }else{
            value = v;
        }
    };
}

// 使用 addPrivateProperty() 方法
let o = {};

// 添加属性访问器方法，并确保只能设置字符串值
addPrivateProperty(o, "Name", x => typeof x === "string");

o.setName("Frank");        // 设置属性的值
o.getName();               // => "Frank"
o.setName(0);              // !TypeError：尝试设置一个错误类型的值
```



## 函数属性和方法

函数使用 `typeof` 操作符返回字符串 `function`，但函数实际上是一种特殊的对象，因此也有属性和方法，甚至构造函数

**`length` 属性**

- 函数有一个只读的 `length` 属性，表示函数的元数，即函数在参数列表中声明的形参个数
- 剩余形参不包含在 `length` 属性内



**`name` 属性**

- 函数有一个只读的 `name` 属性，表示定义函数时使用的名字
- 若函数是未命名的，表示在第一次创建此函数时赋给该函数的变量名或属性名
- 主要用于记录调试或排错消息



**`prototype` 属性**

- 除了箭头函数，所有函数都有一个 `prototype` 属性，这个属性引用原型对象的对象
- 每个函数都有原型对象
- 当函数被作为构造函数使用时，新创建的对象从这个原型对象继承属性



**`call()` 和 `apply()` 方法**

- `call()` 和 `apply()` 允许间接调用一个函数

- `call()` 和 `apply()` 的第一个参数为要在其上调用这个函数的对象，即函数的调用上下文，在函数体内会成为 `this` 关键字的值，箭头函数调用这两个方法会忽略第一个参数

  ```js
  // 函数 f() 对象 o
  f.call(o);
  f.apply(o);
  ```

- `call()` 的后续参数会传给被调用的函数，`apply()` 传给函数的参数需要以数组的形式提供

  ```js
  f.call(o, 1, 2);
  f.apply(o, [1,2]);
  ```

  

**`bind()` 方法**

- `bind()` 方法的主要目的是将函数绑定到对象

  ```js
  function f(y){ return this.x + y;}  // 需要绑定的函数
  let o = { x: 1};                    // 要绑定的对象
  let g = f.bind(o);                  // 调用g(x)会在o上调用f()
  g(2)                                // => 3
  let p = { x: 10, g};                // 作为这个对象的方法调用g()
  p.g(2)                              // => 3：g仍然绑定到o，而非p
  ```

- 调用 `bind()` 最常见的目的是让非箭头函数变得像箭头函数，箭头函数从定义的环境中继承 `this` 值，且不能被 `bind()` 覆盖

- `bind()` 方法也可以执行“部分应用”（柯里化），即在第一个参数之后传给 `bind()` 的参数也会随着 `this` 值一起被绑定

  ```js
  let sum = (x,y) => x + y;     
  let succ = sum.bind(null,1);
  succ(2)      // => 3：x绑定到1，2会传给参数y
  
  function f(y,z){ return this.x + y + z}
  let g = f.bind({x: 1}, 2);      // 绑定this和y
  g(3)         // => 6：this.x绑定到1，y绑定到2，z是3
  ```

  

**`Function()` 构造函数**

- `Function()` 构造函数可以接收任意多个字符串参数，其中最后一个参数是函数体的文本

- 与函数字面量一样，`Function()` 构造函数创建的也是匿名函数

- `Function()` 构造函数允许在运行时动态创建和编译 JavaScript 函数

- `Function()` 构造函数创建的函数不使用词法作用域，而是始终编译为如同顶级函数一样

  ```js
  let scope = "global";
  function constructFunction(){
      let scope = "local";
      return new Function("return scope");    // 不会捕获局部作用域
  }
  // 这行代码返回“global”，因为Function()构造函数返回的函数不使用局部作用域
  constructFunction()()          // => "global"
  ```







## 函数式编程

JavaScript 可以把函数作为对象来操作意味着可以在 JavaScript 中使用函数实编程技巧

### 使用函数处理数组

假设有一个数值数组，要求计算平均值和标准差，

- 使用非函数式编程风格

  ```js
  let data = [1,1,3,5,5];
  
  // 计算平均值
  let total = 0;
  for(let i = 0; i < data.length; i++) total += data[i];
  let mean = total/data.length;
  
  // 计算标准差，计算每个元素相对于平均值偏差的平方
  total = 0;
  for(let i = 0; i < data.length; i++){
      let deviation = data[i] - mean;
      total += deviation * deviation;
  }
  let stddev = Math.sqrt(total/(data.length-1));
  ```

- 使用函数式编程风格

  ```js
  // 先定义两个简单的函数
  const sum = (x,y) => x+y;
  const square = x => x*x;
  
  // 使用数组方法计算平均值和标准差
  let data = [1,1,3,5,5];
  let mean = data.reduce(sum)/data.length;    // mean == 3
  let deviations = data.map(x => x-mean);
  let stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length-1));
  stddec          // => 2
  ```

- 定义方法的函数版

  ```js
  // 定义 map() 和 reduce() 函数
  const map = function(a,...args) { return a.map(...args);};
  const reduce = function(a,...args) { return a.reduce(...args);};
  
  // 先定义两个简单的函数
  const sum = (x,y) => x+y;
  const square = x => x*x;
  
  // 使用数组方法计算平均值和标准差
  let data = [1,1,3,5,5];
  let mean = reduce(data,sum)/data.length;    
  let deviations = map(data, x => x-mean);
  let stddev = Math.sqrt(reduce(map(deviation,square),sum)/(data.length-1));
  stddec        
  ```



### 高阶函数

高阶函数即操作函数的函数，它接收一个或多个函数作为参数并返回一个新函数

以下的 `mapper()` 函数接收一个函数参数并返回一个新函数

```js
// 返回一个函数，这个函数接收一个数组并对每个元素应用f
// 返回每个返回值的数组
// 比较这个函数与之前的map() 函数
function mapper(f){
    return a => map(a, f);
}
const increment = x => x+1;
const incrementAll = mapper(increment);
incrementAll([1,2,3])    // => [2,3,4]
```

以下这个高阶函数接收两个函数，返回一个新函数

```js
// 返回一个计算f(g(...))的新函数
// 返回的函数h会把它接收的所有参数传给g，
// 再把g的返回值传给f，然后返回f的返回值
// f和g被调用时都使用与h被调用时相同的this值
function compose(f, g){
    return function(...args){
        // 这里对f使用call是因为只给它传一个值
        // 对g使用apply是因为正在传一个值的数组
        return f.call(this, g.apply(this, args));
    };
}

const sum = (x,y) => x+y;
const square = x => x*x;
compose(square, sum)(2,3)    // => 25,平方和
```



### 函数柯里化

函数f的 `bind()` 方法返回一个新函数，这个新函数在指定的上下文中以指定的参数调用f，此时 `bind()` 方法在左侧部分应用函数，即传给 `bind()` 的参数会放在原始函数的参数列表的开头

```js
// 传给这个函数的参数会传到左侧
function partialLeft(f, ...outerArgs){
    return function(...innerArgs){                  // 返回这个函数
        let args = [...outerArgs, ...innerArgs];    // 构建参数列表
        return f.apply(this, args);                 // 通过它调用f
    };
}

// 传给这个函数的参数会传到右侧
function partialRight(f, ...outerArgs){
    return function(...innerArgs){                  // 返回这个函数
        let args = [...innerArgs, ...outerArgs];    // 构建参数列表
        return f.apply(this, args);                 // 通过它调用f
    };
}

// 这个函数的参数列表作为一个模板，这个参数列表中的 undefined值
// 会被来自内部参数的值填充
function partial(f, ...outerArgs){
    return function(...innerArgs){   
        let args = [...outerArgs];    	// 外部参数模板的局部副本
        let innerIndex = 0;             // 下一个是哪个内部函数
        // 循环遍历 args，用内部参数填充 undefined 值
        for(let i = 0; i < args.length; i++){
            if(args[i] === undefined) args[i] = innerArgs[innerIndex++];
        }
        // 现在把剩余的内部参数加进去
        args.push(...innerArgs.slice(innerIndex));
        return f.apply(this, args);   
    };
}

// 下面是有3个参数的函数
const f = function(x,y,z) { return x * (y -z);};
// 以下3个部分应用的区别
partialLeft(f, 2)(3, 4)            // => -2：绑定第一个参数：2 * (3 - 4)
partialRight(f, 2)(3, 4)           // =>  6：绑定最后一个参数：3 * (4 - 2)
partial(f, undefined, 2)(3, 4)     // => -6：绑定中间的参数：3 * (2 - 4)
```

以上的部分应用函数允许在已经定义的函数基础上轻松定义其他函数，如

```js
const increment = partialLeft(sum, 1);
const cuberoot = partialRight(Math.pow, 1/3);
cuberoot(increment(26))            // => 3
```

也可以将部分应用的函数与其他高阶函数组合，如通过组合与部分应用定义 `not()` 函数

```js
const not = partialLeft(compose, x => !x);
const even = x => x % 2 === 0;
const odd = not(even);
const isNumber = not(isNaN);
odd(3) && isNumber(2)    // => true
```



### 函数记忆

在函数式编程中，缓存称为函数记忆

以下代码展示了高阶函数 `memoize()` 可以接收一个函数参数，然后返回这个函数的记忆版

```js
// 返回f的记忆版
// 只适用于f的参数都有完全不同的字符串表示的情况
function memoize(){
    const cache =new Map();    // cache保存在这个闭包中
    
    return function(...args){
        // 创建参数的字符串版，以用作缓存键
        let key = args.length + args.join("+");
        if(cache.has(key)){
            return cache.get(key);
        }else{
            let result = f.apply(this, args);
            cache.set(key, result);
            return result;
        }
    };
}
```

这个 `memoize()` 函数创建一个新对象作为缓存使用，并将这个对象赋值给一个局部变量，从而让其（在闭包中）成为被返回的函数的私有变量。返回的函数将其参数数组转换为字符串，并使用该字符串作为缓存对象的属性。若缓存存在某个值，直接返回该值；否则，调用指定函数计算这些参数值，然后缓存这个值，最后返回这个值，以为为使用上述函数实例

```js
// 使用欧几里得算法返回两个整数的最大公约数
function gcd(a,b){        // 省略了对a和b的类型检查
    if(a < b){            // 开始时保证a ≥ b
        [a, b] = [b, a];  
    }
    while(b !== 0){       // 欧几里得算法
        [a, b] = [b, a%b];
    }
    return a;
}

const gcdmemo = memoize(gcd);
gcdmemo(85, 187)          // => 17

// 在编写记忆的递归函数时，通常希望递归记忆版，而非原始版
const factorial = memoize(function(n){
    return (n <= 1) ? 1 : n * factorial(n-1);
});
factorial(5)              // => 120：也为4、3、2和1缓存了值
```

