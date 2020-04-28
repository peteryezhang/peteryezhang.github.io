---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript中的模块          # 标题 
subtitle:    #副标题
date:       2020-04-28             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

*本篇摘选自Mozilla的JavaScript文档： https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Details_of_the_Object_Model*


## 前言

Javascript 程序本来很小——在早期，它们大多被用来执行独立的脚本任务，在你的 web 页面需要的地方提供一定交互，所以一般不需要多大的脚本。过了几年，我们现在有了运行大量 Javascript 脚本的复杂程序，还有一些被用在其他环境（例如 Node.js）。  

因此，近年来，有必要开始考虑提供一种将 JavaScript 程序拆分为可按需导入的单独模块的机制。Node.js 已经提供这个能力很长时间了，还有很多的 Javascript 库和框架 已经开始了模块的使用（例如， CommonJS 和基于 AMD 的其他模块系统 如 RequireJS, 以及最新的 Webpack 和 Babel）。  

好消息是，最新的浏览器开始原生支持模块功能了，这是本文要重点讲述的。这会是一个好事情 — 浏览器能够最优化加载模块，使它比使用库更有效率：使用库通常需要做额外的客户端处理。

## 基本文件结构

在我们的第一个例子 (see basic-modules) 文件结构如下:

```
index.html
main.mjs
modules/
    canvas.mjs
    square.mjs
```

## 导出模块的功能

为了获得模块的功能要做的第一件事是把它们导出来。使用 `export` 语句来完成。  

最简单的方法是把它（指上面的export语句）放到你想要导出的项前面，比如：

```
export const name = 'square';

export function draw(ctx, length, x, y, color) {
  ctx.fillStyle = color;
  ctx.fillRect(x, y, length, length);

  return {
    length: length,
    x: x,
    y: y,
    color: color
  };
}
```
一个更方便的方法导出所有你想要导出的模块的方法是在模块文件的末尾使用一个export 语句， 语句是用花括号括起来的用逗号分割的列表。比如：
```
export { name, draw, reportArea, reportPerimeter };
```

## 导入模块的功能

你想在模块外面使用一些功能，那你就需要导入他们才能使用。最简单的就像下面这样的：  

```
import { name, draw, reportArea, reportPerimeter } from '/js-examples/modules/basic-modules/modules/square.mjs';
```

## 创建模块对象

上面的方法工作的挺好，但是有一点点混乱、亢长。一个更好的解决方是，导入每一个模块功能到一个模块功能对象上。可以使用以下语法形式：  

```
import * as Module from '/modules/module.mjs';
```
这将获取module.mjs中所有可用的导出，并使它们可以作为对象模块的成员使用，从而有效地为其提供自己的命名空间。 例如：  
```
Module.function1()
Module.function2()
etc.
```

