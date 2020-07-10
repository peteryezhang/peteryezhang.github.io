---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes 容器运行时CRI     # 标题 
subtitle:     #副标题
date:       2020-07-10             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---


## 从kubelet讲起

在Kubernetes中，`kubelet`可谓是调度和资源管理相关的核心组件。  

实际上，在调度这一步完成后，Kubernetes 就需要负责将这个调度成功的 Pod，在宿主机上创建出来，并把它所定义的各个容器启动起来。这些，都是 kubelet 这个核心组件的主要功能。  

kubelet 本身，也是按照“控制器”模式来工作的。它实际的工作原理，可以用如下所示的一幅示意图来表示清楚。  

![avatar](https://static001.geekbang.org/resource/image/91/03/914e097aed10b9ff39b509759f8b1d03.png)  

可以看到，kubelet 的工作核心，就是一个控制循环，即：SyncLoop（图中的大圆圈）。而驱动这个控制循环运行的事件，包括四种：  

1. Pod 更新事件；
2. Pod 生命周期变化；
3. kubelet 本身设置的执行周期；
4. 定时的清理事件。  

在具体的对Pod的处理中，**kubelet 调用下层容器运行时的执行过程，并不会直接调用 Docker 的 API，而是通过一组叫作 CRI（Container Runtime Interface，容器运行时接口）的 gRPC 接口来间接执行的。**  
Kubernetes 项目之所以要在 kubelet 中引入这样一层单独的抽象，当然是为了对 Kubernetes 屏蔽下层容器运行时的差异。*实际上，对于 1.6 版本之前的 Kubernetes 来说，它就是直接调用 Docker 的 API 来创建和管理容器的。*  
在用了CRI之后，Kubernetes 以及 kubelet 本身的架构，就可以用如下所示的一幅示意图来描述。  

![avatar](https://static001.geekbang.org/resource/image/51/fe/5161bd6201942f7a1ed6d70d7d55acfe.png)  

如果你使用的容器项目是 Docker 的话，那么负责响应这个请求的就是一个叫作 `dockershim` 的组件。它会把 CRI 请求里的内容拿出来，然后组装成 Docker API 请求发给 Docker Daemon。  

## CRI 与 容器运行时

CRI 机制能够发挥作用的核心，就在于每一种容器项目现在都可以自己实现一个 CRI shim，自行对 CRI 请求进行处理。这样，Kubernetes 就有了一个统一的容器抽象层，使得下层容器运行时可以自由地对接进入 Kubernetes 当中。  





