---
title: 【前端面试-JS】基础面试题
date: 2022-05-26 14:49:41
tags: js
categories: js
toc: true
---

# JS 基础面试

## 延迟加载js方式

- async

  -  后台下载

  -  执行会中断页面渲染

  -  下载好后立刻执行，没有顺序（谁先下载完谁先执行）

- defer 

  - 后台下载
  - 不会中断页面渲染（等页面渲染完后执行）
  - 顺次执行（多个js文件会按先后顺序执行）

  

![async vs defer attributes - Growing with the Web](http://www.growingwiththeweb.com/images/2014/02/26/async-vs-defer-twitter.png)



## 数据类型

### 基本类型

string, number, boolean, undefined, null, symbol（, bigint)

### 引用类型

object(包括数组等)



### 数据类型的隐式转换

> true+1 // 2
>
> 'name' + true // 'nametrue' （字符串和其他类型相加，都是转化成字符串）
>
> undefined + 1 // NaN（是一个number)
>
> typeof null // object



## null 和undefined 区别

`undefined`

一个变量最原始的状态；此处应该有一个值，但是还没有定义；转为数值时为NaN

- 变量被声明了，但没有赋值
- 调用函数时，但没有传递实参
- 访问对象上不存在的属性或者未定义的变量
- 函数没有返回值时，默认返回undefined

`null`

 表示一个被人为的重置为空对象；该处的值为空；转为数值时为0

典型用法：

- 作为函数的参数，表示该函数的参数不是对象。

- 作为对象原型链的终点。



在实际使用过程中，为了保证变量所代表的语义，不要对一个变量显式的赋值 undefined，当需要释放一个对象时，直接赋值为 null 即可。



## == 和 === 区别

== 

比较的是值

===

不仅比较值，还比较类型



## 微任务和宏任务

### 背景知识

js是单线程，但是有异步处理。js是同步任务执行完毕后再去执行异步任务。

### 什么是宏任务和微任务

微任务： `promise.then`

宏任务：定时器，用户交互事件，ajax，读取文件



### js异步执行原则

1 同步

2 `process.nextTick`

3 异步

3.1 微任务： `promise.then`

3.2 宏任务：定时器，用户交互事件，ajax，读取文件

4 `setImmediate`方法

> 注意：
>
> 同步的任务都执行完后，才会执行事件循环的内容。
>
> 要执行宏任务，前提是清空所有微任务。



js代码执行流程： 同步执行完后 => 事件循环（宏任务和微任务） => 微任务 => 宏任务 => 微任务 ... 

<img src="/myblog.github.io/images/js_question_eventloop.png" alt="在这里插入图片描述" style="zoom: 80%;" />

进入事件循环：请求，定时器，事件...



## JS作用域

1 除函数外，js没有块级作用域

2 作用域链：内部可以访问到外部的变量，但是外部不能访问内部的变量。

注意：如果有内部，优先查找内部，如果没有，就查找外部

3 声明变量没有用var let const，那这个变量就是全局的(window的)

4 js变量提升机制

5 优先级： 声明变量 > 声明普通函数 > 参数 > 变量提升

### var

- 变量提升
- 变量覆盖
- 没有块级作用域

### const

- 声明之后必须赋值
- 定义的值不可修改
- 其他和let一样

### let

- 没有变量提升
- 不允许重复声明
- 所声明的变量，只在let命令所在的代码块内有效

### 暂时性死区

使用 `let` 或 `const` 声明的变量，在声明赋值没有到达之前，访问该变量都会导致报错，就连一直以为安全的 `typeof` 也不再安全

```js
{  //函数作用域开始，TDZ开始
    console.log(temp)  //ReferenceError: temp is not defined
    //声明
    let temp  
    console.log(temp)  //ReferenceError: Cannot access 'temp' before initialization
    //赋值
    temp = 345  //TDZ结束
    console.log(temp)  //345
    //块级作用域结束
}
//在块级作用域外访问
console.log(temp)  //ReferenceError: temp is not defined

```

**注意**：任何在暂时性死区内访问变量的企图都会导致 **运行时错误**（Runtime Error）。只有执行到变量的声明语句时，该变量才会从暂时性死区内被移除并可以安全使用。

## JS对象

1 对象通过new操作符构建的，所以对象不相等(=== 是false)

2 对象是引用类型（地址）

3 对象的key是字符串类型

