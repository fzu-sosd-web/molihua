# Docker

* 概述
* 安装
* 命令
* 镜像
* 容器的数据卷
* DockerFile
* Docker网络原理
* IDEA整合docker
* Docker compose
* Docker Swarm
* CI/CD



# Docker概述



## 什么是docker

**为什么会出现Docker？**

在从开发--->上线  有俩套环境，配置

例子：开发和运维 “在我的电脑上可以运行！！！”

发布一个jar+（jdk（开发工具包），mysql服务，redis服务）包时，项目都不能带上环境打包

同时，配置不能跨平台，Windows到最后的Linux



Docker给出了解决方案

类比手机应用商店

Java --- apk  ---  发布到应用商店 --- 使用apk

Java --- jar+环境 --- 打包项目带上环境（镜像） --- 放到Docker仓库（应用商店） --- 下载镜像直接运行

![image-20240121191906764](./markdown-img/Docker.assets/image-20240121191906764.png)



Docker思想：集装箱

**核心思想**：隔离

## Docker的历史

2010年，几个年轻人再美国成立了一家公司``dotCloud``

做一些pass的云计算服务和LXC有关的容器技术

他们将容器化技术命名为Docker

刚诞生的时候没那么火，后来在Docker开源后，逐渐引起了一些开发者的注意，发现了docker的优点

2013年，Docker开源

2014年4月，Docker1.0发布

为什么这么火？十分轻巧！

在容器技术出来之前，我们使用的是虚拟机技术

虚拟机：在Windows里装一个vmware，在这软件里虚拟一台电脑，比较笨重

虚拟机也是属于虚拟化技术，Docker容器技术也是虚拟化技术！

```markdown
# vm下Linux centos（相当与一个电脑），为了隔离，我们需要多个虚拟机

# Docker下 为了隔离 一个镜像（最核心的环境4M + jdk + MySQL）十分的小巧 我们只需要运行镜像就可以了
```

## 聊聊Docker

Docker是基于go语言开发的开源项目！

Docker文档：https://docs.docker.com/ 非常详细

## Docker能干嘛

> 之前的虚拟化技术

![image-20240121195202232](./markdown-img/Docker.assets/image-20240121195202232.png)

缺点：

1. 资源占用十分多
2. 冗余步骤多
3. 启动慢



> 容器化技术

容器化技术不是模拟的一个完整的操作系统

![image-20240121195512137](./markdown-img/Docker.assets/image-20240121195512137.png)



二者的比较

* 传统虚拟机，虚拟出一套硬件，运行一个完整的操作系统，然后安装运行软件
* 容器内的应用直接运行在 宿主机 内，容器没有自己的内核，也没有虚拟硬件，每个容器互相隔离



> DevOps (开发，运维)

1. 更快速的交付和部署

   打包镜像发布测试，一键运行

2. 更便捷的升级和扩缩容

3. 更简单的系统运维

   开发测试环境都是高度一致的

4. 更高效的计算资源利用

   内核级别的虚拟化



# Docker安装

## Docker基本组成

![image-20240121200918809](./markdown-img/Docker.assets/image-20240121200918809.png)



**镜像（image）**：

​	好比是一个模板，通过这模板来创建容器服务

​	tomcat镜像 ==>run ==> tomcat01容器（提供给服务器），通过这个镜像可以创建多个容器，最终服务运行或项目运行就是在容器里的

**容器（container）**：

​	Docker利用容器技术，独立运行一个或一个组应用，通过镜像来创建的

​	基本命令：启动，停止，删除

​	容器可以理解为一个简易的Linux系统

**仓库（repository）**：

​	存放镜像的地方！

​	仓库分为公有和私有！

​	Docker Hub（默认是国外的），阿里云...都有容器服务，需要配置镜像服务



## 安装Docker

> 环境准备

环境查看

```shell'
# 系统内核在3.10以上
[root@iZ0jl9478atab6g1ij4qxvZ ~]# uname -r
3.10.0-1160.105.1.el7.x86_64
```

```shell
# 系统版本
[root@iZ0jl9478atab6g1ij4qxvZ ~]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

1、卸载旧的Docker

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine
Loaded plugins: fastestmirror
No Match for argument: docker
No Match for argument: docker-client
No Match for argument: docker-client-latest
No Match for argument: docker-common
No Match for argument: docker-latest
No Match for argument: docker-latest-logrotate
No Match for argument: docker-logrotate
No Match for argument: docker-engine
No Packages marked for removal
```

