---
layout:     post                    # 使用的布局（不需要改）
title:     Develop Windows service apps    # 标题 
subtitle:     #副标题
date:       2020-08-19             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - .NET
    
---
## Introduction

Microsoft Windows services, formerly known as NT services, enable you to create **long-running executable applications** that run in their own Windows sessions.   

Service Applications vs. Other Visual Studio Applications:  

+ You cannot debug or run a service application by pressing F5 or F11; you cannot immediately run a service or step into its code. Instead, you must install and start your service, and then attach a debugger to the service's process. For more information, see How to: Debug Windows Service Applications.  

+ Unlike some types of projects, you must create installation components for service applications.   

+ The Main method for your service application must issue the `Run` command for the services your project contains.  


## Service Lifetime

+ First, the service is installed onto the system on which it will run. **Services Control Manager**

+ After the service has been loaded, it must be started.  ou can start a service from the **Services Control Manager**, from **Server Explorer**, or from code by calling the `Start` method

+ A service can exist in one of three basic states: Running, Paused, or Stopped.

## Types of Services

+  Win32OwnProcess: Services that are the only service in a process
+  Win32OwnProcess:  Services that share a process with another service

## Add installers to the service

Before you run a Windows service, you need to install it, which registers it with the **Service Control Manager**.  



## Reference

1. [Develop Windows service apps](https://docs.microsoft.com/en-us/dotnet/framework/windows-services/)
