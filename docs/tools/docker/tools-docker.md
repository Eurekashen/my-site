---
title: Docker
date: 2023-10-02
author: Shuaike Shen
tags: ['Tools','Docker']
categories: 
- Tools
- Docker
---

# Docker

模拟完全相同的本地开发环境

和虚拟机的思想非常相似，但是不会像虚拟机一样去模拟底层的硬件，更加的轻量。只是为每一个程序提供隔离的运行环境，不会相互影响。这些环境就是“容器”

- Dockerfile

  一个自动化脚本，用来创建下面说的镜像。

- Image

  镜像，相当于虚拟机的快照snapshot。里面包含了我们要部署的应用程序和关联的所有依赖。

- Container

  通过镜像我们可以创建多个不同的Container，这些Container之间相互独立不影响，可以在容器中配置不同的工具软件。


## 实例：

- Dockerfile

  From命令指定基础镜像

  ```docker
  From python:3.8-slim-buster
  ```

  WORKDIR指定之后的工作操作路径

  ```docker
  WORKDIR /app
  ```

  COPY将程序拷贝到镜像中

  ```docker
  COPY <source> <des>
  ```

  RUN命令允许在创建镜像的时候执行所有的shell命令

  ```docker
  RUN pip install -r requirment.txt
  ```

  CMD是在创建容器的时候操作的，而RUN是在创建镜像的时候执行的。

- 创建镜像

  ```bash
  docker build -t image_name /path/to/dockerfile
  ```

- 启动容器

  ```bash
  docker run -p 80:5000 -d iamge_name
  ```

  -p参数是用来做端口映射用的，后面是容器的端口，前面是本地主机上的端口。-d是在后台运行的意思，这样输出就不会在终端输出。

- 其他指令（都可以在docker desktop中操作）

- 删除一个容器的时候里面的修改和数据会一同销毁，如果想要保留。可以使用数据卷volume功能（相当于本地主机和不同容器之间的共享文件夹）

  ```bash
  docker volume create
  ```
  
  创建一个数据卷,在启动容器docker run的时候使用-v参数挂载

  ```bash
  docker run -dp 80:5000 -v my-finance-data:/etc/finance my-finance
  ```
  
- 多个容器的共同协作：dokcer-compose.yml文件

  - 使用services定义多个容器，可以指定环境变量和数据卷
  - 使用docker compose up运行所有的容器
  - 使用docker compose down来停止并且删除所有的容器，但是数据卷需要手动删除，除非加上-volumes参数

- Docker和Kubernetes的区别和联系

  - 不是同一个层面的东西，Kubernetes取代的是docker的容器引擎的部分
  - Docker的所有容器都是在一台计算机上运行的，当工程体量大到一定程度的时候一台计算机就显得力不从心。
  - Kubernetes做的就是把一个程序分发到一个集群上去运行，并且进行自动化的管理和维护升级。