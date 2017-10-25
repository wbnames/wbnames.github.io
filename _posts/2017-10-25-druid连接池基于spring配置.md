---
layout: post
title: 关于druid连接池基于spring的配置
category: spring
tags: [spring]
---

# 关于druid连接池基于spring的配置
# ======
pom 文件

<!--druid连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.4</version>
        </dependency>

需要版本请去 https://github.com/alibaba/druid 
查看自己需要的版本

# 关于sql监控的配置

<!-- -->
     <servlet>
           <servlet-name>DruidStatView</servlet-name>
           <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
     </servlet>
        <servlet-mapping>
           <servlet-name>DruidStatView</servlet-name>
          <url-pattern>/druid/*</url-pattern>
     </servlet-mapping>

# 基于spring管理的连接池配置

细节:durid 是通过url来自动识别你的 驱动url的 不同于其他连接池

	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
		<property name="url" value="jdbc:oracle:thin:@192.168.131.128:1521:ORCL" />
		<property name="username" value="wangboideabos" />
		<property name="password" value="root" />

		<property name="filters" value="stat" />

		<property name="maxActive" value="20" />
		<property name="initialSize" value="1" />
		<property name="maxWait" value="60000" />
		<property name="minIdle" value="1" />

		<property name="timeBetweenEvictionRunsMillis" value="60000" />
		<property name="minEvictableIdleTimeMillis" value="300000" />

		<property name="testWhileIdle" value="true" />
		<property name="testOnBorrow" value="false" />
		<property name="testOnReturn" value="false" />

		<property name="poolPreparedStatements" value="true" />
		<property name="maxOpenPreparedStatements" value="20" />

		<property name="asyncInit" value="true" />
	</bean>


## 手动管理druid连接池配置

略..  提示: 把bean==new

## 项目初期用的C3P0连接池 

#### 配置对比
##### c3p9 基于spring 配置

	<!-- <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="oracle.jdbc.driver.OracleDriver" />
		<property name="jdbcUrl" value="jdbc:oracle:thin:@192.168.131.128:1521:ORCL" />
		<property name="user" value="wangbocrm" />
		<property name="password" value="root" />
	</bean> -->

## sql监控功能 以及 如何打开自带sql监控页面

       <servlet>
    <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
    <init-param>
      <!-- 允许清空统计数据 -->
      <param-name>resetEnable</param-name>
      <param-value>true</param-value>
    </init-param>
    <init-param>
      <!-- 用户名 -->
      <param-name>loginUsername</param-name>
      <param-value>wangbo</param-value>
    </init-param>
    <init-param>
      <!-- 密码 -->
      <param-name>loginPassword</param-name>
      <param-value>root</param-value>
    </init-param>
       </servlet>	

##### 其他功能 请见 以下地址   配置方式基本相同

https://github.com/alibaba/druid/wiki/FAQ



