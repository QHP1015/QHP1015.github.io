---
title: JS类型、值和变量
author: 从此在回忆里
date: 2021-10-09 20:03:53
tags: 前端学习
categories: JavaScript
---

> 前言：编程语法的词法结构是一套基本规则，规定如何使用这门语言编写程序。同时本篇讲解JavaScript的类型、值和变量

<!--more-->

## 注释

JavaScript 支持两种注释

- 单行注释以 // 开头到一行末尾的内容
- 多行注释位于 /* 和 */ 之间，可以跨行，不能嵌套

```js
// 单行注释
/* 注释 */ // 另一个注释
/*
 * 多行注释
 * 每行开头的 * 字符非必需，仅为了美观
 */
```





## 字面量

字面量（literal）是一种直接出现在程序中的数据值，后续解释数指和字符串字面量

字面量示例

```js
12				// 数值 12
1.2				// 数值 1.2
“hello world”   // 字符串
true			// 布尔值
null			// 无对象
```





## 标识符和保留字

标识符用于在 JavaScript中 命名常量、变量、属性、函数和类，以及为某些循环提供标记（label）

JavaScript 标识符必须以**字母、下划线（_）、或美元符号（$）**开头，后续字符可以是字母、数字、下划线、美元符号

JavaScript 不能使用保留字作为常规标识符





## Unicode

JavaScript 程序使用 Unicode 字符集编写，因此在字符串和注释中可以使用任意 Unicode 字符

- Unicode 转义序列：以 \u 开头，后跟 4 位十六进制数字或包含在一对花括号内的 1~6 位十六进制数字

  ```js
  caf\u{E9} ;
  cafe\u00e9;  
  console.log("u{1f600}")  // 打印一个笑脸符号
  ```

- Unicode 归一化：Unicode 标准为所有字符定义了首选编码并规划了归一化例程，用于把文本转换为适合比较的规范形式






## 数值

JavaScript 的主要数值类型 Number 用于表示整数和近似实数

JavaScript 使用 IEEE 754 标准定义的 64 位浮点格式表示数值

### 数值字面量

- 整数字面量

  - 基数为 10 的整数直接写成数字序列

    ```js
    0
    3
    100000000
    ```

  - 十六进制字面量以 0x 或 0X 开头，后跟一个十六进制数字字符串

    ```JS
    0xff            // => 255
    0xBADCAFE       // => 195939070
    ```

  - 二进制或八进制，分别使用前缀 0b 和 0o（或 0B 和 0O）

    ```js
    0b10101			// => 21
    0o377			// => 255
    ```

- 浮点字面量

  - 浮点字面量实数值有数值整数部分、小数点和数值的小数部分组成

    ```js
    3.14
    2345.6789
    .3333333333333
    ```

  - 指数计数法，实数值后面跟 e（或 E），跟一个可选的加号或减号，在跟一个整数指数

    ```js
    6.02e23
    1.4738223E-32       
    ```

- 数值字面量的分隔符：可以用下划线将数值字面量分隔

  ```js
  let billion = 1_000_000_000;	// 以下划线作为千分位分隔符
  let bytes = 0x89_AB_CD_EF;		// 作为字节分隔符
  let bits = 0b0001_1101_0111;	// 作为半字节分隔符
  let fraction = 0.123_456_789;	// 用于小数分隔
  ```



### 数值算术

JavaScript使用算术操作符来操作数值，如加法（+）、减法（-）、乘法（*）、取模（%），ES6新增取幂（**）

Math对象属性提供一组函数和常量

```js
Math.pow(2,53)				// => 2 的 53 次方
Math.round(.6)				// => 1.0：舍入到最接近的整数
Math.ceil(.6)				// => 1.0：向上舍入到一个整数
Math.floor(.6)				// => 0.0：向下舍入到一个整数
Math.abs(-5)				// => 5：绝对值
Math.max(x,y,z)				// 返回最大的参数
Math.min(x,y,z)				// 返回最小的参数
Math.random					// 伪随机数下，其中 0 ≤ x < 1.0
Math.PI						// Π：圆周率吧
Math.E						// e：自然对数的底数
Math.sqrt(3)				// => 3 的平方根
Math.pow(3,1/3)				// => 3 的立方根
Math.sin(0)					// 三角函数：还有 Math.cos、Math.aten 等
Math.log(10)				// 10 的自然对数
Math.log(100)/Math.LN10		// 以 10 为底 100 的对数
Math.log(512)/Math.LN2		// 以 2 为底 512 的对数 
Math.exp(3)					// Math.E 的立方
```

