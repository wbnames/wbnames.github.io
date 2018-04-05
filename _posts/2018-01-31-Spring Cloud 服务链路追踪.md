---
layout: post
title: spring cloud 服务链路追踪
category: spring cloud
tags: [spring cloud]
---

# Spring Cloud Sleuth
微服务架构上通过业务来划分服务的，通过REST调用，对外暴露的一个接口，可能需要很多个服务协同才能完成这个接口功能，如果链路上任何一个服务出现问题或者网络超时，都会形成导致接口调用失败。随着业务的不断扩张，服务之间互相调用会越来越复杂。


三个工程组成:一个server-zipkin,它的主要作用使用ZipkinServer 的功能，收集调用数据，并展示；一个service-hi,对外暴露hi接口；一个service-miya,对外暴露miya接口；这两个service可以相互调用；并且只有调用了，server-zipkin才会收集数据的，这就是为什么叫服务追踪了。

随着服务的越来越多，对调用链的分析会越来越复杂。它们之间的调用关系也许如下：
A-Z的N子集 依赖A-Z


工程取名为server-zipkin
在其程序入口类, 加上注解@EnableZipkinServer，开启ZipkinServer的功能：

在配置文件application.yml指定服务端口为：

server.port=9411

创建service-hi

配置文件
server.port=8988
spring.zipkin.base-url=http://localhost:9411
spring.application.name=service-hi

service-miya 配置一样 

顺序启动 访问注册中心 完成测试