```js
a = { aa: 1 }
b = { bb: 2 }
c = { cc: 3 }
a[b] = '123'
a[c] = '456'

console.log(a[b]) // 456

b和c是对象，当成为a的属性的时候，会变成字符串，值是[object object]。c会覆盖b的值。
相当于a = {
	'[object object]':456,
	aa:1
} 
```

4 对象如何找属性或者方法（原型链）

对象本身找 => 构造函数找 => 对象原型找 `__proto__`=> 构造函数原型找  prototype => 对象上一层原型查找 

###  instanceof

判断对象的类型，原理是通过判断对象的原型链中是不是能找到构造函数的 `prototype`。

> 语法 `object instanceof constructor`

```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
const auto = new Car('Honda', 'Accord', 1998);

console.log(auto instanceof Car);
// expected output: true

console.log(auto instanceof Object);
// expected output: true
```



## 判断变量是否是数组

1 `isArray`

```js
const arr = [1,2,3]
console.log(Array.isArray(arr))
```

2 `instanceof`

```js
const arr = [1,2,3]
console.log(arr instanceof Array)
```

3 原型`prototype`

```js
const arr = [1,2,3]
Object.prototype.toString.call(arr).indexOf('Array') > -1
```

4 `isPrototypeOf`

```js
const arr = [1,2,3]
Array.prototype.isPrototypeOf(arr)
```

5 `constructor`

```js
const arr = [1,2,3]
arr.constructor.toString().indexOf('Array') > -1
```



## new 操作符具体做了什么

1 创建了一个空对象

2  将空对象的原型，指向构造函数的原型

fn.prototype === new fn().`__proto__`

3 将空对象作为构造函数的上下文（改变this的方向）

```js
function Foo(){
	this.name = 'aa' // this指向window
}
console.log(new Foo()) // Foo {name: 'aa'} this指向new出来的对象

```

4 对构造函数有返回值的处理判断

如果返回的是基本类型，则没有影响，如果是对象，则就是返回值

```js
function Foo(){
	this.name = 'aa' // this指向window
    return 11
    // return {}
    // return [1,2,3]
}
console.log(new Foo()) // Foo {name: 'aa'} 返回的11没有影响

```

###  手写new

```js
function create(fn,...args){
    // 创建一个空对象
	const obj = {}
    // 将空对象的原型，指向构造函数的原型
    Obeject.setPrototypeOf(obj,fn.prototype)
    // 将空对象作为构造函数的上下文(改变this指向)
    const result = fn.apply(obj,args)
    // 对构造函数的有返回值的处理判断
    return result instanceof Object ? result : obj
    
}
```



## 闭包

### 1 闭包是什么

闭包是一个函数加上创建函数的作用域链接，闭包“关闭”了函数的自由变量。

通俗的描述：函数嵌套函数，内部函数就是闭包

正常情况下，函数执行完后，内部的变量会销毁，但是有了闭包后，函数内部的变量就可以保存。等内部的函数（闭包）执行外后，该变量才会被销毁。

```js
const lis = document.getElmentsByTagName('li') // 加上lis长度为5
for(var i=0;i<lis.length;i++){
	lis[i].onclick = function(){
		alert(i)
	}
}
// 每次点击列表的某个元素，都显示5
// 下面看加了闭包
for(var i=0;i<lis.length;i++){
    (function(i){
        lis[i].onclick = function(){
            alert(i)
        }
    })(i)
}
// 每次点击列表的某个元素，对应显示下标0,1,2,3,4
```

### 2 闭包解决什么问题

- 延长变量的生命周期（原理是内部函数可以访问到外部函数的局部变量）

- 实现模块化功能（创建私有环境）

  vue 的组件中的data是一个函数就是利用闭包的思想，可以保护组件的数据不被其他组件影响

  ```js
  const cal = (function(){
      let a = 10, b = 20
      function add(){
          reurn a+b
      }
      function sub(){
          return a-b
      }
      return {
          add,
          sub
      }
  })()
  
  console.log(cal.add()) // 30
  console.log(cal.sub()) // -10
  ```

### 3 闭包缺点

变量驻留在内存中，造成内存损耗

## 原型对象

### 原型

每个对象都有原型对象，原型对象上的所有属性和方法，该对象都能访问。

```js
let cat = {
	name:"喵喵"
}
cat.__proto__.eat = function(){
	console.log("吃鱼")
}
cat.eat() // 吃鱼
```

