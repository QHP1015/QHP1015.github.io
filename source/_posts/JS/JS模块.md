---
title: JS模块
author: 从此在回忆里
mathjax: true
date: 2021-10-25 00:00:42
tags: 前端学习
categories: JavaScript
---

模块化编程的目标是能够使用不同作者和来源的代码模块组装成大型程序

实践中，模块化的作用主要体现在封装和隐藏私有实现细节，以及保证全局命名空间清结上，因而模块之间不会意外修改各自定义的变量、函数和类

<!--more-->

## 基于类、对象的模块

类有一个重要特性，即类充当自己方法的模块。

不相关的类的方法都被定义为独立原型对象的属性，因此这些方法相互独立

在函数中声明的局部变量和嵌套函数是函数私有的，即可以使用立即调用的函数表达式来实现某种模块化

```js
// 一个小型统计模块，暴露mean()和stddev()函数，隐藏实现细节
const stats = (function(){
    // 模块私有的辅助函数
    const sum = (x, y) => x + y;
    const square = x => x * x;
    
    // 要导出的两个公有函数
    function mean(data){
        return data.reduce(sum)/data.length;
    }
    
    function stddev(data){
        let m = mean(data);
        return Math.sqrt(
        	data.map(x => x - m).map(square).reduce(sum)/(data.length-1);
        );
    }
    
    // 将公有函数作为一个对象的属性导出
    return {mean, stddev};
}());

stats.mean([1, 3, 5, 7, 9])      // => 5
stats.stddev([1, 3, 5, 7, 9])    // => Math.sqrt(10)
```







## 基于闭包的自动化模块

若存在一个工具，能解析代码文件，将每个文件的内容包装在一个立即调用的函数表达式中，还可以跟踪每个函数的返回值，并将所有内容拼接为一个大文件，即针对浏览器的代码打包工具（如 Webpack 和 Parcel）

```js
const modules = {};
function require(moduleName) { return modules[moduleName]; }

modules["sets.js"] = (function(){
    const exports = {};
    
    // sets.js文件的内容在这里
    exports.BitSet = class BitSet { ... };
    
    return exports;
}());

modules["stats.js"] = (function(){
    const exports = {};
    
    // stats.js文件的内容在这里
    const sum = (x, y) => x + y;
    const square = x => x * x;
    exports.mean = function(data) { ... };
    exports.stddev = function(data) { ... };
                                     
    return exports;
}());
```

将所有模块打包到类似上面的单个文件之中，即可如下操作

```js
// 取得对所需模块（或模块内容）的引用
const stats = require("stats.js");
const BitSet = require("sets.js");

// 使用这些模块
let s = new BitSet(100);
s.insert(10);
s.insert(20);
s.insert(30);
let average = stats.mean([...s]);
```







## Node中的模块

编写 Node 程序时，可以随意将程序拆分到任意多个文件中

在 Node 中，每个文件都是一个拥有私有命名空间的独立模块，在一个文件中定义的常量、变量、函数和类对该文件是私有的，除非文件导出它们，被该模块导出的值只有被另一个模块显式导入后才会在此模块可见

Node 使用 `require()` 函数导入其他模块，通过设置 `Exports` 对象的属性或完全替换 `module.exports` 对象来导出公共 API

`module.exports` 的默认值与 `exports` 引用同一个对象



### Node 的导出

Node定义一个全局 exports 对象，这个对象始终有定义

```js
// 若要导出多个值的 Node 模块，可以直接将这些值设为此对象的属性
const sum = (x, y) => x + y;
const square = x => x * x;

exports.mean = data => data.reduce(sum)/data.length;
exports.stddev = function(data){
    let m = exports.mean(d);
    return Math.sqrt(
        	data.map(x => x - m).map(square).reduce(sum)/(data.length-1);
        );
};
```

```js
// 让模块导出一个函数或类,直接将想导出的值赋给module.exports
module.exports = class BitSet extends AbstractWritableSet{
    // .......
}
```

```js
// 还可以在模块末尾导出一个对象

// 定义所有公有函数和私有函数
const sum = (x, y) => x + y;
const square = x => x * x;
const mean = data => data.reduce(sum)/data.length;
const stddev = function(data){
    let m = exports.mean(d);
    return Math.sqrt(
        	data.map(x => x - m).map(square).reduce(sum)/(data.length-1);
        );
};

// 最后只导出公有函数
module.exports = {mean, stddev};
```



### Node 的导入

Node 模块通过调用 `require()` 函数导入其他模块，这个函数的参数时要导入模块的名字，返回值是该模块导出的值（通常是一个函数、类或对象）

若要导入 Node 内置的系统模块或通过包管理器安装在系统上的模块，可以使用模块的非限定名（不带会被解析为文本系统路径的”/“字符的模块名）

```js
// Node内置的模块
const fs = require("fs");       // 内置的文件系统模块
const http = require("http");   // 内置的HTTP模块

// Express HTTP 服务器框架是第三方模块
// 不属于Node，但已安装在本地
const express = require("express")
```

若要导入自己代码的模块，利用文件路径导入

```js
const stats = require('./stats.js');
const BitSet = require('./utils/bitset.js');
```

若模块只导出一个函数或类，只要调用 `require()` 取得返回值即可

若模块导出一个带多个属性的对象，则可以有以下两种选择

- 导入整个对象
- 通过解构赋值只导入打算使用的特点属性

```js
// 导入整个对象，包含所有函数
const stats = require('./stats.js');

// 虽然导入用不到的函数，但这些函数都隐藏在命名空间之后
let average = stats.mean(data);

// 也可以使用解构赋值直接向本地命名空间导入想用的函数
const { stddev } = require('./stats.js');

// 这样简洁，只是stddev()函数没有‘stats’前缀作为命名空间，少了上下文信息
let sd = stddev(data);
```







