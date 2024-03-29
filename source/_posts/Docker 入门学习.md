---
title: Docker 入门学习
date: 2021-09-22 00:00:00
updated: 2021-09-22 00:00:00
tags:
  - Docker
---

什么是 Docker ？更小更快速的虚拟机？

<!-- more -->

维基百科上这么解释：

> Docker 容器与虚拟机类似，但二者在原理上不同。容器是将操作系统层虚拟化，虚拟机则是虚拟化硬件，因此容器更具有便携性、高效地利用服务器。容器更多的用于表示 软件的一个标准化单元。由于容器的标准化，因此它可以无视基础设施（Infrastructure）的差异，部署到任何一个地方。

# Docker 三个基本概念

1. 镜像（Images）是一个只读模板，用于指示创建容器。 镜像分层(layers)构建的，而定义这些层次的文件叫 Dockerfile，可以本地创建或者拉取别人创建好的镜像。
2. 容器（Containers）是镜像的可运行的实例。容器可通过 API 或 CLI（命令行）进行操控，容器 = 镜像 + 读写层。
3. 仓库（Repository）是集中存放镜像文件的场所。

# 运行 Hello World

```
docker pull library/hello-world
```

`docker pull images` 是抓取 image 文件，library/hello-world 是 image 文件在仓库里面的位置，其中 library 是 image 文件所在的组，hello-world 是 image 文件的名字。

抓取成功后通过 `docker images` 就可以查看本机的拉取的 image 文件了。

```
docker run hello-world
```

运行后 hello world 就会停止运行，容器自动终止。有些容器不会自动终止，因为提供的是服务，比如 MySQL 镜像等。

# Docker 常用命令

```docker
docker pull image_name  # 拉取Docker镜像
docker images # 查看主机下存在多少镜像
docker ps # 查看正在运行的容器
docker start container_id #启动
docker restart container_id #重启
docker stop container_id #停止
docker exec -it container_id bash #进入容器中
docker rm container_name/container_id #删除容器
docker rmi image_name #删除镜像
```

# 使用 Dockerfile 创建镜像命令

```docker
FROM # 用于指定基础的 images ，一般格式为 FROM <image> or FORM <image>:<tag>
MAINTAINER #用于指定镜像创建者和联系方式，一般格式为 MAINTAINER <name>
COPY #用于复制本地主机的 <src> (为 Dockerfile 所在目录的相对路径)到容器中的 <dest> ,当使用本地目录为源目录时，推荐使用 COPY
WORKDIR #用于配合 RUN，CMD，ENTRYPOINT 命令设置当前工作路径
RUN #用于容器内部执行命令。每个 RUN 命令相当于在原有的镜像基础上添加了一个改动层，原有的镜像不会有变化
EXPOSE #用来指定对外开放的端口
CMD #指定启动容器时执行的命令，每个 Dockerfile 只能有一条 CMD 命令。如果指定了多条命令，只有最后一条会被执行，可被替代。
```

例如在当前目录新建 Dockerfile 并写入

```docker
FROM nginx
RUN echo '<h1>hello.xxx</h1>' > /usr/share/nginx/html/index.html
```

然后在当前目录运行 `docker build -t image_name .`

- -t 是为新镜像设置仓库和名称
- image_name 为镜像名称
- . 为 Dockerfile 的相对路径

运行成功 运行 `docker images` 就可以看见新建的镜像了

然后运行:

```docker
docker run --name [container_name] -d -d -p 7301:80 [image_name] #这条命令会用 Nginx 镜像启动一个容器，命名为 container_name ，并且映射了 7301 端口。
```

> 引用

- [Docker](https://zh.wikipedia.org/wiki/Docker)
- [这可能是最为详细的 Docker 入门总结](https://juejin.cn/post/6844903713115488269#heading-0)