### 获取原型的方法

- 通过对象的`__proto__`

- 通过构造函数（或者类）的`prototype`

  new 创建的是一个对象。

```js
function Cat(name,age){
    this.name = name
    this.age = age
}
let cat = new Cat("喵喵",2)
Cat.prototype.eat = function(){
	console.log("吃鱼")
}
cat.eat() // 吃鱼
```

### 类与继承

1 类

ES5创建对象用构造函数，ES6创建对象用`class`

```js
class Cat{
	constructor(name,age){
        this.name = name
    	this.age = age
    }
}
Cat.prototype.eat = function(){
	console.log("吃鱼")
}
let cat = new Cat("喵喵",2)
cat.eat() // 吃鱼
```

2 继承

- ES6

```js
class User{
	constructor(username,password){
        this.username = username
    	this.password = password
    }
    login(){
    	console.log("log in")
    }
}
class Admin extend User{
	deletePerson(){
		console.log("删除了一个用户")
	}
}

let admin = new Admin()
admin.login() // log in
```

- ES5： prototype

```js
function User(username,password){
	this.username = username
	this.password = password
    this.login(){
    	console.log("log in")
    }
}
function Admin(){
	this.deletePerson(){
		console.log("删除了一个用户")
	}
    
}
Admin.prototype = new User()
let admin = new Admin()
admin.login() // log in
```



### 原型链

1. 原型链是什么

   一个对象有原型，其原型又是一个对象，该对象也有原型，可以看，一直往上，有一条原型链存在。（最顶端是`Obejct.prototype`)

2. 解决什么问题

   对象共享属性和共享方法

3. 谁有原型

   函数：prototype

   对象：`__proto__`

4. 对象查找属性或者方法的顺序

   对象本身找 => 构造函数找 => 对象原型找 `__proto__`=> 构造函数原型找  prototype => 对象上一层原型查找 



### JS继承

1 ES6

2 原型链

3 构造函数继承

4 组合式继承

## call, apply, bind区别

### 共同点：功能一致

- 改变this指向，
- 语法：函数.call() 、函数.apply()、函数.bind()

### 区别

- call, apply可以立即执行，bind不会立即执行（返回的是一个函数）
- 参数：apply第二个参数是数组，call和bind有多个参数是需要挨着写(arg1,arg2,arg3,...)

### 场景

- 继承：子类可以继承父类的方法

```js
function Animal(){
    this.eat = function(){
        console.log("吃东西")
    }
}
function Cat(){
    Animal.call(this)
}

let cat = new Cat()
cat.eat() // 吃东西
```

- 求最大值

```js
const arr = [1,2,3,4,5,6,7]
console.log(Math.max.apply(null,arr)) // 求最大值
// Math.max(...arr)
```

2 bind

```js
const bnt = document.getElmentById('btn')
const hls = document.getElmentById('hls')
bnt.onclick = function(){
    console.log(this.id)
}.bind(hls)
```

### 手写bind, call, aplly

- 手写`bind`

  ```js
  Function.prototype.myBind = function(obj,...args){
      return (...rest)=>{
          this.call(obj,...args,...rest)
      }
  }
  ```

- 手写`call`

  ```js
  Function.prototype.myCall = function (obj, ...args) {
      // this表示f.myCall的f函数
      obj.fn = this
      const result = obj.fn(...args)
      // 删除 fn
      delete obj.fn
      return result
  }
  ```

- 手写`apply`

```js
Function.prototype.myApply = function (obj, args) {
    // this表示f.myCall的f函数
    obj.fn = this
    const result = obj.fn(...args)
    // 删除 fn
    delete obj.fn6ew
    return result
}
```



## this指向

### 1 普通函数或作为对象属性

函数作为对象的属性被调用的时候，其`this`指向调用该函数的对象，否则其`this`指向`window`

```js
const fn = function () {
  console.log(this); // 指向window
};

const obj = { name: 'OBJ', fn };

fn(); // window（相当于window.fn()） 

obj.fn();// {name: 'OBJ', fn: function() {console.log(this)}} // obj

const fn1 = obj.fn; 
fn1(); // window
```

### 2 事件绑定

在进行事件绑定的时候，事件绑定函数中的`this`是绑定事件的元素

### 3 构造函数(`new Fn`)

