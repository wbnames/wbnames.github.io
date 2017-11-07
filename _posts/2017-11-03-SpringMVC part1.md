---
layout: post
title: SpringMVC Part1
category: Spring
tags: [Spring]
---

# SpringMVC part1 

## SpringMVC INC
```
SpringMVC 属于 spring组织下的一个开源的免费的控制层 (业务模型)
servlet(单例) struts(单例) struts2(多例) SpringMVC(单例)
```
## 作用
```
接收前台参数 <br>
处理好的数据 放入request域中, 供jsp使用<br>
控制返回的视图页面<br>
```
## simple 
```
环境搭建 :   jar包 ,  配置文件 , 日志配置文件, springmvc的配置文件   web.xml配置文件
```
## springMVC 架构

图:![](https://i.imgur.com/iVWCDzs.png)
```
理解: 根据ip可以找到对应服务器, 根据端口号可以找到服务器下的web容器,根据项目名称可以找到对应的项目, 项目后的url交给web.xml处理, 如果web.xml里面配置的是springmvc , 那么项目后的url要交给springmvc处理, 通过url找到对应方法, 并处理请求,返回视图名称, 走视图解析器, 查找真正的jsp全路径,web容器从request域中取出数据, 进行渲染jsp层生成html, 将html返回给用户浏览器看. 浏览器将html渲染成网页,
```

## SSM框架整合 INC
```
jar包需要 spring mybatis, springmvc,mybatis and spring 桥包,

配置文件<br>
web.xml (扫描action层,前端控制器)
	<br>
	applicationContext-dao.xml<br>
加载db.properties, 数据源,会话工厂, mapper扫描
<br>
	applicationContext-service.<br>
事务管理器
<br>
SpringMVC.xml<br>
配置扫描service和action注解, 视图解析器, 注解驱动(映射器和适配器)

<br>
日志配置文件<br>
数据库连接<br>
映射文件和接口文件<br>




```
## 简单流程
```
web.xml配置拦截器
*.xxx   //  <br>
或者 /.. 放行.jsp后缀的请求  (视图)

(return 封装)
 <mvc:annotation-driven /
<br>
最新映射器  和  适配器
(配置三大组件  注解方式才能用 上面的做法)
```
## 对于SSM的整合
```
(监听器无法扫描注解)
(接口里只写实现类 不写接口 某些时候)
(selectByExample 条件)
```
## 参数绑定
```
a: 默认支持的环境类型(HttpServletRequest,HttpServletResponse,HttpSession,Model)

	b: 基础类型的参数绑定(String,Int,Long,double....)
		要求: 前台传入的参数名称必须等于方法的参数名称
	
	c: pojo类型
		要求: 前台传入的参数名称必须等于pojo类的属性名称
		
	d: pojo的包装类型
		要求: 前台传入的参数名称必须等于包装类里面的  属性.属性....
	
	e: converter转换器
		一般string类型转成其他类型,例如int,double,long..都是可以正常转换,
		但是string类型转date类型,需要编写转换器
	
```
## 转换器
```
实现Converter接口
配置到 MVC配置文件里面
需要配置一个转换器  再注入到映射器里面
```
## MVC与struts2的区别
```
7	springmvc与struts2不同
1、	springmvc的入口是一个servlet即前端控制器，而struts2入口是一个filter过虑器。
2、	springmvc是基于方法开发(一个url对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例(建议单例)，struts2是基于类开发，传递参数是通过类的属性，只能设计为多例。
3、	Struts采用值栈存储请求和响应的数据，通过OGNL存取数据， springmvc通过参数解析器是将request请求内容解析，并给方法形参赋值，将数据和视图封装成ModelAndView对象，最后又将ModelAndView中的模型数据通过reques域传输到页面。Jsp视图解析器默认使用jstl。
```

## ModelAndView

API  detail

![](https://i.imgur.com/zrrGApI.png)
