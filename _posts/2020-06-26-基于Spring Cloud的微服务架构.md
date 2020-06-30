---
layout:     post                    # 使用的布局（不需要改）
title:      基于Spring Cloud的微服务架构       # 标题 
subtitle:     #副标题
date:       2020-06-29             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Spring Cloud
    - 
---

## 前言

## 微服务的概念

微服务是一种架构风格，在这一风格中多个服务独立运行，每个服务占用独立进程。  

## 微服务与单体架构的区别

1. 单体架构所有模块耦合在一起，代码量大维护困难。微服务每个模块相当于一个单独的项目，维护相对方便。  
2. 单体架构所有模块共用一个数据库，存储方式单一。微服务每个模块可以使用不同存储方式（redis, musql等）。
3. 单体架构所有模块所使用的技术一样。 微服务每个模块可以使用不同的开发方式。  

## 微服务主要架构

1. Spring Cloud
2. Dubbo
3. Dropwizard
4. Consul

## Spring Cloud主要模块

1. 服务发现 - Netflix Eureka(Nacos)
2. 服务调用 - Netflix Feign
3. 熔断器 - Netflix Hystrix
4. 服务网关 - Spring Cloud Gateway
5. 分布式配置 - Spring Cloud Config(Nacos)
6. 消息总线 - Spring Cloud Bus(Nacos)


## Nacos

Nacos可用于服务注册，配合Feign可用于远程调用不同服务的API。  

## Spring Cloud远程调用流程 

Feign -> Hystrix -> Ribbon -> Http Client (Apache http components or Okhttp) 

Hystrix充当熔断器，判断来自消费者的调用请求是否可以远程调用。  

Ribbon用于负载均衡，将来自多个消费者的请求分发到各个生产者。  

