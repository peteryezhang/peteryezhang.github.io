---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes深入理解Stateful状态     # 标题 
subtitle:      #副标题
date:       2020-07-08             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---

## 有状态的应用

容器用来封装“无状态应用”（Stateless Application），尤其是 Web 服务，非常好用，但是并不适合有状态的应用。Kubernetes是第一个吃螃蟹的人。  

Kubernetes 项目很早就在 Deployment 的基础上，扩展出了对“有状态应用”的初步支持。这个编排功能，就是：`StatefulSet`。  

StatefulSet 的设计其实非常容易理解。它把真实世界里的应用状态，抽象为了两种情况：

1. 拓扑状态。
2. 存储状态。  

StatefulSet 的核心功能，就是通过某种方式记录这些状态，然后在 Pod 被重新创建时，能够为新 Pod 恢复这些状态。  

## 拓扑状态 

### Kubernetes中的Service - Headless Service

Service 是 Kubernetes 项目中用来将一组 Pod 暴露给外界访问的一种机制。比如，一个 Deployment 有 3 个 Pod，那么我就可以定义一个 Service。然后，用户只要能访问到这个 Service，它就能访问到某个具体的 Pod。  

Service有以下两种访问机制：

1. 以 Service 的 VIP（Virtual IP，即：虚拟 IP）方式
2. 以 Service 的 DNS 方式，还可以具体分为两种方法
    + 第一种处理方法，是 Normal Service。这种情况下，你访问“my-svc.my-namespace.svc.cluster.local”解析到的，正是 my-svc 这个 Service 的 VIP，后面的流程就跟 VIP 方式一致了。
    + 第二种处理方法，正是 `Headless Service`。这种情况下，你访问“my-svc.my-namespace.svc.cluster.local”解析到的，直接就是 my-svc 代理的某一个 Pod 的 IP 地址。**可以看到，这里的区别在于，Headless Service 不需要分配一个 VIP，而是可以直接以 DNS 记录的方式解析出被代理 Pod 的 IP 地址。**  

实际上，在部署“有状态应用”的时候，应用的每个实例拥有唯一并且稳定的“网络标识”，是一个非常重要的假设。 StatefulSet 这个控制器的主要作用之一，就是使用 Pod 模板创建 Pod 的时候，对它们进行编号，并且按照编号顺序逐一完成创建工作。而当 StatefulSet 的“控制循环”发现 Pod 的“实际状态”与“期望状态”不一致，需要新建或者删除 Pod 进行“调谐”的时候，它会严格按照这些 Pod 编号的顺序，逐一完成这些操作。  

### 存储状态

StatefulSet 对存储状态的管理机制。这个机制，主要使用的是一个叫作 `Persistent Volume Claim` 的功能。  

Persistent Volume Claim（PVC）和 Persistent Volume（PV）的 API 对象，大大降低了用户声明和使用持久化 Volume 的门槛。**Kubernetes 中 PVC 和 PV 的设计，实际上类似于“接口”和“实现”的思想。**  

### StatefulSet 的工作原理

1. **首先，StatefulSet 的控制器直接管理的是 Pod**这是因为，StatefulSet 里的不同 Pod 实例，不再像 ReplicaSet 中那样都是完全一样的，而是有了细微区别的。比如，每个 Pod 的 hostname、名字等都是不同的、携带了编号的。而 StatefulSet 区分这些实例的方式，就是通过在 Pod 的名字里加上事先约定好的编号。
2. **其次，Kubernetes 通过 Headless Service，为这些有编号的 Pod，在 DNS 服务器中生成带有同样编号的 DNS 记录**只要 StatefulSet 能够保证这些 Pod 名字里的编号不变，那么 Service 里类似于 web-0.nginx.default.svc.cluster.local 这样的 DNS 记录也就不会变，而这条记录解析出来的 Pod 的 IP 地址，则会随着后端 Pod 的删除和再创建而自动更新。这当然是 Service 机制本身的能力，不需要 StatefulSet 操心。
3. **最后，StatefulSet 还为每一个 Pod 分配并创建一个同样编号的 PVC**这样，Kubernetes 就可以通过 Persistent Volume 机制为这个 PVC 绑定上对应的 PV，从而保证了每一个 Pod 都拥有一个独立的 Volume。

