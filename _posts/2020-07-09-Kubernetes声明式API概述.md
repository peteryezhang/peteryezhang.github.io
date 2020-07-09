---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes声明式API概述     # 标题 
subtitle:   声明式 API 与 Kubernetes 编程范式   #副标题
date:       2020-07-09             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---

## 以Istio为例看API对象的用途

### 命令式命令行操作

`Dock Swarm`中的编排都是基于命令行的:
```
$ docker service create --name nginx --replicas 2  nginx
$ docker service update --image nginx:1.7.9 nginx
```
像这样的两条命令，就是用 Docker Swarm 启动了两个 Nginx 容器实例。  

### 命令式配置文件操作

类似的操作在Kubernetes中则是通过配置YAML文件实现的:

1. 首先编写一个YAML的Deployment文件:
```

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```
2. 然后用`kubectl create` 命令在 Kubernetes 里创建这个 Deployment 对象：  

```
$ kubectl create -f nginx.yaml
```  

### 声明式API操作

使用`kubectl apply` 命令来创建这个 Deployment：  

```
$ kubectl apply -f nginx.yaml
```  

### `kubectl replace` 与 `kubectl apply`的区别

实际上可以地理解为，`kubectl replace` 的执行过程，是使用新的 YAML 文件中的 API 对象，替换原有的 API 对象；**而 `kubectl apply`，则是执行了一个对原有 API 对象的 `PATCH` 操作。**  

### Istio的例子

Istio是一个基于 Kubernetes 项目的微服务治理框架。它的架构非常清晰，如下所示：  

![avatar](https://static001.geekbang.org/resource/image/d3/1b/d38daed2fedc90e20e9d2f27afbaec1b.jpg)

在上面这个架构图中，我们不难看到 Istio 项目架构的核心所在。**Istio 最根本的组件，是运行在每一个应用 Pod 里的 Envoy 容器。**  

Istio 项目使用的，是 Kubernetes 中的一个非常重要的功能，叫作 Dynamic Admission Control。  

一个应用的Pod在被提交到Kunernetes之后，Istio会在它对应的对象里面自动加上Envoy容器的配置：  

```

apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
  - name: envoy
    image: lyft/envoy:845747b88f102c0fd262ab234308e9e22f693a1
    command: ["/usr/local/bin/envoy"]
    ...
```  

**Istio 项目的核心，就是由无数个运行在应用 Pod 中的 Envoy 容器组成的服务代理网格。**  

## Kubernetes API对象创建之旅

当我们把一个 YAML 文件提交给 Kubernetes 之后，它究竟是如何创建出一个 API 对象的呢？  

在 Kubernetes 项目中，一个 API 对象在 Etcd 里的完整资源路径，是由：Group（API 组）、Version（API 版本）和 Resource（API 资源类型）三个部分组成的。通过这样的结构，整个 Kubernetes 里的所有 API 对象，实际上就可以用如下的树形结构表示出来：  

![avatar](https://static001.geekbang.org/resource/image/70/da/709700eea03075bed35c25b5b6cdefda.png)  

比如，现在我要声明要创建一个 CronJob 对象，那么我的 YAML 文件的开始部分会这么写：  

```
apiVersion: batch/v2alpha1
kind: CronJob
...
```  

在这个 YAML 文件中，“CronJob”就是这个 API 对象的资源类型（Resource），“batch”就是它的组（Group），v2alpha1 就是它的版本（Version）。  

### 具体的API对象创建

![avatar](https://static001.geekbang.org/resource/image/df/6f/df6f1dda45e9a353a051d06c48f0286f.png)  

1. 首先，当我们发起了创建 CronJob 的 POST 请求之后，我们编写的 YAML 的信息就被提交给了 APIServer。  

    而 APIServer 的第一个功能，就是过滤这个请求，并完成一些前置性的工作，比如授权、超时处理、审计等。
2. 然后，请求会进入 MUX 和 Routes 流程。  
    MUX 和 Routes 是 APIServer 完成 URL 和 Handler 绑定的场所。而 APIServer 的 Handler 要做的事情，就是按照我刚刚介绍的匹配过程，找到对应的 CronJob 类型定义。
3. 接着，APIServer 最重要的职责就来了：根据这个 CronJob 类型定义，使用用户提交的 YAML 文件里的字段，创建一个 CronJob 对象。  
    而在这个过程中，APIServer 会进行一个 Convert 工作，即：把用户提交的 YAML 文件，转换成一个叫作 Super Version 的对象，它正是该 API 资源类型所有版本的字段全集。这样用户提交的不同版本的 YAML 文件，就都可以用这个 Super Version 对象来进行处理了。
4. 接下来，APIServer 会先后进行 Admission() 和 Validation() 操作。
5. 最后，APIServer 会把验证过的 API 对象转换成用户最初提交的版本，进行序列化操作，并调用 Etcd 的 API 把它保存起来。  

### 自定义API资源

在Kubernetes中，我们通过CRD（Custom Resource Definition）来创建自定义的API。  

1. 首先我们需要定义一个CRD的YAML模板：

```
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: networks.samplecrd.k8s.io
spec:
  group: samplecrd.k8s.io
  version: v1
  names:
    kind: Network
    plural: networks
  scope: Namespaced
```  

2. 然后我们需要做一些go的coding工作，一般存放路径`pkg/apis`。（略）  

## 自定义控制器 Custom Controller

总得来说，编写自定义控制器代码的过程包括：编写 main 函数、编写自定义控制器的定义，以及编写控制器里的业务逻辑三个部分。  

![avatar](https://static001.geekbang.org/resource/image/32/c3/32e545dcd4664a3f36e95af83b571ec3.png)  



