---
title: 【前端面试基础--HTML】一键复制功能 02
date: 2022-03-25 10:28:05
tags: HTML
categories: 前端面试基础
---

> 作者：©[Iaineisalsoyan ](https://github.com/janice143?tab=repositories)
> 嘿，我又开始新的系列学习了！😊😊😊（上一系列的学习是30 天原生js编程挑战）。这次我开始准备面试题库了，和我一起进步吧！哦，对了，本系列的学习来源是 [山月大佬的博客](https://q.shanyue.tech/fe/)，他非常用心得整理了一份大厂面试题的项目，推荐大家去看看！
>
> Have fun with the website! ♪(^∇^*)



## 一键复制功能描述

在一些博客网站中，可以看到代码区域的右上角会有一个复制的按钮，点击之后，可以迅速将代码框中的代码复制到剪切板上。



## 解决方案

### 1`Clipboard` API

```js
navigator.clipboard.writeText(text);
```

### 2 选中+复制

对于不支持 `Clipboard API` 的浏览器，使用选中+复制的API进行复制。

- #### 选中 Selection API+Range API

```js
const selection = window.getSelection();
const range = document.createRange();

// RangeAPI: 制造区域
range.selectNodeContents(element);

// Selection: 选中区域
selection.addRange(range);

selectedText = selection.toString();
```

- #### 复制: `execCommand`

```js
document.execCommand("copy");
```

