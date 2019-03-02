---
layout: post
title: springboot项目部署到docker容器
date: 2018-04-09 16:46:29
categories: blog
author:     "jarvis"
catalog:    true
tags:
    - springboot
    - docker
    - idea
---

> 前言:学习将springboot写的示例,使用dockerfile方式构建镜像,运行在docker容器中

#### 环境
* IDEA 2018.1
* ubuntu 16.04
* docker 18.03 ce

#### 创建springboot项目

使用IDEA新建项目,New-Project,选择Spring Initializr创建springboot项目,勾选web即可(示例代码,不需要勾选其他模块)

##### 添加一个Controller
 添加一个controller即可完成代码部分的工作

```java
@RestController
public class HelloDockController {
    @RequestMapping("/")
    public String helloDocker(){
        return "a springboot demo for docker";
    }
}
````

#### 修改maven pom.xml文件

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>1.0.0</version>
    <configuration>
        <imageName>springboot/${project.artifactId}</imageName>
        <dockerDirectory>src/main/docker</dockerDirectory>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```

#### 编写dockerfile
路径 : /springboot-docker-demo/src/main/docker/Dockerfile

```text
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD springboot-docker-demo-0.1.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

>FROM openjdk:8-jdk-alpine --表示镜像使用啦openjdk的镜像

>VOLUME 将数据挂载到宿主机器的/tmp下

>ADD 将package包引入到镜像文件,并重命名为app.jar

>ENTRYPOINT 容器启动时需要执行的命令


#### maven package

系统没有配置maven的环境变量,可以用idea集成的maven工具进行打包

![idea 集成maven打包](/images/2018/4/maven_package.png)

#### 构建镜像
同样在idea的maven中执行命令

```docker
mvn package docker:build
```
如图所示,即成功.
![build success](/images/2018/4/mvn_build.png)

在终端中查看镜像
```bash
$ docker images
REPOSITORY                          TAG                 IMAGE ID            CREATED             SIZE
springboot/springboot-docker-demo   latest              dbfbdd42df1b        4 hours ago         118MB
```
已经能够看到刚刚构建的镜像,现在运行测试一下
#### 运行测试
``` bash
 docker run -p 8080:8080 -t springboot/springboot-docker-demo
 ```
在浏览器输入访问地址 http://127.0.0.1:8080

![运行结果](/images/2018/4/docker_run.png)
