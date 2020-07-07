---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes容器化DaemonSet & Job     # 标题 
subtitle:   DaemonSet 与 Job  #副标题
date:       2020-07-08             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---

## 什么是DaemonSet

`DaemonSet`守护进程有以下特点:
1. 这个 Pod 运行在 Kubernetes 集群里的每一个节点（Node）上；
2. 每个节点上只有一个这样的 Pod 实例；
3. 当有新的节点加入 Kubernetes 集群后，该 Pod 会自动地在新节点上被创建出来；而当旧节点被删除后，它上面的 Pod 也相应地会被回收掉。
4. DaemonSet 开始运行的时机，很多时候比整个 Kubernetes 集群出现的时机都要早。

### DaemonSet的API对象

```

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: k8s.gcr.io/fluentd-elasticsearch:1.20
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```  
这个 DaemonSet，管理的是一个 fluentd-elasticsearch 镜像的 Pod。这个镜像的功能非常实用：通过 fluentd 将 Docker 容器里的日志转发到 ElasticSearch 中。  

**可以看到，DaemonSet 跟 Deployment 其实非常相似，只不过是没有 replicas 字段；**它也使用 selector 选择管理所有携带了 name=fluentd-elasticsearch 标签的 Pod。  


### DaemonSet实现原理

1. 通过`nodeAffinity`字段在指定Node上创建节点。
2. 通过在模板中使用`Toleration`使调度器在调度这个 Pod 的时候，就会忽略当前节点上的“污点”，从而成功地将Pod调度到这台机器上（提前）启动起来。  


`nodeAffinity`字段YAML举例图下：
```
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: metadata.name
            operator: In
            values:
            - node-geektime
```

## 什么是Job

之前介绍的Deployment、StatefulSet，以及 DaemonSet 这三个编排概念针对的都是Long Running Task（长作业）。比如，我在前面举例时常用的 Nginx、Tomcat，以及 MySQL 等等。这些应用一旦运行起来，除非出错或者停止，它的容器进程会一直保持在 Running 状态。  

但是，有一类作业显然不满足这样的条件，这就是“离线业务”，或者叫作 Batch Job（计算业务）。  

这类作业在Kubernetes中就叫做`Job`.  

```

apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: resouer/ubuntu-bc 
        command: ["sh", "-c", "echo 'scale=10000; 4*a(1)' | bc -l "]
      restartPolicy: Never
  backoffLimit: 4
```

### Job Controller对并行作业的控制

在 Job 对象中，负责并行控制的参数有两个：
1. spec.parallelism，它定义的是一个 Job 在任意时间最多可以启动多少个 Pod 同时运行；
2. spec.completions，它定义的是 Job 至少要完成的 Pod 数目，即 Job 的最小完成数。  

### Job的使用方法

1. 外部管理器 +Job 模板。
2. 拥有固定任务数目的并行 Job。
3. 指定并行度（parallelism），但不设置固定的 completions 的值。

### CronJob的概念

`CronJob`即为定时任务，对应的SAML API如下：
```

apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```
在这个 YAML 文件中，最重要的关键词就是 jobTemplate。看到它，你一定恍然大悟，**原来 CronJob 是一个 Job 对象的控制器（Controller）！**  

没错，CronJob 与 Job 的关系，正如同 Deployment 与 ReplicaSet 的关系一样。**CronJob 是一个专门用来管理 Job 对象的控制器。**  

