---
title: '【JavaScript】apply,call,bind区别'
date: 2022-04-13 18:25:22
tags: 'JavaScript知识点'
---

## 三者的用法

```js
obj = {
    name:'Joey',
    age:12,
}
function printObj(name1,name2,name3){
    console.log(this.name,name1,name2,name3)
}
printObj.call(obj,'Tom','Ben','Jack')
printObj.apply(obj,['Tom','Ben','Jack'])
printObj.bind(obj)('Tom','Ben','Jack')
```

call, apply, bind都是函数的原型函数，任何函数都有这三个方法。

在上述代码中，call、apply和bind方法都将this指向了obj对象。

## call和apply的区别

```js
fun.call(thisArg,arg1,age2,...)
fun.apply(thisArg,[argsArray])
```

**参数**
**thisArg** ：在fun函数运行时指定的this值。

**arg1,age2,...**： 指定的参数列表。

**argsArray**：可选。一个数组或者类数组对象，其中的数组元素将作为单独的的参数传给fun函数。

所以call和apply的唯一区别在于第二个参数，前者是参数列表，后者是一个数组或者类数组

## bind

bind方法返回的是一个函数。和call相似，传入的第二个参数是一个参数列表。

## apply、call、bind总结

apply、call、bind 三者都是用来改变函数this对象的指向。
 apply、call、bind 三者第一个参数都是this要指向的对象，也就是指定的上下文。
 apply、call、bind 三者都可以利用后续参数传参。

bind是返回对应的函数，便于稍后调用。
 apply、call 则是立即调用。

## 面试题： js 中如何实现 bind

问题描述：编写一个fakeBind函数，实现功能和bind一样。

```js
function f(b) {
  console.log(this.a, b);
}

//=> 3, 4
f.fakeBind({ a: 3 })(4);
```

解决：

```
Function.prototype.fakeBind(obj,...args){
 return (...rest)=>{
  this.call(obj,...args,...rest)
 }
}
```

## 📖📖📖 参考博客

在网上查找资料的时候，觉得这篇[博客]()不错，特地小抄了一下。
