---
title: 【NodeJS】node小项目——购物车
date: 2022-04-14 16:44:31
tags: Node.js
categories: node.js
---

## 项目描述

项目提供了初始的前端代码（放在public文件夹中），现在需要利用node.js来实现项目的后端部分。

## 项目初始化

### 1 npm初始化

会生成一个`package.json`文件。

```bash
npm init
```

npm是包管理工具，下载node的时候回顺带下载到npm。更多想了解的可以参考文档：[菜鸟教程](https://www.runoob.com/nodejs/nodejs-npm.html)

### 2 项目所需库

#### 2.1 介绍

这个项目需要用到`express`,`ejs`,`stripe`以及`dotenv `四个库。先大致说一下这几个库的用途。

- `express`是用来创建服务器，以及接收请求和响应请求等；

  📖📖📖 官方文档：https://www.expressjs.com.cn/guide/routing.html

- `ejs`是用一套模板引擎（或者叫模板语言），因为本项目需要陈列一些商品信息，这些信息我们用一个json文件管理，然后需要动态渲染出来，这时候就用到了`ejs`语言。

  📖📖📖 官方文档：https://ejs.bootcss.com/

- `stripe`是stripe(一个支付服务平台，有点像国内的阿里)的一个线上支付工具包，另外stripe还提供了一些线上支付的接口，需要注册使用，才能获取到私密钥和公共密钥。

  📖📖📖 官方文档：https://stripe.com/docs/payments?payments=popular

- `dotenv`用来管理环境变量的，stripe提供的接口中申请的私人密钥和公共密钥，其中私人密钥不能出现在生产环境中，只用来测试的时候用，所以需要管理我们的环境变量，让有些模块不会出现的生产环境中。

#### 2.2 安装

```bash
npm install express ejs stripe dotenv --save
```

**注意**：加上save会将该依赖存入到package.json 里的dependcies里，不加则不会。

下载 dotenv (只在开发的时候下载来用，生产环境下是不需要的)

```bash
npm install dotenv -save-dev
```

有些模块只是在本地开发的时候用到，这些模块会在`devDependencies`，在生产环境这些模块不会被打入包内。

在下载包的时候加上 `--also=dev`可配置。

## 项目过程

本项目的大致思路是：

1. 新建server.js文件，利用express搭建 web 服务器

2. 利用git clone[下载初始文件]( https://github.com/WebDevSimplified/Node.js-Stripe-Shopping-Cart)，将初始文件里的public/fonts文件夹移动那当前项目文件夹中，然后删除多余初始文件，将fonts文件夹重命名为public

3. 提供的初始文件是一个静态网页+少量的js交互。利用`app.use(express.static('public'))`可查看

   当你点击导航栏的`STORE`，可以浏览商品页面，点击商品加入购物车，然后点击购买，会alert出“成功购买”的信息。这些是初始文件在js中设计好的，我们项目的就是搭建服务器，将商品信息动态地渲染到页面中。选择商品加入购物车后，在点击购买的时候，调用stripe的接口完成线上支付。

4. 新建`items.json`文件，根据页面的商品信息，做一个简单的商品数据库，以便后期加入其他商品

5. 新建`views`文件夹，把初始文件`store.html`移动到该文件夹中，然后把该文件后缀名改为`.ejs`

6. 利用ejs的语法在.ejs文件中修改对应的商品内容

7. 在`server.js`文件中编写代码，将ejs文件渲染出来

8. 注册stripe 官网的账号，在开发者测试中拿到密钥

9. 新建.env文件，文件名为.env，开始配置环境变量，一个是公钥，一个是密钥（密钥是敏感信息，决不能外露，不然会被随意消费）

10. 新建.gitignore文件，把环境配置文件.env写入里面，这样代码打包的时候就不会把环境配置这个文件打包

11. 在server.js引入.env中的公钥和密钥

12. 把公钥通过ejs文件传给store.js文件，下面我们开始修改store.js文件上的点击支付按钮的交互

13. 在ejs文件中引入stripe的js文件（浏览器版本）

14. 在store.js设置stripe的处理器

15. 在server.js中设计app.post，发出支付请求

### expressJS 网络服务器框架

#### 1 基本使用

```js
const express = require('express')
const app = express()
// respond with "hello world" when a GET request is made to the homepage
app.get('/', function (req, res) {
  res.send('hello world')
})
app.listen(3000)
```

编写完这段代码，然后在浏览器上输入localhost:3000，就可以看到页面上显示`hello world`字样。这说明我们成功搭建了一个node.js服务器。







.env

https://juejin.cn/post/6844904153890684935

.gitignore



https://blog.csdn.net/beiniao520/article/details/79500647

#### 过程页面的跳转恰好用的是res.[render](https://so.csdn.net/so/search?q=render&spm=1001.2101.3001.7020)的方式，搭配上ejs模板引擎实现了页面的渲染。

# Node Express框架中EJS模板引擎的使用

https://blog.csdn.net/weixin_41162427/article/details/107864130
