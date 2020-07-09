---
layout:     post                    # 使用的布局（不需要改）
title:     Kubernetes基于角色的权限控制RBAC     # 标题 
subtitle:     #副标题
date:       2020-07-09             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Kubernetes
    
---

## RBAC 基本概念

Kubernetes 中所有的 API 对象，都保存在 Etcd 里。可是，对这些 API 对象的操作，却一定都是通过访问 kube-apiserver 实现的。其中一个非常重要的原因，就是你需要 APIServer 来帮助你做授权工作。  

而在 Kubernetes 项目中，负责完成授权（Authorization）工作的机制，就是 **RBAC：基于角色的访问控制（Role-Based Access Control）。**  

RBAC中有三个基本概念：

1. Role：角色，它其实是一组规则，定义了一组对 Kubernetes API 对象的操作权限
2. Subject：被作用者，既可以是“人”，也可以是“机器”，也可以是你在 Kubernetes 里定义的“用户”
3. RoleBinding：定义了“被作用者”和“角色”的绑定关系  

### Role

Role 本身就是一个 Kubernetes 的 API 对象，定义如下所示：  

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: mynamespace
  name: example-role
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```  

这个 Role 对象的 rules 字段，就是它所定义的权限规则。在上面的例子里，这条规则的含义就是：允许“被作用者”，对 mynamespace 下面的 Pod 对象，进行 GET、WATCH 和 LIST 操作。  

### Rolebinding

RoleBinding 本身也是一个 Kubernetes 的 API 对象。它的定义如下所示：  

```

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-rolebinding
  namespace: mynamespace
subjects:
- kind: User
  name: example-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: example-role
  apiGroup: rbac.authorization.k8s.io
```  

YAML中的`roleRef` 字段使得RoleBinding 对象就可以直接通过名字，来引用我们前面定义的 Role 对象（example-role），从而定义了“被作用者（Subject）”和“角色（Role）”之间的绑定关系。  

## ServiceAccount 工作流程

`ServiceAccount`在Kubernetes中负责管理的“内置用户”。  

```
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: mynamespace
  name: example-sa
```

然后，我们通过编写 RoleBinding 的 YAML 文件，来为这个 ServiceAccount 分配权限：  

```

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-rolebinding
  namespace: mynamespace
subjects:
- kind: ServiceAccount
  name: example-sa
  namespace: mynamespace
roleRef:
  kind: Role
  name: example-role
  apiGroup: rbac.authorization.k8s.io
```

接着，我们用 kubectl 命令创建这三个对象：  

```
$ kubectl create -f svc-account.yaml
$ kubectl create -f role-binding.yaml
$ kubectl create -f role.yaml
```  

Kubernetes 会为一个 ServiceAccount 自动创建并分配一个 Secret 对象，即：上述 ServiceAcount 定义里最下面的 secrets 字段。**这个 Secret，就是这个 ServiceAccount 对应的、用来跟 APIServer 进行交互的授权文件，我们一般称它为：Token。Token 文件的内容一般是证书或者密码，它以一个 Secret 对象的方式保存在 Etcd 当中。**  

这时候，用户的 Pod，就可以声明使用这个 ServiceAccount 了，比如下面这个例子：  

```
apiVersion: v1
kind: Pod
metadata:
  namespace: mynamespace
  name: sa-token-test
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
  serviceAccountName: example-sa
```  