ES6 新定义函数

```js
Math.cbrt(27)				// => 3：立方根
Math.hypot(3,4)				// => 5：所有参数平方和的平方根
Math.log10(100)				// => 2：以 10 为底的对数
Math.log2(1024)				// => 10：以 2 为底的对数
Math.log1p(x)				// (1+x) 的自然对数；精确到非常小的x
Math.expm1(x)				// Math.exp(x)-1；Math.log1p() 的逆运算
Math.sign(x)				// 对 <、==或 >0的参数返回 -1、0 或 1
Math.imul(2,3)				// => 6：优化的 32 位整数乘法
Math.clz32(0xf)				// => 28：32 位整数中前导 0 的位数
Math.trunc(3.9)				// => 3：剪掉分数部分得到整数
Math.fround(x)				// 舍入到最接近的 32 位浮点数
Math.sinh(x)				// 双曲线正弦，还有 Math.cosh()、Math.tanh() 
Math.asinh(x)				// 双曲线反正弦，还有 Math.acosh()、Math.atanh() 
```

JavaScript 算术中上溢出结果返回 `Infinity`，某个负数的绝对值超过最大可表示负数的绝对值，返回 `-Infinity`

下溢出发生在数值操作的结果比最小可表示数值更接近 0 的情况下，返回 0 ；若下溢来自负数，返回 -0

被零除返回 `Infinity`或 `-Infinity`，0 除 0 返回非数值 `NaN（Not a Number）`

JavaScript预定义了全局常量 `Infinity` 和 `NaN`，这些值也可以通过 `Number` 对象属性获取

```js
Infinity						// 因为太大而无法表示的整数
1/0								// => Infinity
-Infinity						// 因为太小而无法表示的负数
-Number.MAX_VALUE * 2 			// => -Infinity

NaN								// 非数值
Number.NaN						// 同上
Infinity/Infinity				// => NaN

Number.MIN_VALUE/2				// => 0：下溢出
-Number.MIN_VALUE/2				// => -0：负零
-1/Infinity						// => -0：负零

//ES6定义属性
Number.parseInt()				// 同全局parseInt()函数
Number.parseFloat()				// 同全局parseFloat()函数
Number.isNaN()					// 判断 x 是不是 NaN
Number.isFinite()				// 判断 x 是数值还是无穷
Number.isInteger()				// 判断 x 是不是整数
Number.isSafeInteger()			// 
Number.MIN_SAFE_INTEGER			// => -(2**53-1)
Number.MAX_SAFE_INTEGER			// => 2**53-1
Number.EPSILON					// => 2**52：数值与数值之间最小的差
```

非数值与任何值比较都不相等，也不等于自身，即不能通过 `x === NaN` 确定是不是非数值，只能通过 `x != x` 或 `Number.isNaN(x)`

负零值与正零值相等，几乎无法区别



### 二进制浮点数

JavaScript使用的 IEEE-754 浮点表示法是一种二进制表示法，可以精确地表示如 1/2、1/8 和 1/1024 等分数，但不能精确表示如0.1这样地数

虽然JavaScript数值有足够大地精度，但仍然可能导致一些问题

```js
let x = .3 - .2;			// 30美分减20美分
let y = .2 - .1;			// 20美分减10美分
x === y;					// => false：这两个值不一样
x === .1;					// => false：.3-.2 不等于 .1
y === .1;					// => true：.2 -.1 等于 .1
```

若需要使用浮点近似值，可以考虑使用等量整数



### BigInt

ES2020为 JavaScript 定义了一种新的数值类型 BigInt（整数）

BigInt 增加的目的是为了表示 64 位整数，可以兼容很多其他语言和 API。

BigInt 可以满足对大数（可表示数千甚至数百万个数字）的需求，但是 BigInt 地实现不适合加密，没有考虑防止时序攻击

