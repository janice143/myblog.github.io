---
title: 第一次发布npm
date: 2022-08-20 12:21:46
tags:
---

本文介绍如何打包发布自己写的组件库到npm上。

## 本项目配置

本项目是使用vue 2.x + vue cli3搭建的UI组件库。目录结构如下

```
public
src
    examples // 展示组件库使用示例
VUI
    packages // 组件的源文件
    theme // 组件用到的统一主题样式文件
    index.js


```

在本项目中，因为还没有将项目发布npm，所以通过在`main.js` 文件使用以下指令引入

```javascript
import vui from "../VUI";
Vue.use(vui);
```

## 发布npm

1. 首先修改`package.json`文件，增加`lib`指令。

在该指令中，

* `--target`: 构建目标，默认为应用模式。这里修改为 `lib` 启用库模式。
* `--dest` : 输出目录，默认 `dist`。这里我们改成 `lib`
* `[entry]`: 最后一个参数为入口文件，默认为 `src/App.vue`。这里我们指定编译 `VUI/index.js` 组件库目录。

```
"scripts": {
   "lib": "vue-cli-service build --target lib --name vui --dest lib VUI/index.js"
}
```

修改好后，通过`npm run lib`指令就可以将组件库打包，可以发现新增了一个  `lib`文件夹。

这条命令会生成一个 lib 文件夹，将所有的组件打包。然后 `npm publish` 发布到 `npm`仓库。



2. 配置 `package.json` 文件中发布到 npm 的字段
* `name`: 包名，该名字是唯一的。可在 npm 官网搜索名字，如果存在则需换个名字。
* `version`: 版本号，每次发布至 npm 需要修改版本号，不能和历史版本号相同。
* `description`: 描述。
* `main`: 入口文件，该字段需指向我们最终编译后的包文件。
* `keyword`：关键字，以空格分离希望用户最终搜索的词。
* `author`：作者
* `private`：是否私有，需要修改为 false 才能发布到 npm
* `license`： 开源协议

本项目配置如下：

```
 "name": "view-ui-components",
  "version": "0.1.0",
  "description": "基于 Vue.2x 的UI组件库",
  "main": "lib/vui.umd.min.js",
  "keyword": "vue ui components, custom ui, element",
  "private": false,

```

3. 添加 `.npmignore` 文件，设置忽略发布文件

我们发布到 npm 中，只有编译后的 `lib` 目录、package.json、README.md才是需要被发布的。所以我们需要设置忽略目录和文件。和 `.gitignore` 的语法一样，具体需要提交什么文件，看各自的实际情况。

    # 忽略目录
    examples/
    packages/
    public/
    
    # 忽略指定文件
    vue.config.js
    babel.config.js
    *.map

5. 登录npm开始发布

设置npm的源 

`npm config set registry http://registry.npmjs.org`

这时候会遇到下列提示：

> npm notice Beginning October 4, 2021, all connections to the npm registry - including for package installation - must use TLS 1.2 or higher. You are currently using plaintext http to connect. Please visit the GitHub blog for more information: https://github.blog/2021-08-23-npm-registry-deprecating-tls-1-0-tls-1-1/

则是因为从2021年10月4日开始，所有与npm网站和npm注册表的连接（包括软件包安装）必须使用TLS 1.2或更高版本。

使用以前命令安装相关环境

> npm install -g https://tls-test.npmjs.com/tls-test-1.0.0.tgz

安装成功后会显示如下内容

> Hello! The tls-test package was successfully downloaded and installed.  
> Congratulations! Your package manager appears to support TLS 1.2.  
> tls-test@1.0.0  
> added 1 package from 1 contributor in 3.718s

之后继续执行以下步骤

    //注意是https
    npm config set registry https://registry.npmjs.org 
    npm login

这是我遇到了`npm ERR! code E426`
npm ERR! 426 Upgrade Required - PUT http://registry.npmjs.org/-/user/org.couchdb.user:iaine

需要升级 `npm install -g npm@latest`

接着继续登录

```
npm config set registry http://registry.npmjs.org
npm login
npm publish .
```

经过这些步骤，过一会会受到邮件，提示你已经成功发布一个npm库了！

## 使用

```
npm install view-ui-components

import viewUiComponents from 'view-ui-components' //引入库
import 'view-ui-components/lib/vui.css' // 引入样式
Vue.use(viewUiComponents)
```


