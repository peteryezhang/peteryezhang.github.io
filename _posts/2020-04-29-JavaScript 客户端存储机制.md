---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript 客户端存储机制         # 标题 
subtitle:    #副标题
date:       2020-04-29             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

*本篇摘选自Mozilla的JavaScript文档： https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage*

## 前言

现代web浏览器提供了很多在用户电脑web客户端存放数据的方法 — 只要用户的允许 — 可以在它需要的时候被重新获得。这样能让你存留的数据长时间保存, 保存站点和文档在离线情况下使用, 保留你对其站点的个性化配置等等。  

## 传统的cookies

cookie的唯一优势是它们得到了非常旧的浏览器的支持，所以如果您的项目需要支持已经过时的浏览器（比如 Internet Explorer 8 或更早的浏览器），cookie可能仍然有用，但是对于大多数项目（很明显不包括本站）来说，您不需要再使用它们了。其实cookie也没什么好说的，`document.cookie`一把梭就完事了。

## 新流派：Web Storage 和 IndexedDB

现代浏览器有比使用 cookies 更简单、更有效的存储客户端数据的 API。

1. Web Storage API 提供了一种非常简单的语法，用于存储和检索较小的、由名称和相应值组成的数据项。当您只需要存储一些简单的数据时，比如用户的名字，用户是否登录，屏幕背景使用了什么颜色等等，这是非常有用的。
2. IndexedDB API 为浏览器提供了一个完整的数据库系统来存储复杂的数据。这可以用于存储从完整的用户记录到甚至是复杂的数据类型，如音频或视频文件。

## 存储简单数据 — web storage

Web Storage API 非常容易使用 — 你只需存储简单的 键名/键值 对数据 (限制为字符串、数字等类型) 并在需要的时候检索其值。  

所有的 web storage 数据都包含在浏览器内两个类似于对象的结构中： `sessionStorage` 和 `localStorage`。 第一种方法，只要浏览器开着，数据就会一直保存 (关闭浏览器时数据会丢失) ，而第二种会一直保存数据，甚至到浏览器关闭又开启后也是这样。我们将在本文中使用第二种方法，因为它通常更有用。  

### 基本语法

```
localStorage.setItem('name','Chris');
localStorage.getItem('name');
localStorage.removeItem('name');

```

## 存储复杂数据 — IndexedDB

IndexedDB API（有时简称 IDB ）是可以在浏览器中访问的一个完整的数据库系统，在这里，你可以存储复杂的关系数据。其种类不限于像字符串和数字这样的简单值。你可以在一个IndexedDB中存储视频，图像和许多其他的内容。  

## 离线文件存储

每次访问网站时仍然需要下载主要的HTML，CSS和JavaScript文件，这意味着当没有时，它将无法工作网络连接。  

![avatar](https://mdn.mozillademos.org/files/15759/ff-offline.png)  

这就是`Service Worker`和密切相关的`Cache API`的用武之地。  



