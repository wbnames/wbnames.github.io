---
layout: post
title: spring cloud 初见
category: spring cloud
tags: [spring cloud]
---

# SpringCloud初见
SpringCloud 是一个 基于springBoot实现的云应用开发工具,它为基于jvm的云应用开发中涉及的配置管理, 服务发现,断路器,智能路由,,微代理, 控制总线, 全局锁, 决策竞选,分布式会话和集群状态管理等操作提供了一种简单的开发方式..

## 微服务简单提及
微服务在这几年非常火热  以至于关于微服务架构相关的开源产品被反复的提及, spring cloud 也因spring社区的强大知名度和影响力也被广大的架构师和开发者备受关注
"微服务架构?" 简单说就是 将一个完整的应用从数据存储开始垂直拆分成多个不同的服务,  每个服务都能独立部署, 独立维护, 独立扩展, 服务于服务之间通过注入restful api的方式 互相调用

## 服务的治理

简单说明springcloud和微服务架构后  再来说说如何使用springcloud来实现服务治理

spring cloud 为服务治理做了一层抽象接口, 所以在springcloud应用中 可以支持多种不同的服务治理框架 如 netflix eureka, consul, zookeeper, 在spring cloud服务治理抽象层的作用下, 我们可以无缝的切换服务治理实现, 并且不影响任何其他的服务注册, 服务发现, 服务调用等逻辑,

所以, 介绍两种服务治理的实现来体会springcloud 这一层抽象所带来的好处


##Spring Cloud Eureka
通过它来治理服务

加入相关依赖
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.4.RELEASE</version>
    <relativePath/>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka-server</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-dependencies</artifactId>
           <version>Dalston.SR1</version>
           <type>pom</type>
           <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

通过@EnableEurekaServer注解启动一个服务注册中心提供给其他应用进行对话。这一步非常的简单，只需要在一个普通的Spring Boot应用中添加这个注解就能开启此功能

在默认设置下，该服务注册中心也会将自己作为客户端来尝试注册它自己，所以我们需要禁用它的客户端注册行为，只需要在application.properties配置文件中增加如下信息：
```
spring.application.name=eureka-server
server.port=1001

eureka.instance.hostname=localhost
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```

为了与后续要进行注册的服务区分，这里将服务注册中心的端口通过server.port属性设置为1001。启动工程后，访问：http://localhost:1001/，可以看到下面的页面，其中还没有发现任何服务。


## 创建服务提供方

```
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-dependencies</artifactId>
           <version>Dalston.SR1</version>
           <type>pom</type>
           <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```


## 测试服务编写
```
@RestController
public class DcController {

    @Autowired
    DiscoveryClient discoveryClient;

    @GetMapping("/dc")
    public String dc() {
        String services = "Services: " + discoveryClient.getServices();
        System.out.println(services);
        return services;
    }

}
```


```
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        new SpringApplicationBuilder(
            ComputeServiceApplication.class)
            .web(true).run(args);
    }
}
```

```
我们在完成了服务内容的实现之后，再继续对application.properties做一些配置工作，具体如下：

spring.application.name=eureka-client
server.port=2001
eureka.client.serviceUrl.defaultZone=http://localhost:1001/eureka/
```

## 从eureka治理体系 切换到consul的治理体系

括号中的eureka-client就是通过Spring Cloud定义的DiscoveryClient接口在eureka的实现中获取到的所有服务清单。由于Spring Cloud在服务发现这一层做了非常好的抽象，所以，对于上面的程序，我们可以无缝的从eureka的服务治理体系切换到consul的服务治理体系中区。

##Spring Cloud Consul
Spring Cloud Consul项目是针对Consul的服务治理实现。Consul是一个分布式高可用的系统，它包含多个组件，但是作为一个整体，在微服务架构中为我们的基础设施提供服务发现和服务配置的工具。它包含了下面几个特性：

服务发现
健康检查
Key/Value存储
多数据中心

以之前实现的基于Eureka的示例（eureka-client）为基础，我们如何将之前实现的服务提供者注册到Consul上呢？方法非常简单，我们只需要在pom.xml中将eureka的依赖修改为如下依赖：

<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>

到此为止，我们将eureka-client转换为基于consul服务治理的服务提供者就完成了。前文我们已经有提到过服务发现的接口DiscoveryClient是Spring Cloud对服务治理做的一层抽象，所以可以屏蔽Eureka和Consul服务治理的实现细节，我们的程序不需要做任何改变，只需要引入不同的服务治理依赖，并配置相关的配置属性就能轻松的将微服务纳入Spring Cloud的各个服务治理框架中。

下面可以尝试让consul的服务提供者运行起来。这里可能读者会问，不需要创建类似eureka-server的服务端吗？由于Consul自身提供了服务端，所以我们不需要像之前实现Eureka的时候创建服务注册中心，直接通过下载consul的服务端程序就可以使用。

我们可以用下面的命令启动consul的开发模式：
https://www.consul.io/downloads.html 前提是要下客户端 并且放到pash下  