构造函数(`new Fn`)执行，函数中的`this`是当前类的实例

```js
var x = 100;
const Fn = function () {
  this.x = 200;
  console.log(this.x);
};

const fn = new Fn(); // 200

```

### 4 箭头函数

箭头函数中没有自身的`this`，所用到的`this`都是其最近父级上下文中的`this`

```js
const fn = function () {
  console.log(this); //  {x:100, fn: function() {...}} // obj
  setTimeout(() => {
    console.log(this); // window 定时器是全局的
  }, 1000);
  setTimeout(function () {
    console.log(this); // {x:100, fn: function() {...}} // obj
  });
};

const obj = { x: 100, fn };

obj.fn();

```

###  5 `call/apply/bind`改变`this`指向

`call/apply/bind`传入的第一个参数即为函数的`this`

```js
var x = 100;
const obj = { x: 200, y: 200 };
const fn = function () {
  console.log(this.x);
};

fn(); // 100
fn.call(obj); // 200
fn.apply(obj); // 200

const fixedThisFn = fn.bind(obj);
fixedThisFn(); //200

```

- `call`在执行时，第一个参数为`this`指向，之后的参数为`fn`执行时的参数

- `apply`在执行时，第一个参数为`this`指向，之后的参数为`fn`执行时的参数组成的数组，数组的每一项会和`fn`的每一个参数进行对应
- `bind`在执行时，第一个参数为预先传入`this`指向，之后的参数为实际调用`fn`前预先传入的参数，返回值为一个函数`fixedThisFn`，`fixedThisFn`内部会调用`fn`并指定其`this`指向



## `promiss`和`async`函数

### 1 `promiss`用法

```js
let p = new Promiss((resolve)=>{
	console.log(11)
    resolve(22)
})
// .then的data是resolve里的，只有调用resolve才会执行.then
p.then((data)=>{
    console.log(data)
})
```

### 2 `async`函数

```js
async functin fun(){
    return 1
}
console.log(fun()) // 返回一个promiss

// ------------- 上面这代码和下面的代码等效 ----------------

function fun(){
    return new Promiss((resolve)=>{
        resolve(1)
    })
}

```

### 3 `async await`

`await` 后面是一个`promiss`对象，可以拿到`resolve`的值

```js
let p1 = new Promiss((resolve)=>{
	resolve(1)
})
let p1 = new Promiss((resolve)=>{
	resolve(2)
})
async function fun(){
    let a = await p1
    let b = await p2
    console.log(a,b) // 1,2
}
```

### 4 [手写`promiss`](https://juejin.cn/post/7100754145891581983#heading-2)

一个简单的 `Promise` 的粗糙实现，关键点在于

1. 有三种状态：`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）
2. `Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。
3. 设置两个队列，`onResolvedCallbacks`，`onRejectedCallbacks`，用于存放成功和失败的回调函数，当状态发生改变时依次从数组中取出执行。
4. 利用`setTimeout`实现`resolve`和`rejected`异步
5. `then`返回`promiss`

```js
class Prom {
    constructor(fn) {
        this.state = "PENDING"
        this.value = undefined
        this.reason = undefined
        // 保存尚未fulfilled的then中的回调函数(异步)
        this.onResolvedCallbacks = [];
        // 保存尚未rejected的then中的回调函数(异步)
        this.onRejectedCallbacks = [];
        const resolve = value => {
            setTimeout(() => {
                this.state = "RESOLVED"
                this.value = value
                // 判断成功回调是否存在，如果存在就调用
                // 循环回调数组. 把数组前面的方法弹出来并且直接调用
                while (this.onResolvedCallbacks.length) {
                    this.onResolvedCallbacks.shift()(value)
                }
            })
        }
        const reject = reason => {
            setTimeout(() => {
                this.state = "REJECTED"
                this.reason = reason
                while (this.onRejectedCallbacks.length) {
                    this.onRejectedCallbacks.shift()(reason)
                }
            })
        }
        fn(resolve, reject)
    }

    then(onFulfilled) {
        // 当传入的then回调函数为空的时候。。创建对应的空函数
        onFulfilled = typeof onFulfilled === "function" ? onFulfilled : () => { }
        if (this.state === "RESOLVED") {
            const result = onFulfilled(this.value);
            // 需要返回一个 Promise
            // 如果状态为 resolved，直接执行
            return Prom.resolve(result);
        } 
        // then方法执行时如果状态是等待态，就将其回调函数存入对应数组
        if (this.state === "PENDING") {
            return new Prom((resolve, reject) => {
                // 新增等待态判断，此时异步代码还未走完，回调入数组队列
                this.onResolvedCallbacks.push(onFulfilled)
            });
        }
    }
    catch(onRejcted) {
        onRejcted = typeof onRejcted === "function" ? onRejcted : () => { }
        if (this.status === "REJECTED") {
            const reason = onRejcted(this.reason);
            return Prom.reject(reason);
        }
        if (this.status === "PENDING") {
            return new Prom((resolve, reject) => {
                // 新增等待态判断，此时异步代码还未走完，回调入数组队列
                this.onRejectedCallbacks.push(onRejcted)
            });

        }
    }
}

```



