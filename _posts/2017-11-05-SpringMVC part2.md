---
layout: post
title: SpringMVC Part2
category: Spring
tags: [Spring]
---

# SpringMVC part2

## 高级参数绑定
A: 数组参数绑定 <br> 参数绑定 1,2这是springmvc特有的 实际不能这样传<br>
B: list参数绑定 <br>
(主要前台参数传递)

## requestMapping的几个作用
A:标记url到方法的映射关系<br>
B:标记到类<> 想到与struts2的namespace 每一个方法加一段父路径<br>
C:限制方法:处理的请求类型

## controller返回值

String  <br>
返回指定视图名称,
内部请求转发 ,  返回 <br> forward:url<br>
					redirect:url

<br>

ModelAndView <br>
void  :一般用于ajax <br>
```
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix="fmt"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
	<base href="<%=basePath%>">

```
重定向:地址发生改变 , 转发数据失效
转发: url 不会发生改变
Model: addAttribute 
如果发现你是重定向  把里面的参数拼接到url后面

model可以监控到返回值是重定向 就会把内部参数转换 拼接成?和&方式

#### 相对路径和绝对路径

相对路径:没有/开头  只有在同一个控制类里面的方法转向 重定向 才可以使用 相对
绝对路径:/开头  如果被转入的方法不在同一个类面, 必须使用绝对路径


## 异常处理

全局异常处理类
实现HandlerExceptionResolver
配置文件
message
error  走视图解析器

=============
抛出  全局异常类接受到   instanceof 判断是否是自定义异常 
强制转换

## 文件上传
MVC没有对文件上传的实现, 只有接口, 我们需要第三方的引入
配置解析器
MultipartFile multipartFile 接口
multipartFile.getOriginalFilename()
 multipartFile.transferTo()

src/ 代表80后的一个/  从端口号开始

## springmvc对json数据的支持
Springmvc对json数据的支持. 没有实现 , 只有接口 我们需要依赖于第三方的json项目  杰克逊 fastjson等等  可以对springmvc完美的支持
web数据参数的接收?user=xxx

post ?去掉

json数据的参数接收 (移动端 多)(国际标准)
JSON.stringify(data) json对象 转成一个json字符串
<br>
//@RequestBody: 标记当前参数的值  来源于一个json文本中
<br>
//@ResponseBody: 标记当前返回值,先转换成json字符串,然后调用response的getWrite方法将json文本传送前台


## Restful 风格  (封装框架用的多)
RESTful 编辑 :无参  名词
一种软件架构风格，设计风格而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

@PathVariable  标记值来自url路径里面
("xxx") 如果和前台不一致的话 这样获取

静态资源放行
mapping:放行资源映射


## 拦截器 跟filter一样 一般做验证使用

#### HandlerIntercaptor
包含放行

prehandle   postHandle  afterCompletion
