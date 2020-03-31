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

## `SynchronizationContext` & `TaskScheduler` 跟 `await`之间的关系

在涉及UI线程的demo中，如果我们想要点击某个button async执行某些异步操作，然后将返回的结果返回到button的content中，我们必须确保UI thread跟async操作返回结果的线程是同一个线程。  
如果显示地使用`TaskScheduler`，我们需要如下操作:

```
private static readonly HttpClient s_httpClient = new HttpClient();

private void downloadBtn_Click(object sender, RoutedEventArgs e)
{
    s_httpClient.GetStringAsync("http://example.com/currenttime").ContinueWith(downloadTask =>
    {
        downloadBtn.Content = downloadTask.Result;
    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

如果显示地使用`SynchronizationContext`，我们需要如下操作:

```
private static readonly HttpClient s_httpClient = new HttpClient();

private void downloadBtn_Click(object sender, RoutedEventArgs e)
{
    SynchronizationContext sc = SynchronizationContext.Current;
    s_httpClient.GetStringAsync("http://example.com/currenttime").ContinueWith(downloadTask =>
    {
        sc.Post(delegate
        {
            downloadBtn.Content = downloadTask.Result;
        }, null);
    });
}
```

但是在具体使用`await`操作符的实践中，我们是这样写代码的：
```
private static readonly HttpClient s_httpClient = new HttpClient();

private async void downloadBtn_Click(object sender, RoutedEventArgs e)
{
    string text = await s_httpClient.GetStringAsync("http://example.com/currenttime");
    downloadBtn.Content = text;
}
```
可见，`await`为我们隐藏了幕后的很多操作。具体地，当对某一Task使用`await`时,`await`会调用`GetAwaiter`方法来查看任务返回的`TaskAwaiter<T>`。然后这一`awiter`会调用之后的`continuation`，在本例中为
```
    downloadBtn.Content = text;
```
同时`awaiter`会尝试获取目前能获取的`context/scheduler`：
```
object scheduler = SynchronizationContext.Current;
if (scheduler is null && TaskScheduler.Current != TaskScheduler.Default)
{
    scheduler = TaskScheduler.Current;
}
```
也就是`awaiter`会先尝试获取当前的`SynchronizationContext`，如果获取不到的话会再去获取当前的`TaskScheduler`。  

## ConfigureAwait(false)的用法

首先需要了解`await`可以用在任何返回正确design pattern的结构之前。如果在一个Task后面使用`ConfigureAwait(false)`，如下：
```
var t = Task.Run(delegate);
var configuredTaskAwaitable = t.ConfigureAwait(continueOnCapturedContext: false);
```
也就是说`ConfigureAwait`会返回一个`await`能处理的结构体`configuredTaskAwaitable`，然后把具体的Task t转换成如下的结构：
```
object scheduler = null;
if (continueOnCapturedContext)
{
    scheduler = SynchronizationContext.Current;
    if (scheduler is null && TaskScheduler.Current != TaskScheduler.Default)
    {
        scheduler = TaskScheduler.Current;
    }
}
```
因为`continueOnCapturedContext == false`，所以调用`ConfigureAwait(false)`的task将不再以来`SynchronizationContext`或者`TaskScheduler`。  

## 什么时候应该使用ConfigureAwait(false)

如果是应用级别的代码，比如Windows Forms, WPF, ASP.NET Core，基本可以默认不使用。如果是Library级别的通用代码，则一般无需考虑了synchrnizationContext的问题，可以使用。  



## 参考
1. [Parallel Computing - It's All About the SynchronizationContext](https://docs.microsoft.com/en-us/archive/msdn-magazine/2011/february/msdn-magazine-parallel-computing-it-s-all-about-the-synchronizationcontext)  
2. [ConfigureAwait FAQ](https://devblogs.microsoft.com/dotnet/configureawait-faq/)
