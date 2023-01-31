---
title: NodeJS基础 ｜ 青训营笔记.md
date: 2023-1-31 23:00:36
tags: 青训营笔记
---


**这是我参与「第五届青训营」伴学笔记创作活动的第 15 天**

## 一、本堂课重点内容：

1. NodeJS 应用场景
2. NodeJS 运行时结构
3. 使用 NodeJS 编写简单的 HTTP Server
4. NodeJS延伸

## 二、详细知识点介绍：

### 1. NodeJS应用场景

NodeJS被广泛应用于前端工程化（`Vue`、`React`、`AngularJS`等）、

Web 服务端应用（`Vercel`）和 跨端桌面应用（`Electron`等）场景

许多熟知的打包构建工具都有NodeJS参与，例如 `webpack`、`vite`、`esbuild`和`parcel`等，也有例如`babel`和`TS`等语言转换程序也由`nodejs`编写。

在前端工程化领域，`NodeJS`也有许多优势，首先是学习曲线平缓、开发效率较高、其次是运行效率较高，社区生态丰富。且由于其语言优势，和前端结合更加顺畅，例如某些服务端渲染场景。

在跨端应用方面，微软的`VSCode`、聊天工具`Discord`都由`Electron`开发，值得在项目选型时考虑。

难以替代

与前端结合的场景有很大的优势SSR

字节使用`NodeJS`，飞书就是`electron`应用  每年新增1000+`nodejs`应用

### 2.NodeJS运行时结构

- 用户代码
- npm
- js core
- cpp core
- v8
- openssl
- ...

异步、单线程、跨平台

### 3. 使用 NodeJS 编写简单的 HTTP Server

0 ． 安装 `node.js`
1 .    编写 Http Server + Client, 收发 GET, POST 请求
2 ． 编写静态文件服务器
3 ． 编写 React SSR 服务
4 ． 适用 inspector 进行调试 、 诊断
5 ． 部署简介

### 4. NodeJS延伸

- 编译
- 诊断追踪
- wasm
- napi
- ...

## 三、实践练习例子：

### 简单的 Hello World

```js
const http = require('http')

const port = 3000

const server = http.createServer((req, res) => {
    res.end('hello')
})

server.listen(port, () => {
    console.log(`listening port on ${port}`)
})
```

使用 `require('http')` 引用 NodeJS 自带的 http 模块。

使用 `createServer` 方法为请求绑定处理事件函数，

使用 `res.end('hello')` 来结束请求并返回 `hello` 字符串。

使用 `listen` 函数设置监听的端口，第一个参数是端口号，第二个参数是绑定成功的回调函数。

用浏览器访问127.0.0.1:3000 会显示hello

### 简单的静态文件服务

```js
const http = require('http')
const fs = require('fs')
const path = require('path')
const url = require('url')

const port = 3000

const server = http.createServer((req, res) => {
    const info = url.parse(req.url)
    const file = fs.createReadStream(path.resolve(__dirname, '.' + info.pathname))
    file.pipe(res)
})

server.listen(port, () => {
    console.log(`listening port on ${port}`)
})
复制代码
```

通过 NodeJS 自带的文件操作模块 `fs` 的 `createReadStream` 创建了一个文件流，

通过流内部方法pipe将其输出给res（响应对象）

能将对应路径的文件返回给浏览器

## 四、课后个人总结：

今天的课程主要介绍了**NodeJS**的应用场景以及运行时结构，并且结合实例介绍了如何使用**NodeJS**编写简单的HTTP Server。通过今天的学习，我更加深入地了解了**NodeJS**，再次感受到它的强大，它可以应用于前端工程化、Web 服务端应用以及跨端桌面应用等多种场景，它的未来也是非常可观的。