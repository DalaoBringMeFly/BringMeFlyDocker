# BMF 项目容器化

## Docker 简介

### 什么是 Docker

docker 是一种容器技术，作用是用来快速部署服务

### 相比于虚拟机的优势

容器提供了与虚拟机相似的资源分配和隔离优势。然而，相同之处仅此而已。

一个虚拟机需要它自己的客户操作系统而容器共享主机操作系统的内核。这意味着容器更加轻量而且需要更少的资源。从本质上讲，一个虚拟机是操作系统中的一个操作系统。而另一方面的容器则更像是操作系统中的其它应用程序。基本上，容器需要的资源（内存、磁盘空间等等）比虚拟机少很多，并且具有比虚拟机快很多的启动时间。

### DockerFile 用处

实际上，如果只是为了单次部署，可以通过启动并进入docker容器，然后搭建相应的服务，最后保存到自定义镜像里即可。但是如此生成的镜像不可重现，因为单从镜像文件启动的容器信息很难反推当初自己做过什么环境搭建和服务部署了。Dcokerfile的好处是让这些过程变得透明，因为其中描述了镜像生成的全过程，以及容器启动的入口等。

## 利用 Docker 部署 Go Web 程序

### Dockerfile 详细

```
FROM centos:7

MAINTAINER dalaobringmefly <hongzc@mail2.sysu.edu.cn>

RUN yum install -y gcc

RUN rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

RUN yum install golang -y

ENV GOROOT /usr/lib/golang
ENV PATH=$PATH:/usr/lib/golang/bin

RUN mkdir -p /root/gopath
RUN mkdir -p /root/gopath/src
RUN mkdir -p /root/gopath/pkg
RUN mkdir -p /root/gopath/bin
ENV GOPATH /root/gopath

RUN go get github.com/boltdb/bolt
RUN go get github.com/graphql-go/graphql
RUN go get -u github.com/go-sql-driver/mysql

RUN mkdir -p /root/gopath/src/BringMeFlyServer/
COPY . /root/gopath/src/BringMeFlyServer/

WORKDIR /root/gopath/src/BringMeFlyServer
RUN go build -o server.bin main.go

CMD /root/gopath/src/BringMeFlyServer/server.bin
```

易错点：

 - `COPY` 时，如果需要保留原文件结构，不能加 `*`

### 生成镜像

在 DockerFile 所在目录执行以下命令

```
docker build -t server:v0.1 .
```

### 启动容器

启动容器

```
docker run -d -p 80:80 server:v0.1 
```

-p 将容器的80端口映射到本机的80端口

### docker-compose.yml

```
docker-compose up
```

```
docker-compose ps
```


### 引用

按有用程度进行排名：

1. [基于Docker和Golang搭建Web服务器](https://blog.csdn.net/m0_37554486/article/details/78440367) 

2. [将Golang应用部署到Docker](https://studygolang.com/articles/12670?fr=sidebar)


3. [DockerFile 详细命令解析](https://www.cnblogs.com/boshen-hzb/p/6400272.html)

4. [Docker COPY 保留文件夹结构](https://cloud.tencent.com/developer/ask/98544)


5. [如何使用DOCKER部署一个GO WEB应用程序](https://www.cnblogs.com/zhangboyu/p/7452751.html)

