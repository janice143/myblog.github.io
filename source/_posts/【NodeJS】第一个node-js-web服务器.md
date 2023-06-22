---
title: 【NodeJS】第一个node.js web服务器
date: 2022-04-14 14:18:47
tags: Node.js
categories: node.js
---

## 1 📖📖📖  Node.js文档：

官方文档：https://nodejs.org/dist/latest-v14.x/docs/api/synopsis.html

官方文档对于初学者不太友好，有点难度，所以我又在网上其他文档看，比如下面这个

👇👇👇

[Node.js 入门指南](https://rualc.com/frontend/nodejs-basic/#yi-ge-jian-dan-de-li-zi)

[菜鸟教程](https://www.runoob.com/nodejs/nodejs-http-server.html)

## 2 第一个node.js代码：app.js

```js
const http = require('http');
const port = 3000;
const server = http.createServer(function(req,res){
    res.write('Hello Node')
    res.end()
})
server.listen(port,function(error){
    if(error){
        console.log('Something went wrong')
    }else{
        console.log('Server is listening on port')
    }
})
```

这个代码是基本结构。写好之后，在终端输入 `node app.js`，然后在浏览器里输入`localhost:3000`即可打开。

1. **引入 required 模块：**我们可以使用 **require** 指令来载入 Node.js 模块。
2. **创建服务器：**服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。
3. **接收请求与响应请求** 服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

## 3 在node.js里引入html文件

如果提前编写好了一个html文件，想要在web服务器打开，可以写一个app.js，然后用node.js编译。

在app.js中需要引入fs内置模块。

```js
const http = require('http');
const fs = require('fs');


const port = 3000;
const server = http.createServer(function(req,res){
     // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    res.writeHead(200,{'Content-Type':'text/html'})
    fs.readFile('index.html',function(error,data){
        if(error){
            res.writeHead(404)
            res.write('Error: File not found')
        }else{
            res.write(data)
        }
        res.end()
    })
})
server.listen(port,function(error){
    if(error){
        console.log('Something went wrong')
    }else{
        console.log('Server is listening on port')
    }
})
```

## 3 新式的写法——使用express框架

先用npm下载express的包

```
npm install express --save
```

然后编写下面的代码就可以了

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000, () => {
  console.log('示例应用正在监听 3000 端口!');
});
```

好了，到这你就学会了使用Node.js实现一个web服务器！
