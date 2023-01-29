---
title: Nuxt3与服务端渲染SSR（高级篇） ｜ 青训营笔记
date: 2023-1-29 16:13:02
tags: 青训营笔记
---

**这是我参与「第五届青训营」伴学笔记创作活动的第 13 天**

## 前言

前两篇写了一些nuxt3和SSR的基础

这一篇讲讲nuxt3比较进阶的部分

## 自动引入

#### nuxt3自带自动引入 

包括一些nuxt3的api都是不用导入的直接就可以用

例如

- 数据获取的函数`useFetch` `useAsyncData` 

- 状态管理函数`useState` 

- 运行变量获取函数 `useRuntimeConfig`

- 运行时上下文获取函数 `useNuxtApp`

#### 还有一些Vue的api

例如

- 响应式的函数`ref`

- 计算属性 `computed`

#### 文件夹下的内容自动引入

例如

- `components/`文件夹下的组件
- `composables/`文件夹下的响应式函数
- `utils/`文件夹下的工具函数

#### 可以使用`#imports`来使导入明确

```
<script setup>
  import { ref, computed } from '#imports'

  const count = ref(1)
  const double = computed(() => count.value * 2)
</script>

```

#### 关闭自动引入

在`nuxtconfig`里加入

```
export default defineNuxtConfig({
  imports: {
    autoImport: false
  }
})
```

（😡谁会这么做

## 自动引入的组件

假设目录如下

```
| components/
--| base/
----| foo/
------| Button.vue

```

此时可以使用`<BaseFooButton />`标签引入这个button组件

另外，可以使用一个Lazy前缀来懒加载组件

例如

```
<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show: false
    }
  }
}
</script>

```

这样可以减少打包大小，增加性能

## 路由

写在`pages/`里的组件会自动写入路由

`pages/index.vue`会匹配路由`/`

只需要在`app.vue`里添加

`<NuxtPage />`标签

可以通过插槽写法例如写`[id].vue`来匹配路由内的参数

例如如下文件树

```
-| pages/
---| index.vue
---| users-[group]/
-----| [id].vue

```

在`[id].vue`里写下

```
<template>
  <p>{{ $route.params.group }} - {{ $route.params.id }}</p>
</template>
```

读取group参数和id参数

访问路由`/users-admins/123`会渲染成

```
<p>admins - 123</p>
```

## 渲染模式

#### 传统的Vue项目渲染方式为  `客户端渲染`

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230129144946.png)

浏览器会先下载到空的html文件→下载js→渲染出来

这种方式的优点是：

- 开发速度快：当完全在客户端上工作时，我们不必担心代码的服务器兼容性，例如，使用像windows这样的浏览器api。 

- 更便宜的服务器成本：运行一个服务器会增加一个基础设施的成本，因为您需要在一个支持JavaScript的平台上运行。我们可以在任何具有HTML、CSS和JavaScript文件的静态服务器上托管仅限客户端的应用程序。 

- 下载好后离线：因为代码完全在浏览器中运行，所以它可以很好地在互联网不可用时很好地保持工作。

缺点为：

- 性能不好：用户必须等待浏览器下载、解析和运行JavaScript文件。根据下载部分的网络和用户的解析和执行设备，这可能需要一些时间，并影响用户的体验。
- 不友好的搜索引擎优化：索引和更新通过客户端呈现交付的内容比使用服务器呈现的HTML文档需要更多的时间。

#### nuxt3的`服务端渲染`

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230129145732.png)

有内容的html文件会下载到浏览器→用户在此时可以点击和浏览页面上的内容（部分功能）→需要的组件渲染完成、此时的页面有全部功能

这种方式的优点是：

- 性能好：用户可以立即访问页面的内容，因为浏览器显示静态内容的速度比`javascript`生成的静态内容要快得多。
-  搜索引擎优化：通用呈现将页面的整个HTML内容作为一个经典的服务器应用程序传递到浏览器。Web爬虫可以直接索引页面的内容。

缺点是：

- 开发约束：服务器和浏览器环境不提供相同的api，而且编写可以在两边无缝运行的代码可能会很棘手。幸运的是，Nuxt提供了特定的变量来同步这些。
- 成本高：服务器需要运行，才能动态地呈现页面。这就像任何传统服务器一样，增加了每月的成本。但是，由于浏览器接管了客户端导航的通用渲染，服务器调用大大减少了。

## 类型检查

使用 `TypeScript` 后可以使用nuxi带的语法检查

```
yarn nuxi typecheck
```

nuxt也会自动生成类型

在[`.nuxt/nuxt.d.ts`](https://nuxt.com/docs/guide/concepts/typescript#nuxtnuxtdts)目录下

## 文件目录

一个完整的nuxt3项目文件目录应该包含以下文件夹和文件

```
.nuxt                      nuxt生成的文件目录里面有 运行的类型等
.output                    build产物文件夹
assets                     css文件、字体文件、图片文件
components                 组件目录
composables                响应式函数的文件夹
content                    基于文件的cms的文件夹
layouts                    布局组件的文件夹
middleware                 中间件文件夹
node_modules               依赖文件夹
pages                      路由的页面
plugins                    nuxt插件
public                     公共文件文件夹，可以放ico图标，图片
server                     服务端api文件夹
utils                      工具函数文件夹
.gitignore                 git忽略文件
.nuxtignore                不加入nuxt编译的忽略文件
app.config.ts              config文件 可以使用useAppConfig读取
app.vue                    vue入口 
nuxt.config.ts             nuxt配置文件
package.json               依赖描述文件
tsconfig.json              ts检查配置文件
```

https://nuxt.com/docs/guide/directory-structure/nuxt

## 总结

本篇文章介绍了nuxt3的高级用法

我也是第一次使用nuxt3来开发项目

踩到的坑还是很多的，预计可以写一篇踩坑记录

😎🥰

##  参考

https://nuxt.com/docs/guide/concepts/auto-imports

https://nuxt.com/docs/guide/directory-structure/composables

https://nuxt.com/docs/guide/directory-structure/components

https://github.com/unjs/nitro