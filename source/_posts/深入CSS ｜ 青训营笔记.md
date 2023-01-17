---
title: 深入CSS ｜ 青训营笔记
date: 2023-1-17 22:39:40
tags: 青训营笔记
---
**这是我参与「第五届青训营」伴学笔记创作活动的第 2 天**

## 课程重点

1. 选择器的特异度
2. 属性的继承
3. Layout 方式
4. 盒子模型
5. 块级元素和行级元素
6. flex
7. grid
8. float
9. position

## 详细知识点介绍：

### 1. 选择器的特异度（Specificity）

即 CSS 选择器的**优先级**，当一个元素能够匹配多个样式时，浏览器会根据优先级为元素赋予正确的样式。

一般情况下，ID选择器优先级最高，其次是类选择器/属性选择器/伪类，优先级最低的是类型选择器和伪元素选择器。不同选择器的叠加也会改变优先级。`!important` 的样式会覆盖其他样式。

在 `VSCode` 中，鼠标悬浮在选择器上是，能实时看到选择器的优先级数值

id选择器 大于 类选择器 大于 标签选择器

### 2. CSS 属性的继承

某些属性会自动继承其父元素的计算值除非显式指定一个值

不同的 CSS 属性有着不同的继承规则。对于一个能够被继承的样式属性，子元素能够从父元素继承相同的属性值，而不需要额外的设置。当然，对于一个默认不继承的样式，也可以通过将其值设置为`inherit`来强制从父元素继承。

初始值：CSS 中，每个属性都有一个初始值 `background-color` 的初始值为 `transparent`
`margin-left` 的初始值为 0
可以使用 `initial` 关键字显式重置为初始值
`background-color: initial`

### 3. CSS 布局方式

CSS 存在许多种布局方式。总体上分为三类：常规流/文档流、浮动、绝对定位。其中，在正常的文档流中，又可以细分出很多布局方式，例如行级、块级、表格布局、弹性布局（flex）、网格布局（grid）

盒子模型：`margin` `border` `padding`

### 4. CSS 盒子模型

width: content box 的宽度

height: content box 的高度

padding: 元素内边距，百分比相对于**元素宽度**

border: 元素的边框样式、粗细和颜色

margin: 元素外边距，百分比相对于**元素宽度**

当 box-sizing 取值为 border-box 时，模型会发生相应变化。

### 5. 块级元素

即 `display` 属性值为 `block` 的元素。例如 `body`、`article`、`div`、`main`、`section`、`p`、`h1`~`h6`、`ul`/`ol`、`li` 等

### 行级元素

即 `display` 属性值为 `inline` 的元素。

### 6. flex

布局的传统解决方案，基于盒状模型，依赖 display属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。

2009年，W3C提出了一种新的方案—Flex布局，可以简便、完整、响应式地实现各种页面布局。目前，它已经得到了所有浏览器的支持，这意味着，现在就能很安全地使用这项功能。

Flex是Flexible Box的缩写，意为”弹性布局”，用来为盒状模型提供最大的灵活性.

```css
.box{
  display: flex;
}
```

任何一个容器都可以指定为Flex布局。

采用Flex布局的元素，称为Flex容器（flex container），简称”容器”。

- flex-direction
- flex-wrap
- flex-flow
- justify-content
- align-items
- align-content

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。

```css
.box {
  flex-direction: row | row-reverse | column | column-reverse;
}
```

项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

- row（默认值）：主轴为水平方向，起点在左端。
- row-reverse：主轴为水平方向，起点在右端。
- column：主轴为垂直方向，起点在上沿。
- column-reverse：主轴为垂直方向，起点在下沿。

### 7. grid

网格是一组相交的水平线和垂直线，它定义了网格的列和行。

CSS 提供了一个基于网格的布局系统，带有行和列，可以让我们更轻松地设计网页，而无需使用浮动和定位。

当一个 HTML 元素将 display 属性设置为 grid 或 inline-grid 后，它就变成了一个网格容器，这个元素的所有直系子元素将成为网格元素。

