---
title: vue3+vite项目性能优化
date: 2022-08-19 15:58:48
tags:
---

经历三次重构

### 开启gzip压缩

#### 什么是gzip

一种类似winRaR的文件压缩程序。用于在服务器给客户端传送内容的时候，对内容进行压缩（压缩效果很明显），从而可以缩短页面加载时间，提高网站性能。

> gzip对纯文本内容可压缩到原大小的40%。但png、gif、jpg、jpeg这类图片文件并不推荐使用gzip压缩（svg是个例外），首先经过压缩后的图片文件gzip能压缩的空间很小。事实上，添加标头，压缩字典，并校验响应体可能会让它更大。

#### 如何实现

服务器可以给文件进行gzip压缩，这需要消耗服务器的性能。同时，前端也在打包资源文件时，也可以打包成gzip压缩包，这样可以省去服务器压缩的时间，减少一些服务端的消耗。

所以现在的一半做法是

1. 前端使用vite-plugin-compression插件

```javascript
import viteCompression from 'vite-plugin-compression'; // 开启gzip压缩

export default defineConfig({
  plugins: [
    viteCompression() 
  ],
```

2. 服务器配置nginx
   
   ```shell
   # 开启
   gzip on;
   
   # 开启gzip静态压缩功能
   gzip_static on; 
   
   # gzip缓存大小
   gzip_buffers 4 16k;
   
   # gzip http版本
   gzip_http_version 1.1;
   
   # gzip 压缩级别 1-10 
   gzip_comp_level 5;
   
   # gzip 压缩类型
   gzip_types text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
   
   # 是否在http header中添加Vary: Accept-Encoding，建议开启
   gzip_vary on;
   
   ```

#### 效果查看

在前端使用插件压缩后，利用npm run build进行打包，可以在控制台上看到比较直观的压缩结果

![](E:\webbb\iainesBlog\source\images\vue3_1.png)

750kb的文件压缩了70%。

同时，将网页部署到服务器上后，打开网页调试，可以看到如下所示的响应头

![request header](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f6730c0872d47f4be16fc6d8a93e2fb~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### 参考博客

1. [前端性能优化gzip初探（补充gzip压缩使用算法brotli压缩的相关介绍) - 掘金](https://juejin.cn/post/6844903887871148046#heading-1)

### 图片CDN

如果图片不要求高清，可以使用[tinypng](https://tinypng.com/)压缩；

其次，选择图片格式为webp，体积更小；

使用CDN加载图片，[jsdelivr](https://link.juejin.cn/?target=https%3A%2F%2Fwww.jsdelivr.com%2F "https://www.jsdelivr.com/") 是一个免费的开源CDN，可以免费加速托管在Github上的图片（但是很不稳定）

#### 参考博客

[使用CDN加载博客图片 - 掘金](https://juejin.cn/post/6900907445120401416)

### 首屏渲染时间

在`Lighthouse`中我们可以得到 FMP 值，FMP（全称 First Meaningful Paint，翻译为首次有效绘制）表示页面的主要内容开始出现在屏幕上的时间点，它是我们测量用户加载体验的主要指标。我们可以认为`FMP`的值就是首屏时间，但是浏览器并没有把`FMP`的数据提供出来。

1. [统计页面首屏时间，很多人第一步就错了](https://zhuanlan.zhihu.com/p/350657764)

2. [如何统计首屏渲染时间 - 掘金](https://juejin.cn/post/6962742206692065287#heading-2)

lighthouse总结

[使用Lighthouse更好推动项目性能优化，性能指标详解，优化方法，需要关注指标分析 - 糖~豆豆 - 博客园](https://www.cnblogs.com/sugartang/p/15811844.html)

性能测量的工具

1. 浏览器
   
   ●Lighthouse(Chrome)
   
   Network monitor
   
   Performance monitor

2. 第三方
   
   PageSpeed Insights(Google)
   ·webPage Test
   GTMetrics (actually Lighthouse)


