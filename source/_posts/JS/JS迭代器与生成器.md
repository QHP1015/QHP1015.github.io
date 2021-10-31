---
title: JS迭代器与生成器
author: 从此在回忆里
mathjax: true
date: 2021-10-30 14:08:06
tags: 前端学习 JavaScript
categories: JavaScript
---

可迭代对象及其相关的迭代器是 ES6 的一个特性，数组（包括 Typed Array）、字符串、Set 对象和 Map 对象都是可迭代的，即可以通过 `for/of` 循环来迭代

<!--more-->

迭代器让 `...` 操作符能展开或“扩展”可迭代对象，如

```js
let chars = [..."abcd"];        // => ["a", "b", "c", "d"]
let data = [1, 2, 3, 4, 5];
Math.max(...data)               // => 5
```

迭代 Map 对象时，返回值是 [key, value] 对，在 `for/of` 循环中可直接使用解构赋值

```js
let m = new Map([["one", 1], ["two", 2]]);
for(let [k,v] of m) console.log(k, v);      // 打印'one 1'和'two 2'
```

若只想迭代键或值，可以使用 `keys()` 或 `values()` 方法

```js
[...m]                // => [["one", 1], ["two", 2]]:默认迭代
[...m.entries()]      // => [["one", 1], ["two", 2]]:entries()方法相同
[...m.keys()]         // => ["one", "two"]:keys()方法只迭代键
[...m.values()]       // => [1, 2]:values()方法只迭代值
```



## 迭代器原理

- 可迭代对象

  可迭代对象指的是任何具有专门迭代器方法，且该方法返回迭代器对象的对象

- 迭代器对象

  迭代器对象指的是任何具有 `next()` 方法，且该方法返回迭代结果对象的对象

- 迭代结果对象

  迭代结果对象是具有属性 `value` 和 `done` 的对象

迭代一个可迭代对象，首先要调用其迭代器方法获得一个迭代器对象，然后重复调用这个迭代器对象的 `next()` 方法，直至返回 `done` 属性为 `true` 的迭代结果对象

可迭代对象的迭代器方法使用符号 `Symbol.iterator` 作为名字

```js
// 简单for/of循环也可以写成如下形式（可迭代对象iterator）
let iterable = [99];
let iterator = iterable[Symbol.iterator]();
for(let result = iterator.next(); !result.done; result = iterator.next()){
    console.log(result.value)      // result.value == 99
}
```

内置可迭代数据类型的迭代器对象本身也是可迭代的，即其有一个名为 `Symbol.iterator` 的方法，返回它们自己

```js
// 迭代“部分使用”的迭代器
let list = [1,2,3,4,5];
let iter = list[Symbol.iterator]();
let head = iter.next().value;        // head == 1
let tail = [...iter];                // tail == [2,3,4,5]
```







## 实现可迭代对象

实现类可迭代，需要定义一个名为 `Symbol.iterator` 的方法

```js
// 可迭代的数值Range类

/*
 * Range对象表示一个数值范围{x: from <= x <= to}
 * Range定义了has()方法用于测试给定数值是不是该范围的成员
 * Range是可迭代的，迭代其范围内的所有整数
 */
class Range{
    constructor(from, to){
        this.from = from;
        this.to = to;
    }
    
    // 让Range对象像数值的集合一样
    has(x) { return typeof x === "number" && this.from <= x && x <= this.to; }
    
    // 使用集合表示法返回当前范围的字符串表示
    toString() { return `{ x | ${this.from} <= x <= {this.to}}`; }
    
    // 通过返回一个迭代器对象，让Range对象可迭代
    // 这个方法的名字是一个特殊符号，不是字符串
    [Symbol.iterator](){
        // 每个迭代器示例必须相互独立、互不影响地迭代自己的范围
        // 需要一个状态变量跟踪迭代的位置，从第一个大于等于from的整数开始
        let next = Math.ceil(this.from);         // 下一个要返回的值
        let last = this.to;                      // 不会返回大于这个值的值
        return{
            // next()方法是迭代器对象的标志，必须返回一个迭代器结果对象
            next(){
                return (next <= last)            // 若还没有返回last 
                    ? { value: next++ }          // 返回next并给它加1
                	: { done: true };            // 否则返回表示完成的对象
            },
            
            // 为了方便，让迭代器本身也可迭代
            [Symbol.iterator]() { return this; }
        };
    }
}

for(let x of new Range(1, 10)) console.log(x);   // 打印数值1到10
[...new Range(-2, 2)];                           // => [-2, -1, 0, 1, 2]
```