BigInt 字面量写作一串数字后跟小写字母 n 

```js
1234n							// 基数为 10 的 BigInt
0b111111n						// 二进制 BigInt
0o7777n							// 八进制 BigInt
0x8000000000000000n				// => 2n**63n：一个 64 位整数
```

可以用 BigInt() 函数把常规数值或字符串转换为 BigInt 值

```js
BigInt(Number.MAX_SAFE_INTEGER)				// => 9007199254740991n				
let string = "1" + "0".repeat(100);			// 1 后跟 100 个零
BigInt(string)								// => 10n**100n
```

BigInt 值的算术运算与常规 JavaScript 数值类似，只不过**除法会丢弃余数并会向下（向零）舍入**

常规运算操作符可以用于 BigInt ，但不能混用 BigInt 操作数和常规数值操作数

比较操作符允许混合操作数类型

```js
1 < 2n					// => true
2 > 1n					// => true
0 == 0n					// => true
0 === 0n				// => false：=== 也检查类型是否相等
```

位操作符通常可以用于 BigInt 操作数，但 Math对象的任何函数不接收 BigInt 操作数



### 日期和时间

JavaScript 为表示和操作与日期及时间相关的数据定义了 Date 类。Date 是对象，也可以用数值表示形式（自 1970.1.1 起的毫秒数，也叫时间戳）

```js
let timestamp = Date.now();					// 当前时间的时间戳（数值）
let now = new Date();						// 当前时间的日期对象
let ms = now.getTime();						// 转换位毫秒时间戳
let iso = now.toISOString();				// 转换位标准格式的字符串
```





## 文本

JavaScript 中表示文本的类型是 `String`，即字符串

字符串是 16 位值的不可修改的有序序列，每个值表示一个 Unicode 字符

### 字符串字面量

JavaScript 字符串可以用一对匹配的单引号、双引号或反引号来包含

> ES6新增可以使用反引号定界字符串

单引号、双引号、反引号可以相互嵌套

JavaScript 早期版本要求字符串字面量写在一行，ES5 可以在每行末尾加一个反斜杠（ \ ）写道多行上，ES6 的反引号支持跨行字符串

```js
// 写在一行但表示两行的字符串：
'two\nlines'

// 写在三行但只有一行的字符串：
"one\
 long\
 line"

// 写在两行实际也是两行的字符串：
'two
 nlines'
```



### 转义序列

| 序列    | 表示的字符                                                   |
| ------- | ------------------------------------------------------------ |
| \ 0     | NUL 字符                                                     |
| \ b     | 退格符                                                       |
| \ t     | 水平制表符                                                   |
| \ n     | 换行符                                                       |
| \ v     | 垂直制表符                                                   |
| \ f     | 进纸符                                                       |
| \ r     | 回车符                                                       |
| \ "     | 双引号                                                       |
| \ '     | 单引号                                                       |
| \ \     | 反斜杠                                                       |
| \ xnn   | 由 2 为十六进制数字 nn 指定的 Unicode 字符                   |
| \ unnnn | 由 4 为十六进制数字 nnnn 指定的 Unicode 字符                 |
| \ u{n}  | 由码点 n 指定的 Unicode 字符，其中 n 是介于 0 和 10FFFF 之间的 1 到 6 位十六进制数字（ES6） |



### 字符串使用

JavaScript 可以使用 + 操作符拼接字符串，可以使用 `===` 和 `!==` 操作符比较字符串，也可以使用其比较字符串如： <、 <=、>和 >=

操作字符串的API

