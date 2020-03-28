---
layout: post
title: Docker 学习日记
categories:
  - 日记
tags:
  - Docekr
  - 学习
flag: red
date: 2020-03-27T16:49:25.000Z
goal: 1000
---

{% centerquote %}本文是一个 Docker 初学者学习 Docker 的过程记录。{% endcenterquote %}

<!-- more -->

# DAY 1

## 明确学习目标

1. 容器云平台的软件设计与研发工作

2. 基于容器云的CI/CD软件

3. DevOps 软件系统的设计与开发

4. Kubernetes 云编排服务框架的研发工作

在整个学习中首先需要明白的就是什么是容器，这引出了我们的第一个问题。

##  什么是容器

容器（Container）可以理解为一个名称，用于描述有固定样式的打包好的一个整体。

而计算机中的容器，可以类比我们生活中用于出租的小隔间，容器就是这个一个个被分隔开来的小隔间。

在我们理解容器是什么之后随之而来的问题就是，原来的环境好好的，为什么要引入容器这个概念呢？它又是怎么实现的呢？

## 为什么要引入容器

我们引入容器的目的其实也很简单，随着计算机技术的不断更新发展，计算机系统、软件、运行环境碎片化情况越来越明显，每个用户计算机的环境都不相同，你怎么知道你的软件，能在各个机器上跑起来？如果某些老旧的模块与当前环境不兼容，那就麻烦了。

环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。为了让用户在使用一些软件的时候不用浪费过多的时间在配置环境上，同时也是为了更多的压榨计算机性能，容器的想法就由此诞生。

其实在容器之前也有类似的，想减少用户配置环境的一些项目，但是原来的想法都比较粗暴，想的都是直接从硬件层面上虚拟出一整个机器，再在其上安装系统，这样在这个虚拟出来的电脑上就能运行一个环境完全虚拟可控的应用程序了。但是随之而来的问题也是很显著的，需要完整的从硬件层面上虚拟出一个机器再在上面套一个系统，系统内再套娃一个应用程序，这么多步虚拟，相应的计算性能的损耗也是惊人的。

其实在实际日常使用中，我们并不需要完全的虚拟出一个新的操作系统，我们需要的只是在相同系统中虚拟出一个适合程序的运行环境就行。所以更为轻量级的系统层虚拟化就更符合人们的需求。

## Docker 容器的简单实现方法

以下以Docker作为容器典型来简单描述一下，Docker容器的实现方法，容器作为系统层面的虚拟化其实并不需要完整的虚拟出一个底层程序，很多可能直接拿来公用的部分可以直接使用宿主机，很好的一个典型就是系统内核（Kernel）。对于不同的系统，系统内核也是不同的，所以Docker在不同系统的上使用的虚拟方式也有所区别。

首先，从内核共享与否

