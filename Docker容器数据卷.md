# Docker容器数据卷

## 数据卷

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

## DockerFile添加

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

## 数据卷容器

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

