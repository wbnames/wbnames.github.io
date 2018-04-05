---
layout: post
title: spring cloud 高可用服务注册中心
category: spring cloud
tags: [spring cloud]
---

## 高可用服务注册中心  衔接第一章
介绍了服务注册与发现，其中服务注册中心Eureka Server，是一个实例，当成千上万个服务向它注册的时候，它的负载是非常高的，这在生产环境上是不太合适的，这篇文章主要介绍怎么将Eureka Server集群化。


Eureka通过运行多个实例，使其更具有高可用性。事实上，这是它默认的熟性，你需要做的就是给对等的实例一个合法的关联serviceurl。

改变  server  两个配之间

service-hi 改造配置文件

你会发现注册了service-hi，并且有个peer2节点，同理访问localhost:8769你会发现有个peer1节点。

client只向8761注册，但是你打开8769，你也会发现，8769也有 client的注册信息。

个人感受：这是通过看官方文档的写的demo ，但是需要手动改host是不是不符合Spring Cloud 的高上大？