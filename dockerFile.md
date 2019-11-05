# dockerFile

dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本

构建三步骤：1、编写Dockerfile文件 2、docker build 3、docker run

基础内容：1、每条保留字指令都必须为大写字母且后面要跟至少一个参数

​					2、指令按照从上到下，顺序执行

​					3、#表示注释

​					4、每条指令都会创建一个新的镜像层，并对镜像进行提交

Docker执行DockerFile的大致流程

​	1、docker从基础镜像运行一个容器

​	2、执行一条指令并对容器修改

​	3、执行类似docker commit的操作提交一个新的镜像层

​	4、docker在基于刚提交的镜像运行一个新的容器

​	5、执行dockerfile中的的下一条指令直到所有指令都执行完成



从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，

\*  Dockerfile是软件的原材料

\*  Docker镜像是软件的交付品

\*  Docker容器则可以认为是软件的运行态。

Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石。

1 Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;

2 Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像时，会真正开始提供服务;

3 Docker容器，容器是直接提供服务的。

## dockerfile关键字

![](C:\Users\yangkun\Desktop\bupt_study\docker\笔记截图\dockerfile关键字.png)

## dockerfile 案例

​	**自定义tomcat9**

1、先创建mydockerfile/tomcat9

```
/mydockerfile/tomcat9/
```

2、在上述目录创建c.txt

```
touch c.txt
```

3、将jdkh和tomcat9安装压缩包拷贝到上述文件夹

4、在/mydockerfile/tomcat9目录下新建dockerfile文件

```
FROM         centos:6.10
MAINTAINER    yk<ykwork2015@126.com>
#把宿主机当前上下文的c.txt拷贝到容器/usr/local/路径下
COPY c.txt /usr/local/cincontainer.txt
#把java与tomcat添加到容器中
ADD jdk-8u131-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.27.tar.gz /usr/local/
#安装vim编辑器
RUN yum -y install vim
#设置工作访问时候的WORKDIR路径，登录落脚点
ENV MYPATH /usr/local
WORKDIR $MYPATH
#配置java与tomcat环境变量
ENV JAVA_HOME /usr/local/jdk1.8.0_131
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.27
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.27
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
#容器运行时监听的端口
EXPOSE  8080
#启动时运行tomcat
# ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh" ]
# CMD ["/usr/local/apache-tomcat-9.0.8/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-9.0.27/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.27/bin/logs/catalina.out

```

5、 构建

```
docker build -f dockerfile -t mytomcat9:1.2 .
```



6、run

```
docker run -d -p 9080:8080 --name myt9 -v /mydockerfile/tomcat9/test:/usr/local/apache-tomcat-9.0.27/webapps/test -v /mydockerfile/tomcat9/tomcat9logs/:/usr/local/apache-tomcat-9.0.27/logs --privileged=true mytomcat9:1.1
 
```

7、在宿主机发布web的test服务

​	7.1 在test中创建a.jsp 和 WEB-INF文件夹

​	a.jsp

     <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
    
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    
    <html>
    
      <head>
    
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    
        <title>Insert title here</title>
    
      </head>
    
      <body>
    
        -----------welcome------------
    
        <%="i am in docker tomcat self "%>
    
        <br>
    
        <br>
    
        <% System.out.println("=============docker tomcat self");%>
    
      </body>
    
    </html>
    


在WEB-INF中创建web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://java.sun.com/xml/ns/javaee"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"

  id="WebApp_ID" version="2.5">
  <display-name>test</display-name>
</web-app>
```

