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

{% centerquote %}本文是一个 Docker 初学者学习 Docker 的过程记录。目前仅仅是学习过程中的记录并未整理和简化，内容过于干涩，后续会有相应的修改。{% endcenterquote %}

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

环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。为了让用户在使用一些软件的时候不用浪费过多的时间在配置环境上，同时也是为了灵活使用计算机性能，容器的想法就由此诞生。

其实在容器之前也有类似的，想减少用户配置环境的一些项目，但是原来的想法都比较粗暴，想的都是直接从硬件层面上虚拟出一整个机器，再在其上安装系统，这样在这个虚拟出来的电脑上就能运行一个环境完全虚拟可控的应用程序了。但是随之而来的问题也是很显著的，需要完整的从硬件层面上虚拟出一个机器再在上面套一个系统，系统内再套娃一个应用程序，这么多层虚拟，相应的计算性能的损耗也是惊人的。

其实在实际日常使用中，我们并不需要完全的虚拟出一个新的操作系统，我们需要的只是在相同系统中虚拟出一个完全隔离且适合程序的运行环境就行。所以更为轻量级的系统层虚拟化就进入了人们的视野。

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

对于 Docker 的核心实现原理，可以查看这篇文章，[Docker 核心技术与实现原理](https://draveness.me/docker)（不过目前我有很多地方都不是很读得懂。对于 Linux 内核指令及其层级关系的部分有点晦涩。）

既然大致明白 Docker 的原理，那么就要我们结合实际情况来进行学习一下镜像与容器的基本操作。

类似于数据库的增、删、改、查一样，Docker 也有几个比较常规的操作。

## 常规操作

### 获取容器

`docker pull [选项] [Docker Registery 地址[:端口]/]仓库名[:标签]`

举例：
```
docker pull ubuntu:16.04
```

说明：从 Docker Hub （如不填写指定的 Docker Registery 地址情况下默认为从 Docker Hub 上拉取镜像）上拉取，标签(tag)为 16.04 的 Ubuntu 镜像。

[选项]说明：

* **-a : **拉取所有 tagged 镜像

* **--disable-content-trust :**忽略镜像的校验,默认开启

### 运行容器

`docker run [选项] 镜像 [命令]  [ARG...]`

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

### 删除镜像

`docker rm [选项] 容器[容器...]`

举例：
```
docker rm ubuntu
```

说明：删除一个已经结束且命名为 ubuntu 的容器。

选项说明：

*   **-f :**通过 SIGKILL 信号强制删除一个运行中的容器。

*   **-l :**移除容器间的网络连接，而非容器本身。

*   **-v :**删除与容器关联的卷。

## 初阶操作

### 列出镜像

`docker image ls`

### 镜像大小

`docker system df`

### 容器操作

`docker start/stop/restart [OPTIONS] CONTAINER [CONTAINER...]`

举例：
```
docker start/stop/restart CONTAINER
```

说明：启动已被停止的容器 CONTAINER /停止运行中的容器 CONTAINER /重启容器 CONTAINER。

### 进入容器

`docker exec [命令] -it [参数]`

举例：
```
docker exec -it ubunut /bin/bash
```

说明：进入名为 ubuntu 的容器内，并运行 bash。

> 注:使用`docker exec`的好处在于，如果从这个 stdin 中退出不会导致容器停止。

### 删除容器（作用同上删除镜像部分）

`docker container rm CONTAINER`

举例：
```
docker container rm ubuntu
```

说明：删除名为 ubuntu 的容器。

注：`docker container prune`为删除所有处于停止状态的容器，`docker container ls -a`删除所有容器。


## 进阶操作

### Docker 镜像定制

由于 Docker 的镜像文件属性是只读，所以如果改变了由此镜像创造的 Container 能通过很简单的`docker diff CONTAINER`指令直接看到对容器存储层的改动。

也因为这种特性，Docker 也有类似于 Git 类似的版本控制管理方法，可通过`docker commit`命令，将当前状态下的 Container 保存起来，甚至于保存为镜像部署在其他环境或用于 Debug 。

举例：
```shell
docker commit \

--author "Tiny Xiu" \

--message "2020年3月29日备份" \

webserver \

nginx:backup001
```

其中 --author 是指定修改的作者， --message 是记录本次修改的备注。这点和 Git 版本控制相似。

虽然`docker commit`命令能让我们轻松的修改一个 Docker image 但是在实际的操作中，不推荐这样进行操作。因为通过前面的学习，我们知道 Docker image 的构建是通过一层一层的 Docker 层构建上去的。通过`docker commit`来构建会徒增 Docker 的层数，消耗存储空间增加构建时间。

最优的方案就是我们自己构建自己的 `Dockerfile`。Dockerfile 是⼀个⽂本⽂件，其内包含了⼀条条的指令(Instruction)，每⼀条指令构建⼀层，因此每⼀条指令的内容，就是描述该层的构建方法。

首先先弄个最简单的例子。

举例：

```shell
FROM nginx
RUN echo '这是一个本地构建的nginx镜像'  >  /usr/share/nginx/html/index.html
```

说明：这里使用了 Nginx 作为基础镜像，并对其初始页面进行了修改。

**注**：Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大。以 && 符号连接命令，这样执行后，只会创建 1 层镜像。

**FROM**：定制的镜像都是基于 FROM 的镜像，这里的 nginx 就是定制需要的基础镜像。后续的操作都是基于 nginx。

**RUN**：用于执行后面跟着的命令行命令。有以下两种格式：

> shell 格式：
> `RUN <命令行命令>  `
> `# <命令行命令> 等同于，在终端操作的 shell 命令。`
>   exec 格式：
> `RUN ["可执行文件",  "参数1",  "参数2"]  # 例如：  # RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline`

类似于**FROM**、**RUN**Dockerfile 中也定义了一些其他的指令:

**COPY**

复制指令，从上下文目录中复制文件或者目录到容器里指定路径。

格式：

`COPY [--chown=<user>:<group>]  <源路径1>...  <目标路径> COPY [--chown=<user>:<group>]  ["<源路径1>",...  "<目标路径>"]`

* **[--chown=<user>:<group>]**：可选参数，用户改变复制到容器内文件的拥有者和属组。

* **<源路径>**：源文件或者源目录，这里可以是通配符表达式，其通配符规则要满足 Go 的 filepath.Match 规则。例如：
COPY hom*  /mydir/ COPY hom?.txt /mydir/

* **<目标路径>**：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建。

**ADD**

* ADD 指令和 COPY 的使用格式一致（同样需求下，官方推荐使用 COPY）。功能也类似，不同之处如下：

* ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。
* ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。

**CMD**

类似于 RUN 指令，用于运行程序，但二者运行的时间点不同:

*   CMD 在docker run 时运行。
*   RUN 是在 docker build。

**作用**：为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖。

**注意**：如果 Dockerfile 中如果存在多个 CMD 指令，仅最后一个生效。

格式：

CMD <shell 命令> CMD ["<可执行文件或命令>","<param1>","<param2>",...] CMD ["<param1>","<param2>",...]  # 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数

推荐使用第二种格式，执行过程比较明确。第一种格式实际上在运行的过程中也会自动转换成第二种格式运行，并且默认可执行文件是 sh。

**ENTRYPOINT**

类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。

但是, 如果运行 docker run 时使用了 --entrypoint 选项，此选项的参数可当作要运行的程序覆盖 ENTRYPOINT 指令指定的程序。

**优点**：在执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数。

**注意**：如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。

格式：

ENTRYPOINT ["<executeable>","<param1>","<param2>",...]

可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参，以下示例会提到。

示例：

假设已通过 Dockerfile 构建了 nginx:test 镜像：

FROM nginx

ENTRYPOINT ["nginx",  "-c"]  # 定参 CMD ["/etc/nginx/nginx.conf"]  # 变参 

1、不传参运行

$ docker run  nginx:test

容器内会默认运行以下命令，启动主进程。

nginx -c /etc/nginx/nginx.conf

2、传参运行

$ docker run  nginx:test -c /etc/nginx/new.conf

容器内会默认运行以下命令，启动主进程(/etc/nginx/new.conf:假设容器内已有此文件)

nginx -c /etc/nginx/new.conf

**ENV**

设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量。

格式：

ENV <key>  <value> ENV <key1>=<value1>  <key2>=<value2>...

以下示例设置 NODE_VERSION = 7.2.0 ， 在后续的指令中可以通过 $NODE_VERSION 引用：

ENV NODE_VERSION 7.2.0 RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \ && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"

**ARG**

构建参数，与 ENV 作用一至。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。

构建命令 docker build 中可以用 --build-arg <参数名>=<值> 来覆盖。

格式：

ARG <参数名>[=<默认值>]

**VOLUME**

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

作用：

*   避免重要的数据，因容器重启而丢失，这是非常致命的。
*   避免容器不断变大。

格式：

VOLUME ["<路径1>",  "<路径2>"...] VOLUME <路径>

在启动容器 docker run 的时候，我们可以通过 -v 参数修改挂载点。

**EXPOSE**

仅仅只是声明端口。

作用：

*   帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。
*   在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

格式：

EXPOSE <端口1>  [<端口2>...]

**WORKDIR**

指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）。

docker build 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。

格式：

WORKDIR <工作目录路径>

**USER**

用于指定执行后续命令的用户和用户组，这边只是切换后续命令执行的用户（用户和用户组必须提前已经存在）。

格式：

`USER <用户名>[:<用户组>]`

**HEALTHCHECK**

用于指定某个程序或者指令来监控 docker 容器服务的运行状态。

格式：

HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令 HEALTHCHECK NONE：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令 HEALTHCHECK [选项] CMD <命令>  :  这边 CMD 后面跟随的命令使用，可以参考 CMD 的用法。

**ONBUILD**

* 用于延迟构建命令的执行。简单的说，就是 Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行（假设镜像为 test-build）。当有新的 Dockerfile 使用了之前构建的镜像 FROM test-build ，这是执行新镜像的 Dockerfile 构建时候，会执行 test-build 的 Dockerfile 里的 ONBUILD 指定的命令。

* 格式：`ONBUILD <其它指令>`

### Docker 数据共享

>构思怎么写啊啊啊啊啊啊啊啊

### Docker 网络模式

>构思怎么写啊啊啊啊啊啊啊啊啊，感觉日记不应该记录这么多的内容，而是更少一点，精细化。配上图文。降低理解难度那就更好了。

# DAY 3

Docker 的三架马车 Docker Compose、Docker Machine 、Docker Swarm。所谓的三架马车指的是 Docker，官方提供的一些 Docker 管理工具，本文主要学习相关工具的使用方法。

## Docker Compose

Docker Compose 是 Docker 官⽅编排（Orchestration）项⽬之⼀，负责快速的部署分布式应⽤。其代
码⽬前在[GitHub](https://github.com/docker/compose)上开源。Compose 定位是 「定义和运⾏多个 Docker 容
器的应⽤（Defining and running multi-container Docker applications）」，其前身是开源项⽬ Fig 。

之前我们学过编写单一的`Dockerfile`模板文件，可以很方便的定义一个单独的应用容器。然而在日常工作中经常会碰到，需要多个软件相互配合来完成某个人物的情况。这时候就需要 Compose 来满足这个需求了。它让用户通过`docker compose.yml`模板⽂件（YAML 格式）来定义⼀组相关联的应⽤容器为⼀个项⽬（project）。

Compose 中有两个重要的概念：

* 服务 (**service**)：⼀个应⽤的容器，实际上可以包括若⼲运⾏相同镜像的容器实例。

* 项⽬ (**project**)：由⼀组关联的应⽤容器组成的⼀个完整业务单元，在 docker-compose.yml ⽂件中定义。

### 安装与卸载

Compose ⽀持主流操作系统。Compose 可以通过pip、⼆进制⽂件、 Docker 容器进行安装。

### 使⽤

编写 Dockerfile ⽂件，内容为:

```shell
FROM python:3.6-alpine
ADD . /code
WORKDIR /code
RUN pip install redis flask
CMD ["python", "app.py"]
```

然后是编写 docker-compose.yml ⽂件，这个是 Compose 使⽤的主模板⽂件。这里设置了两个服务，分别是`Web`和`redis`，在编译程序时如果要实现容器内软件互联 host 处填写的是服务对应的名称。

```shell
version: '3'
services:
  web:
    build: .
    ports:- "5000:5000"
    volumes:- 
      .:/code
  redis:
    image: "redis:alpine"

```

运⾏ compose 项⽬:

`docker-compose up`

此时访问本地 5000 端⼝，每次刷新⻚⾯，计数就会加 1。

### Compose 命令

对于 Compose 来说，⼤部分命令的对象既可以是项⽬本身，也可以指定为项⽬中的服务或者容器。

如果没有特别的说明，命令对象将是项⽬，这意味着项⽬中所有的服务都会受到命令影响。

执⾏docker-compose [COMMAND] --help或者docker-compose help [COMMAND]可以查看具体某个命令的使⽤格式。

docker-compose 命令的基本的使⽤格式是:

`docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]`

命令选项：

* -f, --file FILE 指定使⽤的 Compose 模板⽂件，默认为 docker-compose.yml，可以多次指定。

* -p, --project-name NAME 指定项⽬名称，默认将使⽤所在⽬录名称作为项⽬名。

* --x-networking 使⽤ Docker 的可拔插⽹络后端特性

* --x-network-driver DRIVER 指定⽹络后端的驱动，默认为 bridge

* --verbose 输出更多调试信息。

* -v, --version 打印版本并退出。

build 格式为docker-compose build [options] [SERVICE...]。 构建（重新构建）项⽬中的服务容器。服务容器⼀旦构建后，将会带上⼀个标记名，例如对于 web 项⽬中的⼀个 db 容器，可能是 web_db。

可以随时在项⽬⽬录下运⾏ docker-compose build 来重新构建服务。选项包括：

* --force-rm 删除构建过程中的临时容器。

* --no-cache 构建镜像过程中不使⽤ cache（这将加⻓构建过程）。

* --pull 始终尝试通过 pull 来获取更新版本的镜像。

config: 验证 Compose ⽂件格式是否正确，若正确则显示配置，若格式错误显示错误原因。

down：此命令将会停⽌ up 命令所启动的容器，并移除⽹络

exec：进⼊指定的容器。

help：获得⼀个命令的帮助。

images：列出 Compose ⽂件中包含的镜像。

kill：格式为docker-compose kill [options] [SERVICE...]。通过发送 SIGKILL 信号来强制停⽌服务容

器。⽀持通过 -s 参数来指定发送的信号，例如通过如下指令发送 SIGINT 信号。

`docker-compose kill -s SIGINT`

logs：格式为docker-compose logs [options] [SERVICE...]，查看服务容器的输出。默认情况下，docker-compose 将对不同的服务输出使⽤不同的颜⾊来区分。可以通过 --no-color 来关闭颜⾊。该命令在调试问题的时候⼗分有⽤。

pause：格式为docker-compose pause [SERVICE...]，暂停⼀个服务容器。

port：格式为docker-compose port [options] SERVICE PRIVATE_PORT，打印某个容器端⼝所映射的公共端⼝。选项：

--protocol=proto 指定端⼝协议，tcp（默认值）或者 udp。

--index=index 如果同⼀服务存在多个容器，指定命令对象容器的序号（默认为 1）。

ps：格式为docker-compose ps [options] [SERVICE...]，列出项⽬中⽬前的所有容器。选项：

-q 只打印容器的 ID 信息。

pull：格式为 docker-compose pull [options] [SERVICE...] ，拉取服务依赖的镜像。选项：

--ignore-pull-failures 忽略拉取镜像过程中的错误。

push：推送服务依赖的镜像到 Docker 镜像仓库。

restart：格式为docker-compose restart [options] [SERVICE...]，重启项⽬中的服务。选项：

-t, --timeout TIMEOUT 指定重启前停⽌容器的超时（默认为 10 秒）。

rm：格式为docker-compose rm [options] [SERVICE...],删除所有（停⽌状态的）服务容器。推荐先执

⾏ docker-compose stop 命令来停⽌容器。选项：

-f, --force 强制直接删除，包括⾮停⽌状态的容器。⼀般尽量不要使⽤该选项。

-v 删除容器所挂载的数据卷。

run：格式为docker-compose run [options] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND]

[ARGS...]，在指定服务上执⾏⼀个命令。例如：

`docker-compose run ubuntu ping docker.com`

将会启动⼀个 ubuntu 服务容器，并执⾏ ping docker.com 命令。默认情况下，如果存在关联，则所有

关联的服务将会⾃动被启动，除⾮这些服务已经在运⾏中。

## Docker Machine



## Docker Swarm