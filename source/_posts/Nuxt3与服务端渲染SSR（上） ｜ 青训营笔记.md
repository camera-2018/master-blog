---
title: Nuxt3与服务端渲染SSR（上） ｜ 青训营笔记
date: 2023-1-27 18:41:32
tags: 青训营笔记
---

**这是我参与「第五届青训营」伴学笔记创作活动的第 11 天**



## 前言

Nuxt同时提供了前端和后端功能，因此您可以专注于重要的事情：创建web应用程序。

[Nuxt3](https://nuxt.com/docs) 是基于 `Vite`、`Vue3` 和 `Nitro` 的 `Nuxt` 框架的重构，具有一流的 `Typescript` 支持，且这次更新对内核进行了精简，使之速度更快，体验更好。

## 了解 `SSR`

`Nuxt` 是一个基于 `Vue.js` 的服务端渲染应用框架

### 什么是 `SSR`

`服务器端渲染`（Server-Side Rendering）是指由服务端完成页面的 HTML 结构拼接的页面处理技术，发送到浏览器，然后为其绑定状态与事件，成为完全可交互页面的过程。

简单理解就是html是由服务端写出，可以动态改变页面内容，即所谓的动态页面。早年的 php 、 asp 、 jsp  这些 Server page 都是 SSR 的。

### 为什么使用 `SSR`

- 网页内容在服务器端渲染完成，一次性传输到浏览器，所以 `首屏加载速度非常快`；
- `有利于SEO`，因为服务器返回的是一个完整的 html，在浏览器可以看到完整的 dom，对于爬虫、百度搜索等引擎就比较友好；

## Nuxt 3

`Nuxt` 是一个基于 `Vue.js` 的服务端渲染应用框架，

刚去查了一下 nuxt3在1月23日变成了默认版本

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125003129.png)

在1月25日发布了3.1.0版本

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230127185726.png)

- 实验组件群岛 准备就绪

- 新的api

- Nitro v2 ，vite 4 ，rollup3

- 性能改进

## 什么是Nuxt？

要理解Nuxt是什么，我们需要了解创建一个现代应用程序需要什么：

- JavaScript框架： 一个带来JavaScript组件的JavaScript框架，支持Vue.js。
- 打包工具：支持开发中的热模块替换和生产代码，支持webpack 5和Vite。
- 最新JavaScript语法：在支持遗留浏览器的同时编写最新的JavaScript语法的转换器，支持esbuild。
- 服务器端：Nuxt是一个在开发中服务于应用程序的服务器，同时也支持服务器端呈现或API路由，它使用h3进行部署的多功能性，如serverless, workers, Node.js和无与伦比的性能。
- 路由：一个处理客户端导航的路由库，支持 [vue-router](https://router.vuejs.org/).。

## 初始化项目

https://nuxt.com/docs/getting-started/installation#prerequisites

```bash
pnpm dlx nuxi init <project-name>
```

```bash
code <project-name>
```

```bash
# Make sure you have `shamefully-hoist=true` in `.npmrc` before running pnpm install
pnpm install
```

**注意：** 如果你用 `pnpm` 安装依赖，请创建一个 `.npmrc` 文件，且设置:

```ini
shamefully-hoist=true
```

安装完成😋

## 启动

```bash
pnpm dev -o
```

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230127193306.png)

看一眼文件树

抛开编译结果`.nuxt`文件夹不谈 就只有一个`app.vue` 和`nuxt.config.ts`

很简陋 怎么会这样呢？我们一般写的vue框架也不是这样的啊

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230127193603.png)

## 创建目录

创建 `components/` `pages/`

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230127200417.png)

具体操作见官方文档

https://nuxt.com/docs/getting-started/views

也可以新建`src`目录 把 `components/` `pages/`目录放到`src`里

在nuxt里组件是自动导入的，不用配置

## 路由跳转

路由是自动创建的

```html
<NuxtLink to="/about">About</NuxtLink>
```

以上的代码点击会自动跳到`page/about.vue`

也可以使用路由

```vue
<script setup>
const route = useRoute()

// When accessing /posts/1, route.params.id will be 1
console.log(route.params.id)
</script>

```

`/posts/1`是创建的`/posts/[id].vue`这样id会被当做props传入

## SEO优化

nuxt服务端渲染最重要的就是搜索引擎优化

可以被拉取到带内容的html

而不是一般vue项目的空html

```js
export default defineNuxtConfig({
  app: {
    head: {
      charset: 'utf-16',
      viewport: 'width=500, initial-scale=1',
      title: 'My App',
      meta: [
        // <meta name="description" content="My amazing site">
        { name: 'description', content: 'My amazing site.' }
      ],
    }
  }
})
```

在`nuxt.config.ts`配置如上内容

或者在`app.vue`里添加

```vue
<script setup lang="ts">
useHead({
  title: 'My App',
  meta: [
    { name: 'description', content: 'My amazing site.' }
  ],
  bodyAttrs: {
    class: 'test'
  },
  script: [ { children: 'console.log(\'Hello world\')' } ]
})
useServerSeoMeta({
  title: 'My Amazing Site',
  ogTitle: 'My Amazing Site',
  description: 'This is my amazing site, let me tell you all about it.',
  ogDescription: 'This is my amazing site, let me tell you all about it.',
  ogImage: 'https://example.com/image.png',
  twitterCard: 'summary_large_image',
})
</script>
```

这几个函数可以添加meta头

还有一些设置可以看官方文档 https://nuxt.com/docs/getting-started/seo-meta

也可以使用 https://nuxt.com/modules/seo-kit

这个模块来配置SEO

很方便😎

## 结尾

本篇文章介绍了大概一半的nuxt3特性，下一篇会介绍另外一半

我也是第一次使用nuxt3来开发项目

踩到的坑还是很多的，预计可以写一篇踩坑记录

😎

##  参考

https://nuxt.com/modules/seo-kit

https://nuxt.com/docs/getting-started/seo-meta

https://nuxt.com/docs/getting-started/views

https://nuxt.com/docs

https://juejin.cn/post/7170746000112353293

https://juejin.cn/post/7037336504418435103