Docker在Linux上共享内核，无需虚拟化，完全支持Native功能[https://docs.docker.com/engine/installation/linux/](https://docs.docker.com/engine/installation/linux/%EF%BC%89)。 

Docker在windows上，启用Hyper-V[https://docs.docker.com/docker-for-windows/](https://docs.docker.com/docker-for-windows/)或者虚拟化技术[https://docs.docker.com/toolbox/toolbox_install_windows/](https://docs.docker.com/toolbox/toolbox_install_windows/)，通过虚拟机来实现，不共享windows内核。 

Docker在mac os上，同样用虚拟化技术xhyve或者virtualbox来实现（[https://docs.docker.com/engine/installation/mac/](https://docs.docker.com/engine/installation/mac/) ），不共享Mac OS 内核。

其次，能创建何种类型Docker

1. Docker在Linux上共享内核，只能创建Linux类Docker。 

2. Docker在Windows上，可以创建Linux类Docker和Windows类Docker。 

3. Docker在Mac os上，只能创建Linux类Docker，不能创建Mac OS的Docker。

Docker的虚拟化最简单的其实就是隔离机制，类似于用`chroot`命令讲根目录隔离一样，不过Docker运用的是内核（Kernel）3.8版本支持的一些新特性，这使得Docker在Linux上能获得几乎等同于Native的体验。

Linux下通过内核自带的Namespace与Cgroups进行实现，Windows下可通过Hyper-V进行虚拟化。

# DAY 2

对于 Docker 的核心实现原理，可以查看这篇文章，[Docker 核心技术与实现原理](https://draveness.me/docker)（不过目前我有很多地方都不是很读得懂。）

既然大致的明白的 Docker 的原理，那么就需要我们结合实际情况来进行学习。

## 镜像与容器的基本操作

类似于数据库的增、删、改、查一样，Docker 也有几个比较常规的操作。

### 常规操作

#### 获取容器

```Shell
docker pull [选项] [Docker Registery 地址[:端口]/]仓库名[:标签]
```

举例：`docker pull ubuntu:16.04`

说明：从 Docker Hub （如不填写指定的 Docker Registery 地址情况下默认为从 Docker Hub 上拉取镜像）上拉取，标签(tag)为 16.04 的 Ubuntu 镜像。

[选项]说明：

* **-a : **拉取所有 tagged 镜像

* **--disable-content-trust :**忽略镜像的校验,默认开启

#### 运行容器

```Shell
docker run [选项] 镜像 [命令]  [ARG...]
```

举例：

```Shell
docker run -it --rm
  ubuntu:16.04 \
  /bin/bash
```


说明：运行一个可交互的 ubuntu16.04 的镜像，镜像在运行结束后强行移除。

OPTIONS说明：

*   **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

*   **-d:** 后台运行容器，并返回容器ID；

*  **-rm**容器在结束后删除容器；

*   **-i:** 以交互模式运行容器，通常与 -t 同时使用；

*   **-P:** 随机端口映射，容器内部端口**随机**映射到主机的高端口

*   **-p:** 指定端口映射，格式为：主机(宿主)端口:容器端口

*   **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

*   **--name="nginx-lb":** 为容器指定一个名称；

*   **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；

*   **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；

*   **-h "mars":** 指定容器的hostname；

*   **-e username="ritchie":** 设置环境变量；

*   **--env-file=[]:** 从指定文件读入环境变量；

*   **--cpuset="0-2" or --cpuset="0,1,2":** 绑定容器到指定CPU运行；

*   **-m :**设置容器使用内存最大值；

*   **--net="bridge":** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

*   **--link=[]:** 添加链接到另一个容器；

*   **--expose=[]:** 开放一个端口或一组端口；

*   **--volume , -v: **绑定一个卷

#### 删除容器

```shell
docker rm [选项] 容器[容器...]
```

举例：`docker rm ubuntu`

说明：删除一个已经结束且命名为 ubuntu 的容器。

选项说明：

*   **-f :**通过 SIGKILL 信号强制删除一个运行中的容器。

*   **-l :**移除容器间的网络连接，而非容器本身。

*   **-v :**删除与容器关联的卷。

### 初阶操作

#### 列出镜像

`docker image ls`

#### 镜像大小

`docker system df`

#### 容器操作

`docker start/stop/restart [OPTIONS] CONTAINER [CONTAINER...]`

举例：`docker start/stop/restart CONTAINER`

说明：启动已被停止的容器 CONTAINER /停止运行中的容器 CONTAINER /重启容器 CONTAINER。

#### 进入容器

`docker exec [命令] -it [参数]`

举例：`docker exec -it ubunut /bin/bash`

说明：进入名为 ubuntu 的容器内，并运行 bash。

> 注:使用`docker exec`如果从这个 stdin 中退出的时候不会导致容器停止。

#### 删除容器

`docker container rm CONTAINER`

举例：`docker container rm ubuntu`

说明：删除名为 ubuntu 的容器。

注：`docker container prune`为删除所有处于停止状态的容器，`docker container ls -a`删除所有容器。

