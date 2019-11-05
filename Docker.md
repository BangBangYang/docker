

### Docker

[TOC]

#### 1 、安装

#### 2、原理

#### 3、操作命令

**帮助命令**

```
[root@hadoop101 atguigu]# docker --help
[root@hadoop101 atguigu]# docker info
[root@hadoop101 atguigu]# docker version
```

**镜像命令**

列出本地主机的镜像

```
[root@hadoop101 atguigu]# docker images
options说明：
-a 列出本地所有镜像(含中间映像层)
-q 只显示镜像ID
--digests 显示镜像的摘要信息
--no-trunc 显示完整的镜像信息
```

在dockerHub(http://hub.docker.com)上查找某个镜像名字

```
docker search 某个xx镜像的名字
options说明:
--no-trunc 显示完整的镜像描述
-s 列出收藏数不小于指定值的镜像
--automated 只列出atuomated build类型的镜像
```

下载镜像

```
docker pull 某个镜像的名字
[root@hadoop101 atguigu]# docker pull tomcat
```

删除镜像

```
删除单个：docker rmi -f 某个镜像名字
删除多个：docker rmi -f 某个镜像名字1 某个镜像名字2
删除全部：docker rmi -f $(docker images -qa)
```

**容器命令**

新建并启动容器

```
docker run [options] images的名字 [command]
OPTIONS说明（常用）：有些是一个减号，有些是两个减号

 

--name="容器新名字": 为容器指定一个名称；

-d: 后台运行容器，并返回容器ID，也即启动守护式容器；

-i：以交互模式运行容器，通常与 -t 同时使用；

-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；

-P: 随机端口映射；

-p: 指定端口映射，有以下四种格式

      ip:hostPort:containerPort

      ip::containerPort

      hostPort:containerPort

      containerPort
#使用镜像centos:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。
docker run -it centos:6.10 /bin/bash 

```

列出当前所有正在运行的docker进程

```
docker ps [options]
OPTIONS说明（常用）：
-a :列出当前所有正在运行的容器+历史上运行过的

-l :显示最近创建的容器。

-n：显示最近n个创建的容器。

-q :静默模式，只显示容器编号。

--no-trunc :不截断输出。

```

退出容器

​	方式一 容器停止退出

```
exit
```

​	 方式二  容器不停止退出

```
ctrl+P+Q
```

启动容器

```
docker start 容器ID或者容器名
```

重启容器

```
docker restart 容器名或者容器ID
```

停止容器

```
docker stop 容器ID或者容器名
```

重新进入容器

```
docker exec -it 容器名  /bin/bash
```

强制停止容器

```
docker kill 容器ID或者容器名
```

删除已经停止的容器

```
docker rm 容器ID
docker rm -f $(docker ps -a -q)
docker ps -a -q|xages docker rm
```

**重点**

​		启动守护进程

```
#使用镜像centos:latest以后台模式启动一个容器

docker run -d centos

问题：然后docker ps -a 进行查看, 会发现容器已经退出

很重要的要说明的一点: Docker容器后台运行,就必须有一个前台进程.

容器运行的命令如果不是那些一直挂起的命令（比如运行top，tail），就是会自动退出的。

这个是docker的机制问题,比如你的web容器,我们以nginx为例，正常情况下,我们配置启动服务只需要启动响应的service即可。例如

service nginx start

但是,这样做,nginx为后台进程模式运行,就导致docker前台没有运行的应用,

这样的容器后台启动后,会立即自杀因为他觉得他没事可做了.

所以，最佳的解决方案是,将你要运行的程序以前台进程的形式运行

```

​		产看容器日志

```
[root@hadoop101 atguigu]#  docker run -d centos:6.10 /bin/sh -c "while true;do echo hello zzyy;sleep 2;done"


docker logs -f -t --tail 容器ID
-t: 加入时间戳
-f: 跟随最新日志打印
--tail: 显示最后多少条
```

​		查看容器内运行的进程

```
docker top 容器ID
```

​		产看容器内部细节

```
docker inspect 容器ID
```

​		进入正在运行的容器并以命令行进行交互

```
方式一
docker exec -it 容器ID /bin/bash
方式二
docker attach 容器ID
区别:
attach 直接进入容器启动命令终端，不会启动新的进程
exec 是在容器中打开新的终端，并且可以启动的新的进程
如：
[root@hadoop101 atguigu]# docker exec -t 374eb7369f66 ls /tmp
	anaconda-post.log  yum.log

```

​		从容器内拷贝文件到主机上

```
docker cp 容器ID:容器内路径 目的主机的路径
```

**Docker镜像commit操作案列**

```
1 从hub上下载tomcat镜像到本地并且成功运行
docker run -it -p 8888:8080 tomcat
-p 主机端口：docker容器端口
-P 随机分配端口
-i 交互
-t 终端
```

```
2 故意删除上一步镜像生产的镜像生产tomcat容器的文档
```

```
3 也即当前的tomcat运行实例是一个没有文档的容器，
以它为模板commit一个没有doc的tomcat新镜像yk/tomcat:1.1
[root@hadoop101 atguigu]# docker commit -a="yk" -m="tomcat without docs" 404582abfcd7 yk/tomcat:1.1
```

```
4启动我们的镜像和原来对比新启动的yk/tomcat:1.1打不开documention，也就是他没有docs。重新pull一个tomcat启动，它是有docs文件也就是能打开documention
```

#### 4、 Docker容器数据卷

**数据卷**

用于容器和主机之间的数据共享，容器停止退出后，主机修改后数据依然同步

1、直接命令添加

```
1、docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名
[root@hadoop101 atguigu]# docker run -it -v /dataVolume:/dataVolumeContainer centos:6.10

查看数据卷是否挂载成功，看文件Volumes和VolumesRW键值对
docker inspect 容器ID

[root@hadoop101 dataVolume]# docker inspect e3af9baffba2
[
{
    "Id": "e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3",
    "Created": "2019-10-31T07:27:30.6497512Z",
    "Path": "/bin/bash",
    "Args": [],
    "State": {
        "Running": true,
        "Paused": false,
        "Restarting": false,
        "OOMKilled": false,
        "Dead": false,
        "Pid": 3430,
        "ExitCode": 0,
        "Error": "",
        "StartedAt": "2019-10-31T07:27:31.160761091Z",
        "FinishedAt": "0001-01-01T00:00:00Z"
    },
    "Image": "1a8b0bd60174176ba5db1623a003e065757429bae14d6d92f4d9c58c9f83f1cf",
    "NetworkSettings": {
        "Bridge": "",
        "EndpointID": "51b1e6fbfe462e30e6344ca5710845d597e06d88460d82f14b4cce32a75fdf1e",
        "Gateway": "172.17.42.1",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "HairpinMode": false,
        "IPAddress": "172.17.0.1",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "LinkLocalIPv6Address": "",
        "LinkLocalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:11:00:01",
        "NetworkID": "806457f2b6803f8f233e91885d248952a1f8b9f0d250c08e680e31426f2af372",
        "PortMapping": null,
        "Ports": {},
        "SandboxKey": "/var/run/docker/netns/e3af9baffba2",
        "SecondaryIPAddresses": null,
        "SecondaryIPv6Addresses": null
    },
    "ResolvConfPath": "/var/lib/docker/containers/e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3/resolv.conf",
    "HostnamePath": "/var/lib/docker/containers/e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3/hostname",
    "HostsPath": "/var/lib/docker/containers/e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3/hosts",
    "LogPath": "/var/lib/docker/containers/e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3/e3af9baffba204462a24071a41e964f7c276937c9f336914de1f92e012bb22b3-json.log",
    "Name": "/trusting_bartik",
    "RestartCount": 0,
    "Driver": "devicemapper",
    "ExecDriver": "native-0.2",
    "MountLabel": "",
    "ProcessLabel": "",
    "Volumes": {
        "/dataVolumeContainer": "/dataVolume"
    },
    "VolumesRW": {
        "/dataVolumeContainer": true
    },
    "AppArmorProfile": "",
    "ExecIDs": null,
    "HostConfig": {
        "Binds": [
            "/dataVolume:/dataVolumeContainer"
        ],
        "ContainerIDFile": "",
        "LxcConf": [],
        "Memory": 0,
        "MemorySwap": 0,
        "CpuShares": 0,
        "CpuPeriod": 0,
        "CpusetCpus": "",
        "CpusetMems": "",
        "CpuQuota": 0,
        "BlkioWeight": 0,
        "OomKillDisable": false,
        "Privileged": false,
        "PortBindings": {},
        "Links": null,
        "PublishAllPorts": false,
        "Dns": null,
        "DnsSearch": null,
        "ExtraHosts": null,
        "VolumesFrom": null,
        "Devices": [],
        "NetworkMode": "bridge",
        "IpcMode": "",
        "PidMode": "",
        "UTSMode": "",
        "CapAdd": null,
        "CapDrop": null,
        "RestartPolicy": {
            "Name": "no",
            "MaximumRetryCount": 0
        },
        "SecurityOpt": null,
        "ReadonlyRootfs": false,
        "Ulimits": null,
        "LogConfig": {
            "Type": "json-file",
            "Config": {}
        },
        "CgroupParent": ""
    },
    "Config": {
        "Hostname": "e3af9baffba2",
        "Domainname": "",
        "User": "",
        "AttachStdin": true,
        "AttachStdout": true,
        "AttachStderr": true,
        "PortSpecs": null,
        "ExposedPorts": null,
        "Tty": true,
        "OpenStdin": true,
        "StdinOnce": true,
        "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        ],
        "Cmd": [
            "/bin/bash"
        ],
        "Image": "centos:6.10",
        "Volumes": null,
        "VolumeDriver": "",
        "WorkingDir": "",
        "Entrypoint": null,
        "NetworkDisabled": false,
        "MacAddress": "",
        "OnBuild": null,
        "Labels": {
            "org.label-schema.build-date": "20180804",
            "org.label-schema.license": "GPLv2",
            "org.label-schema.name": "CentOS Base Image",
            "org.label-schema.schema-version": "1.0",
            "org.label-schema.vendor": "[root@hadoop101 dataVolume]# docker inspect 3406e937af82
[
{
    "Id": "3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e",
    "Created": "2019-10-31T07:40:41.738486191Z",
    "Path": "/bin/bash",
    "Args": [],
    "State": {
        "Running": true,
        "Paused": false,
        "Restarting": false,
        "OOMKilled": false,
        "Dead": false,
        "Pid": 3741,
        "ExitCode": 0,
        "Error": "",
        "StartedAt": "2019-10-31T07:40:42.09987451Z",
        "FinishedAt": "0001-01-01T00:00:00Z"
    },
    "Image": "1a8b0bd60174176ba5db1623a003e065757429bae14d6d92f4d9c58c9f83f1cf",
    "NetworkSettings": {
        "Bridge": "",
        "EndpointID": "328f9cb11793bc877302287f210435fba42c59aca2c3696796daa6b859c2946a",
        "Gateway": "172.17.42.1",
        "GlobalIPv6Address": "",
        "GlobalIPv6PrefixLen": 0,
        "HairpinMode": false,
        "IPAddress": "172.17.0.2",
        "IPPrefixLen": 16,
        "IPv6Gateway": "",
        "LinkLocalIPv6Address": "",
        "LinkLocalIPv6PrefixLen": 0,
        "MacAddress": "02:42:ac:11:00:02",
        "NetworkID": "806457f2b6803f8f233e91885d248952a1f8b9f0d250c08e680e31426f2af372",
        "PortMapping": null,
        "Ports": {},
        "SandboxKey": "/var/run/docker/netns/3406e937af82",
        "SecondaryIPAddresses": null,
        "SecondaryIPv6Addresses": null
    },
    "ResolvConfPath": "/var/lib/docker/containers/3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e/resolv.conf",
    "HostnamePath": "/var/lib/docker/containers/3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e/hostname",
    "HostsPath": "/var/lib/docker/containers/3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e/hosts",
    "LogPath": "/var/lib/docker/containers/3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e/3406e937af82f1ebd1600d3ac3adcb473a26db9d47148d03a73b5fd55393a73e-json.log",
    "Name": "/pensive_lovelace",
    "RestartCount": 0,
    "Driver": "devicemapper",
    "ExecDriver": "native-0.2",
    "MountLabel": "",
    "ProcessLabel": "",
    "Volumes": {
        "/dataVolumeContainer": "/dataVolume"
    },
    "VolumesRW": {
        "/dataVolumeContainer": false
    },
    "AppArmorProfile": "",
    "ExecIDs": null,
    "HostConfig": {
        "Binds": [
            "/dataVolume:/dataVolumeContainer:ro"
        ],
        "ContainerIDFile": "",
        "LxcConf": [],
        "Memory": 0,
        "MemorySwap": 0,
        "CpuShares": 0,
        "CpuPeriod": 0,
        "CpusetCpus": "",
        "CpusetMems": "",
        "CpuQuota": 0,
        "BlkioWeight": 0,
        "OomKillDisable": false,
        "Privileged": false,
        "PortBindings": {},
        "Links": null,
        "PublishAllPorts": false,
        "Dns": null,
        "DnsSearch": null,
        "ExtraHosts": null,
        "VolumesFrom": null,
        "Devices": [],
        "NetworkMode": "bridge",
        "IpcMode": "",
        "PidMode": "",
        "UTSMode": "",
        "CapAdd": null,
        "CapDrop": null,
        "RestartPolicy": {
            "Name": "no",
            "MaximumRetryCount": 0
        },
        "SecurityOpt": null,
        "ReadonlyRootfs": false,
        "Ulimits": null,
        "LogConfig": {
            "Type": "json-file",
            "Config": {}
        },
        "CgroupParent": ""
    },
    "Config": {
        "Hostname": "3406e937af82",
        "Domainname": "",
        "User": "",
        "AttachStdin": true,
        "AttachStdout": true,
        "AttachStderr": true,
        "PortSpecs": null,
        "ExposedPorts": null,
        "Tty": true,
        "OpenStdin": true,
        "StdinOnce": true,
        "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        ],
        "Cmd": [
            "/bin/bash"
        ],
        "Image": "centos:6.10",
        "Volumes": null,
        "VolumeDriver": "",
        "WorkingDir": "",
        "Entrypoint": null,
        "NetworkDisabled": false,
        "MacAddress": "",
        "OnBuild": null,
        "Labels": {
            "org.label-schema.build-date": "20180804",
            "org.label-schema.license": "GPLv2",
            "org.label-schema.name": "CentOS Base Image",
            "org.label-schema.schema-version": "1.0",
            "org.label-schema.vendor": "CentOS"
        }
    }
}
]
"
        }
    }
}
]
2、docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名

```

2、DockerFile添加

```
1、根目录下新建mydocker文件夹并进入
[root@hadoop101 /]# mkdir mydocker
[root@hadoop101 /]# cd mydocker/
2、编辑dockerfile文件
[root@hadoop101 mydocker]# vim dockerfile

文本内容：
# volume test

FROM centos

VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]

CMD echo "finished,--------success1"

CMD /bin/bash

说明：
出于可移植和分享的考虑，用-v 主机目录:容器目录这种方法不能够直接在Dockerfile中实现。
由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样的特定目录。

3、build后生成镜像，生成一个新的镜像ykk/centos
[root@hadoop101 mydocker]# docker build -f /mydocker/dockerfile -t ykk/centos .

4、run容器
docker run -it *** /bin/bash

主机和容器内的卷目录地址对应路径
docker inspect 容器ID

注：
Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied
解决办法：在挂载目录后多加一个--privileged=true参数即可


```

**数据卷容器**

命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器

案例描述：

```
以ykk/centos为模板并运行容器dc01/dc02/dc03,它们已经具有容器/dataVolumeContainer1和/dataVolumeContainer2
```

1. 先启动一个父容器,并在/dataVolumeContainer2里边重建dc01.txt

```
[root@hadoop101 mydocker]# docker run -it --name dc01 ykk/centos

```

2. dc02/dc03继承自dc1    --volumes-from

```
[root@hadoop101 mydocker]# docker run -it --name dc02 --volumes-from dc01 ykk/centos
[root@hadoop101 mydocker]# docker run -it --name dc03 --volumes-from dc01 ykk/cento

dc02 dc03 都继承了dc01的文件
```

3. 回到dc01 可以看到02/03各自的添加都能共享，比如在02添加文件，03机器上也能共享

4. 删除01，02修改文件03也能同样访问(虽然02 03各自继承01)
5. 结论：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止

#### 4、dockerFile

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

**1、dockerfile关键字**

![](C:\Users\yangkun\Desktop\bupt_study\docker\笔记截图\dockerfile关键字.png)

**2、dockerfile 案例**

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



#### 5、本地镜像推送到阿里云

![阿里云镜像流程](C:\Users\yangkun\Desktop\bupt_study\docker\笔记截图\阿里云镜像流程.png)

**1. 登录阿里云Docker Registry**

```
$ sudo docker login --username=18810192190 registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。 

**2. 从Registry中拉取镜像**

```
$ sudo docker pull registry.cn-hangzhou.aliyuncs.com/yk66/mytomcat9:[镜像版本号]
```

**3. 将镜像推送到Registry**

```
$ sudo docker login --username=18810192190 registry.cn-hangzhou.aliyuncs.com$ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/yk66/mytomcat9:[镜像版本号]$ sudo docker push registry.cn-hangzhou.aliyuncs.com/yk66/mytomcat9:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。