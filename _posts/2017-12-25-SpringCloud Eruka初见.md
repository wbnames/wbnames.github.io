---
layout: post
title: spring cloud eureka初见
category: spring cloud
tags: [spring cloud]
---

## 服务的注册与发现（Eureka）

eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下erureka server也是一个eureka client ,必须要指定一个 server。eureka server的配置文件appication.yml


## 服务消费者（rest+ribbon）


需要指明spring.application.name,这个很重要，这在以后的服务与服务之间相互调用一般都是根据这个name 。 
启动工程，打开http://localhost:8761 ，即eureka server 的网址：


Spring cloud有两种服务调用方式，一种是ribbon+restTemplate，另一种是feign。在这一篇文章首先讲解下基于ribbon+rest。

 return restTemplate.getForObject("http://service-hi/hi?name="+name,String.class);
这里注意 必须用服务器名来做负载均衡


