---
layout: post
title: spring cloud 消息总线
category: spring cloud
tags: [spring cloud]
---

# 消息总线(Spring Cloud Bus)

Spring Cloud Bus 将分布式的节点用轻量的消息代理连接起来。它可以用于广播配置文件的更改或者服务之间的通讯，也可以用于监控。


改造config-client

起步依赖spring-cloud-starter-bus-amqp
还有
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
<dependency>
            <groupId>org.springframework.retry</groupId>
            <artifactId>spring-retry</artifactId>
        </dependency>


在配置文件application.properties中加上RabbitMq的配置，包括RabbitMq的地址、端口，用户名、密码，

次启动eureka-server、confg-cserver,启动两个config-client，端口为：8881、8882。

访问http://localhost:8881/hi 或者http://localhost:8882/hi 浏览器显示：

foo version 3
这时我们去代码仓库将foo的值改为“foo version 4”，即改变配置文件foo的值。如果是传统的做法，需要重启服务，才能达到配置文件的更新。此时，我们只需要发送post请求：http://localhost:8881/bus/refresh，你会发现config-client会重新读取配置文件

@EnableEurekaClient
@RefreshScope

http://localhost:8991/bus/refresh
必须用post请求 来刷新
management.security.enable=false  安全检查要关闭