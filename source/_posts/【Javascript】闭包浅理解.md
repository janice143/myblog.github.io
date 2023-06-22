---
title: 【Javascript】闭包浅理解
date: 2022-04-12 21:30:29
tags: 'JavaScript知识点'
---

## 什么是闭包closure

先看下面这段代码：

```js
var name = 'Joey'
function printName(){
    console.log(name)
}
printName(); // Joey
```

这段代码最终会打印出`Joey`这个名字，这是JS语言的独特之处，就是函数内部可以读取到外部的变量。

在这段代码中，具有两个作用域`scope`，一个是整段代码处于一个作用域，另一个是function函数里面有一个作用域。函数内部可以访问到外部的变量`name`。

其实这个`printName`函数就是一个闭包。闭包就是能够访问其他函数内部变量的函数。但是这个闭包并不是我们经常说的闭包。在理解现在的闭包的概念之前，我们再看一段代码：

```js
var name = 'Joey'
function printName(){
    var age = 12;
    console.log(name)
}
console.log(age) // 报错
```

这段代码会报错，原因是因为在函数外部自然无法读取函数内的变量。

## 那如何在函数外部访问到函数内部的变量呢？

在函数内部在定义一个函数，然后返回该函数

```js
var name = 'Joey'
function printName(){
    var age = 12;
    function printAge(){
        console.log(age)
    }
    return printAge;
}
let age = printName();
age(); // 12

```

在这段代码中，因为函数内部可以访问到外部的变量，所以`printAge`函数可以访问到外部的`age`变量。因此，只要` return printAge`就可以在外部最外部的作用域中访问到`age`这个变量。

这段代码中`printAge`函数就是一个闭包，这也是我们现在认为的闭包。

## 闭包的概念

闭包就是能够读取其他函数内部变量的函数，简答来讲就是，闭包就是函数内部的函数。

## 闭包的作用

1. 读取函数内部的变量
2. 让函数内部的变量保存在内存中。函数内部的变量为局部变量，闭包的作用，就是把函数的内部的局部变量变成了全局变量。
