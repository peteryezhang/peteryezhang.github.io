---
layout:     post                    # 使用的布局（不需要改）
title:      什么是TaskScheduler以及SynchronizationContext          # 标题 
subtitle:    #副标题
date:       2020-03-28             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - .NET
    - Parallel
---

## 前言

练功练到深处，必然要学会知其然也要知其所以然。在TAP编程中，仅仅知道`async/await`是远远不够的。  
所以今天我们来简单谈一下TAP编程中的底层逻辑——`TaskScheduler` and `SynchronizationContext`。  

## 什么是`SynchronizationContext`

从.NET出现之前，多线程编程就面临一个问题：如何从一个线程将一组工作传给另一个线程。  Windows程序(.NET 之前)默认使用Message Loops来实现从一个线程传递消息到另一个线程。  
所以在.NET框架出现后，这一"Message Loop"的机制被设计成了一个新的类，即`ISynchronizeInvoke`，其最早是出现在 Windows Forms中。  
到了 .NET 2.0中，`ISynchronizeInvoke`被 `SynchronizationContext`替代，并被应用到了ASP.NET。  
`SynchronizationContext`提供了一种方法来把一组工作连接到一个上下文(context)中，注意这里是上下文而不是特定线程。  

不同的框架对于`SynchronizationContext`有不同的实现：
1. WindowsFormsSynchronizationContext， Windows Forms中用于控制UI的上下文；
2. DispatcherSynchronizationContext， WPF中的上下文；
3. Default (ThreadPool) SynchronizationContext， console中的默认上下文；
4. AspNetSynchronizationContext， ASP .NET中的上下文。

`Post`作为`SynchronizationContext`中最常用的方法，做的事情就是异步执行某一工作。  


## 什么是`TaskScheduler`

[System.Threading.Tasks.TaskScheduler](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.taskscheduler?view=netframework-4.8)简单来说就是用来管理任务在不同线程上执行的底层结构。  
默认的TaskScheduler类是基于 .NET Framework 4的线程池，这也是TPL以及PLINQ使用的类。其可以控制同步线程的最大数量(thread injection/retirement for maximum throughput)，防止线程偷懒(work-stealing for load-balancing)以及监控整体任务队列。  
具体地，`TaskScheduler`提供了`QueueTask`为任务提供FIFO的队列。


## 参考
1. [Parallel Computing - It's All About the SynchronizationContext](https://docs.microsoft.com/en-us/archive/msdn-magazine/2011/february/msdn-magazine-parallel-computing-it-s-all-about-the-synchronizationcontext)
2.https://devblogs.microsoft.com/dotnet/configureawait-faq/
https://docs.microsoft.com/en-us/dotnet/api/system.threading.synchronizationcontext?view=netframework-4.8