定义返回可迭代值的函数，以下两个函数可以代替 JavaScript 数组的 `map()` 和 `filter()` 方法

```js
// 返回一个可迭代对象，迭代的结果是对传入的
// 可迭代对象的每个值应用f()的结果
function map(iterable, f){
    let iterator = iterable[Symbol.iterator]();
    return{            // 这个对象既是迭代器也是可迭代对象
        [Symbol.iterator]() { return this; },
        next(){
            let v = iterator.next();
            if(v.done){
                return v;
            }else{
                return { value: f(v,value) };
            }
        }
    };
}

// 把一个范围内的整数映射为它们的平方并转换为一个数组
[..map(new Range(1,4), x => x * x)]     // => [1, 4, 9, 16]

// 返回一个可迭代对象
// 只迭代predicate返回true的函数
function filter(iterable,predicate){
    let iterator = iterable[Symbol.iterator]();
    return {           // 这个对象既是迭代器也是可迭代对象
        [Symbol.iterator]() { return this; },
        next(){
            for(;;){
                let v = iterator.next();
                if(v.done || predicate(v.value)){
                    return v;
                }
            }
        }
    };
}

// 筛选整数范围，只保留偶数
[...filter(new Range(1,10), x => x % 2 === 0)]      // => [2,4,6,8,10]
```

可迭代对象和迭代器有一个重要的特点，即天性懒惰：如果计算下一个值需要一定计算量，则相应计算会推迟到实际需要下一个值的时候再发生

示例：存在一个很长的字符串，要对其进行分词，返回以空格分隔的单词，若使用字符串的 `split()` 方法，哪怕一个单词不用也要处理整个字符串，这样可能会占用很多内存来保存返回的数组和其中的字符串。如下定义一个函数，对字符串的单词懒惰迭代，不必将其全部保存在内存里

```js
function words(){
    var r = /\s+|$/g;                           // 匹配一个或多个空格或末尾
    r.lastIndex = s.match(/[^ ]/).index;        // 开始匹配第一个非空格
    return{                                     // 返回一个可迭代的迭代器对象
        [Symbol.iterator]() { return this; },   // 这个方法是可迭代对象必需的
        next(){                                 // 这个方法是迭代器必需的
            let start = r.lastIndex;            // 从上次匹配结束的地方恢复
            if(start < s.length){               // 若还没处理完
                let match = r.exec(s);          // 匹配下一个单词边界
                if(match){
                    return { value: s.substring(start, match.index) };
                }
            }
            return { done: true };              // 否则，返回表示处理完成的结果
        }
    };
}

[...words(" abc deff  ghi! ")]                  // => ["abc", "def", "ghi!"]
```



**关闭迭代器：`return()` 方法**

若迭代在 `next()` 返回 `done` 属性为 `true` 的迭代结果之前停止（通常是因为通过 `break` 语句提前退出 `for/of` 循环），解释器就会检测迭代器对象是否有 `return()` 方法，若有，解释器会调用它（不传参），让迭代器有机会关闭文件、释放内存，或者做一些其他清理工作

`return()` 方法必须返回一个迭代器结果对象







## 生成器

生成器是一种用新 ES6 语法定义的迭代器，适合与要迭代的值不是某个数据结构的元素，而是计算结果的场景

```js
// 这个生成器函数回送一组素数
function* oneDigitPrimes(){ // 调用这个函数不会运行函数体
    yield 2;                // 只会返回一个生成器对象。调用
    yield 3;                // 该对象的next()开始运行，直至一个yield语句
    yield 5;                // 为next()方法提供返回值
}

// 调用生成器函数，得到一个生成器
let primes = oneDigitPrimes();

// 生成器是一个迭代器对象，可以迭代回送的值
primes.next(). value        // => 2
primes.next(). value        // => 3
primes.next(). value        // => 5
primes.next(). done         // => true

// 生成器有一个Symbol.iterator方法，因此也是可迭代对象
primes[Symbol.iterator]()   // => primes

// 可以像使用其他可迭代对象一样使用生成器
[...oneDigitPrimes()]       // => [2,3,5]
let sum = 0;
for(let prime of oneDigitPrimes()) sum += prime;
sum                         // => 10
```

