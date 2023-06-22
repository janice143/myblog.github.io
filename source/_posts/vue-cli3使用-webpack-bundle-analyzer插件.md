---
title: 第一次在vue-cli3 使用 webpack-bundle-analyzer 插件
date: 2022-08-19 20:33:33
tags:

---

## 引言

`webpack-bundle-analyzer` 作为可视化的 `webpack` 打包后文件的分析插件，是比较成熟的方案。

`webpack-bundle-analyzer作用`:

1. 认识打包后的文件和大小；
2. 以便优化打包后的文件。

安装
--

    npm install webpack-bundle-analyzer -D
    复制代码

vue.config.js配置
-----------------------

    module.exports = {
        chainWebpack: config => {
            config
            .plugin('webpack-bundle-analyzer')
            .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin)
    
        },
    }
    复制代码

使用
--

    npm run serve (这时会执行webpack-bundle-analyzer插件功能 完成优化后可自行删掉 配置 )

## 结果

打开网站后，看到如下结<img title="null" src="https://github.com/janice143/myblog.github.io/blob/master/images/webpack_bundle1.png?raw=true" alt="null" data-align="inline">



可以看到，在这里项目中， chunk-vendors.js 文件很大，大概占74%。这个文件是什么？

chunk-vendors.js 文件一般是第三方模块或供应商模块。通常，`/node_modules` 中的第三方包都会打包到 `chunk-vendors.js` 中，自然会出现文件过大的问题。

## 优化方案

### 方式1 Gizp压缩

使用 compression-webpack-plugin 插件，将文件压缩成gzip文件。（在Vite项目里用vite-plugin-commpression插件）

安装插件——npm安装。

了解到还有一种brotli压缩，和gzip算是目前主流的压缩程序，但是前者只适用于https通信。

```shell
npm install --save-dev compression-webpack-plugin
```

### 方式2 配置分割

通过 `webpack` 前端配置，将第三方包分开打包，这样不会将所有第三方包都打包到 `chunk-vendors.js` 文件

```javascript
configureWebpack: {
    // 开启分离 js
     optimization: {
      splitChunks: {
        minSize: 10000,
        maxSize: 250000,
      }
    }
  },
```

### 方式3 CND链接

比如将有些大的 `js`、`css` 通过 `cdn` 的方式链接，可以多种方案配合一起使用的。

下图是我使用方法2得到的结果，可以看到原来的问价被分割成了很多js文件。

<img title="null" src="https://github.com/janice143/myblog.github.io/blob/master/images/webpack_bundle2.png?raw=true" alt="null" data-align="inline">



## 参考博客

[vue3.0 + typescript + webpack-bundle-analyzer实现代码分析和CDN - 掘金](https://juejin.cn/post/7055082226655952903#heading-5)

https://stackoverflow.com/questions/51816020/how-to-break-the-js-files-into-chunks-in-vue-cli-3-with-webpack-performance-obje

https://blog.bitsrc.io/5-methods-to-reduce-javascript-bundle-size-67f2e1220457

[Vue 打包 chunk-vendors.js 文件过大导致页面加载缓慢解决方案 - 掘金](https://juejin.cn/post/6999913086165467172#heading-3)
