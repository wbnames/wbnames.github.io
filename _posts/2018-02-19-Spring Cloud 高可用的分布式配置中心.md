---
layout: post
title: spring cloud 高可用分布式配置中心
category: spring cloud
tags: [spring cloud]
---

# 高可用的分布式配置中心

创建一个eureka-server工程，用作服务注册中心。
在配置文件application.yml上，指定服务端口为8889，加上作为服务注册中心的基本配置，代码如下：
server:
  port: 8889

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

## 改造config-server
配置文件application.yml，指定服务注册地址为http://localhost:8889/eureka/，其他配置同上一篇文章
最后需要在程序的启动类Application加上@EnableEureka的注解。
spring.application.name=config-server
server.port=8888

spring.cloud.config.server.git.uri=https://github.com/forezp/SpringcloudConfig/
spring.cloud.config.server.git.searchPaths=respo
spring.cloud.config.label=master
spring.cloud.config.server.git.username= your username
spring.cloud.config.server.git.password= your password
eureka.client.serviceUrl.defaultZone=http://localhost:8889/eureka/

## config-client改造
加上服务地址
spring.cloud.config.discovery.enabled 是从配置中心读取文件。
spring.cloud.config.discovery.serviceId 配置中心的servieId，即服务名。


#spring.cloud.config.uri= http://localhost:8888/

spring.cloud.config.discovery.enabled=true
spring.cloud.config.discovery.serviceId=config-server
从ip 变成了 服务名 从而多可用