## 可枚举、不可枚举

对象的属性所有的特性有以下几种：

```markdown
value:属性的值
writable:如果为false，属性的值就不能被重写。
get: 一旦目标属性被访问就会调回此方法，并将此方法的运算结果返回用户。
set:一旦目标属性被赋值，就会调回此方法。
configurable:如果为false，则任何尝试删除目标属性或修改属性性以下特性（writable, configurable, enumerable）的行为将被无效化。

### enumerable:是否能在for...in循环中遍历出来或在Object.keys中列举出来。
```

其中`enumerable`表示可枚举特性(true为可，false为不可)。



## 对象拷贝

基本类型存储在栈内存中，对象存储在堆内存中

### 1 浅拷贝

```js
function copy(obj){
	let newObj = {}
	for(let i in obj){
		newObj[i] = obj[i]
	}
	return newObj
}
```

缺点：对象的属性只能是基本类型，不能是对象，所以该方法只能进行浅拷贝

### 2 深拷贝（对象里面包含对象的拷贝）

```js
function copy(obj){
	let newObj = {}
	for(let i in obj){
		if(obj[i] instanceof Object){
			newObj[i] = copy(obj[i])
		}else{
			newObj[i] = obj[i]
		}
	}
	return newObj
}
```

### 3 对象的拷贝

- 方法一 Object.assign()

  ```js
  const person = {
     name: 'Wes Bos',
     age: 80
   };
  const cap2 = Object.assign({}, person, { number: 99, age: 12 });
  console.log(cap2); // Object {name: "Wes Bos", age: 12, number: 99}
  ```

- 方法二 JSON 转换

  ```js
  const wes = {
    name: 'Wes',
    age: 100,
    social: {
      twitter: '@wesbos',
      facebook: 'wesbos.developer'
    }
  };
  const dev2 = JSON.parse(JSON.stringify(wes));
  console.log(dev2);
  ```

## 防抖节流

### 防抖

防止触发事件过于频繁，只执行最后一次

#### 1 手写防抖

```js
let inp = ducoment.querySelector('input')
inp.onchange = debounce(function(){
	console.log(this.value)
},500)

function debounce(fn,delay){
	let t = null
    return function(){
        if(t !== null){
            clearTimeout(t)
        }else{
            t = setTimeout(()={
                fn.call(this)
            },delay)
        }
    }
}
```

#### 2 防抖应用场景

1. 登录、发短信等按钮避免用户点击太快，以致于发送了多次请求，需要防抖
2. 调整浏览器窗口大小时，resize 次数过于频繁，造成计算过多，此时需要一次到位，就用到了防抖
3. 文本编辑器实时保存，当无任何更改操作一秒后进行保存

### 节流

控制执行次数

#### 1 手写节流

```js
let inp = ducoment.querySelector('input')
window.onscroll = throttle(function(){
    console.log("滚动")
},delay)

function throttle(fn,delay){
	let flag = true
    return function(){
        if(flag){
            setTimeout(()={
                fn.call(this)
	            flag = true
            },delay)
        }
    	flag = false
    }
}
```

#### 2 节流应用场景

1. `scroll` 事件，每隔一秒计算一次位置信息等
2. 浏览器播放事件，每个一秒计算一次进度信息等
3. input 框实时搜索并发送请求展示下拉列表，每隔一秒发送一次请求 (也可做防抖)

### 总结 (简要答案)

