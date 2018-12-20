---
title: Ubuntu系统中Docker安装Tomcat
categories:
  - java
copyright: true
date: 2018-12-19 10:30:06
tags: [java,docker,tomcat]
top: true
password:
---

**Docker构建镜像的方法主要有两种：**

1. 使用docker commit命令;

2. 使用docker build命令和Dockerfile文件（更为强大、灵活和常用）;

**一、准备centos镜像**

````shell
docker pull ubuntu
````

**二、下载jdk、tomcat安装包，上传/usr/local/soft目录下**

jdk1.8下载：<http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>
tomcat8下载：<https://tomcat.apache.org/download-80.cgi>

**三、解压，创建Dockerfile文件**

```shell
tar -zxvf apache-tomcat-8.5.31.tar.gz #解压tomcat
tar -zxvf jdk-8u171-linux-x64.tar.gz #解压jdk
rm -rf apache-tomcat-8.5.31.tar.gz #删除安装包
rm -rf jdk-8u171-linux-x64.tar.gz #删除安装包
touch Dockerfile #创建文件
```

**四、编写Dockerfile文件**

```shell
#指定操作的镜像，此处镜像名必须小写
FROM ubuntu
# 维护者信息
MAINTAINER coulson
#执行命令：创建目录
RUN mkdir -p /usr/local/soft
#将jdk1.8.0_171添加到镜像centos的/usr/local/soft/目录下，并命名为jdk
ADD jdk1.8.0_171 /usr/local/soft/jdk
#将apache-tomcat-8.5.31添加到镜像centos的/usr/local/soft/目录下，并命名为tomcat
ADD apache-tomcat-8.5.31 /usr/local/soft/tomcat
#添加环境变量
ENV JAVA_HOME /usr/local/soft/jdk
ENV CATALINA_HOME /usr/local/soft/tomcat
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin
#暴露8080端口
EXPOSE 8080
#启动时运行tomcat
CMD ["/usr/local/soft/tomcat/bin/catalina.sh","run"]
```

说明：

> FROM : 指定基础镜像，并且必须是第一条指令
>
> MAINTAINER ： 指定作者
>
> RUN : 运行指定的命令
>
> ADD : 复制命令，把文件复制到镜像中。
>
> ENV : 设置环境变量
>
> EXPOSE : 功能为暴漏容器运行时的监听端口给外部
>
> CMD : 指定容器启动时运行的命令

**五、构建Docker镜像**

```shell
docker build -t coulson/tomcat:v1 .
```

-t 设置tag名称, 命名规则registry/image:tag（若不添加版本号,默认latest）
. 表示使用当前目录下的Dockerfile文件（注意语句后面有一个点）

**六、启动镜像，访问**

```shell
docker run -d -p 8080:8080 --name Iubuntu coulson/tomcat:v1
```

说明：

> -d 后台运行     
>
> -p 端口映射  宿主机port : 容器port
>
> --name 指定容器运行名称
>
> coulson/tomcat:v1后面的镜像名版本v1，若在第五步未指定tag，则不需要带；否则必须带着tag。

在浏览器访问http://192.168.24.132:8080/即可看到Tomcat默认页。