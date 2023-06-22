---
title: 【前端面试基础-CSS】左侧固定右侧自适应 02
date: 2022-03-26 11:25:44
tags: CSS
categories: 前端面试基础
---

# css 如何实现左侧固定宽度 300px，右侧自适应的布局

初始元素如下：

```html
<div class="container">
  <div class="left"></div>
  <div class="main"></div>
</div>
```

## 解决方案

### 1 flex布局

```css
.container{ 
    display:flex; 
} 
.left{ 
    width:300px;
} 
.main{ 
    flex:1; 
}
```

```css
.container {
  display: flex;
}
.left {
  flex: 0 0 300px;
}
.main {
  flex: 1 1;
}
```

```css
.container {
  display: flex;
}

.left {
  flex-basis: 300px;
  flex-shrink: 0;
}

.main {
  flex-grow: 1;
}
```

### 2 Grid 布局

```
.container {
  display: grid;
  grid-template-columns: 300px 1fr;
}
```

### 3 calc 方法

```
.left {
  width:300px;
}
.main {
  width: calc(100% - 300px)
}
```

