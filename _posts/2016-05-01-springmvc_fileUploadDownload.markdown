---
layout: post
title: 基于springmvc框架实现上传下载业务（一）
date: 2016-05-02 18:53:22
categories: blog
author:     "jarvis"
tags:
    - java上传
    - java下载
---

>最近由于工作原因，要写到上传下载的业务，所以从之前搭建的springmvc框架来写这次博客。

##### IDE工具

IntelliJ idea14(下文简称idea)

###### 先看一下目录结构

![项目目录结构.png](/images/2016/05/02/jiegou.png)

###### 配置pom文件

由于项目是使用maven进行管理，所以要在pom文件中配置jar包依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.jarvis</groupId>
    <artifactId>fileUploadDownload</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>fileUploadDownload Maven Webapp</name>
    <url>http://maven.apache.org</url>

    <properties>
        <spring.version>4.1.1.RELEASE</spring.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.2.2</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>1.4</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.21</version>
        </dependency>

    </dependencies>
    <build>
        <finalName>fileUploadDownload</finalName>
    </build>
</project>

```

###### 前台页面

```html
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8" %>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<base href="<%=basePath%>">
<html>
<body>
<h2>Hello World!</h2>
<form method="post" action="${pageContext.request.contextPath }/fileAction/fileUpload"   enctype="multipart/form-data">
    <input type="file" name="uploadFile"><br>
    <input type="submit" value="上传" >
</form>
</body>
</html>
```

###### 后台代码

``` java
package com.jarvis.action;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.util.UUID;

/**
 * Created by jarvis on 2016/5/1.
 */
@RequestMapping("/fileAction")
@Controller
public class FileAction {
    @RequestMapping("/fileUpload")
    @ResponseBody
    public String fileUpload(@RequestParam  MultipartFile uploadFile, HttpServletRequest request, HttpServletResponse response){
        String oldName=uploadFile.getOriginalFilename();//获取原来的文件名
        String newName= UUID.randomUUID()+oldName;
        String text="";
        //File文件流 设置上传文件位置
        File file=new File("E:/test/upload/"+newName);
        try {
            uploadFile.transferTo(file);
            text="success";
        } catch (IOException e) {
            e.printStackTrace();
            text="failure";
        }
        return text;
    }
}
```
>后台代码，主要还是调用了spring封装的transferTo()方法，比较简单，后面会补充ftp上传

###### web.xml文件配置

``` xml
<web-app version="2.4"
         xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
	http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

  <display-name>Spring MVC Application</display-name>

  <servlet>
    <servlet-name>mvc-dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:mvc-dispatcher-servlet.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>mvc-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>utf-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```

继续看。。。spring的配置文件

``` xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.1.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <mvc:annotation-driven/>
    <!--配置资源文件访问方式，本次用不到-->
    <mvc:default-servlet-handler/>
    <context:component-scan base-package="com.jarvis.action"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="utf-8" />
        <property name="maxUploadSize" value="10485760000" />
        <property name="maxInMemorySize" value="40960" />
    </bean>
</beans>
```

>OK,开始测试


![页面未做修饰](/images/2016/05/02/form.png)

*选中文件后，点击上传，成功后会看到返回页面*


![上传成功提示](/images/2016/05/02/uploadsuccess.png)

>PS: 文件下载稍后更新。。。
