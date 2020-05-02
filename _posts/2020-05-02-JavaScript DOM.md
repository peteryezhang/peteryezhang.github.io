---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript DOM         # 标题 
subtitle:    #副标题
date:       2020-05-02             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

*本篇摘选自Mozilla的JavaScript文档： https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model*

## 什么是DOM

文档对象模型 (DOM) 是HTML和XML文档的编程接口。它提供了对文档的结构化的表述，并定义了一种方式可以使从程序中对该结构进行访问，从而改变文档的结构，样式和内容。DOM 将文档解析为一个由节点和对象（包含属性和方法的对象）组成的结构集合。简言之，它会将web页面和脚本或程序语言连接起来。  

 [W3C DOM](https://dom.spec.whatwg.org/) 和[WHATWG DOM](https://dom.spec.whatwg.org/)标准在绝大多数现代浏览器中都有对DOM的基本实现。许多浏览器提供了对W3C标准的扩展，所以在使用时必须注意，文档可能会在多种浏览器上使用不同的DOM来访问。  

 ### DOM 和 JavaScript

开始的时候，JavaScript和DOM是交织在一起的，但它们最终演变成了两个独立的实体。JavaScript可以访问和操作存储在DOM中的内容，因此我们可以写成这个近似的等式：  

API (web 或 XML 页面) = DOM + JS (脚本语言)

### 如何访问 DOM?

当您在创建一个脚本时-无论是使用内嵌 `<script>`元素或者使用在web页面脚本加载的方法— 您都可以使用 document或 window 元素的API来操作文档本身或获取文档的子类（web页面中的各种元素）。  

### DOM 接口

您在DOM编程时，通常使用的最多的就是 Document 和 window 对象。简单的说， window 对象表示浏览器中的内容，而 document 对象是文档本身的根节点。Element 继承了通用的 Node 接口,  将这两个接口结合后就提供了许多方法和属性可以供单个元素使用。在处理这些元素所对应的不同类型的数据时，这些元素可能会有专用的接口，如上节中的  table  对象的例子。  

下面是在web和XML页面脚本中使用DOM时，一些常用的API简要列表。  

1. document.getElementById(id)
2. document.getElementsByTagName(name)
3. document.createElement(name)
4. parentNode.appendChild(node)
5. element.innerHTML
6. element.style.left
7. element.setAttribute()
8. element.getAttribute()
9. element.addEventListener()
10. window.content
11. window.onload
12. window.dump()
13. window.scrollTo()


## 使用 W3C DOM Level 1 核心

### DOM Level 1 核心能让我们做什么?

W3C的DOM Level 1允许你随意改变内容树。其功能之强大足以从零构建出任何HTML文档。它允许作者在任何时候，都可以通过脚本来修改文档里的任何内容。这是网页制作者通过JavaScript动态改变DOM的最简单途径。在版本较旧的浏览器里，使用JavaScript都是通过访问全局对象 document 属性来得到文档。  

## 使用Javascript和DOM Interfaces来处理HTML

本文概述了一些强大的，基本的DOM 1 级别中的方法以及如何在JavaScript中使用它们。你将会如何动态地创建，访问，控制以及移除HTML元素。**这里提到的DOM方法，并非是HTML专有的；它们在XML中同样适用。**  

etc.

## 使用选择器Selectors API 定位DOM元素

Selectors API提供了通过与一组选择器匹配来快速轻松地从DOM检索  Element节点的方法。这比以前的技术要快得多，其中有必要使用JavaScript代码中的循环来定位您需要查找的特定项目。  

### NodeSelector 接口

此规范向实现  Document, DocumentFragment,  或 Element 接口的任何对象添加了两种新方法：

1. querySelector
    返回节点子树内与之相匹配的第一个 Element 节点。如果没有匹配的节点，则返回null。
2. querySelectorAll
    返回一个NodeList  包含节点子树内所有与之相匹配的Element节点，如果没有相匹配的，则返回一个空节点列表。

### Selectors

选择器方法接受一个或多个逗号分隔的选择器来确定应该返回哪些元素。

例如，要选择文档中所有CSS的类(class)是warning或者note的段落(p)元素,可以这样写：  

```
var special = document.querySelectorAll( "p.warning, p.note" );

```
也可以通过ID来查询，例如：  

```
var el = document.querySelector( "#main, #basic, #exclamation" );

```
执行上面的代码后，el就包含了文档中元素的ID是main，basic或exclamation的所有元素中的第一个元素。  

## 事件及DOM

这一章节介绍了DOM事件模型（DOM Event Model）。主要描述了事件（Event）接口本身以及DOM节点中的事件注册接口、事件监听接口，以及几个展示了多种事件接口之间相互关联的较长示例。  

这里有一张非常不错的图表清晰地解释了在DOM3级事件草案（DOM Level 3 Events draft）中通过DOM处理事件流的三个阶段。  

![avatar](https://www.w3.org/TR/DOM-Level-3-Events/images/eventflow.svg)

### 注册事件监听器

这里有3种方法来为一个DOM元素注册事件回调。

1.EventTarget.addEventListener

```
// 假设 myButton 是一个按钮
myButton.addEventListener('click', greet, false);
function greet(event) {
    // 打印并查看event对象
    // 打印arguments，以防忽略了其他参数
    console.log('greet: ' + arguments);
    alert('Hello world');
} 
```  

2. HTML 属性

```
<button onclick="alert('Hello world!')">

```
属性中的JS代码触发时通过event参数将Event类型对象传递过去的。其返回值以特殊的方式来处理，已经在HTML规范中被描述。  
**应该尽量避免这种书写方式**，这将使HTML变大并减少可读性。考虑到内容/结构及行为不能很好的分开，这将造成bug很难被找到。  

3. DOM 元素属性  

```
// 假设 myButton 是一个按钮
myButton.onclick = function(event){alert('Hello world');};
```
这种方式的问题是每个事件及每个元素只能被设置一个回调。

### 访问事件接口




## References

1. [Traversing an HTML table with JavaScript and DOM Interfaces](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Traversing_an_HTML_table_with_JavaScript_and_DOM_Interfaces)
2. [Document Object Model (Core) Level 1](https://www.w3.org/TR/REC-DOM-Level-1/level-one-core.html)
3. [DOM Examples](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Examples)




