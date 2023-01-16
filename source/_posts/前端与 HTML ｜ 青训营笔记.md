---
title: 前端与 HTML ｜ 青训营笔记
date: 2023-1-16 12:18:33
tags: 青训营笔记
---
**这是我参与「第五届青训营」伴学笔记创作活动的第 1 天**

## 本堂课重点内容：

1. 前端的介绍
2. HTML 标签的简单介绍
3. HTML 语义化

## 详细知识点介绍：

1. 前端技术栈
2. 前端的边界、开发环境（ide、浏览器）
3. DOM 树的简单介绍
4. HTML 语法简单介绍
5. 各类常用标签（h1-h5、p、form、a）
6. 常见的语义化标签（main/footer/section）
7. 语义化的优势

## 实践练习例子：

### 1. 标题元素

使用 `h1`~`h6` 标签将标题文本包裹，标题会根据级别拥有默认的字重、字号和外边距。

```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```

### 2. 图片元素

使用 `src` 属性来确定图片链接，`alt` 属性用于表示图片无法显示时的替代文本

```html
<img src="https://example.com/image" />
<img src="https://example.com" alt="显示不出来的图片" />
```

### 3. 单选框

`radio` 类型的 `input` 元素，相同 `name` 的单选框会归为同一组（只能选其中一个） 使用 `label` 标签来标识单选框的内容

```html
<input type="radio" id="el1" name="group1" />
<label for="el1">第一组-选项1</label>
<input type="radio" id="el2" name="group1" />
<label for="el2">第一组-选项1</label>
<br>
<input type="radio" id="el3" name="group2" />
<label for="el3">第二组-选项1</label>
<input type="radio" id="el4" name="group2" />
<label for="el4">第二组-选项2</label>
```

### 4. 超链接

使用 `a` 标签包裹文本，`href` 属性是目标链接，默认在当前页面打开，为了在新标签页打开，可以将 `target` 属性设置为 `_blank`

```html
<a href="https://example.com">在当前页面打开</a>
<a href="https://example.com" target="_blank">在新页面打开</a>
```

### 5. 有序列表

最外层使用 `ol`标签包裹，内部包含多个 `li` 标签

```html
<ol>
    <li>第一</li>
    <li>第二</li>
    <li><em>第三</em></li>
</ol>
```

### 6. 无序列表

最外层使用 `ul`标签包裹，内部包含多个 `li`标签

```html
<ul>
    <li>第一</li>
    <li>第二</li>
    <li><em>第三</em></li>
</ul>
```

### 课后个人总结：

本节课主要介绍了前端的技术栈，以及 HTML 标签的简单介绍和 HTML 语义化，通过本节课的学习，我对 HTML 标签有了一定的了解，掌握了如何使用标签来表达页面元素，并且学习了如何使用语义化标签来提高前端代码的可读性，以及提升代码的可维护性。