```js
let s = "Hello, world";
s.length								// => 12：返回字符串长度

// 取得字符串的一部分
s.substring(1,4)						// => "ell"：第 2~4 个字符
s.slice(1,4)							// => "ell"：同上
s.slice(-3)								// => "rld"：最后 3 个字符
s.split(", ")							// => ["Hello","world"]：从定界处拆分

// 搜索字符串
s.indexOf("l")							// => 2：第一个字母 l 的位置
s.indexOf("l",3)						// => 3：位置 3 后面第一个 l 的位置
s.indexOf("zz")							// => -1：s 并不包含子串 zz
s.lastIndexOf("l")						// => 10：最后一个字母 l 的位置

// ES6 及之后版本中的布尔值搜索函数
s.startsWith("hell") 					// => true：字符串是以这些字符开头的
s.endWith("!")							// => false：s 不是以它结尾的
s.includes("or")						// => true：s包含子串 or

// 创建字符串的修改版本
s.replace("llo","ya")					// => "Heya,world"
s.toLowerCase()							// => "hello, world"
s.toUpperCase()							// => "HELLO, WORLD"
s.normalize()							// Unicode NFC 归一化：ES6 新增
s.normalize("NFD")						// NFD 归一化。还有 NFKC 和 NFKD

// 访问字符串的个别（16位值）字符
s.charAt(0)								// => "H"：第一个字符
s.charAt(s.length-1)					// => "d"：最后一个字符
s.charCodeAt(0)							// => 72：指定位置的16位数值
s.codePointAt(0)						// => 72：ES6，适用于码点大于 16 位的情形

// ES2017 新增字符串填充函数
"x".padStart(3)							// => "  x"：在左侧添加空格，让字符串长度变成3
"x".padEnd(3)							// => "x  "：在右侧添加空格，让字符串长度变成3
"x".padStart(3,"*")						// => "**x"：在左侧添加星号，让字符串长度变成3
"x".padEnd(3,"-")						// => "x--"：在右侧添加破折号，让字符串长度变成3

// 删除空格函数
" test ".trim()							// => "test"：删除开头和末尾的空格
" test ".trimStart()					// => "test "：删除左侧的空格。也叫 trimLeft
" test ".trimEnd()						// => " test"：删除右侧的空格。也叫 trimRight

// 未分类字符串方法
s.concat("!")							// => "Hello, world!"：可以用 + 操作符代替
"<>".repeat(5)							// => "<><><><><>"：拼接 n 次。ES6 新增
```

JavaScript中的字符串是不可修改的。像 `replace()` 和 `toUpperCase()` 这些都返回新字符串

字符串也可以被当成只读数组，用方括号访问



### 模板字面量

ES6 及之后的版本，字符串字面量可以用反引号定界，反引号中可以包含模板字面量，模板字面量可以包含任意JavaScript表达式

```js
let name = "Bill";
let greeting = 'Hello ${ name }.';		 // => greeting == "Hello Bill."
```

**标签化模板字面量**：在开头的反引号前添加函数名（标签），则模板字面量中的文本和表达式的值作为参数传给这个函数

”标签化模板字面量“的值即这个函数的返回值

ES6 提供内置的标签函数：`String.raw()` ，用于返回反引号中未经处理的文本

```js
'\n'.length							// => 1：字符串中只包含一个换行符
String.raw'\n'.length				// => 2：一个反斜杠字符和一个字母n
```





## 布尔值

布尔值只有两个值：`true` 和 `false`

JavaScript 的任何值都可以转换为布尔值，以下的值转换为布尔值 `false`

```js
undefined
null
0
-0
NaN
""		// 空字符串
```

所有其他值转换为布尔值 `true`

布尔值的 `toString()` 方法，用于将自己转换为字符串 ”true“ 或 ”false“





## null与undefined

`null` 是关键字，通常用于表示某个值不存在

```js
typeof(null)			// => "object"
```

`null` 可以看作一种特殊对象，表示 ”没对象“

`undefined` 也表示值不存在

```js
typeof(undefined)			// => "undefined"
```

`undefined` 情形

- 变量的值未初始化时
- 查询不存在的对象属性或数组元素
- 没用明确返回值的函数返回值
- 没有传值的函数参数的值



```js
null == undefined			// => true
null === undefined			// => false
```





## 符号

ES6 新增的一种原始类型，用作非字符串的属性名

```js
let strname = "string name";				// 可以用作属性名的字符串
let symname = Symbol("propname");			// 可以用作属性名的符号
typeof strname								// => "string"：strname 是字符串
typeof symname								// => "symbol"：symname 是符号
let o = {};									// 创建一个新对象
o[strname] = 1;								// 使用字符串名定义一个属性
o[symname] = 2;								// 使用符号名定义一个属性
o[strname]									// => 1：访问字符串名字的属性
o[symname]									// => 2：访问符号名字的属性
```

