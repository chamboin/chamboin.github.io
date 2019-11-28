---
layout: post
title: Docker安装以及更换国内镜像源
categories: Linux
description: 安装Docker，优化镜像下载速度
keywords: Docker, Linux
---

## Docker是什么

**Docker**使用 Google 公司推出的[Go 语言](https://golang.org)进行开发实现，基于 Linux 内核的[cgroup](https://zh.wikipedia.org/wiki/Cgroups)，[namespace](https://en.wikipedia.org/wiki/Linux_namespaces)，以及 [AUFS](https://en.wikipedia.org/wiki/Aufs) 类的 [Union FS](https://en.wikipedia.org/wiki/Union_mount) 等技术，对进程进行封装隔离，属于 [操作系统层面的虚拟化技术](https://en.wikipedia.org/wiki/Operating-system-level_virtualization)。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。最初实现是基于 LXC，从 0.7 版本以后开始去除 LXC，转而使用自行开发的 [libcontainer](https://github.com/docker/libcontainer)，从 1.11 开始，则进一步演进为使用 [runC](https://github.com/opencontainers/runc) 和 [containerd](https://github.com/containerd/containerd)。

**Docker**在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等等，极大的简化了容器的创建和维护。使得 Docker 技术比虚拟机技术更为轻便、快捷。  
传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

![Docker架构](/images/posts/linux/docker-on-linux.png)
<p align="center">Docker架构<p>

## 为什么要用Docker

Docker的优势有很多，各应用都进行了打包，相互隔离，也不需要在主机上安装各种依赖配置各种环境，也不需要考虑各种复杂的权限问题，对于开发也是非常友好的。而且相对传统的虚拟机或是直接在宿主机安装，Docker启动速度非常快。还有一点就是Docker迁移方便，可以很方便的在各VPS间迁移，省去很多配置的问题。


## 安装Docker


Docker分为 CE 和 EE 两大版本。CE 即社区版（免费，支持周期7个月），EE 即企业版，强调安全，付费使用。

Docker CE 分为`stable` `test` 和 `nightly` 三个更新频道。每六个月发布一个 `stable` 版本。

官方网站上有各种环境下的[安装指南](https://docs.docker.com/install/)，这里主要介绍 Docker CE 在 Linux 上的安装。

推荐使用Debian系的Linux发行版（或是Ubuntu），曾经我也无脑 CentOS ，虽然没有什么大的坑，但是Debian用过都说好。

### 卸载旧版本

如果宿主机是初次安装应该没有自带Docker，不过还是在安装前执行一下以保万无一失。使用以下命令卸载：
```Bash
$ sudo apt remove docker \
            docker-engine \
            docker.io
```

### 使用APT安装

首先添加使用 HTTPS 传输所需的软件包以及 CA 证书
```Bash
$ sudo apt update

$ sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    lsb-release \
    software-properties-common
```
此外还需要添加软件源的 GPG 密钥以确保所软件包未被篡改。  
国内主机的话建议使用国内源，即注释中的内容，默认使用官方源。
```Bash
$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

# 国内源
# $ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -
```
然后需要向apt源即 `source.list` 中添加 Docker CE 的软件源：
```Bash
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) \
    stable"

# 国内源
# $ sudo add-apt-repository \
#   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian \
#   $(lsb_release -cs) \
#   stable"
```
>如需测试版或其他版本请将 stable 改为 test 或 nightly

更新apt缓存，安装 docker-ce
```Bash
$ sudo apt update

$ sudo apt install docker-ce
```

### 使用脚本安装

官方提供了一键安装脚本， Debian 系统可以使用脚本一件安装，并且可以指定 `--mirror` 参数使用国内镜像源安装：
```Bash
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun

# $ sudo sh get-docker.sh --mirror AzureChinaCloud
```
脚本执行结束会将 stable 版安装到系统中。

### CentOS 安装

同样的，首先卸载旧版本：
```Bash
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
安装依赖包：
```Bash
$ sudo yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2
```
添加 yum 源：
```Bash
$ sudo yum-config-manager \
     --add-repo \
     https://download.docker.com/linux/centos/docker-ce.repo

# 国内镜像源
# $ sudo yum-config-manager \
#    --add-repo \
#    https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
```
更新 yum 缓存，安装 Docker CE：
```Bash
$ sudo yum makecache fast
$ sudo yum install docker-ce
```
使用脚本安装步骤与 Debian 下相同

### 启动 Docker CE

```Bash
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

### 建立 Docker 用户组

默认情况下，`docker` 命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。因此，更好地做法是将需要使用 docker 的用户加入 docker 用户组。

建立 `docker` 组：
```bash
$ sudo groupadd docker
```
将当前用户加入 `docker` 组：
```Bash
$ sudo usermod -aG docker $USER
```

### 测试是否正确安装

```Bash
$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
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
若正常输出以上信息，则说明安装成功。

### 更换镜像源

国内因为网络原因，从[官方仓库](https://hub.docker.com)拉取镜像会比较困难，下载速度慢不说还容易断线，所以要配置国内的官方仓库镜像来加速镜像下载。国内很多云服务商都提供了加速服务，比如：
- [Azure 中国镜像 https://dockerhub.azk8s.cn](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)
- [阿里云加速器（需要帐号登录）](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
- [七牛云加速器 https://reg-mirror.qiniu.com](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)
>建议同时配置多个镜像，根据运行 Docker 的云平台选择对应的镜像加速服务，具体请参考官方文档。
这里以 Azure 中国镜像为例进行介绍：

在 `/etc/docker/daemon.json` 中写入以下内容（文件不存在请自行创建）
```JSON
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```
**注意**：文件要符合 JSON 规范，否则 Docker 将无法启动。

重启服务
```Bash
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

>注意：如果您之前查看旧教程，修改了 `docker.service` 文件内容，请去掉您添加的内容（`--registry-mirror=https://dockerhub.azk8s.cn`）。

至此，Docker 的安装已经完成了，关于使用方面，多看官方文档。

## Docker Compose

### 项目简介

Docker Compose 是 Docker 官方编排（Orchestration）项目之一，负责快速的部署分布式应用。接下来将介绍 Compose 项目情况以及安装和使用。

Compose 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。代码开源在<https://github.com/docker/compose>。

它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

Compose 中有两个重要的概念：

- 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。

- 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

Compose 项目由 Python 编写，实现上调用了 Docker 服务提供的 API 来对容器进行管理。因此，只要所操作的平台支持 Docker API，就可以在其上利用 Compose 来进行编排管理。

### Docker Compose 安装

Compose 可以通过 Python 的包管理工具 pip 进行安装，也可以直接下载编译好的二进制文件使用，甚至能够直接在 Docker 容器中运行。

#### 二进制包安装

从 官方 [GitHub Release](https://github.com/docker/compose/releases) 处直接下载编译好的二进制文件即可。

例如，在 Linux 64 位系统上直接下载对应的二进制包。
```Bash
$ sudo curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

#### 使用 PIP 安装

这种方式是将 Compose 作为一个 Python 应用进行安装，我比较喜欢这种方式，但是dalao推荐 x86 架构的使用二进制安装 ARM 架构再用这种方式。

执行：
```Bash
$ sudo pip install -U docker-compose
```
看到类似这样的输出就是安装成功：
```Bash
Successfully installed docker-compose cached-property requests texttable websocket-client docker-py dockerpty six enum34 backports.ssl-match-hostname ipaddress
```

##  参考来源

[Docker——从入门到实践](https://yeasy.gitbooks.io/docker_practice/)
