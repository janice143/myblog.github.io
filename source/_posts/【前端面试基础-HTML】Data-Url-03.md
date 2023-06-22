---
title: 【前端面试基础--HTML】Data Url 03
date: 2022-03-25 11:19:41
tags: HTML
categories: 前端面试基础
---

> 作者：©[Iaineisalsoyan ](https://github.com/janice143?tab=repositories)
> 嘿，我又开始新的系列学习了！😊😊😊（上一系列的学习是30 天原生js编程挑战）。这次我开始准备面试题库了，和我一起进步吧！哦，对了，本系列的学习来源是 [山月大佬的博客](https://q.shanyue.tech/fe/)，他非常用心得整理了一份大厂面试题的项目，推荐大家去看看！
>
> Have fun with the website! ♪(^∇^*)

## 什么是Data URL?

Data URL 是将图片转换为 base64 直接嵌入到了网页中，使用`<img src="data:[MIME type];base64"/>`这种方式引用图片，不需要再发请求获取图片

## 缺点

- base64 编码后的图片会比原来的体积大三分之一左右。
- Data URL 形式的图片不会缓存下来，每次访问页面都要被下载一次。可以将 Data URL 写入到 CSS 文件中随着 CSS 被缓存下来。