## ES6 中的模块

ES6 为 JavaScript 添加了 `import` 和 `export` 关键字，用于支持模块作为核心语法



### ES6 的导出

要从 ES6 模块导出常量、变量、函数和类，在声明前加上 `export` 关键字

```js
export const PI = Math.PI;
export function degreesToRadians(d) { return d * PI / 180; };
export class Circle{
    constructor(r) { this.r = r; };
    area() { return PI * this.r * this.r; };
}

// 可以先正常定义常量、变量、函数和类，再通过export导出想要的值
export {Circle, degressToRadians, PI };
```

若一个模块只导出一个值（通常是一个函数或类），通常使用默认导出（export default）

```js
export default class BitSet {
    .....
}
```

默认导出在导入时稍微简单些，在只要一个导出值的情况，使用 `export default` 可以简化使用导出值的模块代码

使用 `export` 的常规导出只对有名字的声明有效，使用 `export default` 则可以导出任意表达式，包括匿名函数表达式和匿名表达式

`export` 关键字只能出现在 JavaScript 代码的顶层，不能在类、函数、循环或条件内部导出值



### ES6 的导入

导入其他模块导出的值使用 `import` 关键字

```js
// 导入定义默认导出的模块
import BitSet from './bitset.js';
```

`import` 关键字只能出现在 JavaScript 代码的顶层，不能在类、函数、循环或条件内部导出值，非强制性，导入与函数声明类型，会被提升到顶部

```js
// 从导出多个值的模块导入想要的值
import {mean, stddev} from "./stats.js";

// 导入所有值
import * as stats from "./stats.js"
```

导入所有值会将模块里的每个非默认导出都变为这个 `stats` 对象的属性

```js
// 导入没有任何导出的模块
import "./analytics.js"
```



### 导入和导出时重命名

若两个模块使用相同的名字导入两个不同的值，且希望同时导入这两个值，那必须在导入时对其中至少一个进行重命名

若导入某个值时发现已有同名，也需要重命名这个导入值

在命名导入时使用 `as` 关键字对导入值重命名

```js
import { render as renderImage } from "./imageutils.js";
import { render as renderUI} from "./ui.js"
```

同时导入默认导出和命名导出的方式

```js
import { default as Histogram, mean, stddev } from "./histogram-stats.js"
```

导出值也可以进行重命名

```js
export {
    layout as calculateLayout,
    render as renderLayout
};
```

`export` 关键字需要 `as` 前面是一个标识符，而非表达式，即不能如下重命名

```js
export { Math.sin as sin, Math.cos as cos };       // SyntaxError
```



### 再导出

ES6 模块通过组合 `expor`t 和 `from` 关键字构造一个“再导出”语句

```js
export { mean } from "./stats/mean.js";
export { stddev } from "./stats/stddev.js";
```

若不需要选择性地再导出，而是希望到处另一个模块地所有命名值

```js
export * from "./stats/mean.js";
export * from "./stats/stddev.js";
```

若要将另一个模块的命名符号再导出为当前模块地默认导出

```js
export { mean as default } from "./stats.js"
```

若要将另一个模块的默认导出再导出为当前模块地默认导出

```js
export { default } from "./stats/mean.js"
```



### Web 使用 JavaScript 模块

在浏览器中以原生方式使用 `import` 指令，必须通过 `<scripe type="module">` 标签告诉浏览器这是一个模块

ES6 模块的重要特性是每个模块的导入都是静态的，即只要有一个起始模块，浏览器就可以加载它导入的所有模块

添加 `async` 属性可以改变执行模块代码的时机

支持 `<scripe type="module">` 的浏览器必须也支持 `<scripe nomodule>`

支持模块的浏览器会忽略 `nomodule` 属性的脚本，不支持模块的浏览器不认识 `nomodule` 属性，会忽略这个属性运行其脚本，可以兼容旧版本浏览器

常规脚本和模块脚本的另一个重要区别涉及跨源加载



### 通过 import() 动态导入

ES6 的 `import` 和 `export` 指令都是静态的，静态导入的模块可以保证导入的值在任何模块代码运行之前就可以使用

若代码在 CPU 相对较慢的移动设备上执行，不适用于静态模块导入的场景，静态模块导入需要先加载完全部程序再执行，通过动态导入可以解决

传给 `import()`  一个模块标识符，会返回一个期约对象，表示加载和运行指定模块的异步过程，动态导入完成后，这个期约会“兑现”并产生一个对象，与使用静态导入语句 `import * as` 得到的对象类似

```js
// 静态导入
import * as stats from "./stats.js";

// 动态导入
import("./stats.js").then(stats => {
    let average = stats.mean(data);
})
// 或在一个async函数中
async analyzeData(data){
    let stats = await import("./stats.js");
    return{
        average: stats.mean(data),
        stddev: stats.stddev(data)
    };
}
```



### import.meta.url

在 ES6 模块中，`import.meta` 引用一个对象，这个对象包含当前正在执行模块的元数据，这个对象的 url 属性是加载模块时使用的 url（在 Node 中是 `file://URL`）

`import.meta.url` 的主要使用场景是引用与模块位于同一（或相对）目录下的图片、数据文件或其他资源

使用 `URL()` 构造函数可以非常方便地相对于 `import.meta.url` 这样地绝对 URL 来解析相对 URL

示例：若要写一个模块，其中包含需要本地化的字符串，而相关地本地化文件保存在 l10n/ 目录下，这个目录也保存着模块本身，则你的模块可以通过使用如下的函数创建的 URL 来加载字符串

```js
function localStringURL(locale){
    return new URL(`l10n/${local}.json`, import.meta.url)
}
```