`Symbol` 类型的 "真实值" 无法获取，即**Symbol类型没有对应的字面量**

`Symbol` 值只能通过调用 `Symbol()` 来获取，且返回的都是唯一的值，即使每次传入的参数相同 

通过调用 `toString()` 方法可以返回包含参数的值

```js
let s = Symbol("sym_x");
s.toString						// => "Symbol(sym_x)"
```

为了可以与其他代码共享符号值，JavaScript 定义了一个全局符号注册表

`Symbol.for()` 函数接收一个字符串参数，返回一个与该字符串关联的符号值，若没有关联的符号值，创建返回新符号，可以通过 `Symbol.keyFor()` 得到字符串参数

```js
let s = Symbol.for("shared");
let t = Symbol.for("shared");
s === t							// => true
s.toString()					// => "Symbol(shared);"
Symbol.keyFor(t)				// => "shared"
```





## 原始值与对象引用

JavaScript 中的原始值（undefined、null、布尔值、数值和字符串）是不可修改的，修改后都是返回新的值。比较两个不同的字符串时，当且仅当两个字符串长度相同且每个索引的字符也相同时，才相等

```javascript
let s = "hello";
s.toUpperCase();
s						// => "hello"：原始字符串并未改变
```

JavaScript 中的对象和数组是可修改的，即其值是可变的。两个不同的对象和数组即使用完全相同的属性和值，也不是相等的

```javascript
let o = {x: 1}, p = {x: 1};				// 两个对象，有相同的属性
o === p									// => false：不同的对象永远也不会相等
let a = [], b = [];						// 两个不同数组
a === b									// => false：不同的数组永远也不会相等
```

当且仅当两个对象引用同一个底层对象时，才相等

```js
let a = [];			// 这个变量引用一个空数组
let b = a;			// b 引用同一个数组
b[0] = 1;			// 修改变量b引用的数组
a[0]				// => 1：变化也能通过变量 a 看到
a === b				// => true：a 和 b 引用同一个对象，相等
```

创建对象或数组的副本，必须显示复制对象或数组的元素

```js
let a = ["a","b","c"];						
let b = [];
for(let i = 0; i < a.length; i++){
	b[i] = a[i];							// 把 a 的元素复制到 b 中
}
let c = Array.from(b);						// ES6 可以使用 Array.from() 复制数组
```





## 类型转换

JavaScript 的类型转换

| 值                    | 转换为字符串    | 转换为数值 | 转换为布尔值 |
| --------------------- | --------------- | ---------- | ------------ |
| undefined             | “undefined”     | NaN        | false        |
| null                  | “null”          | 0          | false        |
| true                  | “true”          | 1          |              |
| false                 | “false”         | 0          |              |
| “”（空字符串）        |                 | 0          | false        |
| “1.2”（非空，数值）   |                 | 1.2        | true         |
| “one”（非空，非数值） |                 | NaN        | true         |
| 0                     | “0”             |            | false        |
| -0                    | “0”             |            | false        |
| 1（有限，非零）       | “1”             |            | true         |
| Infinity              | “Infinity”      |            | true         |
| -Infinity             | “-Infinity”     |            | true         |
| NaN                   | “NaN”           |            | false        |
| { }（任何对象）       |                 |            | true         |
| [ ]（空数组）         | “”              | 0          | true         |
| [9]（一个数值元素）   | “9”             | 9          | true         |
| ['a']（任何其他数组） | 使用 join()方法 | NaN        | true         |
| Function( )[ ]        |                 | NaN        | true         |

### **显示转换**

- `Boolean()`
- `Number()`
- `String()`

除 `null` 和 `undefined` 之外的所有值都有 `toString()` 方法，返回的结果通常与 `String()` 相同

某些 JavaScript 操作符会执行隐式类型转换

```js
x + ""						// => String(x)
+x							// => Number(x)
x-0							// => Number(x)
!!x							// => Boolean(x)：注意这里两次取反
```



### 数值到字符串

Number 类定义的  `toString()` 方法可以接收一个参数（ 2~36 ），用于指定一个基数或底数，默认为 10

