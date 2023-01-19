---
title: TypeScript ｜ 青训营笔记
date: 2023-1-19 23:55:39
tags: 青训营笔记
---
**这是我参与「第五届青训营」伴学笔记创作活动的第 4 天**

## 本堂课重点内容

1. TypeScript 定义解析
2. TypeScript 基础语法

## 详细知识点介绍

1. 语言特性
2. 基本数据类型

### 语言特性

JavaScript 是动态类型的语言，而TypeScript顾名思义是静态类型的语言。与js相比，静态类型的特性为ts带来了许多优势

- 更强的可读性
- 更强的可维护性
- 大型项目中提升稳定性
- 开发效率

而作为js的超集，ts兼容所有js特性，且支持与js共存，能够渐进式地引入和升级。

### 基础数据类型

TypeScript能够在变量名称后面添加冒号和期望的类型来为变量赋予具体的类型，例如下面的代码，常见的类型有 `string` `number` `boolean` `null` `undefined`

```ts
let a: string = "string"
let b: number = 12
let c: boolean = true
```

如果在之后尝试给变量赋值不同类型的值，将会产生报错

### 对象类型

ts使用`interface`关键字来定义新类型

```ts
interface ICustomObject {
    name: string
    age: number
    hobby?: string
    readonly id: number
}

const obj: ICustonObject {
    name: "hello world",
    age: 12,
    hobby: 'coding',
    id: 10
}
```

对于只读属性，可以在类型定义时增加readonly修饰符，在之后倘若修改只读属性，将会报错。在属性名后面紧跟问号表示该属性可选（可能不存在），后续倘若直接读取该属性，将导致报错（需要先判断该属性是否存在）

> 一般情况下，约定自定义类型以大写字母`I`开头，例如上面的 `ICustomObject`

#### 特殊情况

在某些情况下，对象的键名（key）可能并不固定，比如我希望某个对象键名是任意string，键值是布尔型，那么可以向下面这样定义

```ts
interface IObj {
    [key: string]: boolean
}
```

或者使用 `type` 关键字

```ts
type IObj = Record<string, boolean>
```

### 函数类型

假设有一个js函数 `add`

```js
function add(x, y) {
    return x + y
}
```

为其添加类型声明后

```ts
function add(x: number, y: number):number {
    return x + y
}
```

对于匿名的箭头函数，我们也可以为其添加类型

```ts
// js
const add = (x, y) => x + y

// ts
const add: (x: number, y: number) => number = (x, y) => x + y
```

或许在某些情况下，将类型和函数写在一起会稍显凌乱，那么我们也可以将函数类型单独定义，例如下面这样

```ts
interface IAdd {
    (x: number, y: number): number
}

const add: IAdd = (x, y) => x + y
```

### 数组类型

数组的类型定义有很多方法，最常见的是像c语言那样的类型定义

```ts
type IArray = number[]

const array: IArray = [1, 2, 3, 4, 5]
```

同样也可以使用ts提供的Array泛型，是一样的效果

```ts
type IArray = Array<number>

const array: IArray = [1, 2, 3, 4, 5]
```

因为数组实际上就是键名特殊的对象，所以也可以用表示对象的方法来表示

```ts
interface IArray {
    [key: number]: number
}

const array: IArray = [1, 2, 3, 4, 5]
```

### TS 新增的类型

为了实现一些特殊需求，ts也新增了许多类型方便使用

#### 空类型

```ts
function test(): void {
    alert("hello")
}
```

顾名思义，表示无赋值，例如函数没有返回值

### 任意类型

```ts
type IArray = Array<any>

const array: IArray = [1, "string", true, {a: 1}]
```

### 枚举类型

该类型能够通过枚举名查枚举值，同时能够使用枚举值查枚举名

```ts
enum EnumTest {
    man = 'male',
    woman = 'female',
}

EnumTest['man'] === 'male'
EnumTest['male'] === 'man'
```

在不提供枚举名的时候，将默认为由0开始的索引值

```ts
enum EnumWeekDay { Mon, Tue, Wed, Thu, Fri }

EnumTest['Mon'] === 0
EnumTest['Tue'] === 1
```

### 高级类型

#### 联合类型与交叉类型

联合类型： `IA | IB`;

交叉类型： `IA & IB;`

#### 类型保护

可以直接通过`.type`然后拿到某个变量的类型，可用于后续变量类型的判断，达到类型保护的作用。

#### 补充类型

- 空类型，表示无赋值
- 任意类型，是所有类型的子类型
- 枚举类型：支持枚举值到枚举名的正、反向映射



## 课后个人总结

TypeScript是一个非常有用的语言，给JavaScript开发带来了严格的检查



## 引用参考

www.typescriptlang.org/docs/

https://bytedance.feishu.cn/file/boxcnIjrYQpF7pL6nWGtTl5W8Jd