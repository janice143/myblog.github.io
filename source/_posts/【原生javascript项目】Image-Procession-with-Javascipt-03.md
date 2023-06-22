---
title: 【原生javascript项目】Image Procession with Javascipt 03
date: 2021-11-13 11:43:56
tags: 原生javascript项目
categories: 30个原生javascript项目
---



### 引言

本文利用javascript更新css变量，实现一个简易的图像处理网页。对于图像处理部分，本网页可调节图像的边框宽度、模糊度以及边框颜色值。

网址为(https://janice143.github.io/imageProcessionwithJS/)

### 正文

#### 1 页面布局



页面分为三部分：标题、三个input组成的控件、图像

通过调节三个Input的值，可以实现对外边距、模糊值和颜色的改变。 

一、html代码

1 标题

```html
<h2>利用<span class="text-color">JS</span>更新CSS自定义变量</h2>
    
    <img src="https://source.unsplash.com/7bwQXzbF6KE/800x500">

</div>
```

2 三个input值

```html
<div class="controlers">
    <label for="spacing">外边距:</label>
    <input id="spacing" type="range" name="spacing" value="10" data-unit="px">

    <label for="blur">模糊值:</label>
    <input id="blur" type="range" name="blur" value="10" data-unit="px">

    <label for="color">底色:</label>
    <input id="color" type="color" name="color" value="#fecc00">
</div>
```

data-unit="px"是自己设置的dataset(数据集合)。

3 图像

```html
<img src="https://source.unsplash.com/7bwQXzbF6KE/800x500">
```

二、css代码

1 css变量

变量声明：在选择器里（任何选择器），变量名前面要加两根连词线（`--`）。本文里在:root根元素里声明，这是全局变量的声明方式，保证任何选择器都可以使用。变量名大小写敏感。

```css
img{
    margin-top:50px;
    width:600px;
    height: 375px;
    padding: var(--spacing);/* var函数获取变量 */
    background-color: var(--color);
    filter:blur(var(--blur));
}
```

读取变量：利用var()函数读取

```css
 :root{
     --color:#fecc00;
     --spacing:10px;
     --blur:10px;
}
```

css变量参考网址：（https://www.ruanyifeng.com/blog/2017/05/css-variables.html）

三、javascript代码

1 suffix中文意思为 后缀。在js代码中，设置了一个suffix变量，这是因为spacing和blur的值在css中需要加上px单位，而color的值没有，所以使用了||逻辑运算。

2 this.dataset需要注意一下，在html代码中，我们设置了自定义了data-unit数据，this.dataset会取出所有自定义的数据集，比如你可以在Html中设置data-key(回想起前几天的js程序）,data-name,data-poo任何数据名字，因为是自定义。this.dataset.unit是本文在html代码中提前设置好的。

3 document.documentElement用来获取当前文档的直接子节点，对于Html文档而言，一般是<html>。

4 style.setProperty(propertyName, value);给css样式某个属性名propertyName赋一个新值value。

5 `--${this.name}`为模板字面量，可以嵌入表达式的字符串字面量，${}表示一个占位符。一般的字符串是用单引号或者双引号标注的，这些字符串之间没有任何区别，而模板字面量则可以通过一个占位符来进行插值，模板字面量用反引号``实现。

6 箭头函数优点：剪短、this指向函数定义生效时所在对象。

函数声明：关键字、函数名、参数、函数体

```js
function functionName ([arg1 [,arg2 [...,argn]]]) {
  // functionBody 函数体
}
```

没有函数名时，函数为匿名函数

```js
const square = function (number) {
  return number * number;
};
// 相当于箭头函数：number=>number*number;
```

具名函数由函数名，而且函数内部可以代指本身（迭代函数使用）

```js
const factorial = function fac(n) {  return n < 2 ? 1 : n * fac(n - 1);};
console.log(factorial(3));
var x = square(4);// x 得到的值为16
```

箭头函数；如果函数体只有一个表达式，可以不用{}符号

```js
const fn = () => {
  // do something
};
```



```javascript
// 获取Input的value，并且将value赋值给css
const inputs = document.querySelectorAll('.controlers input');
function updateValue(){
    const suffix = this.dataset.unit || ''; // 给blur和spacing加单位
    document.documentElement.style.setProperty(`--${this.name}`,this.value + suffix);
}
inputs.forEach(input=>input.addEventListener('change',updateValue));
```

### 结论

完整代码放在了[Github](https://github.com/janice143/imageProcessionwithJS)上，如果读者有兴趣，不妨试一试。
