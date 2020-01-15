---
layout:     post                    # 使用的布局（不需要改）
title:      使用Powershell 并行处理              # 标题 
subtitle:   Parallel & Sequence的应用                    #副标题
date:       2020-01-11              # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - PowerShell
---
# 使用Powershell 并行处理
PowerShell并行处理依赖于[Workflow](https://docs.microsoft.com/en-us/system-center/sma/overview-powershell-workflows).  
Workflow使用PowerShell的cmdlet但事实上是基于 Windows PowerShell Workflows的一种用于自动化的工具。  
使用Workflow除了能够并行执行action的优点之外，还可以实现自主从失败中恢复。  
一个基本的Workflow如下：  
```
Workflow Test-Runbook
{
   <Commands>
}
```
Parallel的执行常用的三种template。
1. 使用Parallel 关键字
```
Parallel
{
  <Activity1>
  <Activity2>
}
<Activity3>
```
2. 在循环中并行
```
ForEach -Parallel ($<item> in $<collection>)
{
  <Activity1>
  <Activity2>
}
<Activity3>
```
3. 使用Sequence在并行代码中顺序执行
```
Parallel
{
  <Activity1>
  <Activity2>

  Sequence
  {
   <Activity3>
   <Activity4>
  }
}
<Activity5>
```