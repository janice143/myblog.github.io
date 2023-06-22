---
title: 【ejs模板引擎】第一次用ejs
date: 2022-04-15 21:45:42
tags: ejs.js
categories: ejs.js
---

## 1 ejs是什么

一套简单的模板语言，可利用普通的 JavaScript 代码生成 HTML 页面

#### 📖📖📖  ejs.js文档

官方文档：https://ejs.bootcss.com/#install

## 2 入门

### 1 在**浏览器 JS 环境**中如何使用

1.1 [下载到ejs的js文件](https://github.com/mde/ejs/releases/latest)

2.2 新建html文件，html内容如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
  <div id ="app"></div>
</body>
<script type="text/javascript" src="ejs.min.js"></script>
<script>
  let people = ['geddy', 'neil', 'alex'],
    html = ejs.render('<%= people.join(", "); %>', {people: people});
/* 将写好的ejs进行渲染 */
  document.getElementById('app').innerHTML = html;

</script>
</html>
```

说明：需要引入`ejs`的js文件， `ejs.render`会返回一个字符串，将字符串挂载到页面中即可显示出来。 

`ejs.render`的语法是`ejs.render(str, data, options);`str是一个模板字符串，data是模板字符串里面需要用到的数据。







首先需要用`npm`安装`ejs`

```bash
npm install ejs
```

