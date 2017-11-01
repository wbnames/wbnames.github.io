---
layout: post
title: Mybatis部分总结part1
category: Mybatis
tags: [Mybatis]
---

# Mybatis

## inc

 Mybatis 本身是apache的一个开源项目 iBatis 10年从googlecode上
后13年 迁移到github上  跟名 Mybatis 持久层框架
(Mybatis 几乎避免了 所有JDBC的代码 和 手动设置参数以及获取结果集  )
(注意:  这里的几乎  表示说明还是不是很完善)

## Mybatis 和 hibernate的区别
1:hibernate对jdbc的封装更加彻底, 我们开发周期更短
mybatis相对封装比较轻量化,, sql语句必须我们自己编写 <br>

2: hiber相对mybatis性能要相对低 , 而且sql语句不好优化 <br>

3: hiber可以跨数据库, mybatis不可以跨数据库 <br>

4: hiber学习成本相对mybatis要高 <br>

## mybatis的dao层开发

A: 原始的dao层开发方式
特点: 接口和实现类都需要我们自己编写

B: 动态代理的dao开发方式
特点: 只需要我们编写接口 不需要编写实现类, 实现类有mybatis帮我们在运行周期生成

引入映射文件规则: 要求我们必须使用mapper标签的class属性

规则1: 接口文件和映射文件必须在同一个包下 <br>
规则2: 接口文件和映射文件名称必须相同 <br>

动态代理开发规范
1:映射文件的namespace必须等于接口的全路径名称 <br>
2:映射文件的sql唯一标识id必须等于接口的方法名称
 <br>
3:映射文件的parmeterType必须等于接口方法的参数类型
 <br>
4:映射文件的resultType必须等于接口方法的返回类型
 <br>

C:selectOne和selectList调用时机
 <br>
根据接口返回类型规定, 如果返回类型是单个对象调动selectOne  <br>
如果返回类型是list对象, 调用selectList方法


# 简单使用以及使用流程
## 1 环境搭建

jar包   以及 配置文件
entity,model,javabean,vo,do,pojo

log4j mybatis 默认使用 <br>
SqlMapConfig.xml核心配置文件 <br>
以及 映射文件 <br>

## 配置文件导入约束
```****
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

## 核心配置文件

```mybatis
>     	<?xml version="1.0" encoding="UTF-8" ?>
>     <!DOCTYPE configuration
>     PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
>     "http://mybatis.org/dtd/mybatis-3-config.dtd">
>     <configuration>
>     <!-- 和spring整合后 environments配置将废除 -->
>     <environments default="development">
>     <environment id="development">
>     <!-- 使用jdbc事务管理 -->
>     <transactionManager type="JDBC" />
>     <!-- 数据库连接池 -->
>     <dataSource type="POOLED">
>     <property name="driver" value="com.mysql.jdbc.Driver" />
>     <property name="url"
>       value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
>     <property name="username" value="root" />
>     <property name="password" value="root" />
>     </dataSource>
>     </environment>
>     </environments>
>     <mappers>
>     
>     <mapper resource="User.xml"></mapper>
>     
>     </mappers>
>     </configuration>

```
## 占位符  和  拼接sql串
{}表示一个占位符号 <br>
${value}表示拼接sql串 注意:里面必须写value

## 插入语句问题  
### 自动增长id  
插入后需要 角色 权限id 不能获取为0 或者null
解决方案   Select insert id()
修改映射文件 讲mysql主键自增返回:
# mybatis架构

![](https://i.imgur.com/u0tBCua.png)



## mybatis的dao层开发
原始Dao开发中存在以下问题： <br>
Dao方法体存在重复代码：通过SqlSessionFactory创建SqlSession，调用SqlSession的数据库操作方法<br>
调用sqlSession的数据库操作方法需要指定statement的id，这里存在硬编码，不得于开发维护。

## 动态代理方式

从resource 更改成 class 引入 <br>
1 接口文件和映射文件必须在同一个包下<br>
2 接口文件和映射文件名称必须相同<br>
3 namespace必须等于接口的全限定名<br>
4 sqlid 必须 等于接口里的方法名称<br>
5 映射文件的 type 必须等于接口参数类型<br>
6 映射文件的 result 必须等于接口的返回类型<br>
 session.getMapper();


## selectOne 和 selectList 调用时机<br>
根据接口返回类型  如果单个对象 调用one  
如果返回List对象 调用list

> <properties resource="db.properties"></properties>
> 	
> 	<!-类型起别名 -->
> 	<typeAliases>
> 		<!-
> 			typeAlias: 起别名
> 				type: 类型
> 				alias: 别名,无大小写区分
> 		<typeAlias type="cn.itcast.pojo.User" alias="user"/>
> 		 -->
> 		 
> 		 <!-
> 		 	package: 批量扫描 起别名
> 		 		别名: 就是类名
> 		  -->
> 		 <package name="cn.itcast.pojo"/>
> 	</typeAliases>
> 	
> 	<!-和spring整合后 environments配置将废除 -->
> 	<environments default="development">
> 		<environment id="development">
> 			<!-使用jdbc事务管理 -->
> 			<transactionManager type="JDBC" />
> 			<!-数据库连接池 -->
> 			<dataSource type="POOLED">
> 				<property name="driver" value="${jdbc.driver}" />
> 				<property name="url"
> 					value="${jdbc.url}" />
> 				<property name="username" value="${jdbc.username}" />
> 				<property name="password" value="${xxx}" >

## 别名 typeAliases
result里可以用别名代替  比如cn.wang.user 可以改为user

别名没有大小写区分

## 扫描方式 package
批量扫描  起别名  类包前一个包  别名就是类名 没有大小写区分
packge 

type 里面也有别名
比如int  默认扫描 java.lang.Integer
如果非要使用int类型  就用_int

decimal
bigdecimal  浮点类型
map(重点)

## 批量扫描方式

<packge name="xxx"  类包前一级