```css
grid: none;
grid: "a" 100px "b" 1fr;
grid: [linename1] "a" 100px [linename2];
grid: "a" 200px "b" min-content;
grid: "a" minmax(100px, max-content) "b" 20%;
grid: 100px / 200px;
grid: minmax(400px, min-content) / repeat(auto-fill, 50px);

grid: 200px / auto-flow;
grid: 30% / auto-flow dense;
grid: repeat(3, [line1 line2 line3] 200px) / auto-flow 300px;
grid: [line1] minmax(20em, max-content) / auto-flow dense 40%;

grid: auto-flow / 200px;
grid: auto-flow dense / 30%;
grid: auto-flow 300px / repeat(3, [line1 line2 line3] 200px);
grid: auto-flow dense 40% / [line1] minmax(20em, max-content);

grid: inherit;
grid: initial;
grid: unset;

```

我们通过 **grid-template-columns** 和 **grid-template-rows** 属性来定义网格中的行和列。

这些属性定义了网格的轨道，一个网格轨道就是网格中任意两条线之间的空间。

在下图中你可以看到一个绿色框的轨道——网格的第一个行轨道。第二行有三个白色框轨道。

轨道可以使用任何长度单位进行定义。

网格引入了 **fr** 单位来帮助我们创建灵活的网格轨道。一个 fr 单位代表网格容器中可用空间的一等份。

以下实例定义了一个网格定义将创建三个相等宽度的轨道，这些轨道会随着可用空间增长和收缩。

一个网格单元是在一个网格元素中最小的单位， 从概念上来讲其实它和表格的一个单元格很像。现在再看回我们前面的一个例子, 一旦一个网格元素被定义在一个父级元素当中，那么他的子级元素将会排列在每个事先定义好的网格单元中。

列与列，行与行之间的交接处就是网格线。

Grid 会为我们创建编号的网格线来让我们来定位每一个网格元素。

```css
#container {
  display: grid;
  grid: repeat(2, 60px) / auto-flow 80px;
}

#container > div {
  background-color: #8ca0ff;
  width: 50px;
  height: 50px;
}
```

网格线的编号顺序取决于文章的书写模式。在从左至右书写的语言中，编号为 1 的网格线位于最左边。在从右至左书写的语言中，编号为 1 的网格线位于最右边。

接下来我使用了 grid-column-start, grid-column-end, grid-row-start 和 grid-row-end 属性来演示如何使用网格线。

以下实例我们设置一个网格元素的网格线从第一列开始，第三列结束：

```css
.item1 {
  grid-column-start: 1;
  grid-column-end: 3;
}
```

https://www.runoob.com/try/gridgarden/index.html

菜鸟教程的grid布局小游戏

## 实践练习例子：

### 特异度相关例子

下面的文字是什么颜色？

```html
<div class="wrapper1">
  <div class="text1">文本1</div>
</div>
<style>
  .wrapper1 .text1 {
    color: red;
  }
  
  .text1 {
    color: black;
  }
</style>
```

正确答案是红色

虽然黑色的属性声明在后面，理应覆盖掉前面的红色，但是前面是两个类选择器的叠加，优先级是 `0, 2, 0` ，后者只有一个类选择器，优先级是 `0, 1, 0`，所以最终浏览器应用了红色。

## 课后个人总结：

本节课中，我学习了CSS中特异度、属性继承、布局方式、盒子模型、块级元素和行级元素、flex、grid等概念，掌握了相关的知识和应用。 特异度是CSS中最重要的概念之一，让我们明白当一个元素能够匹配多个样式时，浏览器会根据优先级为元素赋予正确的样式。

属性的继承也是重要的概念，它会依赖于不同的CSS属性，有了它，我们可以让HTML元素的样式可以从父元素继承。

我们还学习了CSS中的布局方式，包括常规流/文档流、浮动、绝对定位等，以及flex、grid等网格布局。

熟练掌握这些知识，我们可以更好的制作网页，满足各种布局的需求。

## 引用参考：

课程ppt

<https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid>

<https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox>
