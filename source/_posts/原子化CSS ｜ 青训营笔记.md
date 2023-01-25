---
title: 原子化CSS｜ 青训营笔记
date: 2023-1-25 21:12:53
tags: 青训营笔记
---

**这是我参与「第五届青训营」伴学笔记创作活动的第 9 天**

## 前言

原子化 CSS是一个很新颖的定义 出自https://CSS-tricks.com/lets-define-exactly-atomic-CSS/

意思是`原子化 CSS是一种 CSS的架构方式，它倾向于小巧且用途单一的 class，并且会以视觉效果进行命名。`

## 什么是原子化 CSS?

与一般的我们写正常CSS不同，原子化CSS会把元素本身的属性提取出来，加以视觉效果命名，例如：

如下的html

```html
<button class="btn">Basic</button>
```

正常的CSS

```css
.btn {
	padding: 1rem, 2rem;
    font-family: 'semi';
    font-weight: bold; 
}
```

使用原子化CSS后，将不用写CSS文件，改为写class属性

```html
<button class="py-2 px-4 font-semibold rounded-lg shadow-md text-white bg-green-500 hover:bg-green-700">
  Click me
</button>
```

市面上有不少实用至上的 CSS框架，如 [Tailwind CSS](https://tailwindCSS.com/)，[Windi CSS](https://cn.windiCSS.org/) 以及 [Tachyons](https://tachyons.io/) 等。

同时有些 UI 库也会附带一些 CSS工具类作为框架的补充，如 [Bootstrap](https://getbootstrap.com/docs/5.1/utilities/api/) 和 [Chakra UI](https://chakra-ui.com/docs/features/style-props)。

## 写之前，我们谈谈优势

- 类名自由：你绞尽脑汁想这个元素的类名怎么写。在你面前的是一点击就会变颜色的按钮，你想给他起个class或者id，你想了半天，最后取了class名为`click-it-and-turn-it-from-blue-to-green`😰。
- html嵌入：正常情况我们要引入CSS文件去写这个样式，切来切去不太方便。
- 预设的原子化CSS：可以引入插件、其他基于原子化CSS写的组件库。
- IDE支持：插件支持类名提示，很好写。
- 写的快：比如说hover属性，原子化CSS可以直接写在class里，很快很方便，比如说tailwind自带的响应式布局，不用自己写媒体查询就可以适配移动端。

## 常用的原子化CSS框架

- tailwindCSShttps://tailwindcss.com/
  - ![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125214852.png)
- Windi CSShttps://windicss.org/
  - ![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125215026.png)

- [Tachyons](https://tachyons.io/) https://tachyons.io/

  - ![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125215141.png)

  - UnoCSShttps://github.com/unocss/unocss
    - ![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125215337.png)

## 本文的重点：UnoCSS

讲这么多，我们终于回到主题UnoCSS上

>  antfu[重新构想原子化CSS](https://link.juejin.cn/?target=https%3A%2F%2Fantfu.me%2Fposts%2Freimagine-atomic-css-zh) 这篇文章讲了unocss是怎么诞生的

Windi CSS已经足够优秀，但antfu大佬还是不够满意，对于框架预设外的自定义工具的额外配置上，还是比较繁琐，而且配置的方式也不够简便

> 由于 Windi 需要与 Tailwind 兼容，它还必须使用与 Tailwind 完全相同的配置项。尽管数字推断的问题得到了解决，但如果你想添加一些自定义的工具，这将是一场噩梦。

所以经过重新构想原子化CSS，UnoCSS出现了

> [**UnoCSS**](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fantfu%2Funocss) - 具有高性能且极具灵活性的即时原子化 CSS引擎。

它不是像TailWind CSS和Windi CSS属于框架，而是一个引擎，它没有提供预设的原子化CSS工具类

UnoCSS通过编写规则来定制工具类

静态规则

```js
rules: [
	['m-1', { margin: '0.25rem' }]
]
```

使用正则表达式来做动态规则

```ts
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
  [/^p-(\d)$/, (match) => ({ padding: `${match[1] / 4}rem` })],
]
```

当然这些比较常见的规则unocss已经提供了预设在`@unocss/preset-uno`中

```html
<div class="m-100">
  <button class="m-3">
    <icon class="p-5" />
    My Button
  </button>
</div>
```

生成出的css为

```css
.m-100 { margin: 25rem; }
.m-3 { margin: 0.75rem; }
.p-5 { padding: 1.25rem; }
```

unocss提供了一个叫shortcuts的功能，可以批量生成css

```js
shortcuts: [
  // you could still have object style
  {
    btn: 'py-2 px-4 font-semibold rounded-lg shadow-md',
  },
  // dynamic shortcuts
  [/^btn-(.*)$/, ([, c]) => `bg-${c}-400 text-${c}-100 py-2 px-4 rounded-lg`],
]
```

只需要配置这个字段

在使用中可以使用 `btn-green` 和 `btn-red` 

生成的css如下

```css
.btn-green {
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  padding-left: 1rem;
  padding-right: 1rem;
  --un-bg-opacity: 1;
  background-color: rgba(74, 222, 128, var(--un-bg-opacity));
  border-radius: 0.5rem;
  --un-text-opacity: 1;
  color: rgba(220, 252, 231, var(--un-text-opacity));
}
.btn-red {
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  padding-left: 1rem;
  padding-right: 1rem;
  --un-bg-opacity: 1;
  background-color: rgba(248, 113, 113, var(--un-bg-opacity));
  border-radius: 0.5rem;
  --un-text-opacity: 1;
  color: rgba(254, 226, 226, var(--un-text-opacity));
}
```

unocss提供了很多预设

###### Official Presets

- [@unocss/preset-uno](https://github.com/unocss/unocss/tree/main/packages/preset-uno) - The default preset (right now it's equivalent to `@unocss/preset-wind`).
- [@unocss/preset-mini](https://github.com/unocss/unocss/tree/main/packages/preset-mini) - The minimal but essential rules and variants.
- [@unocss/preset-wind](https://github.com/unocss/unocss/tree/main/packages/preset-wind) - Tailwind / Windi CSScompact preset.
- [@unocss/preset-attributify](https://github.com/unocss/unocss/tree/main/packages/preset-attributify) - Provides [Attributify Mode](https://github.com/unocss/unocss/tree/main/packages/preset-attributify#attributify-mode) to other presets and rules.
- [@unocss/preset-icons](https://github.com/unocss/unocss/tree/main/packages/preset-icons) - Use any icon as a class utility.
- [@unocss/preset-web-fonts](https://github.com/unocss/unocss/tree/main/packages/preset-web-fonts) - Web fonts at ease.
- [@unocss/preset-typography](https://github.com/unocss/unocss/tree/main/packages/preset-typography) - The typography preset.
- [@unocss/preset-tagify](https://github.com/unocss/unocss/tree/main/packages/preset-tagify) - Tagify Mode for UnoCSS.
- [@unocss/preset-rem-to-px](https://github.com/unocss/unocss/tree/main/packages/preset-rem-to-px) - Converts rem to px for utils.

###### Community Presets

- [unocss-preset-scalpel](https://github.com/macheteHot/unocss-preset-scalpel) - Scalpel Preset by [@macheteHot](https://github.com/macheteHot/).

- [unocss-preset-chroma](https://github.com/chu121su12/unocss-preset-chroma) - Gradient Preset by [@chu121su12](https://github.com/chu121su12).

- [unocss-preset-scrollbar](https://github.com/action-hong/unocss-preset-scrollbar) - Scrollbar Preset by [@action-hong](https://github.com/action-hong).

- [unocss-applet](https://github.com/unocss-applet/unocss-applet) - Using UnoCSSin applet (UniApp / Taro) by [@zguolee](https://github.com/zguolee).

- [unocss-preset-weapp](https://github.com/MellowCo/unocss-preset-weapp) - Wechat MiniProgram Preset for [UniApp](https://uniapp.dcloud.io/) and [Taro](https://taro-docs.jd.com/taro/docs) by [@MellowCo](https://github.com/MellowCo).

- [unocss-preset-heropatterns](https://github.com/Julien-R44/unocss-preset-heropatterns) - Preset that integrates [Hero Patterns](https://heropatterns.com/) by [@Julien-R44](https://github.com/Julien-R44).

- [unocss-preset-flowbite](https://github.com/Julien-R44/unocss-preset-flowbite) - Port of of [Flowbite Tailwind plugin](https://github.com/themesberg/flowbite) for UnoCSSby [@Julien-R44](https://github.com/Julien-R44).

- [unocss-preset-forms](https://github.com/Julien-R44/unocss-preset-forms) - Port of [@tailwindcss/forms](https://github.com/tailwindlabs/tailwindcss-forms) for UnoCSSby [@Julien-R44](https://github.com/Julien-R44).

- [unocss-preset-extra](https://github.com/MoomFE/unocss-preset-extra) - [Animate.css](https://animate.style/) Preset and some other rules by [@Zhang-Wei-666](https://github.com/Zhang-Wei-666).

- [unocss-preset-daisy](https://github.com/kidonng/unocss-preset-daisy) - daisyUI Preset by [@kidonng](https://github.com/kidonng).

- [unocss-preset-primitives](https://github.com/zirbest/unocss-preset-primitives) - Like [headlessui-tailwindcss](https://github.com/tailwindlabs/headlessui/tree/main/packages/%40headlessui-tailwindcss) , radix-ui , custom for UnoCSSBy [@zirbest](https://github.com/zirbest).

- [unocss-preset-theme](https://github.com/Dunqing/unocss-preset-theme) - Preset for automatic theme switching by [@Dunqing](https://github.com/Dunqing).

  通过这些预设可以达到融合tailwind和windi的效果

## UnoCSS的优点

对比其他原子化框架来说

- 完全可定制：作为一款引擎没有核心实用程序，所有的功能都是通过预设提供的。

- 扩展性强：通过各种预设组合达到windi或者tailwind的效果。
- 快：没有解析，没有AST，没有扫描，它是即时的。
- 小：运行库打包好只有6kb，无痛
- [Shortcuts](https://github.com/unocss/unocss/tree/main#shortcuts)：可以写Shortcuts来预设一些常用样式
- [Attributify mode](https://github.com/unocss/unocss/tree/main/packages/preset-attributify/) ：将class写成组，避免无法阅读的问题
- [Pure CSSIcons](https://github.com/unocss/unocss/tree/main/packages/preset-icons/) ：纯 CSSicon 可以在 https://icones.js.org/ 找到并导入成千上万的icon  安装只需要找到你喜欢的 iconfy 库 `@iconify-json/[the-collection-you-want]` 这样安装就行
- [Variant Groups](https://github.com/unocss/unocss/tree/main/packages/transformer-variant-group) ：和 [Attributify mode](https://github.com/unocss/unocss/tree/main/packages/preset-attributify/) 看起来几乎是一样的功能，写成组
- [CSSDirectives](https://github.com/unocss/unocss/tree/main/packages/transformer-directives) ：可以在style标签里写  `@apply`  把一系列样式打包成一个样式，在windi里是个很受欢迎的功能
- [Compilation mode](https://github.com/unocss/unocss/tree/main/packages/transformer-compile-class/) ：打包优化，生成随机class名
- [Inspector](https://github.com/unocss/unocss/tree/main#inspector) ：自带的debug工具
- [CSS-in-JS Runtime build](https://github.com/unocss/unocss/tree/main/packages/runtime) ：可以CDN导入
- [VS Code extension](https://marketplace.visualstudio.com/items?itemName=antfu.unocss)：自带VSCode提示扩展，很方便

## 结尾

本篇我们介绍了UnoCSS的特点，原子化CSS的好处，下一篇讲讲unocss实战，安装使用之类的

题外话：

> Anthony Fu大佬真的是我目前的偶像，狂热的开源爱好者，人长得又帅，做的东西又好

🤣这是我在掘金搜unocss搜到的一篇文章中的第一句话 文章在这 https://juejin.cn/post/7028841960752283656

antfu大佬确实也是我的偶像

![](https://picxyxsw.oss-cn-hangzhou.aliyuncs.com/20230125225032.png)

22年秋季 antfu大佬在b站办过几场直播写代码，我基本上是每期必看，这个人非常有意思，有很多新鲜的点子，狠活也多 比如说

https://github.com/antfu/1990-script 这个仓库教你怎么把GitHub历史穿越回1990年🤭

他的每个star比较多的仓库我都体验过，感觉设计灵感真的让人眼前一亮，启发基于社区，创造更快的轮子，更好玩的轮子

https://github.com/antfu/retypewriter 这个是直播写的雏形 让写的代码在vsc里回放，便于理清思路

https://github.com/antfu/vue-minesweeper 这个是首次直播写的扫雷，很清晰的vue代码，看他写真的开窍

https://github.com/antfu/vue-starport 这个也是直播写的雏形，为了使组件有更好的过渡

就说这么多，人也长得帅

🥰😘

##  参考

https://juejin.cn/post/7028841960752283656

https://antfu.me/posts/reimagine-atomic-css-zh

https://juejin.cn/post/7027414082378530852

https://juejin.cn/post/7161211941652791304

https://www.tailwindcss.cn/docs/