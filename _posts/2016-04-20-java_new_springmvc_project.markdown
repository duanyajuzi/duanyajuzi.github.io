---
layout: post
title: java创建spring项目
date: 2016-04-04 16:46:29
categories: blog
author:     "jarvis"
catalog:    false
tags:
    - springmvc
    - java
    - mvc
---

>概述
在java开发中比较常见的mvc结构，其带来的好处就不多说了，本文也将简单的介绍springmvc 框架的构建

##### IDE工具

eclise4.5（版本差距不大可忽略）

IntelliJ idea14(下文简称idea)

Tomcat 8

#### 开始吧

Jar包，为了避免jar包版本之间差异性带来的问题，本文提供已经测试过的jar包下载链接

### 先说eclise中创建springmvc项目的过程

打开eclipse，新建File-new-Dynamic web project
![](/images/20160403/new_dynamic_web.PNG)

然后next-next出现如图界面：
![](/images/20160403/1-2.PNG)
勾选Generate web.xml,选择finish即可

>在eclipse中添加jar包

本文提供了测试过的链接
下载链接：链接：http://pan.baidu.com/s/1c2bPz4c 密码：3cq7
添加完成后，如下图所示

![](/images/20160403/1-3.PNG)

准备工作完成了
开始配置web.xml文件，相关配置说明已注释在里面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
  <display-name>springmvcdemo1</display-name>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  <!-- 配置springmvc控制器，将web请求交给springmvc来处理 -->
  <servlet>
  	<servlet-name>springmvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<!-- 加载springmvc配置文件路径，如果不配置，默认为：/WEB-INF/springmvc.xml -->
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:springmvc.xml</param-value>
  	</init-param>
  </servlet>
  <!-- 匹配所有以action结尾的请求 -->
  <servlet-mapping>
  	<servlet-name>springmvc</servlet-name>
  	<url-pattern>*.action</url-pattern>
  </servlet-mapping>
</web-app>
```

接下来，配置spring配置文件
建立springmvc.xml文件，可放置在resources文件下，在eclipse中，可放在src文件下，随着src文件夹初始化进行读取

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-3.2.xsd
		http://www.springframework.org/schema/aop
		http://www.springframework.org/schema/aop/spring-aop-3.2.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx-3.2.xsd "
		>
	<!-- 视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
		<property name="prefix" value="/WEB-INF/pages/"/>
		<property name="suffix" value=".jsp"/>
	</bean>
</beans>
```

 视图解析器可根据个人使用需求配置，降低后面使用过程中链接请求的繁琐。

##### 到此位置 eclipse下简单的搭建springmvc项目算是结束，有不足的地方，会再更新

>由于idea工具对spring集成度较高，建立简单的springmvc项目较为简单，提一下
file-new-project

![](/images/20160403/1-4.PNG)

如图所示，左侧选择spring，右侧勾选SpringMvc，其他默认即可，然后next即可。
