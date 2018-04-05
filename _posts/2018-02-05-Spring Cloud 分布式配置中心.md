---
layout: post
title: spring cloud 分布式配置中心
category: spring cloud
tags: [spring cloud]
---

## 分布式配置中心(Spring Cloud Config)

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。在Spring Cloud中，有分布式配置中心组件spring cloud config ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程Git仓库中。在spring cloud config 组件中，分两个角色，一是config server，二是config client。


在程序的入口Application类加上@EnableConfigServer注解开启配置服务器的功能，代码如下：


@SpringBootApplication
@EnableConfigServer



需要在程序的配置文件application.properties文件配置以下：

spring.application.name=config-server
server.port=8888


spring.cloud.config.server.git.uri=https://github.com/wbnames/SpringcloudConfig/
spring.cloud.config.server.git.searchPaths=respo
spring.cloud.config.label=master
spring.cloud.config.server.git.username=your username
spring.cloud.config.server.git.password=your password


http请求地址和资源文件映射如下:

/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties



## 客户端
bootstrap.properties：
spring.cloud.config.label 指明远程仓库的分支
spring.cloud.config.profile

dev开发环境配置文件
test测试环境
pro正式环境

1.用application.properties，可以用这个默认的配置文件，但必须将服务端口配置为8888，这是默认端口
2.如果用了bootstrap.properties，则可以自定义端口，因为bootstrap.* 命名的文件会被优先读取和配置
3.至于读取foo错误的问题，远程仓库的配置文件名前缀要和配置客户端名字相同