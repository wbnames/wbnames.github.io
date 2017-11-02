---
layout: post
title: Mybatis总结part2
category: Mybatis
tags: [Mybatis]
---

# part 2 基于Spring整合Mybatis

## part1回顾
```
1: 入门程序
		parameterType
			基础类型(String,int,double...): #{}和${}的数量等于一个的时候
			pojo类型:#{}和${}的数量大于一个的时候
			
		ResultType
			pojo类型
			List类型:指定泛型里面的类型
		
		#{}占位符
			如果parameterType是基础类型,那么#{}里面的变量随意填写
			如果parameterType是pojo类型,那么#{}里面的变量必须填写属性名称
		
		${}拼接符
			如果parameterType是基础类型,那么${}里面的变量必须填写value
			如果parameterType是pojo类型,那么${}里面的变量必须填写属性名称

		<selectKey>: 查询自动增长的主键
		
		
	2: 动态代理的开发
		a: 引入映射文件的规则: 必须让我们使用mapper标签clas属性
			1: 要求映射文件和接口文件必须在同一个包下
			2: 映射文件和接口文件名称必须相同,除了扩展名称
			
		b: 动态代理的开发规范
			1: 要求映射文件的namespace必须等于接口的全路径名称
			2: 映射文件的sqlid,必须等于接口方法名称
			3: 映射文件的parameterType必须等于接口方法的参数类型
			4: 映射文件的resultType必须等于接口方法的返回类型
```
## Spring整合mybatis
```

环境搭建:
a: jar包  spring ,mybatis,  spring 和 mybatis 桥梁jar包
b: 配置文件 <br>
日志配置文件 <br>
db.propertis <br>
spring配置文件<br>

原始dao开方式的整改
原配置文件 <br>
property name="configLocation" value="classpath:SqlMapConfig.xml" 
不需要了		

==========
<br>
测试可以继承daosupport
注入会化工厂
====================================

动态代理的开发方式整改 <br>
org.mybatis.spring.mapper.MapperFactoryBean <br>
前者仅用于测试<br>
扫描来创建  开发中用后者 <br>
org.mybatis.spring.mapper.MapperScannerConfigurer <br>
<他会去spring容器中 自动拿session注入 不用再手动注入了>




```
## 输入和 输出映射

```
		parameterType
			基础类型(String,int,double...): #{}和${}的数量等于一个的时候
			pojo类型:#{}和${}的数量大于一个的时候
			pojo的包装类型: 王五购买的电脑
用户表和 商品表	类型不统一
需要用到  queryVo 包装类 做为类型注入
Type="cn.wang.pojo.QueryVo" resultType="map"
如果pojo包装类型, 那么里面#{}的变量必须<br>
写属性.属性...属性


		ResultType
			pojo类型
			List类型:指定泛型里面的类型
			返回基础类型

		
		#{}占位符
			如果parameterType是基础类型,那么#{}里面的变量随意填写
			如果parameterType是pojo类型,那么#{}里面的变量必须填写属性名称
		
		${}拼接符
			如果parameterType是基础类型,那么${}里面的变量必须填写value
			如果parameterType是pojo类型,那么${}里面的变量必须填写属性名称

```
## 动态Sql

if test=""   判断标签   表达式 true false
注意: 任何 xml都不能用&  要用 and
where1=1 会出发索引 性能变慢
=================
wherer条件 第一个and一般不加
1 当where标签里面有任何一个if条件成立
那么where标签会动态添加一个where关键字 <br>
2 去除where关键字后面第一个and关键字 

## Sql片段的封装
Sql 标签  和   include标签 配合使用

## 循环标签 foreach  (用的不多) (可以用string拼接代替)
foreach collection="ids" item="id" open="id in(" close=")" separator="," ></foreach
<br>
item:循环变量
open=第一次sql片段
close:最后一次sql片段
separator:每次结束sql片段
collection:属性id

## 多表关联查询

### 一对一
1 自动映射方式 resultType:靠的是sql语句查询出的列名与 pojo类的属性名称一一应对 <br>
2 手动映射:resultMap
### 一对多
List<User 
Mybatis会帮我们梳理数据  

## mybatis 逆向工程
通过表,逆向生成pojo类, 映射文件和接口文件(单表功能)
与hibernate相反

流程  环境  两个jar  mybatis核心包和 工程包
测试

![](https://i.imgur.com/bcDvNox.png)
