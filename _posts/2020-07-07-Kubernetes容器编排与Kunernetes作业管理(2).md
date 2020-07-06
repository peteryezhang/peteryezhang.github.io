---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes容器编排与Kunernetes作业管理（2）     # 标题 
subtitle:    控制器模型  #副标题
date:       2020-07-07             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---

## Kubernete中的组件编排

Master中的`kube-controller-manager`的组件 实际上就是一系列控制器的集合。我们可以查看一下 Kubernetes 项目的 pkg/controller 目录:

```

$ cd kubernetes/pkg/controller/
$ ls -d */              
deployment/             job/                    podautoscaler/          
cloud/                  disruption/             namespace/              
replicaset/             serviceaccount/         volume/
cronjob/                garbagecollector/       nodelifecycle/          replication/            statefulset/            daemon/
...
```

实际上，这些控制器之所以被统一放在 pkg/controller 目录下，就是因为它们都遵循 Kubernetes 项目中的一个通用编排模式，即：**控制循环（control loop）。**  

+ 在具体实现中，实际状态往往来自于 Kubernetes 集群本身。
+ 而期望状态，一般来自于用户提交的 YAML 文件。

### 控制器模型的具体实现

以 Deployment 为例，我和你简单描述一下它对控制器模型的实现：
1. Deployment 控制器从 Etcd 中获取到所有携带了“app: nginx”标签的 Pod，然后统计它们的数量，这就是实际状态；
2. Deployment 对象的 Replicas 字段的值就是期望状态；
3. Deployment 控制器将两个状态做比较，然后根据比较结果，确定是创建 Pod，还是删除已有的 Pod（调谐 Reconcile）。

一个控制器模型的结构如下图：

![avatar](https://static001.geekbang.org/resource/image/72/26/72cc68d82237071898a1d149c8354b26.png)

可以看到，控制器的结构**实际上都是由上半部分的控制器定义（包括期望状态），加上下半部分的被控制对象的模板组成的。**

**而这个 template 字段里的内容，跟一个标准的 Pod 对象的 API 定义，丝毫不差**。而所有被这个 Deployment 管理的 Pod 实例，其实都是根据这个 template 字段的内容创建出来的。  

## ReplicaSet的概念

现在以Deployment为例，来展开ReplicaSet的概念。  

Deployment 看似简单，但实际上，它实现了 Kubernetes 项目中一个非常重要的功能：Pod 的“水平扩展 / 收缩”`（horizontal scaling out/in）`。这个功能，是从 PaaS 时代开始，一个平台级项目就必须具备的编排能力。而这个能力的实现，依赖的是 Kubernetes 项目中的一个非常重要的概念（API 对象）：`ReplicaSet`。  

一个ReplicaSet的YAML文件如下:
```

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-set
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
```

从这个 YAML 文件中，我们可以看到，一个 ReplicaSet 对象，其实就是由副本数目的定义和一个 Pod 模板组成的。**不难发现，它的定义其实是 Deployment 的一个子集。更重要的是，Deployment 控制器实际操纵的，正是这样的 ReplicaSet 对象，而不是 Pod 对象。**  

![avatar](https://static001.geekbang.org/resource/image/bb/5d/bbc4560a053dee904e45ad66aac7145d.jpg)

通过这张图，我们就很清楚地看到，一个定义了 replicas=3 的 Deployment，与它的 ReplicaSet，以及 Pod 的关系，实际上是一种“层层控制”的关系。  
Deployment 的控制器，实际上控制的是 ReplicaSet 的数目，以及每个 ReplicaSet 的属性。  
Deployment 实际上是一个两层控制器。首先，它通过 ReplicaSet 的个数来描述**应用的版本**；然后，它再通过 ReplicaSet 的属性（比如 replicas 的值），来保证 Pod 的副本数量。  
