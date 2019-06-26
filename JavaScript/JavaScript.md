---
typora-root-url: ./
---

JavaScript

[TOC]

## 内置类型

JS中分为其中内置类型，七种内置类型分为两大类：**基本类型**和**对象**（`Object`）

基本类型有六种：

```javascript
null undefined Boolean number string symbol
```

> 就是的数字类型时浮点类型的，没有整形。NaN也属于`number`类型，并且`NaN`不等于自身。

```javascript
let a = 111  //这只是字面量，不是number类型
a.toString() // 使用的时候才会转换为对象类型
```

对象（`Object`） 是引用类型，在使用的过程中会遇到深拷贝浅拷贝的问题。

```javascript
let a = {name:'EF'}
let b = a
b.name = 'EF'
console.log(a.name)//EF
```

## typeof

`typeof`对于基本类型，除了`null`都可以正常显示。

```javascript
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof b // b 没有声明，但是还会显示 undefined
```

`typeof`对于对象，除了函数都会显示`object`

```javascript
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```

对于`null`来说，它虽然是基本类型，但是会显示`object`，这是一个存在很久了的bug

>*PS：为什么会出现这种情况呢？因为在 JS 的最初版本中，使用的是 32
>位系统，为了性能考虑使用低位存储了变量的类型信息，**000* *开头代表是对象，然而* *null* *表示为全零，所以将它错误的判断为* *object**。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug
>却是一直流传下来。*

如果我们想获得一个变量的正确类型，可以通过Object.prototype.toString.call(xx)。这样我们可以获得类似[object Type] 的字符串。

```javascript
const getType = v =>
  v === undefined ? 'undefined' : v === null ? 'null' : v.constructor.name.toLowerCase();

```

