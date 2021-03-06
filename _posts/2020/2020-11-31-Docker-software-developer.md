---
layout: post
title: "Docker - 软件开发"
subtitle: 'Docker Guide'
author: "叉叉敌"
header-style: text
tags:
  - docker
---
## 什么是docker
Docker 是一个帮助在隔离环境中运行应用程序的工具。
>https://www.docker.com/


![](https://gitee.com/chasays/mdPic/raw/master/uPic/WYd5Ft.png)
 
总是需要确保应用程序能够在`独立的环境中运行`，`以便应用程序能够按预期的方式在不同的机器/服务器上运行`。这对于能够在本地机器上开发应用程序并让它们按预期的方式在部署的环境中运行非常重要。对于开发人员来说，能够在任何计算机上按预期运行应用程序以快速启动和运行并与其他开发人员合作也很重要，不管他们如何配置他们的计算机。

比如：在不同机器上运行应用程序之所以如此困难，是因为必须安装应用程序依赖项的所有正确版本，才能按预期运行。例如，如果试图运行一个由 Node.js 构建的 API，并在一台使用 Node.js 12.8的机器上进行开发和测试，那么在一台安装了 Node.js 10.18版本的机器上可能不一定能运行同样的 API。应用程序中使用的其他依赖项也是如此，比如 python、 ruby、 php、 typescript、 nginx、 apache、 mysql、 postgres 等等。Docker 使得构建容器化应用程序成为可能，这些应用程序拥有所有依赖项的正确版本，并且可以在不同的机器上按预期的方式运行。

Docker 使用4种类型的“对象”来创建这些孤立的环境: 镜像、容器、卷和网络。用户最常直接使用的`两个对象是 Docker 镜像和 Docker 容器`。

![](https://gitee.com/chasays/mdPic/raw/master/uPic/JNWD6B.jpg)

Docker `镜像包含运行应用程序所需的代码`，Docker `容器是运行来自 Docker 镜像的代码的独立环境`。Docker 镜像可以被认为是 Docker 容器使用的蓝图和代码。可以将 Docker 镜像保存到镜像注册中心，供其他人下载镜像。最流行的 Docker 镜像注册表是 Docker Hub。您可以将镜像注册中心看作对于 Docker 镜像的 NPM 等价物。


Docker 卷用于保存运行 Docker 容器所生成的数据。通过将数据保存到 Docker 卷中，即使已经删除、重新创建或重新启动 Docker 容器，也允许应用程序拥有相同的数据。如果两个不同的容器可以访问相同的数据，那么 Docker 卷也是有用的，因为两个容器都可以指向相同的卷。


Docker 网络用于确保容器彼此隔离，也用于允许容器彼此通信。


---
## 什么是虚拟机

虚拟机(vm)经常出现在与 Docker 相同的对话中，因为它们都用于创建隔离的环境。

使用 Docker，应用程序在称为容器的独立环境中运行，这些容器中的每一个都共享同一个机器上的操作系统内核。另一方面，在虚拟机上运行的应用程序运行在自己的操作系统上，不共享底层内核。虚拟机在管理程序的帮助下运行，`管理程序负责管理要运行的操作系统`。

![](https://gitee.com/chasays/mdPic/raw/master/uPic/3gbcuO.jpg)

第一种：虚拟机监控程序(Hypervisor) : 虚拟机监控程序位于物理硬件上，`每个操作系统位于虚拟机监控程序上`。例如 VMware ESXi，Oracle VM Server，微软

第二种：虚拟机监控程序: `虚拟机监控程序位于主机操作系统之上`，例如 Oracle VirtualBox，Parallels，VMware Workstation

>内核基本上是被使用的, 要运行 linuxd ocker 容器，需要在机器上存在一个 Linux 内核。如果你安装了 Mac OS 或 Windows 的 Docker 桌面应用程序，Docker 可以帮你解决这个问题，这个应用程序附带了 Linux Kit: 一个运行在 Mac OS 或 Windows 操作系统上的轻量级 Linux 内核，由 Docker 引擎使用。轻量级的 Linux 内核位于一个管理程序上，该管理程序与 Windows 和 Mac OS 本身相连。


使用 Docker 相对于虚拟机的优势压倒性地支持。与虚拟机相比，`Docker 容器可以在几秒钟到几分钟内运行起来`，是轻量级的(MBs 相对于 GBs 的大小) ，可以轻松配置，`并且使用少量资源`。也许使用虚拟机而不是 Docker 的唯一原因是，由于担心 Docker 容器在主机操作系统上使用共享内核会产生安全漏洞，因此需要高级别的隔离。

---
## Docker 引擎

需要在任何希望执行任何与 Docker 相关的操作的机器上安装并运行 Docker 引擎。启动引擎意味着`启动一个长时间运行的后台进程`(与 dockerd 命令相对应的 Docker 守护进程)来创建一个可以通过 REST API 进行交互的服务器。是大多数人与 REST API 交互来管理 Docker 对象的方式，但是第三方应用程序也可以直接与 REST API 或者通过 Docker 引擎 sdk 交互。


![](https://gitee.com/chasays/mdPic/raw/master/uPic/BZogpi.jpg)


---

## Docker 安装

安装所有与 Docker 相关的依赖项的最简单方法是安装 Docker Desktop。Docker Desktop 提供了一些与 Docker 相关的工具，包括 Docker 引擎、 Docker CLI 和 Docker Compose CLI 。
>https://docs.docker.com/desktop/

macos : https://docs.docker.com/docker-for-mac/install/
windows: https://docs.docker.com/docker-for-windows/install/

安装对应的版本之后，确保 Docker Desktop 正在运行。如果 Docker Desktop 正在运行，则意味着 Docker 引擎正在运行，并且本指南中提到的 Docker CLI 命令将能够执行。


![](https://gitee.com/chasays/mdPic/raw/master/uPic/btjhhA.png)
在 Mac OS 菜单栏中显示 Docker Desktop 正在运行。

---
##  Dockerfile

Dockerfile 是一个文件，它`保存了如何构建镜像的说明`。该文件通常首先指定一个基本镜像，该基本镜像将用作要构建的 Docker 镜像的基础。例如，如果构建一个基于 python 的 API，那么可以使用一个由安装了 python 的 Linux 操作系统组成的基本镜像。在指定一个基本镜像之后，使用其他指令来指定如何构建 Docker 镜像的以下细节:

- 要在容器中设置的`环境变量`
- 镜像显示的`端口`
- 应该将哪些文件复制到镜像中
- 应该安装哪些依赖项
- 在`启动容器时要执行的命令` 例如： yarn start 。。。

举个例子

```docker
# 在Linux操作系统上安装了节点版本12.16.1的基本镜像。
# 这是一个alpinelinux镜像，它比非alpinelinux等效镜像小
FROM node:12.16.1-alpine3.11 

# 安装依赖
RUN apk add --no-cache make g++

# 指定公开端口
EXPOSE 3000

# 指定一个工作目录directory.
WORKDIR /usr/src/app

# 将所有本地源文件复制到Docker容器的工作目录中
COPY . .


# 用yarn安装依赖文件
RUN yarn install

# 启动docker时候运行的命令
CMD [ "yarn", "start" ]
```

`完整列表可以在 Dockerfile 参考文档中找到。`
>https://docs.docker.com/engine/reference/builder/

- FROM 定义一个基本镜像
- RUN  在一个新的镜像层执行命令
- CMD 运行容器时要执行的命令
- EXPOSE 公开端口
- ENV 环境变量
- COPY 将文件/目录复制到镜像中
- ADD  COPY的高阶版
- ENTERYPOINT 定义容器的可执行文件
- VOLUME 定义应将镜像中的哪个目录视为卷。该卷将被给予一个随机名称
- WORKDIR  后续指令定义工作目录Dockerfile
- ARG  定义可以通过docker build --build-arg



如果某些文件`不能被复制到 Docker 镜像中`，则。可以将 `.dockerignore `文件添加到与 Dockerfile 相同的级别，在那里可以指定不应该复制到 Docker 镜像中的文件。这意味着，如果使用 Dockerfile 中的 COPY 或 ADD 指令指定要添加到 Docker 镜像中的文件，则。将忽略 `.dockerignore` 文件，并且不添加到 Docker 镜像中。这可能是可取的，以防止包含敏感信息的文件(例如:。包含 API 键的 env 文件)或大型不必要的文件被添加到 Docker 镜像中。中指定要忽略的文件时，请确保知道正确的语法。多克忽略文件。

---

## 构建和标记 镜像

使用 `docker build ``命令创建 Docker 镜像`。在构建 Docker 镜像时，会给它们一个由 `--tag `选项指定的标记。标记镜像是给 Docker 镜像命名/版本的一种方法，可以知道从镜像仓库中拉出哪个镜像，以及在运行容器时应该使用哪个镜像。

```
docker build --tag chasays:1.0 .
```
![](https://gitee.com/chasays/mdPic/raw/master/uPic/LjD8S0.png)

- docker build 是构建一个镜像的命令
- --tag xx 是创建一个名称为 xxx的标签为1.0
- . 是当前目录

通过`列出所有可用的 Docker 镜像`，可以使用 Docker images 命令来验证镜像是否已经创建:`docker images`

![](https://gitee.com/chasays/mdPic/raw/master/uPic/3rbqHP.png)


除了能够使用 Docker build 命令的 --tag 选项对镜像进行标记之外，还可以使用 Docker 标记命令对 Docker 镜像进行标记。由于同一个镜像可以有多个标记，因此 docker 标记命令允许将一个新标记应用于已经通过 docker build-tag 命令标记的镜像。可以通过使用 docker build-tag 正确地标记镜像来避免使用 docker tag 命令，但是如果镜像应该有不同的标记，则可以使用 docker tag 命令来完成。
```
docker tag chasays:1.0 test/chasays:1.0
```


---
## Docker 镜像的注册， pull、push
Docker 镜像远程保存在 Docker 镜像注册中心，而 Docker 镜像注册中心的默认注册中心是 Docker Hub。Docker 镜像注册中心允许存储 Docker 镜像并将其下载到 Docker 主机上，以供 Docker 容器使用。

>docker pull nginx:1.18.0

![](https://gitee.com/chasays/mdPic/raw/master/uPic/EJTk8c.png)

上面的命令将从 Docker Hub `下载标记为1.18.0的 nginx Docker 镜像。`在运行 docker pull 命令之后，当使用 docker 镜像列出镜像时，镜像应该显示出来。


>如果在拉取时没有明确指定标记，则会拉取带有最新标记的镜像。 这个和安装pip第三方库有点类似
![](https://gitee.com/chasays/mdPic/raw/master/uPic/9xaLKU.png)

上面的示例使用 nginx 镜像，这是一个官方的 Docker Hub 镜像。官方的 Docker Hub 镜像是经过 Docker Hub 正式批准的镜像，这些镜像定期进行安全漏洞测试。
>https://docs.docker.com/docker-hub/official_images/


任何人都可以在 dockerhub 上创建自己的帐户和存储库，`并将镜像推送到存储库`。将镜像推送到 dockerhub 意味着将镜像保存在使用 dockerpush 命令指定的存储库中的 dockerhub 中。命令的形式如下:
```
docker push <hub-user>/<repo-name>:<tag>
```

> 需要登录docker hub中心， 除了docker hub，还有其他的hub，比如amazon，Google等。 `这个一般免费的是一个私有存储库，公共的是无限。数据收费，比如Amazon的是传出收费， 传入免费`


---
## 删除 docker

- rm          Remove one or more containers
- rmi         Remove one or more images

![](https://gitee.com/chasays/mdPic/raw/master/uPic/Golytn.png)

----
## 保存和导入镜像
在某些情况下，将 Docker 镜像保存到文件中，然后通过文件将镜像加载到 Docker 主机上可能是有用的。例如，构建 Docker 镜像的 CI 检查可能在另一个 CI 检查中使用相同的镜像。与将镜像推送到一个镜像存储库并将其拉下来用于另一个 CI 检查相反，将镜像保存到 CI 服务器上的一个持久存储中的文件中，然后从应该使用相同内置镜像的另一个 CI 检查中加载镜像可能是有益的。
用命令： `docker save`
```
docker save --output my-app.tar my-app:1.0

```

对于的`导入`

```
docker load --input my-app.tar

```
----

## 运行
运行命令`docker run xxx`

查看运行状态： `docker ps`

![](https://gitee.com/chasays/mdPic/raw/master/uPic/03pvEP.png)


--- 

## 公开端口
如果一个正在运行的容器公开了一个端口(例如端口3000) ，那么需要在 Docker 主机和 Docker 容器之间建立一个端口映射来访问 Docker 之外的应用程序(例如查看一个运行在 Docker 中的 web 应用程序在 Chrome 浏览器中的 http://localhost:3000)。
```
docker run -p 3000:3000 my-app:1.0
```

上面的命令将运行一个容器，这个容器在主机上的 http://localhost:3000可以访问。浏览器中访问 http://localhost:3000应该显示正在运行的应用程序(假设该应用程序是一个 web 应用程序)。

通常，Docker 主机和 Docker 容器之间的端口号最终是相同的值，但是也可以映射到主机上的不同端口，如果主机上的端口已经被占用

```
docker run -p 8000:3000 my-app:1.0
```

上面的命令将运行一个容器，该容器可以在 http://localhost:8000中访问。

---
## 停止，开始，移除容器

- docker stop  停止
- docker kill 终止
- docker start 开始
- docker rm 删除

![](https://gitee.com/chasays/mdPic/raw/master/uPic/8rEuJm.png)


---

## 执行命令

运行上面的命令将把命令行连接到 Docker 容器的 shell，在那里可以执行进一步的命令，如 cd、 ls、 cat、 echo 等。It 选项用于将命令行 shell 连接到 Docker 容器的 shell。
>https://devconnected.com/docker-exec-command-with-examples/

`docker exec -it my-app sh`
![](https://gitee.com/chasays/mdPic/raw/master/uPic/gzcW3V.png)


docker run 命令还可用于在 Docker 容器中运行命令。`Docker run 和 docker exec 的区别在于，docker exec 需要用在已经运行的容器上，而 docker run 命令将创建一个新容器来运行指定的命令。`

---
## 获取detail
`docker inspect` 命令的输出相当长，因为它包含了大量与 Docker 容器相关的信息，比如容器创建时的完整 ID、容器启动时运行的命令、网络设置等等。

![](https://gitee.com/chasays/mdPic/raw/master/uPic/y787JE.png)

要获取关于 Docker 容器资源使用情况的实时数据，如 CPU、内存和 I/O，请使用 `docker stats` 命令。

![6NEMDd](https://gitee.com/chasays/mdPic/raw/master/uPic/6NEMDd.png)



## readmore

https://docs.docker.com/docker-for-mac/install/
https://github.com/sickcodes/Docker-OSX
https://www.robertcooper.me/docker-guide

>[github博客](https://chasays.github.io/)
>微信公众号：chasays， 欢迎关注一起吹牛逼，也可以加微信号「xxd_0225」互吹。