生成器函数用 `function*` 定义，与常规函数一样，也可以使用表达式定义生成器

```js
const seq = function*(from, to){
    for(let i = from; i <= to; i++) yield i;
};
[...seq(3,5)]        // => [3, 4, 5]
```

在类和对象字面量中，定义方法时可以使用件写

```js
let o = {
    x: 1, y: 2, z: 3,
    // 这个生成器会回送当前对象的每个键
    *g(){
        for(let key of Object.keys(this)){
            yield key;
        }
    }
};
[...o.g()]      // => ["x", "y", "z", "g"]
```

> 不能使用箭头函数语法定义生成器函数



### 生成器示例

回送斐波那契数列

```js
function* fibonacciSequence(){
    let x = 0, y = 1;
    for(;;){
        yield y;
        [x, y] = [y, x+y];     // 注意，解构赋值
    }
}
```

上面这个生成器永远回送值而不返回，若通过扩展操作符使用它，会一直循环到内存耗尽，程序崩溃，可以设置退出条件

```js
// 返回第n个斐波那契数列
function fibonacci(n){
    for(let f of fibonacciSequence()){
        if(n-- <= 0) return f;
    }
}
fibonacci(20)          // => 10946
```

配合如下的 `take()` 生成器，可以用更大用处

```js
// 回送指定可迭代对象的前n个元素
function* take(n, iterable){
    let it = iterable[Symbol.iterator]();   // 取得可迭代对象的生成器
    while(n-- > 0){                         // 循环n次
        let next = it.next();               // 从迭代器中取得下一项
        if(next.done) return;               // 若没有更多的值，直接返回
        else yield next.value;              // 否则，回送这个值
    }
}

// 包含前5个斐波那契数列
[...take(5, fibonacciSequence())]           // => [1, 1, 2, 3, 5]
```



交替回送多个可迭代对象的元素

```js
// 拿到一个可迭代对象的数组，交替回送元素
function* zip(...iterables){
    // 取得每个可迭代对象的迭代器
    let iterators = iterables.map(i => i[Symbol.iterator]());
    let index = 0;
    while(iterators.length > 0){              // 还有迭代器
        if(index >= iterators.length){        // 如果到了最后一个迭代器
            index = 0;                        // 返回至第一个迭代器
        }
        let item = iterators[index].next();   // 从一个迭代器中取得下一项
        if(item.done){                        // 如果该迭代器完成
            iterators.splice(index, 1);       // 则从数组中删除它
        }else{                                // 否则
            yield item.value;                 // 回送迭代的值
            index++;                          // 并前进到下一个迭代器
        }
    }
}

// 交替3个可迭代对象
[...zip(oneDigitPrimes(),"ab",[0])]           // => [2,"a",0,3,"b",5]
```



### yield* 与递归生成器

`yield*` 迭代可迭代对象并回送得到的每个值

```js
function* sequence(...iterables){
    for(let iterable of iterables){
        yield* iterable;
    }
}

[...sequence("abc",oneDigitPrimes())]   // => ["a","b","c",2,3,5]
```

`yield*` 可以用于迭代任何可迭代对象，包括通过生成器实现的，即使用 `yield*` 可以定义递归生成器，利用这个特性可以通过简单的非递归迭代遍历递归定义的树结构





## 高级生成器特性

生成器最常用于创建迭代器，生成器的基本特征是可以暂停计算，回送中间结果，在某个时刻恢复计算

### 生成器函数的返回值

生成器函数的 `return` 语句多用于提前退出，而不是返回值，但是，生成器函数也可以返回值

手动迭代时可以通过显示调用返回

```js
function* oneAndDone(){
    yield 1;
    return "done";
}

// 正常迭代中不会出现返回值
[...oneAndDone()]           // => [1]

// 显式调用next()时可以得到
let generator = oneAndDone();
generator.next()            // => { value: 1, done: false}
generator.next()            // => { value: "done", done: true}
// 如果生成器已经完成，则不再返回值
generator.next()            // => { value: undefined, done: true}
```