[getType，来自30-seconds](https://github.com/30-seconds/30-seconds-of-code#gettype)

## 类型转换

### 转boolean

在条件判断时，除了`undefined`，`unll`，`false`，`NaN`，`''`，`0`，`-0`，其他所有值都转为`true`，包括所有对象。

### 对象转基本类型

对象在转换基本类型时，首先会调用`valueOf`然后调用`toString`。并且这两个方法是可以重写的。

```javascript
let a = {
	valueOf() {
	    return 0
	}
}
```

当然你也可以重写`Symbol.toPrimitive`,该方法在转基本类型时调用优先级最高。

```javascript
let a = {
	valueOf() {
		return 0;
	},
	toString() {
		return '1';
	},
	[Symbol.toPrimitive]() {
		return 2;
	}
}
1 + a // => 3
'1' + a // => '12'
```

### 四则运算符

只有当加法运算时，其中一方是字符串类型，就会把另一个也转化为字符串类型。其他运算符只要其中一方为数字，那么另一方就转为数字。并且加法运算符会触发三种类型转换：将值转换为原始值，转换为数字，转换为字符串。

```javascript
1 + '1' // '11'
2 * '2' // 4
[1, 2] + [2, 1] // '1,22,1'
// [1, 2].toString() -> '1,2'
// [2, 1].toString() -> '2,1'
// '1,2' + '2,1' = '1,22,1'
```

对于加好需要注意这个表达式`'a' + + 'b'`

```javascript
'a' + + 'b' // -> "aNaN"
// 因为 + 'b' -> NaN
// 你也许在一些代码中看到过 + '1' -> 1
```

### ==操作符

![](2019-06-01-043719.png)

### 比较运算符

1. 如果是对象，就通过`toPrimitive`转换对象
2. 如果是字符串，就通过`unicode`字符串索引来比较

## 数组

### map FlatMap和Reduce

#### map

>`Map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后 `append` 到新的数组中。


  ```javascript
[1, 2, 3].map((v) => v + 1)
// -> [2, 3, 4]
  ```

> `Map` 有三个参数，分别是当前索引元素，索引，原数组

```javascript
['1','2','3'].map(parseInt)
//  parseInt('1', 0) -> 1
//  parseInt('2', 1) -> NaN
//  parseInt('3', 2) -> NaN
```

#### FlatMap

> `FlatMap` 和 `map` 的作用几乎是相同的，但是对于多维数组来说，会将原数组降维。可以将 `FlatMap`看成是 `map` + `flatten` ，目前该函数在浏览器中还不支持。

```js
[1, [2], 3].flatMap((v) => v + 1)
// -> [2, 3, 4]
```

#### Reduce

> `Reduce` 作用是数组中的值组合起来，最终得到一个值

```js
function a() {
    console.log(1);
}

function b() {
    console.log(2);
}

[a, b].reduce((a, b) => a(b()))
// -> 2 1
```

### 去重

1.利用数组原型对象上的forEach和includes方法

```js
function unique(arr) {
	var newArr = []
	arr.forEach(item=>{
	 return newArr.includes(item)?'':newArr.push(item)
	})
	return newArr
}
```

2.利用数组原型对象上的lastIndexOf方法

```js
function unique(arr) {
	var res = []
	for(var i =0;i<arr.length;i++) {
		res.lastIndexOf(arr[i])!==-1?'':res.push(arr[i])
	}
	return res
}
```

3.利用es6的set方法

```js
function unique(arr) {
	return Array.from(new Set(arr))
}
```

### 排序

#### sort方法

```js
var arr= [10,20,1,2]
arr.sort(function(a,b){
	return a-b
})
```

> 如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：
>
> - 若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
> - 若 a 等于 b，则返回 0。
> - 若 a 大于 b，则返回一个大于 0 的值。

#### 冒泡排序

```js
var arr = [10,20,1,2]
var t
for(var i =0;i<arr.length;i++) {
	for(var j = i+1;j<arr.length;j++) {
        if(arr[i]>arr[j]) {
            t = arr[i]
            arr[i] = arr[j]
            arr[j] = t
        }
    }
}
```





## 原型

![](68747470733a2f2f79636b2d313235343236333432322e636f732e61702d7368616e676861692e6d7971636c6f75642e636f6d2f626c6f672f323031392d30362d30312d3033333932352e706e67.png)



每个函数都有`prototype`属性，除了`Function.peototype.bind()`，该属性指向原型

每个对象都有__`proto`__属性，指向了创建该对象的构造函数的原型。其实这个属性指向了`[[prototype]]`，但是`[[prototype]]`是内部属性，我们并不能访问到，所以使用_`proto`_来访问。

对象可以通过__`proto`__来寻找不属于该对象的属性，__`proto`__将对象链接起来组成了原型链。

[深入解析原型中的各个难点](https://github.com/KieSun/Dream/issues/2)

## new

1. 新生成一个对象

2. 链接到原型

3. 绑定this

4. 返回新对象

   

在调用new的过程中会发生以上四件事情。

```javascript
function create(){
	//创建一个空对象
	let obj = new Object()
	//获得构造函数
	let Con = [].shift.call(arguments)
	//链接到原型
	obj.__proto__ = Con.prototype
	//绑定this，执行构造函数
	let result = Con.apply(obj,arguments)
	//确保new出来的是个对象
	return typeof result ==='object'?result:obj
}

```

对于实例对象来说，都是通过`new`产生的，无论是`function Foo()`还是`let a = {b:1}`。

对于创建一个对象来说，更推荐使用字面量的方式创建对象（无论性能还是可读性）。因为你使用`new Object()`的方式创建对象需要通过作用域链一层一层找到`Object`，但是你使用字面量的方式就没这个问题。

## instanceof

instanceof可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的prototype。

## this

this是很容易混淆的概念

```javascript
var obj = {   
    foo: function(){   
        console.log(this)   
    }   
}   
var bar = obj.foo  
obj.foo()  // 打印出的 this 是 obj
bar() // 打印出的 this 是 window 
```



### 函数调用



JS(ES5)里面有三种函数调用方式

```javascript
func(p1, p2)    
obj.child.method(p1, p2)   
func.call(context, p1, p2) *// 先不讲 apply*   
```

第三种调用方式,才是正常调用



其他两种都是语法糖,可以等价地变为`call`形式:

`func(p1,p2)` 等价于`func.call(undefined,p1,p2)`

`obj.child.method(p1,p2)` 等价于`obj.child.method.call(obj.child,p1,p2)`

至此函数调用只有一种形式:

**`func.call(context,p1,p2)`**

这样,this就好解释了this就是上面的context

this是你call一个函数时传的context,由于你从来不用call形式的函数调用,所以你一直不知道.

先看`func(p1,p2)`中的this如何确定:

```javascript
function func() {   
    console.log(this)   
}   
func()   
*// 等价于*   
func.call(undefined)    
*//可以简写为*   
func.call()   
*//按照说打印出来的this就是undefined,但是浏览器有一条规则*   
*/***   **   
如果你传的context是null或undefined,那么window对象就是默认的context(严格模式下默认context是undefined)*   
** 因此上面的打印结果是window.*   **/*   
```

因此上面打印的结果是window.如果希望这里是this不是window:

```javascript
 func.call(obj) //这里的this就是obj对象  


var obj = {
	foo:function(){
		console.log(this);
	}
}
var bar = obj.foo
obj.foo() //转换为obj.foo.call(obj) this就是这里的obj
bar()
//转换为 bar.call()
//由于没有传入context
//所以this就是undefined
//最后浏览器给你一个默认的this--window对象
```





### []**语法

```javascript
function fn() {   
    console.log(this);   
}   
var arr = [fn,fn2]   
arr[0] *//这里的this是什么?*   
```



我们可以把arr[0]() 想象为arr.0()

obj.child.method(obj.child,p1,p2)

arr.0.call(arr)

所以这里的this就是arr

小结

1. this就是你call一个函数时,传入的第一个参数
2. 如果你的函数调用不是call形式,请将其转换为call形式

## 闭包

闭包的定义很简单：函数A返回了函数B，并且B中使用了函数A的变量，函数B就被称为闭包。

## 深浅拷贝

```javascript
let a = {
	age:1
}
let b = a
a.age = 2
console.log(b.age)//2
```

从上述例子中我们可以发现，如果给一个变量赋值一个对象，那么两者的值会是同一个引用，其中一方改变，另一方也会相应改变。

通常在开发中我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个问题。

### 浅拷贝

#### Object.assign

```javascript
let a = {
	age:1
}
let b = Object.assign({},a)
a.age = 2
console.log(b.age)//1
```

#### ...

```javascript
let a = {
	age:1
}
let b = {...a}
a.age = 2
console.log(b.age)//1
```

#### Array

```javascript
var arr1 = [1,2],arr2 = arr1.slice()
console.log(arr1)//[1,2]
console.log(arr2)//[1,2]
arr2[0] = 3
console.log(arr1) //[1,2]
console.log(arr2) //[3,2]
```

#### JSON.parse(JSON.stringify(obj))

```javascript
var obj1 = {
	x:1,
	y:{
		m:1
	}
}
var obj2 = JSON.parse(JSON.stringify(obj1))
console.log(obj1)//{x:1,y:{m:1}}
console.log(obj2)//{x:1,y:{m:1}}
obj2.y.m = 2
console.log(obj1) //{x:1,y:{m:1}}
console.log(obj2) //{x:1,y:{m:2}}
```

> undefined 任意的函数以及symbol值,在序列化过程中会被忽略(出现在非数组对象的属性值中时)或者被转换成null(出现在数组中时)

```javascript
var obj1 = {
	x:1,
	y:undefined,
	z:function add(z1,z2) {
		return z1+z2
	},
	a:Symbol("foo")
}
var obj2 = JSON.parse(JSON.stringify(obj1))
console.log(obj1)//{x:1,y:undefined,z:f,a:Symbol(foo)}
console.log(JSON.stringify(obj1))//{"x":1}
console.log(obj2) //{x:1}
```

​	JSON.parse(JSON.stringify(obj)) 不适用场景

1. 会忽略undefined
2. 会忽略symbol
3. 会忽略function
4. 不能序列化函数
5. 不能解决循环引起的对象

### 深拷贝

#### loadsh深拷贝函数

`_.cloneDeep`

#### jquery的$.extends

#### MessageChannel

> 如果你所需拷贝的对象含有内置类型并且不包含函数,可以使用`MessageChannel`

```javascript
function structuralClone(obj) {
  return new Promise(resolve => {
    const {port1, port2} = new MessageChannel();
    port2.onmessage = ev => resolve(ev.data);
    port1.postMessage(obj);
  });
}

var obj = {a: 1, b: {
    c: b
}}
// 注意该方法是异步的
// 可以处理 undefined 和循环引用对象
(async () => {
  const clone = await structuralClone(obj)
})()
```



#### 循环引用拷贝

```javascript
function deepCopy(obj, parent = null) {
    // 创建一个新对象
    let result = {};
    let keys = Object.keys(obj),
        key = null,
        temp= null,
        _parent = parent;
    // 该字段有父级则需要追溯该字段的父级
    while (_parent) {
        // 如果该字段引用了它的父级则为循环引用
        if (_parent.originalParent === obj) {
            // 循环引用直接返回同级的新对象
            return _parent.currentParent;
        }
        _parent = _parent.parent;
    }
    for (let i = 0; i < keys.length; i++) {
        key = keys[i];
        temp= obj[key];
        // 如果字段的值也是一个对象
        if (temp && typeof temp=== 'object') {
            // 递归执行深拷贝 将同级的待拷贝对象与新对象传递给 parent 方便追溯循环引用
            result[key] = DeepCopy(temp, {
                originalParent: obj,
                currentParent: result,
                parent: parent
            });

        } else {
            result[key] = temp;
        }
    }
    return result;
}

var obj1 = {
    x: 1, 
    y: 2
};
obj1.z = obj1;

var obj2 = deepCopy(obj1);
console.log(obj1); //太长了去浏览器试一下吧～ 
console.log(obj2); //太长了去浏览器试一下吧～ 


```



## 模块化

### es6

> 在有Babel的情况下,可以直接使用es6的模块化

```javascript
//file a.js
export function a(){}
export function b(){}
//file b.js
export default function(){}
import {a,b} from './a.js'
import xxx from './b.js'
```

### CommonJS

> CommonJS 是Node独有的规范,在浏览器中使用就需要用到Browserify解析

```javascript
//a.js
module.export = {
	a:1
}
//or
exports.a = 1

//b.js
var module = require('./a.js')
module.a //log 1

```

> 在上述代码中，`module.exports` 和 `exports` 很容易混淆，让我们来看看大致内部实现

```javascript
var module = require('./a.js')
module.a
// 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，
// 重要的是 module 这里，module 是 Node 独有的一个变量
module.exports = {
    a: 1
}
// 基本实现
var module = {
  exports: {} // exports 就是个空对象
}
// 这个是为什么 exports 和 module.exports 用法相似的原因
var exports = module.exports
var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
};
```

再来说说 `module.exports` 和 `exports`，用法其实是相似的，但是不能对 `exports` 直接赋值，不会有任何效果。

对于 `CommonJS` 和 ES6 中的模块化的两者区别是：

- 前者支持动态导入，也就是 `require(${path}/xx.js)`，后者目前不支持，但是已有提案
- 前者是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响
- 前者在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想更新值，必须重新导入一次。但是后者采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化
- 后者会编译成 `require/exports` 来执行的

### AMD

> AMD 是有RequireJS提出的

```javascript
// AMD
define(['./a', './b'], function(a, b) {
    a.do()
    b.do()
})
define(function(require, exports, module) {
    var a = require('./a')
    a.doSomething()
    var b = require('./b')
    b.doSomething()
})
```



## 防抖

>在滚动事件中需要做个复杂计算或者实现一个按钮的防二次点击操作。这些需求都可以通过函数防抖动来实现。尤其是第一个需求，如果在频繁的事件回调中做复杂计算，很有可能导致页面卡顿，不如将多次计算合并为一次计算，只在一个精确点做操作。

## 节流

> 防抖动和节流本质是不一样的。防抖动是将多次执行变为最后一次执行，节流是将多次执行变成每隔一段时间执行。

## 继承

在ES5中，

```js
function Super(){}
Super.prototype.getNumber = function(){
    return 1
}
function Sub(){}
let s = new Sub()
Sub.prototype = Object.create(Super.prototype,{
    constructor:{
		value:Sub,//对象属性的默认值，默认值为undefined
        enumerable:false,//对象属性是否可通过for-in循环，flase为不可循环，默认值为true
        writable:true,//对象属性是否可修改,flase为不可修改，默认值为true
        configurable:true //能否使用delete、能否修改属性特性、或能够修改访问器属性，false为不可重定义，默认值为true
    }
})
```



ES6中，

```
class myDate extends Date{
	test(){
		reutrn this.getTime()
	}
}
let myDate = new MyDate()
myDate.test()
```



## call、bind、apply 的区别



## Promise

Promise是ES6新增的语法，解决了回调地狱的问题。

> 可以把Promise看成一个状态机.初始是pending状态,可以通过函数resolve和reject,将状态转变为resolved或者rejected状态,状态一单改变就不能再次变化

## Generator

> Generator 是 ES6 中新增的语法，和 Promise 一样，都可以用来异步编程

## Map、FlatMap、Reduce



## async 和await



## Proxy

> Proxy 是 ES6 中新增的功能，可以用来自定义对象中的操作

```js
let p = new Proxy(target, handler);
// `target` 代表需要添加代理的对象
// `handler` 用来自定义对象中的操作
```

可以很方便的使用 Proxy 来实现一个数据绑定和监听






## 0.1+0.2！=0.3

> 因为 JS 采用 IEEE 754 双精度版本（64位），并且只要采用 IEEE 754 的语言都有该问题。

## 正则表达式



## ES6

### let const

> let const 都是块级作用域,其有效范围仅在代码块中

### 箭头函数

#### 箭头函数与普通函数的区别

箭头函数时匿名函数,不能作为构造函数,不能使用new

```js
let FunConstructor = () => {
    console.log('lll');
}