```js
let n =17;
let binary = "0b" + n.toString(2);		// binary == "0b10001"
let octal = "0o" + n.toString(8);		// binary == "0o21"
let hex = "0x" + n.toString(16);		// hex == "0x11"
```

Number 类定义了 3 种方法

- `toFixed()` ：转换时可以指定小数点后面位数
- `toExponential()` ：使用指数计数法转换，结果为小数点前 1 位，小数点后为指定位数
- `toPrecision()` ：按照指定的有效数字转换，若有效数字个数不足以表示数值的整数部分，使用指数计数法
- 三种方法必要时都会舍去末尾的数字或补 0

```js
let n = 123456.789;
n.toFixed(0)						// => "123457"
n.toFixed(5)						// => "123456.79"
n.toExponential(1)					// => "1.2e+5"
n.toExponential(3)					// => "1.235e+5"
n.toPrecision(4)					// => "1.235e+5"
n.toPrecision(7)					// => "123456.8"
n.toPrecision(10)					// => "123456.7890"
```



### 字符串到数值

把字符串传给 `Number()` 转换函数，会把字符串当成整数或浮点数字面量解析，只支持基础为 10 的整数，不允许末尾出现无关字符

`parseInt()` 只解析整数，接收可选的第二个参数，用于指定要解析数值的基数或底数（ 2~36 ）

`parseFloat()` 既解析整数也解析浮点数

`parseInt()` 和 `parseFloat()` 会跳过开头空格，尽量解析多的数字，忽略后面的无关字符。若第一个字符不是有效的数值字面量，返回 `NaN` 



### 对象到原始值

所有对象都会继承两个在对象到原始值转换时使用的方法

- `toString()`：返回对象的字符串表示
- `valueOf()`：把对象转换为代表对象的原始值（若存在一个这样的原始值）



JavaScript 规范定义了对象到原始值转换的 3 种基本算法

- 偏字符串：该算法返回原始值，而且只要可能就返回字符串

  > 偏字符串算法首先尝试 `toString()` 方法，若此方法有定义且返回原始值，则使用该原始值；若不存在，或存在但返回对象，尝试 `valueOf()` 方法，若此方法存在且返回原始值，则使用该原始值，否则，报 `TypeError()`

- 偏数值：该算法返回原始值，而且只要可能就返回数值

  > 与偏字符串算法类似，但是先尝试 `valueOf()` 方法，再尝试 `toString()` 方法

- 无偏好：该算法不倾向于任何原始值类型，由类定义自己的转换规则

  > 取决于被转换对象的类

JavaScript 内置类除了 `Date()` 类都实现了偏数值算法，`Date()` 类实现偏字符串算法





## 解构赋值

ES6 实现了一种复合声明与赋值语法，叫做解构赋值（destructuring assignment）

```js
let [x,y] = [1,2];			// 相当于 let x=1, y=2
[x,y] = [x+1,y+1];			// 相当于 x = x + 1, y =y + 1
[x,y] = [y,x];				// 交换两个变量的值
[x,y]						// => [3,2]
```

解构赋值左侧变量的个数不一定与右侧变量相同，左侧多余的会被设为 `undefined` ，右侧多余的会被忽略。左侧的变量列表可以包含额外的逗号，以跳过右侧的某些值

```js
let [x,y] = [1];			// x == 1; y == undefined
[x,y] = [1,2,3];			// x == 1; y == 2
[,x,,y] = [1,2,3,4];		// x == 2; y == 4
```

在解构赋值时，若想把所有未使用或剩余的值收集到一个变量中，可在左侧最后一个变量名前加 3 个点（ ... ）

```js
let [x, ...y] = [1,2,3,4];			// y == [2,3,4]
```

若变量名与对象属性名相同，可以直接赋值，若左侧包含一个不是右侧属性的变量名，该变量被赋值为 `undefined`

```js
// 相当于 const sin=Math.sin, cos=Math.cos, tan=Math.tan
const {sin,cos,tan} = Math;
```

对象解构赋值左侧的每个标识符都可以是一个冒号分隔的标识符对，第一个为要解构其值的属性名，第二个为要把值赋给它的变量名

```js
// 相当于 const cosine=Math.cos, tangent=Math.tan
const { cos:cosine,tan:tangent} = Math;
```