2、需要的安装包

```shell
yum install -y yum-utils
```

3、设置镜像的仓库

```shell
yum-config-manager --add-repo 

https://download.docker.com/linux/centos/docker-ce.repo # 默认是国外的
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo # 阿里云镜像
```

4、更新索引

```shell
yum makecache fast
```

5、安装Docker

```shell
# ce 社区版
yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

6、启动Docker,使用docker version判断是否成功

```shell
systemctl start docker[root@iZ0jl9478atab6g1ij4qxvZ ~]# systemctl start docker
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker version
Client: Docker Engine - Community
 Version:           25.0.1
 API version:       1.44
 Go version:        go1.21.6
 Git commit:        29cf629
 Built:             Tue Jan 23 23:12:51 2024
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          25.0.1
  API version:      1.44 (minimum version 1.24)
  Go version:       go1.21.6
  Git commit:       71fa3ab
  Built:            Tue Jan 23 23:11:50 2024
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.27
  GitCommit:        a1496014c916f9e62104b33d1bb5bd03b0858e59
 runc:
  Version:          1.1.11
  GitCommit:        v1.1.11-0-g4bccb38
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

```

7、使用hello world

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally  # 找不到hello world 镜像
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete  # 去远程拉取
Digest: sha256:4bd78111b6914a99dbc560e6a20eab57ff6655aea4a80c50b0c5491968cbc2e6
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

8、查看镜像

```shell
docker images
```

![image-20240126155055698](./markdown-img/Docker.assets/image-20240126155055698.png)

> 了解：卸载Docker

```shell
# 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras

# 卸载资源
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

阿里云镜像加速

1、登录阿里云找到容器镜像服务

2、找到镜像加速

![image-20240126155557222](./markdown-img/Docker.assets/image-20240126155557222.png)

3、配置使用

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1uiu21al.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker

```

## 回顾HelloWorld执行过程

![image-20240126160056584](./markdown-img/Docker.assets/image-20240126160056584.png)





## 底层原理

Docker是怎么工作的？

Docker是一个Client - Server 结构的系统，Docker的守护进程运行在主机上，通过socket从客户端访问

DockerServer 接收到DockerClient的命令就会执行

![image-20240126162258247](./markdown-img/Docker.assets/image-20240126162258247.png)

**Docker为什么比虚拟机快**

1、Docker有比虚拟机更少的抽象层![image-20240126162530879](./markdown-img/Docker.assets/image-20240126162530879.png)

2、docker利用的是宿主机的内核，vm需要的是guest os

所以说，新建一个容器的时候，Docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导，虚拟机则是加载guest os，分钟级别的，而Docker是利用宿主机的操作系统，省略了这个复杂的过程，是秒级的 

![image-20240126163004114](./markdown-img/Docker.assets/image-20240126163004114.png)

# docker的常用命令

##帮助命令

```shell
docker version # 查看Docker的版本信息
dokcer info # 查看Docker的详细信息
docker 命令 --help # 查看Docker的命令的帮助
```

帮助文档：https://docs.docker.com/reference/

## 镜像命令

```shell
docker images 
```

![image-20240126163915577](./markdown-img/Docker.assets/image-20240126163915577.png)

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker images -a
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d2c94e258dcb   8 months ago   13.3kB

REPOSITORY 镜像的仓库源
TAG 镜像的标签
IMAGE ID 镜像的id
CREATED  镜像的创建时间
SIZE  镜像的大小

-a 列出所有的
-q 只显示id
```

**docker search 搜索镜像**(也可以去Docker hub 相关)

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL
mysql                           MySQL is a widely used, open-source relation…   14801     [OK]

# 可选项，通过收藏来过滤
--filter=STARS=3000 搜索出来的就是stars数大于3000的
```

**docker pull 下载镜像**

```shell
# docker pull 镜像名 [:tag]
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker pull mysql
Using default tag: latest # 如果不写tag，默认就是最新版
latest: Pulling from library/mysql
72a69066d2fe: Pull complete # 分成下载 Docker image 的核心 联合文件信息
93619dbc5b36: Pull complete 
99da31dd6142: Pull complete 
626033c43d70: Pull complete 
37d5d7efb64e: Pull complete 
ac563158d721: Pull complete 
d2ba16033dad: Pull complete 
688ba7d5c01a: Pull complete 
00e060b6d11d: Pull complete 
1c04857f594f: Pull complete 
4d7cfa90e6ea: Pull complete 
e0431212d27d: Pull complete 
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709  # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  # 真实地址

