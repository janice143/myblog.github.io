---
title: 【前端面试基础——HTML】图片懒加载 01
date: 2022-03-24 19:08:14
tags: HTML
categories: 前端面试基础
---

> 作者：©[Iaineisalsoyan ](https://github.com/janice143?tab=repositories)
> 嘿，我又开始新的系列学习了！😊😊😊（上一系列的学习是30 天原生js编程挑战）。这次我开始准备面试题库了，和我一起进步吧！
>
> Have fun with the website! ♪(^∇^*)
>



## 什么是图片懒加载

浏览网页的时候，滑动页面到能看到图片的时候再加载图片。

该功能实际可以拆分成两点：

1. 判断图片出现在了当前视口（因为这个时候需要显示图片）
2. 控制图片的加载



## 一些思考

🤔 类似功能在JS30的项目里做过。参考[【原生javascript项目】Slide in on scroll 13](https://janice143.github.io/myblog.github.io/2022/01/30/%E3%80%90%E5%8E%9F%E7%94%9Fjavascript%E9%A1%B9%E7%9B%AE%E3%80%91Slide-in-on-scroll-13/)

我们现在回忆一下该项目的技术点。当时做该项目的时候，主要通过Scroll事件+位置计算实现了，另外加了函数防抖功能。其中位置计算主要包括屏幕高度（视口高度）、屏幕滚定位置、图片顶部距页面距离、图片半高、图片底部位置。

当屏幕滚动位置+屏幕高度之和 > 图片顶部距页面距离+半高之和，移入图片；当屏幕滚动位置<图片底部距离，移出图片。

- window的scroll事件
  - `window.addEventListener('scroll')`
- 一些位置（像素值）
  - `window.scrollY`  文档当前垂直滚动的像素数
  - `window.innerHeight` viewport部分的高度
  - `sliderImage.offsetTop` 当前元素顶部相对于其 offsetParent 元素的顶部的距离
- `debounce` 的作用（函数防抖）
  -  降低事件监听的频率，使用了 Lodash 中的 debounce 方法



## 解决方案

🔖🔖🔖  速看在这里！！！

| 判断图片出现在当前视口                           |
| ------------------------------------------------ |
| `scrollY`, `offsetTop`,`innerHeight`+ Scroll事件 |
| getBoundingClientRect API + Scroll事件           |
| **控制图片的加载**                               |
| `DataSet` API                                    |
| **一步到位的方法**                               |
| `IntersectionObserver` API                       |



### 1 判断图片出现在当前视口

- 位置计算 + Scroll事件

> 这里出现的意思可以特指能看到图片一半的高度。

```js
const slideInAt = (window.scrollY + window.innerHeight);
const imageBottom = sliderImage.offsetTop + sliderImage.height;
const isHalfShown = slideInAt > (sliderImage.offsetTop + sliderImage.height / 2);
const isNotScrolledPast = window.scrollY < imageBottom;
```

知识点：`scrollY`, `offsetTop`,`innerHeight`



- getBoundingClientRect API + Scroll事件

**该方法返回元素的大小及其相对于视口的位置。**有`top`,`left`,`bottom`,`right`等属性。

```js
img.getBoundingClientRect().top < document.documentElement.clientHeight; // 视口高度
```

> the `document.documentElement` property gives you the `html` element, while the `document.body` property gives you the `body` element.

### 2 控制图片的加载

- `DataSet` API

```html
<img data-src="shanyue.jpg" />
```

首先设置一个临时 Data 属性 `data-src`，控制加载时使用 `src` 代替 `data-src`，可利用 DataSet API 实现

```html
img.src = img.datset.src
```

### 3 一步到位的方法

- `IntersectionObserver` API

异步监听元素是否到了当前视口或者其他元素

一个能够监听元素是否到了当前视口的事件，一步到位

```js
const observer = new IntersectionObserver((changes) => {
  // changes: 目标元素集合
  changes.forEach((change) => {
    // intersectionRatio
    if (change.isIntersecting) {
      const img = change.target;
      img.src = img.dataset.src;
      observer.unobserve(img);
    }
  });
});

observer.observe(img);
```

- `LazyLoading` 属性

```html
<img src="shanyue.jpg" loading="lazy" />
```

不过目前浏览器兼容性不太好。



### 4 其他优化

- `window.scroll` 事件可以加**防抖节流**。一般使用 `lodash.throttle` 。

```js
_.throttle(func, [(wait = 0)], [(options = {})]);
```
