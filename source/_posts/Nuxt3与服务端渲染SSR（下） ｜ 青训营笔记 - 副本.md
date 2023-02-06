---
title: Nuxt3与服务端渲染SSR（下） ｜ 青训营笔记
date: 2023-1-28 21:37:03
tags: 青训营笔记
---

**这是我参与「第五届青训营」伴学笔记创作活动的第 12 天**



## 前言

上一篇文章介绍了大概一半的nuxt3特性，这一篇会介绍另外一半

我也是第一次使用nuxt3来开发项目

踩到的坑还是很多的，预计可以写一篇踩坑记录

## 过渡

Nuxt利用Vue的 [`<Transition>`](https://vuejs.org/guide/built-ins/transition.html#the-transition-component)组件来应用页面和布局之间的转换。

只需要在nuxt config里设置

```js
export default defineNuxtConfig({
  app: {
    pageTransition: { name: 'page', mode: 'out-in' }
  },
})
```

然后在`app.vue`里设置你想要的过渡动画

```vue
<template>
  <NuxtPage />
</template>

<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.4s;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  filter: blur(1rem);
}
</style>
```

类名必须是这几个

就可以实现一个切换页面有虚化效果的动画



更高级的用法详见官方文档

https://nuxt.com/docs/getting-started/transitions

## 使用Api获取数据

nuxt3自带一个`vueuse`里的 `useFetch` api

使用方法为

```
<script setup>
const { data: count } = await useFetch('/api/count')
</script>

<template>
  Page visits: {{ count }}
</template>

```

可以使用`useAsyncData`来异步获取数据

```
<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>

<template>
  Page visits: {{ data }}
</template>

```



更高级的用法详见官方文档

https://nuxt.com/docs/getting-started/data-fetching



## 状态管理

nuxt3提供了`vueuse`的一个api叫`useState`来实现全局状态管理

无需创建state文件

直接使用

```
<script setup>
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>

<template>
  <div>
    Counter: {{ counter }}
    <button @click="counter++">
      +
    </button>
    <button @click="counter--">
      -
    </button>
  </div>
</template>

```

这个例子创建了一个state叫counter 里面默认记录了一个随机数

点击按钮会让随机数进行加减

`useState`默认为响应式 很方便

nuxt3的auto import特性可以使state简写为

```
const counter = useCounter() // Same as useState('counter')
```

## 错误处理

nuxt提供了很多高级的错误处理方式

一个最简单的api是`useError`

```
const error = useError()
```

这句代码可以快速的抛出一条错误

也可以使用`createError`方法

```
<script setup>
const route = useRoute()
const { data } = await useFetch(`/api/movies/${route.params.slug}`)
if (!data.value) {
  throw createError({ statusCode: 404, statusMessage: 'Page Not Found' })
}
</script>
```

## 部署

正常的使用`nuxt build`命令会生成一个在node环境中运行的运行时

`.mjs文件`

```sh
node .output/server/index.mjs
```

即可运行

```bash
$ node .output/server/index.mjs
Listening on http://localhost:3000
```

可以设置环境变量例如

```
PORT=3001 node .output/server/index.mjs
```

在3001端口运行

在 `nuxt.config` 里添加

```
export default {
  nitro: {
    preset: 'node-server'
  }
}
```

或者`NITRO_PRESET=node-server nuxt build`

preset字段可以更改生成的代码的依赖平台

支持的平台有

-  [AWS](https://nitro.unjs.io/deploy/providers/aws)
-  [Azure](https://nitro.unjs.io/deploy/providers/azure)
-  [Cleavr](https://nitro.unjs.io/deploy/providers/cleavr)
-  [CloudFlare](https://nitro.unjs.io/deploy/providers/cloudflare)
-  [Digital Ocean](https://nitro.unjs.io/deploy/providers/digitalocean)
-  [Firebase](https://nitro.unjs.io/deploy/providers/firebase)
-  [heroku](https://nitro.unjs.io/deploy/providers/heroku)
-  [Edgio](https://nitro.unjs.io/deploy/providers/edgio)
-  [Netlify](https://nitro.unjs.io/deploy/providers/netlify)
-  [Render](https://nitro.unjs.io/deploy/providers/render)
-  [Stormkit](https://nitro.unjs.io/deploy/providers/stormkit)
-  [Vercel](https://nitro.unjs.io/deploy/providers/vercel)

## 测试

可以添加vitest库来进行测试

```
yarn add --dev @nuxt/test-utils vitest
```

方法如下

新建test目录 创建文件 `demo.test.ts`

```ts
import { describe, test } from 'vitest'
import { setup, $fetch } from '@nuxt/test-utils'

describe('My test', async () => {
  await setup({
    // test context options
  })

  test('my test', () => {
    // ...
  })
})

```

运行vitest即可获得通过或不通过提示

## 结尾

到这里nuxt3基础就几乎结束了

还有一些更高级的用法没有讲，例如生命周期啊，nuxt-kit啊，自动导入啊

有机会可以写一篇高级教程

😋😎

##  参考

https://nuxt.com/docs/getting-started/state-management

https://nuxt.com/docs/api/composables/use-state

https://juejin.cn/post/7193305766692913189

https://vueuse.org/