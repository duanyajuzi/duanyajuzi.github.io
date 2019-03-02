---
layout: post
title: 记一次hadoop伪分布式配置
date: 2016-08-30 16:46:29
categories: blog
author:     "jarvis"
catalog:    true
tags:
    - hadoop
    - 伪分布式
    - centOs
    - 大数据
---

> 环境

* centos 7

* jdk 1.8.0_101

* hadoop 2.7.3


>准备说明:

  * master ip: 192.168.20.222(虚拟机IP)

  * hadoop2.7.3解压存放在 /opt下

#### 安装jdk&&设置java环境变量

文中jdk为oracle官网下载的linux64位版本,理论上openjdk亦行
使用tar命令解压下载的jdk,本文存放在/opt下,设置java环境变量

```
export JAVA_HOME=/opt/jdk1.8.0_101
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

使用该命令,使环境变量生效

```
source /etc/profile
```

查看是否生效

```bash
$ java -version
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
```





##### SSH 免密设置

CentOS默认没有启动ssh无密登录，去掉/etc/ssh/sshd_config其中2行的注释

```
RSAAuthentication yes
PubkeyAuthentication yes
```

输入命令，ssh-keygen -t rsa，生成key，都不输入密码，一直回车，/root就会生成.ssh文件夹
合并公钥到authorized_keys文件，进入/root/.ssh目录，通过SSH命令合并，

```
cat id_rsa.pub>> authorized_keys
```

#### Hadoop配置

进入hadoop解压过后

##### 指定hadoop中JAVA_HOME

配置java环境变量

```vi /etc/hadoop/hadoop-env.sh```

修改export JAVA_HOME=${JAVA_HOME}为

export JAVA_HOME=/opt/jdk1.8.0_101

##### 配置core-site.xml

输入命令 ``` vi /opt/hadoop-2.7.3/etc/hadoop/core-site.xml```

编辑
/opt/hadoop-2.7.3/etc/hadoop目录下的core-site.xml

```xml
 <configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.20.222:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/opt/hadoop/tmp</value>
    </property>
    <property>
        <name>io.file.buffer.size</name>
        <value>131702</value>
    </property>
 </configuration>

```

##### 配置hdfs-site.xml

输入命令 ``` vi /opt/hadoop-2.7.3/etc/hadoop/hdfs-site.xml```

配置/opt/hadoop-2.7.3/etc/hadoop目录下的hdfs-site.xml

```xml
<configuration>
  <property>
      <name>dfs.namenode.name.dir</name>
      <value>file:/opt/hadoop/dfs/name</value>
  </property>
  <property>
      <name>dfs.datanode.data.dir</name>
      <value>file:/opt/hadoop/dfs/data</value>
  </property>
  <property>
      <name>dfs.replication</name>
      <value>2</value>
  </property>
  <property>
      <name>dfs.namenode.secondary.http-address</name>
      <value>192.168.20.222:9001</value>
  </property>
  <property>
  <name>dfs.webhdfs.enabled</name>
  <value>true</value>
  </property>
</configuration>

```

##### NameNode格式化

输入命令

```
./bin/hdfs namenode -format

```

执行NameNode格式化,结果如下图

![hdfs_forma](/images/2016/10/NameNodeFormat.png)

### 配置hadoop环境变量

输入命令  ``` vi /etc/profile```

编辑

```
export HADOOP_INSTALL=/opt/hadoop-2.7.3
export PATH=$PATH:$HADOOP_INSTALL/bin
export PATH=$PATH:$HADOOP_INSTALL/sbin
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_HOME=$HADOOP_INSTALL
export HADOOP_HDFS_HOME=$HADOOP_INSTALL
export YARN_HOME=$HADOOP_INSTALL
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_INSTALL/lib
```

#### 接着启动

```
start-all.sh
```

启动完成输入 ```jps``` 查看是否运行
也可在浏览器进入web端的管理界面 ```http://192.168.20.222:50070```
