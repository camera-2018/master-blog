---
title: JavaScript ｜ 青训营笔记
date: 2023-1-18 22:51:23
tags: 青训营笔记
---
**这是我参与「第五届青训营」伴学笔记创作活动的第 3 天**

## 课程重点

1. 编码原则
2. 组件封装
3. 过程抽象概念
4. 高阶函数使用模式
5. JavaScript 编程范式

## 详细知识点介绍

### 编码原则

各司其职，组件封装，过程抽象

我们在编码的时候需要注意，尽量让HTML/CSS/JS各司其职，避免不必要的由JS直接操作样式。

### 组件封装

组件是指一个包含模板、功能、样式的单元，好的组件具有封装性、正确性、扩展性、复用性。 

### 过程抽象

过程抽象用来处理细节控制的一些方法。 需要函数式编程的思想。

函数式编程，简单理解就是无副作用的输入->处理->输出。

为了能够让`只执行一次`的需求覆盖不同的事件处理，我们可以将这个需求剥离出来，这个过程我们称为`过程抽象`。

### 高阶函数（HOF）介绍

- 以函数作为参数

- 以函数作为返回值

- 常用于作为函数装饰器

### 常见的高阶函数

#### Once

用于只需要执行一次的函数

```js
function once(fn) {
    return function(...args) {
        if(fn) {
            const ret = fn.apply(this, args);
            fn = null;
            return ret;
        }
    }
}
```

使用方法：

```js
const print = (content) => {
    console.log(content)
}

const printOnce = once(print)

printOnce("1") // 输出 1
printOnce("2") // 不输出
```

常用于只需要执行一次的的事件侦听器（也可以使用事件侦听器自带的once option，或者手动在执行后移除侦听器）

#### 节流（Throttle）

如果快速调用函数，那么只有第一次能成功调用函数，之后时间间隔之内的调用会被忽略。常用于滚动条事件/瀑布流无限滚动的场景（限制函数调用频率，保证一段时间内函数只调用一次）

```js
function throttle(fn, time = 500) {
    let timer = null;
    return function (...args) {
        if (timer) return;
        fn.apply(this, args);
        timer = setTimeout(() => {
            timer = null;
        }, time);
    }
}
```

#### 防抖

当在时间间隔之内快速调用函数时，函数将始终没法执行，直到停止后，函数才会被执行。常用于一些文本输入的场景（比如搜索框停止输入后，展示联想词；文章编辑器中，输入停止后，进行草稿的保存）。

```js
function debounce(fn, time = 500) {
    let timer = null;
    return function (...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(this, args);
        }, time);
    }
}
```



#### Consumer

从名字也很好理解，当快速点击时，函数并不会立刻被调用，而是先将任务推入列表，然后在一定时间后一个一个消费掉。

```js
function consumer(fn, time = 500) {
    let tasks = [];
    let timer = null;
    return function (...args) {
        tasks.push(args);
        if (timer) return;
        timer = setInterval(() => {
            fn.apply(this, tasks.shift());
            if (tasks.length === 0) {
                clearInterval(timer);
                timer = null;
            }
        }, time)
    }
}
```

#### Lterative 可迭代函数

### 编程范式

命令式与声明式。

命令式趋向于怎么做。声明式趋向于做什么。

JS是既有命令式又有声明式。

#### 命令式

```js
let list =[1,2,3]
let map = []

for(let i =0;i<list.length;i++){
    map.push(list[i]*2);
}
```

#### 声明式

```js
let list = [1,2,3,4];
const double = x => x*2;
list.map(double);
```

## 实践练习例子

实现一个只能执行一次的函数：

```js
// 只执行一次函数
const once = (fn) => {
  let done = false;
  return function (...args) {
    if (!done) {
      done = true;
      return fn.apply(this, args);
    }
  }
}

// 测试
const sayHello = (name) => {
  console.log(`Hello ${name}`);
};

const sayHelloOnce = once(sayHello);

sayHelloOnce('xiaoming'); // Hello xiaoming
sayHelloOnce('xiaohong'); // undefined
```



## 课后个人总结

​	本次课程让我学到了很多有关编程原则、组件封装、过程抽象概念、高阶函数使用模式以及JavaScript编程范式的知识，收获很大。 	

​	对于高阶函数，过去只是略有了解，现在能够真正去理解它们是什么，以及它们的用处如何。



## 引用参考

https://bytedance.feishu.cn/file/boxcnxKucsHPvnJ7PfXyCQF5WCd