let fc = new FunConstructor();
```

![](/1299975-20180320163758486-1808142372.png)

箭头函数不绑定arguments,取而代之用reset参数...解决

```js
function A(a){
  console.log(arguments);
}
A(1,2,3,4,5,8);  //  [1, 2, 3, 4, 5, 8, callee: ƒ, Symbol(Symbol.iterator): ƒ]


let B = (b)=>{
  console.log(arguments);
}
B(2,92,32,32);   // Uncaught ReferenceError: arguments is not defined


let C = (...c) => {
  console.log(c);
}
C(3,82,32,11323);  // [3, 82, 32, 11323]
```

箭头函数不绑定this,会捕获其所在的上下文的this值,作为自己的this值

```js
var obj = {
  a: 10,
  b: () => {
    console.log(this.a); // undefined
    console.log(this); // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
  },
  c: function() {
    console.log(this.a); // 10
    console.log(this); // {a: 10, b: ƒ, c: ƒ}
  }
}
obj.b(); 
obj.c();
```

箭头函数通过call()或apply()方法调用一个函数时,只穿入了一个参数,对this没有影响

```js
let obj2 = {
    a: 10,
    b: function(n) {
        let f = (n) => n + this.a;
        return f(n);
    },
    c: function(n) {
        let f = (n) => n + this.a;
        let m = {
            a: 20
        };
        return f.call(m,n);
    }
};
console.log(obj2.b(1));  // 11
console.log(obj2.c(1)); // 11
```

箭头函数没有原型属性

```js
var a = ()=>{
  return 1;
}

function b(){
  return 2;
}

console.log(a.prototype);  // undefined
console.log(b.prototype);   // {constructor: ƒ}
```

箭头函数不能当做Generator函数,不能使用yieid关键字

箭头函数的this永远指向其上下文的this,任何方法都不能改变其指向,如call() bind() apply()

普通函数的this指向调用它的那个对象



### 解构

### 扩展运算符(...)

### 字符串语法

### 数组去重







## 算法



## 工具函数片段

