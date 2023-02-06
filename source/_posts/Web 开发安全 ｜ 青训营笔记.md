---
title: Web 开发安全 ｜ 青训营笔记
date: 2023-2-8 10:51:11
tags: 青训营笔记
---

**这是我参与「第五届青训营」伴学笔记创作活动的第 17 天**



## 课程重点

- Web 相关的攻击介绍

- Web 相关的防御介绍

## 详细介绍

### XSS (Cross-Site Scripting)

主要是由于盲目信任用户输入，直接将用户输入渲染出来，导致了攻击脚本的植入

特点：

1. 通常难以从 UI 上感知
2. 窃取用户信息，例如 cookie 和 token

常见的会导致XSS的代码：

```
document.write
el.innerHTML = anyString
```

总结来说，所有能够渲染dom结构的函数，都有可能导致XSS攻击。

比如论坛场景中，**用户提交的内容包含恶意script标签**

```js
fetch("/submit", {
    body: JSON.stringify({
        id: "1",
        content: `<script>alert("xss");</script>`
    })
})
```

导致代码被插入到帖子中，所有用户访问页面都会执行恶意脚本

XSS 能够分成下面四类

`Stored XSS` 直接将恶意脚本储存到了数据库中，导致之后的所有访问均会携带恶意脚本，危害很大

```
Reflected XSS` 反射型，不储存数据，仅仅从URL传入脚本导致攻击，例如 `/path?param=<script>alert('xss')</script>
```

`DOM-based XSS` 不由服务器参与，攻击的发起和执行都在浏览器，常见于前端框架中

`Mutation-based XSS` 利用浏览器的特性，不同浏览器会有区别，例如

```
<noscript><p title="</noscript><img src=x onerror=alert(1)>">
```

### CSRF (Cross-site request forgery)

特点：

1. 在用户不知情的前提下
2. 利用用户权限（cookie）
3. 构造指定HTTP请求，窃取或修改用户敏感信息

例子：

用户没有访问银行网页，但是访问了一个带有攻击内容的网页，该网页尝试请求银行接口，由于用户登录过银行，接口携带cookie，导致请求成功，使得用户受到损失。

### SQL 注入 (SQL Injection)

在请求参数中构造恶意字符串，拼接SQL语句，导致服务器执行了特定的SQL语句，造成数据库内容泄露

例子：

```js
sql.query(`
    SELECT a, b, c FROM table
    WHERE username = ${username}
    AND form_id = ${form_id}
`)
```

上面的这段后端代码，攻击者就能够通过构造特殊的 username 或 form_id，使得SQL语句的意义被改变

例如传入 form_id = `any; DROP TABLE table;`

SQL 语句拼接后变成 `SELECT a,b,c FROM table WHERE xxxx AND form_id = any; DROP TABLE table;`

将导致数据库被删除

### SSRF (Server-Side Request Forgery)

服务端伪造请求

例子：

```
ctx.body = await fetch(ctx.query.callback)
```

导致能够通过传入的参数访问到服务器内网的相关服务

### DoS (Denial of Service)

`「 不搞复杂的 ， 量大就完事儿了 」`

通过构造特定请求，导致服务器资源被消耗，来不及响应更多请求，引发雪崩效应

例如：

1. 耗时的同步操作
2. 文件备份
3. 数据库写入
4. SQL join
5. 循环执行逻辑

#### DDoS (Distributed DoS)

攻击特点

- 直接访问 IP
- 任意 API
- 消耗大量带宽 （ 耗尽 ）

SYN Flood



## 尾声

安全无小事
使用的依赖 npm package，甚至是 NodeJS 可能成为最薄弱的一环

- 保持学习心态
  npm install 除了带来了果洞 ， 还可以带来漏洞

## 引用

https://bytedance.feishu.cn/file/boxcn9L4YzmTK3mwE3tIBL2UVme

https://baike.baidu.com/item/XSS%E6%94%BB%E5%87%BB/954065