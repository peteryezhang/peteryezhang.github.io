---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript 客户端Web API          # 标题 
subtitle:    #副标题
date:       2020-04-29             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

*本篇摘选自Mozilla的JavaScript文档： https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs*

当你给网页或者网页应用编写客户端的JavaScript时， 你很快会遇上应用程序接口（API ）—— 这些编程特性可用来操控网站所基于的浏览器与操作系统的不同方面，或是操控由其他网站或服务端传来的数据。  

## Web API简介

首先，我们将从一个高层次看看API - 它们是什么；他们如何工作；如何在代码中使用它们，以及它们是如何组织的。我们也将看看不同主要类别的API以及它们的用途。  

### 什么是API?

#### 客户端JavaScript中的API

客户端JavaScript中有很多可用的API — 他们本身并不是JavaScript语言的一部分，却建立在JavaScript语言核心的顶部，为使用JavaScript代码提供额外的超强能力。他们通常分为两类：  

1. 浏览器API内置于Web浏览器中，能从浏览器和电脑周边环境中提取数据，并用来做有用的复杂的事情 。例如Geolocation API提供了一些简单的JavaScript结构以获得位置数据，因此您可以在Google地图上标示您的位置。在后台，浏览器确实使用一些复杂的低级代码（例如C++）与设备的GPS硬件（或可以决定位置数据的任何设施）通信来获取位置数据并把这些数据返回给您的代码中使用浏览器环境；但是，这种复杂性通过API抽象出来，因而与您无关。
2. 第三方API缺省情况下不会内置于浏览器中，通常必须在Web中的某个地方获取代码和信息。  

![avatar](https://mdn.mozillademos.org/files/13508/browser.png)

#### JavaScript，API和其他JavaScript工具之间的关系

如上所述，我们讨论了什么是客户端JavaScript API，以及它们与JavaScript语言的关系。让我们回顾一下，使其更清晰，并提及其他JavaScript工具的适用位置：  

1. JavaScript — 一种内置于浏览器的高级脚本语言，您可以用来实现Web页面/应用中的功能。注意JavaScript也可用于其他象Node这样的的编程环境。但现在您不必考虑这些。
2. 客户端API — 内置于浏览器的结构程序，位于JavaScript语言顶部，使您可以更容易的实现功能。
3. 第三方API — 置于第三方普通的结构程序（例如Twitter，Facebook），使您可以在自己的Web页面中使用那些平台的某些功能（例如在您的Web页面显示最新的Tweets）。
4. JavaScript库 — 通常是包含具有特定功能的一个或多个JavaScript文件，把这些文件关联到您的Web页以快速或授权编写常见的功能。例如包含jQuery和Mootools
5. JavaScript框架 — 从库开始的下一步，JavaScript框架视图把HTML、CSS、JavaScript和其他安装的技术打包在一起，然后用来从头编写一个完整的Web应用。

### API可以做什么？

#### 常见浏览器API

1. 操作文档的API内置于浏览器中。最明显的例子是DOM（文档对象模型）API，它允许您操作HTML和CSS — 创建、移除以及修改HTML，动态地将新样式应用到您的页面，等等。
2. 从服务器获取数据的API，例如Ajax。
3. 用于绘制和操作图形的API。
4. 音频和视频API例如HTMLMediaElement，Web Audio API和WebRTC。
5. 设备API。
6. 客户端存储API在Web浏览器中的使用变得越来越普遍。例如使用Web Storage API的简单的键 - 值存储以及使用IndexedDB API的更复杂的表格数据存储。  

## 操作文档

在编写web页面或应用时，你最想做的事情之一就是以某种方式操作文档结构。这通常使用一套大量使用Document对象来控制HTML和样式信息的文档对象模型（DOM）来实现，在本文中，我们可以更详细的看到怎样使用DOM，连同一些其他有趣的API以有趣的方式改变你的环境。  

### web浏览器的重要部分

![avatar](https://mdn.mozillademos.org/files/14557/document-window-navigator.png)  

1. **window**是载入浏览器的标签，在JavaScript中用Window对象来表示，使用这个对象的可用方法，你可以返回窗口的大小（参见Window.innerWidth和Window.innerHeight），操作载入窗口的文档，存储客户端上文档的特殊数据（例如使用本地数据库或其他存储设备），为当前窗口绑定event handler，等等。
2. **navigator**表示浏览器存在于web上的状态和标识（即用户代理）。在JavaScript中，用Navigator来表示。你可以用这个对象获取一些信息，比如来自用户摄像头的地理信息、用户偏爱的语言、多媒体流等等。
3. **document**（在浏览器中用DOM表示）是载入窗口的实际页面，在JavaScript中用Document 对象表示，你可以用这个对象来返回和操作文档中HTML和CSS上的信息。例如获取DOM中一个元素的引用，修改其文本内容，并应用新的样式，创建新的元素并添加为当前元素的子元素，甚至把他们一起删除。

### 基本的DOM操作

略

## 从服务器获取数据

在现代网站和应用中另一个常见的任务是从服务端获取个别数据来更新部分网页而不用加载整个页面。 这看起来是小细节却对网站性能和行为产生巨大的影响。所以我们将在这篇文章介绍概念和技术使它成为可能，例如： XMLHttpRequest 和 Fetch API.  

### Ajax

这是通过使用诸如 `XMLHttpRequest` 之类的API或者 — 最近以来的 `Fetch API` 来实现. 这些技术允许网页直接处理对服务器上可用的特定资源的 HTTP 请求，并在显示之前根据需要对结果数据进行格式化。  

在早期，这种通用技术被称为Asynchronous JavaScript and XML（Ajax）， 因为它倾向于使用XMLHttpRequest 来请求XML数据。 但通常不是这种情况 (你更有可能使用 XMLHttpRequest 或 Fetch 来请求JSON), 但结果仍然是一样的，术语“Ajax”仍然常用于描述这种技术。  

### 基本的Ajax请求

让我们看看使用XMLHttpRequest 和 Fetch如何处理这样的请求。  

#### XMLHttpRequest

XMLHttpRequest （通常缩写为XHR）现在是一个相当古老的技术 - 它是在20世纪90年代后期由微软发明的，并且已经在相当长的时间内跨浏览器进行了标准化。  

基本结构如下：
```
let request = new XMLHttpRequest();
request.open('GET', url);
request.responseType = 'text';

request.onload = function() {
  poemDisplay.textContent = request.response;
};

request.send();
```


#### Fetch

`Fetch`的基本结构如下：

```
fetch(url).then(function(response) {
  response.text().then(function(text) {
    poemDisplay.textContent = text;
  });
});
```

## 第三方 API

到目前为止我们已经介绍的API是内置在浏览器中的，但并不是所有的API都是。许多大型网站和服务（例如Google地图，Twitter，Facebook，PayPal等）提供的API允许开发者使用他们的数据（例如在博客上显示您的Twitter流）或服务（例如在您的网站上显示自定义Google地图，或者使用Facebook登录来登录你的用户）。本文着眼于浏览器API和第三方API的区别，并展示了后者的一些典型用途。  