docker pull mysql  等价于 docker pull docker.io/library/mysql:latest

# 指定版本下载
docker pull mysql:8.0
```

![image-20240126165234925](./markdown-img/Docker.assets/image-20240126165234925.png)

**docker rmi 删除镜像**

```shell
docker rmi -f 3218b38490ce # 删除多个镜像 id删除 空格分离
docker rmi -f $(docker images -aq) # 删除全部容器
```





## 容器命令

说明：我们有了镜像才可以创建容器

下载一个centos镜像来测试

```shlell
docker pull centos
```

新建容器并启动

```shell
docker run [可选参数]  image # 启动一个镜像

# 参数说明
--name="名字"  容器名字
-d  以后台方式运行
-ti  使用交互方式运行，进入容器查看内容
-p  指定容器的端口 -p 8080:8080
	-p 主机端口：容器端口
	-p 容器端口
	-p ip:主机端口:容器端口
-P 随机指定端口

# 测试，启动并进入容器
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker run -it centos /bin/bash
# 主机名称变成镜像名称
[root@eb23cea2b179 /]# 
# 容器内的centos，基础版本
[root@eb23cea2b179 /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

# 退出命令，从容器里退出主机
[root@eb23cea2b179 /]# exit
exit
```

列出运行的容器

```shell
docker ps
    # 列出当前正在运行的主机
-a  # 列出当前和之前运行的主机
-n=?#显示最近创建的？个容器
-q  # 只显示容器的编号


# 列出所有的运行的主机docker ps
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
# 列出现在和曾经运行的主机
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED             STATUS                         PORTS     NAMES
eb23cea2b179   centos         "/bin/bash"   3 minutes ago       Exited (0) 2 minutes ago                 confident_jemison
00aa8702e75a   d2c94e258dcb   "/hello"      About an hour ago   Exited (0) About an hour ago             recursing_carver
```

**退出容器**

```shell
exit #  退出容器并停止
Ctrl+P+Q #  退出容器但是不停止
```

**删除容器**

```shell
docker rm id # 删除容器，不能删除正在运行的容器，除非加上-f
docker rm -f $(docker ps -aq) #删除所有容器
docker ps -aq | xargs docker rm # 删除所有容器
```

**启动和停止容器的操作**

```shell
docker start   # 启动容器
docker restart # 重启
docker stop    # 停止
docker kill	   # 杀死
```





## 其他常用命令

**后台启动容器**

```shell
# 命令docker run -d 镜像名
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker run -d centos
b424c7a2a6d12ebe52275f0f17dce5c96ac7497e4fb061231ee3101674f1fad0
 
# 问题 docker ps 发现centos停止了
# 常见的坑，Docker的容器使用后台运行，就必须要有一个前台进程，Docker发现没有了应用，就会自动停止
# nginx 容器启动后，发现自己没有提供服务，就直接停止了
```

**查看日志**

```shell
docker logs	-tf -tail n id
-tf # 显示日志和时间戳
-tail n # 显示n条
```

**查看容器里的进程消息**

```shell
top 命令
docker top 容器id
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker top f42f0bb5bbe5
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                12596               12577               0                   18:23               pts/0               00:00:00            /bin/bash

```

**查看镜像的原数据**

```shell
docker inspect id
```

```sh
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker inspect f42f0bb5bbe5
[
    {
        "Id": "f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605",
        "Created": "2024-01-26T10:23:25.428988815Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 12596,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2024-01-26T10:23:25.65549646Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605/hostname",
        "HostsPath": "/var/lib/docker/containers/f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605/hosts",
        "LogPath": "/var/lib/docker/containers/f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605/f42f0bb5bbe5c96c4e70447dec70de0053137656e421f172a850b095bf532605-json.log",
        "Name": "/romantic_nightingale",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                42,
                191
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/a969989ad7807170a525934dd5592c6f567bafb6bef842a604a318f2628a67d2-init/diff:/var/lib/docker/overlay2/f7c8d8bacfcb2ff54bbfd7df96c8ede57371aec056e91820a76609bea8aa7436/diff",
                "MergedDir": "/var/lib/docker/overlay2/a969989ad7807170a525934dd5592c6f567bafb6bef842a604a318f2628a67d2/merged",
                "UpperDir": "/var/lib/docker/overlay2/a969989ad7807170a525934dd5592c6f567bafb6bef842a604a318f2628a67d2/diff",
                "WorkDir": "/var/lib/docker/overlay2/a969989ad7807170a525934dd5592c6f567bafb6bef842a604a318f2628a67d2/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "f42f0bb5bbe5",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "a8c424bf62ea5c13af2ea324e39d28839f69a3945b6a753ce7d3c52c20d6a3fc",
            "SandboxKey": "/var/run/docker/netns/a8c424bf62ea",
            "Ports": {},
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "b05a1be28166d556de38dcbfbe5dc8eb3c235efea7e91ece2f91d6b2345c4268",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:11:00:02",
                    "NetworkID": "1f445435476592e869bc160cbf1f409ccbf0857a6a1106e5cc401e555690d340",
                    "EndpointID": "b05a1be28166d556de38dcbfbe5dc8eb3c235efea7e91ece2f91d6b2345c4268",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DriverOpts": null,
                    "DNSNames": null
                }
            }
        }
    }
]

