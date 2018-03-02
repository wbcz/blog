---
title: Docker
type: "categories"
categories: 其他
---

# docker是什么
可以简单的认为docker容器是一个虚拟机，封装就是把这个虚拟机打包，打包后能在任何系统跑，docker装上即用。也可以形象的比喻成一个集装箱，把所有货物都打包好放到箱子里，不需要再分类运输，集装箱不互相影响

# 好处
1. 统一应用环境
2. 方便迁移
3. 占用资源少 （如果你单独开一个虚拟机，那么虚拟机会占用空闲内存的，docker部署的话，这些内存就会利用起来）

# docker和虚拟机比较
1. 虚拟机是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程
2. 容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟
<img src='https://delimont-flow.alpha.elenet.me/static/upload/QQ20180226-120821.png'/>
<img src='https://delimont-flow.alpha.elenet.me/static/upload/QQ20180226-120843.png'/>
<img src='https://delimont-flow.alpha.elenet.me/static/upload/QQ20180226-121433.png'/>

# docker架构
<img src='https://delimont-flow.alpha.elenet.me/static/upload/QQ20180302-104135.png'/>
<img src='https://delimont-flow.alpha.elenet.me/static/upload/QQ20180302-104203.png'/>

# 基本概念
## 镜像
Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）
镜像镜像只是一个虚拟的概念，且是分层存储的，其实际体现并非由一个文件组成，而是由一组文件系统组成，或者说，由多层文件系统联合组成。
## 容器
1. 镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
2. 容器的本质是进程，但是和宿主执行的进程不一样，它有独立的命名空间，因此有自己的root文件系统，网络配置、进程空间
3. 容器存储层的生存周期和容器一样,容器消亡时，容器存储层也随之消亡,因此任何保存于容器存储层的信息都会随容器删除而丢失。

- 容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。
- 所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高
- 数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡
## 仓库
一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。

## 使用镜像
### 获取镜像
```
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
docker pull ubuntu:16.04
docker run 运行容器的命令

docker run -it --rm \
    ubuntu:16.04 \
    bash
-it：  -i:交互式操作  -t:终端  我们这里打算进入bash执行一些命令并查看返回结果，因此需要交互式终端
--rm：这个参数是说容器退出后随之将其删除
bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。
```

### 列出镜像
```
docker image ls
docker image ls -f dangling=true 虚悬镜像
docker image prune 删除虚悬镜像
docker image ls -a   中间层镜像
docker image ls ubuntu 列出部分镜像
docker image ls -f since=mongo:3.2
docker image ls -f before=mongo:3.2
```
### 删除本地镜像
```
docker image rm [选项] <镜像1> [<镜像2> ...]
docker image rm ID
docker image rm 镜像名
docker image rm 摘要
docker rmi 镜像名
```
# Docker指令
## Dockerfile定制镜像
```
FROM 指定基础镜像
RUN 执行命令
构建镜像 docker build -t nginx:v3 .
```
## COPY 复制文件
1. COPY <源路径>... <目标路径>
```
COPY package.json /usr/src/app/
```
2. 相对于工作目录的相对路径（工作目录可以用 WORKDIR
3. 各种元数据都会保留。比如读、写、执行权限、文件变更时间等
## ADD
Add 比COPY多了些特性，比如 <源路径> 可以是一个 URL
## CMD
1. shell： CMD <命令>
2. exec : CMD ["可执行文件", "参数1","参数2"]
```
CMD echo $HOME
CMD [ "sh", "-c", "echo $HOME" ]
```
## ENV
```
ENV NODE_VERSION 7.2.0
或者ENV NODE_VERSION=7.2.0
比如使用： RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION
```
# 操作容器
## 启动
```
$ docker container run \
  -d \
  -p 127.0.0.2:8080:80 \
  --rm \
  --name mynginx \
  nginx
```
## 重启
```
docker container start
```
## 终止
```
docker stop ID
```
## 进入容器
```
docker attach ID  /exit 会导致容器退出
docker exec -it ID  /exit 不会导致容器退出
```

# Docker Compose 
## 安装和卸载
linux安装
```
$ sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
卸载
```
$ sudo rm /usr/local/bin/docker-compose
```
## 使用
```
docker-compose up
docker-compose down
docker-compose --version
```

# 项目实践

  [1]: /img/bV4JoE
  [2]: /img/bV4JpU
  [3]: /img/bV4Jp8
  [4]: /img/bV4Jqi
  [5]: /img/bV4Jqk
