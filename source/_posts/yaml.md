---
title: yaml
date: 2021-10-20 14:00:18
tags:
---
# 🍉前提知识
1. Nodejs和npm，会使用Npm安装包
2. 了解yaml基础语法
3. 基本的js
4. 从命令行接收参数 http://nodejs.cn/learn/nodejs-accept-arguments-from-the-command-line
5. 如何本地直接运行js代码：打开终端，输入 `node xxx.js`

# 涉及到的库
1. [js-yaml](https://github.com/nodeca/js-yaml)

# 练习内容：无页面版Todo list
1. 在你的项目文件夹新建一个 todo.yaml
   - 包含三大块： todo, doing, done，分别代表待办任务的三种状态
   - 每条任务分布在三大块中，每条任务有以下属性：id，content, 
   
2. 实现以下命令： 
  - `node xxx.js ls`：列出所有任务
  - `node xxx.js ls --status=done`：列出所有任务完成的任务
  - `node xxx.js ls --status=doing`: 列出所有进行中的任务
  - `node xxx.js ls --status=todo`
  - `node xxx.js done 任务id`： 将该id对应的任务置为完成状态
  - `node xxx.js todo 任务id`
  - `node xxx.js doing 任务id`
  - 下面的我省略node xxx.js：
  - delete 任务id
  - add 任务内容
  。。。自行想象添加功能。。。
  
# 一些实现的细节：
  1. 利用js-yaml中的dump保存js对象到yaml文件
  2. 利用js-yaml中的load读取yaml文件到js对象
  3. 可以边写边加入es6语法来练习
---

# 最后声明
> 本练习真的只是为了你练习，主要是练习node,npm,js，es6,所以还是靠自觉完成，**但是您的提交次数会作为我们评优的依据**，完成了请推到你的分支





# links
https://github.com/hduhelp/frontend_2021_pratice/

如何将yaml转为js对象: https://github.com/nodeca/js-yaml

每日一篇之yaml: http://www.ruanyifeng.com/blog/2016/07/yaml.html

每日一篇之es6: https://www.w3cschool.cn/escript6/escript6-827l37er.html  ES6 简介_w3cschool

安利一本书https://es6.ruanyifeng.com/ ES6 入门教程

https://hduhelp21.yuque.com/staff-mhchbm/ergbdy/lobnc5