- 防抖：防止抖动，单位时间内事件触发会被重置，避免事件被误伤触发多次。**代码实现重在清零 `clearTimeout`**。防抖可以比作等电梯，只要有一个人进来，就需要再等一会儿。业务场景有避免登录按钮多次点击的重复提交。
- 节流：控制流量，单位时间内事件只能触发一次，与服务器端的限流 (Rate Limit) 类似。**代码实现重在开锁关锁 `timer=timeout; timer=null`**。节流可以比作过红绿灯，每等一个红灯时间就可以过一批。



- **debounce:** Grouping a sudden burst of events (like keystrokes) into a single one.
- **throttle:** Guaranteeing a constant flow of executions every X milliseconds. Like checking every 200ms your scroll position to trigger a CSS animation.



## ES6 filter函数

```js
const a = [1,2,3,4,5]
// cur 当前值； idx 当前值的下标； arr 数组对象
const b = arr.filter((cur,idx,arr)=>{
	return cur>3
})
console.log(a) //12345
console.log(b) // 45
```

## `forEach` 和`map`区别

1.  `forEach`
    - 没有返回值
    - 不会被break打断
    - 遍历的是`value`
2.  `map`
    - 有返回值，返回是数组，默认返回值是undefined
    - 不会被break打断
    - 遍历的是`value`和`key`

## ES6 解构

### 数组结构

```js
let list = [3, 5]
let first = list[0]
let second = list[1]

let [first, second] = list;

// 解构中交换数据
let car1 = 'bmw';
let car2 = 'audi'
[car2, car1] = [car1, car2]
```

### 对象解构

```js
const family = {
    father: ''
    mother: ''
}
const { father, mother } = family;

// 解构对象并重命名变量
const { father: f, mother:m } =  {father: '1',mother: '2'}
console.log(f); // '1'

// 函数解构
const family = {
    father: 'baba',
    mother: 'mama'
}
function log({ father }) {
    console.log(father)
}
log(family) // baba
```



## 正则表达式

### 常用的正则表达式有哪些？

```javascript
// （1）匹配 16 进制颜色值
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;

// （2）匹配日期，如 yyyy-mm-dd 格式
var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;

// （3）匹配 qq 号
var regex = /^[1-9][0-9]{4,10}$/g;

// （4）手机号码正则
var regex = /^1[34578]\d{9}$/g;

// （5）用户名正则
var regex = /^[a-zA-Z\$][a-zA-Z0-9_\$]{4,16}$/;
```




## 虚拟列表





##  类数组转化为数组的方法

题目描述:类数组拥有 length 属性 可以使用下标来访问元素 但是不能使用数组的方法 如何把类数组转化为数组?

实现代码如下:

```javascript
const arrayLike=document.querySelectorAll('div')

// 1.扩展运算符
[...arrayLike]
// 2.Array.from
Array.from(arrayLike)
// 3.Array.prototype.slice
Array.prototype.slice.call(arrayLike)
// 4.Array.apply
Array.apply(null, arrayLike)
// 5.Array.prototype.concat
Array.prototype.concat.apply([], arrayLike)
```



## 为什么0.1+0.2 ! == 0.3，如何让其相等  

在开发过程中遇到类似这样的问题：

```javascript
let n1 = 0.1, n2 = 0.2
console.log(n1 + n2)  // 0.30000000000000004
复制代码
```

这里得到的不是想要的结果，要想等于0.3，就要把它进行转化：

```javascript
(n1 + n2).toFixed(2) // 注意，toFixed为四舍五入
复制代码
```

`toFixed(num)` 方法可把 Number 四舍五入为指定小数位数的数字。那为什么会出现这样的结果呢？

计算机是通过二进制的方式存储数据的，所以计算机计算0.1+0.2的时候，实际上是计算的两个数的二进制的和。0.1的二进制是`0.0001100110011001100...`（1100循环），0.2的二进制是：`0.00110011001100...`（1100循环），这两个数的二进制都是无限循环的数。那JavaScript是如何处理无限循环的二进制小数呢？

一般我们认为数字包括整数和小数，但是在 JavaScript 中只有一种数字类型：Number，它的实现遵循IEEE 754标准，使用64位固定长度来表示，也就是标准的double双精度浮点数。在二进制科学表示法中，双精度浮点数的小数部分最多只能保留52位，再加上前面的1，其实就是保留53位有效数字，剩余的需要舍去，遵从“0舍1入”的原则。

根据这个原则，0.1和0.2的二进制数相加，再转化为十进制数就是：`0.30000000000000004`。

































