---
title: 【前端面试基础-CSS】CSS变量 03
date: 2022-03-26 11:52:00
tags: CSS
categories: 前端面试基础
---

#  CSS variable，它解决了哪些问题

### 1 减少样式重复定义

比如同一个颜色值要在多个地方重复使用

```text
:root{
  --bgcolor: blue;
  --color: red;
}
p {
  color: var(--color);
}
div {
  backgroung-color: var(--bgcolor);
  color: var(--color)
}
```

### 2 媒体查询

在媒体查询中使用，精简代码，减少冗余

```text
.box {
  --base-size: 10;
  width: calc(var(--base-size)* 10px);
  height: clac(var(--base-size)* 5px);
  padding:calc(var(--base-size) * 1px);
}
@media screen and (min-width: 1480px) {
  .box{
    --base-size: 8;
  }
}
```

### 3 方便在 JS 中使用

```text
// 设置变量
document.getElementById("box").style.setPropertyValue('--color', 'pink')
// 读取变量
doucment.getElementById('box').style.getPropertyValue('--color').trim()    //pink
// 删除变量
document.getElementById('box').style.removeProperty('--color')
```

### 4 其他

- 可以减少 JavaScript 对 DOM 的介入，制作性能更高的动画
- 配合 content 等通过 CSS 给 JS 传参，得到一些通过 JavaScript 难以获取的参数