```



**进入当前正在运行的容器**

```sh
# 我们通常容器都是使用后台方式进行的，我们要进去

docker exec -it 容器id

[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker exec -it f42f0bb5bbe5 /bin/bash
[root@f42f0bb5bbe5 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@f42f0bb5bbe5 /]# exit
exit

# 方式二
docker attach 容器id

docker attach f42f0bb5bbe5

# docker exec  # 进入容器后开启一个新的终端
# docker attach  # 不启动新的终端，进入新的终端


```



**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的的主机的路径

[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker run -it centos /bin/bash
[root@3d50e351d8bb ~]# cd /home
[root@3d50e351d8bb home]# ls
[root@3d50e351d8bb home]# touch test.txt
[root@3d50e351d8bb home]# ls
test.txt
[root@3d50e351d8bb home]# [root@iZ0jl9478atab6g1ij4qxvZ ~]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
3d50e351d8bb   centos    "/bin/bash"   47 seconds ago   Up 46 seconds             elastic_goodall
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker cp 3d50e351d8bb:/home/test.txt /home
Successfully copied 1.54kB to /home
[root@iZ0jl9478atab6g1ij4qxvZ ~]# cd /home
[root@iZ0jl9478atab6g1ij4qxvZ home]# ls
test.txt
```

## 小结

![image-20240126185035978](./markdown-img/Docker.assets/image-20240126185035978.png)



## 练习

### 1、下载nginx

1. 搜索镜像
2. 下载镜像
3. 测试

```shell
docker run -d --name nginx01 -p 3344:80 nginx# 通过公网的3344可以访问到容器80

# -d 后台运行
# --name 容器名
# -p 宿主机端口:容器内端口
[root@iZ0jl9478atab6g1ij4qxvZ sbin]# curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

![image-20240126202921612](./markdown-img/Docker.assets/image-20240126202921612.png)

### 2、安装tomcat

```shell
# 官方
$ docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，容器还是可以查到   官方的方法一般用来测试，用完即删

# 还是先下载
docker pull tomcat:9.0

# 发现问题
[root@iZ0jl9478atab6g1ij4qxvZ local]# docker exec -it tomcat01 /bin/bash
root@d5b38e0e2f09:/usr/local/tomcat# ls
BUILDING.txt  CONTRIBUTING.md  LICENSE	NOTICE	README.md  RELEASE-NOTES  RUNNING.txt  bin  conf  lib  logs  native-jni-lib  temp  webapps  webapps.dist  work
root@d5b38e0e2f09:/usr/local/tomcat# cd webapps
root@d5b38e0e2f09:/usr/local/tomcat/webapps# ls
root@d5b38e0e2f09:/usr/local/tomcat/webapps# 
# 1、Linux命令少了
# 2、没有webapps
# 原因：阿里云镜像的原因，默认是最小的镜像，把不必要的都删了

# 解决 把webapps.dist的内容复制过去
```





## 可视化

* portainer（先用这个）
* rancher

docker 的图形化界面管理工具，提供一个后台面板进行操作 

```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：访问外网的8088

# Docker镜像讲解

## 什么是镜像

独立的软件包，可执行的，用来打包软件运行环境 

* 从远程仓库下载
* 拷贝
* 自己写

## 分层

![image-20240127122333442](./markdown-img/Docker.assets/image-20240127122333442.png)

run起来的镜像，就是容器

但是镜像是只读的，我们的容器就是在镜像上加一层

![image-20240127123210044](./markdown-img/Docker.assets/image-20240127123210044.png)

## 如何提交一个自己的镜像

**commit镜像**

```shell
docker commit 提交容器
docker commit -m="提交的描述" -a="作者" 容器id 目标镜像名[:tag]
```

```shell
- 启动一个默认的tomcat
- 发现者tomcat没有webapps了，需要我们手动复制
- 我们操作过的容器进行commit，成为一个新的镜像，这样我们就可以使用新的镜像了
```

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker commit -a="shaoxiawjc" -m="add webapps" f6db7216d20c tomcat02:1.0
sha256:bea8d80a31dfd095fa3ea3a4b8d5d18dc90cd45c66d26eb29cc56605a44cbbfb
```

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
tomcat02              1.0       bea8d80a31df   29 seconds ago   685MB
nginx                 latest    605c77e624dd   2 years ago      141MB
tomcat                9.0       b8e65a4d736d   2 years ago      680MB
redis                 latest    7614ae9453d1   2 years ago      113MB
centos                latest    5d0da3dc9764   2 years ago      231MB
portainer/portainer   latest    580c0e4e98b0   2 years ago      79.1MB
```



# 容器数据卷

## 什么是容器数据卷

**docker理念：**

将应用和环境打包成为一个镜像，运行起来就变成了容器

**问题：**

如果数据都在容器里，那么容器被移除了==（不是停止）==，数据就丢失了

比如：MySQL

**需求：**

数据持久化

MySQL的数据可以存储在本地

希望容器之间可以有一个数据共享的技术，Docker容器里产生的数据，可以同步到本地

这就是卷技术，目录的挂载！将我们容器的目录挂载在Linux系统里

![image-20240127131007594](./markdown-img/Docker.assets/image-20240127131007594.png)

同时，容器间也是可以共享的



## 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机路径:容器内目录  镜像id
```

```shell
docker run -it -v /home/test:/home centos
```

通过Docker inspect 查看容器消息

![image-20240127131842020](./markdown-img/Docker.assets/image-20240127131842020.png)

测试

文件的同步

![image-20240127132212411](./markdown-img/Docker.assets/image-20240127132212411.png)

停止容器依然成立



优点：我们以后只需要在本地修改就行了



## 测试MySQL

```shell
# 获取镜像
docker pull mysql:8.0

# 运行容器时需要挂载 同时还要注意配置MySQL的密码
 docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
 
 [root@iZ0jl9478atab6g1ij4qxvZ ~]# docker run -d -p 3307:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234567 --name=mysql01 mysql:8.0
c5895557906fb3170973e40c31710499196988aab6567adf7988ce28bf738735

# 配置成功后，防火墙和安全组都完成后，在本地使用sqlyog测试连接成功

```



## 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
在-v时只写容器内的路径
通过docker volume inspect 容器id 来查看具体信息


# 具名挂载
-v 卷名:容器内路径
```

不指定路径时

所有Docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/xxx`



我们通过具名挂载可以方便的找到我们的一个卷

```shell
-v 容器内路径
-v 卷名:容器内路径
-v /宿主机路径:容器内路径
```

拓展:

```sh
在挂载的时候-v最后加上
:ro :rw等等
用来改变读写权限
ro 只读
rw 读写 默认

# 一旦设定了这个权限，容器对我们挂载出来的内容就有限定了

# ro 只能从宿主机来改变了
```







## 初识dockerfile

用来构建docker镜像的构建文件

目录脚本

通过这个脚本可以生成镜像，而镜像是一层一层的，所以命令也是一行一行的

> 创建镜像 方式二

```shell
# 创建dockerfile文件
# 文件里的内容 指令（都是大写的）和参数
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "---------end--------"

CMD /bin/bash
# 这里的每个命令就是镜像的一层
```

![image-20240127142848219](./markdown-img/Docker.assets/image-20240127142848219.png)

```shell
# 启动我们的镜像
```

![image-20240127143548996](./markdown-img/Docker.assets/image-20240127143548996.png)

发现我们的卷

然而我们的

```shell
VOLUME ["volume01","volume02"]
```

显然是一个匿名挂载

我们要通过docker inspect 容器id 来查看对应的数据卷位置



## 数据卷容器

多个MySQL同步数据 

==`--volumes-from`==

![image-20240127144359694](./markdown-img/Docker.assets/image-20240127144359694.png)

```shell
# 启动三个容器
# 使用container启动一个d1容器
docker run -it --name d1 container
# 使用container启动d2容器，同时使用 --volumes-from d1 来 继承 d1
docker run -it --name d2 --volumes-from d1 container

d1创建的数据也可以同步到d2

这里的d1就是数据库容器
```

```shell
# 删除了d1，d2还是有数据
```

![image-20240127145728764](./markdown-img/Docker.assets/image-20240127145728764.png)

 数据卷的生命周期：一直持续待没有容器使用为止







# DockerFile

用来构建docker镜像的文件

命令参数脚本

1. 编写一个dockerfile文件
2. docker build构建
3. docker run 运行
4. docker push 发布镜像（Docker Hub 阿里云镜像 ）

## 构建过程	

基础知识：

1. 指令都是大写
2. 从上到下执行
3. “#”表示注释
4. 每一个指令代表一层

dockerfile 是面向开发的，发布项目和做镜像就是编写dockerfile

DockerFile 构建文件，源代码

DockerImages file构建的镜像

Docker容器 镜像运行起来

![image-20240127155317612](./markdown-img/Docker.assets/image-20240127155317612.png)

![image-20240127155254190](./markdown-img/Docker.assets/image-20240127155254190.png)

```shell
FROM  # 基础镜像,一切从这
MAINTAINER # 镜像是谁写的，姓名+邮箱
RUN  # 镜像构建的时候需要运行的命令
ADD  # 步骤 比如我们有tomcat镜像，我们就在这里添加
WORKDIR  # 镜像的工作目录
VOLUME  # 容器卷，挂载的目录位置
EXPOSE  # 指定暴露端口配置
CMD  # 指定这个容器运行的时候要运行的命令 只有最后一个会生效
ENTRYPOINT  # 指定这个容器运行的时候要运行的命令  可以追加命令
ONBUILD  # 当构建一个被继承的 DockerFile 就会运行这个指令 这是一个触发指令
COPY  # 类似ADD命令 拷贝
ENV  #构建的时候设置环境变量
```



## 实战测试

docker hub 的99% 镜像都是从基础镜像 FROM scratch 来的

> 创建一个自己的centos

![image-20240127160445551](./markdown-img/Docker.assets/image-20240127160445551.png)

docker history 查看镜像的构建过程

> CMD 和 ENTRYPOINT 的区别

使用CMD 时，在run的时候不能在命令后追加，ENTRYPOINT 可以



## 实战tomcat镜像

1、准备镜像文件 tomcat压缩包 jdk压缩包

![image-20240127171524689](./markdown-img/Docker.assets/image-20240127171524689.png)

2、编写dockerfile,官方命名Dockerfile,build会自动寻找这个文件

```sh
FROM centos:7

MAINTAINER shaoxiawjc<1916657085@qq.com>

COPY readme.txt /usr/local/readme.txt

ADD apache-tomcat-9.0.35.tar.gz /usr/local
ADD jdk-8u202-linux-i586.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local

WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_202-i586
ENV CLASSPATH $JAVA_HOME/LIB/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.35
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.35
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/usr/local/apache-tomcat-9.0.35/bin/startup.sh && -F /usr/local/apache-tomcat-9.0.35/bin/logs/catalina.out 
```

==注意：如果这一报错什么没有url mirror 那就是centos版本太高了==

3、构建镜像

```shell
docker build -t diytomcat .
```

4、启动访问

5、发布项目

## 发布自己的镜像

> dockerhub

在服务器里提交

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
```

```shell
[root@iZ0jl9478atab6g1ij4qxvZ ~]# docker login -u shaoxiawjc
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

```shell
docker push shaoxiawjc/diy:1.0
```













# Docker网络

1、清空所有的环境（进程和镜像）

![image-20240127182002463](./markdown-img/Docker.assets/image-20240127182002463.png)

三个网络

```shell
# 问题：docker是如何处理网络访问的？ 
```

![image-20240127184038761](./markdown-img/Docker.assets/image-20240127184038761.png)

```shell
docker pull tomcat:8 # 运行tomcat：8 

可能没有ip addr 要先进容器下载

root@c9478f4e83dd:/usr/local/tomcat# apt update
root@c9478f4e83dd:/usr/local/tomcat# apt install -y iproute2

root@eec23e94d200:/usr/local/tomcat# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
62: eth0@if63: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

思考：Linux能不能ping通Docker容器内部

可以、

> 原理

1、我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡 docker0 桥接模式 使用veth-pair技术

![image-20240127185624945](./markdown-img/Docker.assets/image-20240127185624945.png)

这些容器























