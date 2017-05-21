# 使用Docker镜像

## 什么是Docker镜像

Docker镜像是由文件系统叠加而成，最底层是一个引导文件系统，即bootfs，第二层是root文件系统rootfs，它在bootfs上面，rootfs可以是一种操作系统，比如：ubuntu文件系统。

Docker会利用联合加载技术在rootfs上加载更多的只读文件系统。

> 联合加载技术（union mount）指的是一次同时加载多个文件系统，联合加载会将各层文件系统叠加到一起，这样最终的文件系统会包含所有底层的文件和目录。

Docker将这样的文件系统称之为镜像，一个镜像可以放到另一个镜像顶部，位于底部的镜像成为父镜像。最后从一个镜像启动容器的时候，Docker会在该镜像的最顶层加载一个读写文件系统，而我们进行的读写操作都是在这个读写层中执行的。

> 这里有些像反双亲委派的类加载器结构。

<center>
<img src="http://www.plantuml.com/plantuml/svg/ROvT2e0m38FVTmelu1MyIfcpig6sKZk6jnSs6VFdAUHB876yxKn8sWdQ2P84HLZjYM9SZRA75DI3tQtps6J2hFC1SOoaiU5ZvWrOK0XNOzuxNJznedxuMVcGsdskFbpjt0-AL5fkN000" />
</center>

如果修改一个文件，文件首先重读写层下面的只读层复制到该读写层。注意该文件的只读版本依旧存在，但是已经被读写层中的文件副本所隐藏，这种机制称之为写时复制（copy on write）。

## 列出镜像

通过使用`sudo docker images`可以查看当前机器上已经获取的镜像列表。

```sh
$ sudo docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
weipeng2k/daemon_echo   latest              544097392791        2 weeks ago         508 MB
weipeng2k/ubuntu        16.04               412f213bef98        2 weeks ago         117 MB
ubuntu                  16.04               f7b3f317ec73        3 weeks ago         117 MB
ubuntu                  latest              f7b3f317ec73        3 weeks ago         117 MB
hello-world             latest              48b5124b2768        4 months ago        1.84 kB
```

可以看到该命令返回了当前机器上的镜像列表，第一列是仓库名，紧接着的是标签名以及镜像ID。

本地的镜像都保存在宿主机的`/var/lib/docker/image/aufs`目录下，可以看到有对应的文件以json格式存储了镜像以及其相关信息。

```json
{"Repositories":{"hello-world":{"hello-world:latest":"sha256:48b5124b2768d2b917edcb640435044a97967015485e812545546cbed5cf0233","hello-world@sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12
a1ac8d7":"sha256:48b5124b2768d2b917edcb640435044a97967015485e812545546cbed5cf0233"},"ubuntu":{"ubuntu:16.04":"sha256:f7b3f317ec734a73deca91b34c2b1e3dd7454650da9c8ef3047d29a873865178","ubuntu:latest":"sha2
56:f7b3f317ec734a73deca91b34c2b1e3dd7454650da9c8ef3047d29a873865178","ubuntu@sha256:f3a61450ae43896c4332bda5e78b453f4a93179045f20c8181043b26b5e79028":"sha256:f7b3f317ec734a73deca91b34c2b1e3dd7454650da9c8e
f3047d29a873865178"},"weipeng2k/daemon_echo":{"weipeng2k/daemon_echo:latest":"sha256:544097392791b51128724cd0dc317ee50c144981e1119248db4164c338e19f33"},"weipeng2k/ubuntu":{"weipeng2k/ubuntu:16.04":"sha256
:412f213bef98c936a72e702a8b9802cda72634347d09b0652e1a8a9886998ce1"}}}
```

> 在早期版本是`/var/lib/docker`。

当执行`sudo docker pull ${image_name}`时，可以将镜像下载到本地，而在下载之前，镜像是保存在仓库中的。可以将仓库想象为类似maven或者git的仓库，它包括镜像、层以及镜像的元数据都存储在其中。

DockerHub中有两种类型仓库：用户仓库和顶层仓库，比如：`ubuntu`就是属于顶层仓库的，它只有仓库名部分，由专业相关团队维护。而用户仓库是用户自己创建的，就是由`username/repository`构成，比如：`weipeng2k/ubuntu`，这个就是用户`weipeng2k`下的`ubuntu`仓库。

在了解了仓库概念之后，可以想象，在运行`sudo docker run ${image_name}`时，就会查看当前本地是否存在`${image_name}`，如果没有，将会从Registry下载。

## 查找镜像

很多基础设施都已经进行了镜像化，比如：`memcached`。我们怎么能够发现已经有的镜像呢？或者识别出哪些镜像是官方顶层仓库维护的呢？

可以访问[hub.docker.com](https://hub.docker.com)，进行搜索，或者运行`sudo docker search ${key_words}`搜索镜像。

```sh
$ sudo docker search memcached
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
memcached                       Free & open source, high-performance, dist...   749       [OK]       
sylvainlasnier/memcached        Memcached docker images based on Ubuntu 14...   28                   [OK]
bitnami/memcached               Bitnami Memcached Docker Image                  6                    [OK]
frodenas/memcached              A Docker Image for Memcached                    5                    [OK]
eeacms/memcached                                                                4                    [OK]
webhippie/memcached             Docker images for memcached                     2                    [OK]
phpdockerio/memcached           PHPDocker.io memcached image                    1                    [OK]
getupcloud/memcached            Memcached is an in-memory key-value store....   1                    [OK]
tenstartups/memcached           A minimal memcached docker image based on ...   1                    [OK]
```

## 构建镜像

一般来说，我们不是真正的创建一个镜像，都是基于一个已有的基础镜像进行构建新的镜像。

使用`sudo docker push ${image_name}`可以将镜像推送到DockerHub

> 前提是使用`sudo docker login`进行登录DockerHub，可以现在DockerHub注册用户

```sh
$ sudo docker push weipeng2k/ubuntu
[sudo] password for weipeng2k:
The push refers to a repository [docker.io/weipeng2k/ubuntu]
3a111ef82522: Retrying in 1 second
73e5d2de6e3e: Pushing [==================================================>] 3.072 kB
08f405d988e4: Pushed
511ddc11cf68: Pushed
a1a54d352248: Mounted from library/ubuntu
9d3227c1793b: Mounted from library/ubuntu
```

上面就是将本地构建的使用中国软件源的ubuntu:16.04推送到DockerHub。

通过`sudo docker commit ${container_id} ${image_name}`，完成镜像的构建，但是这种方式不推荐，Docker推荐使用Dockerfile来完成构建镜像。

新建一个目录static_web，在目录中，新建一个文件Dockerfile，这个目录就是当前的构建目录。

```sh
FROM weipeng2k/ubuntu:16.04
MAINTAINER weipeng2k "weipeng2k@126.com"
RUN apt-get update
RUN apt-get install -y nginx
RUN echo 'Hi, I am in Docker!' > /var/www/html/index.html

EXPOSE 80
```

上面每条指令都会新建一个镜像层并对镜像进行提交，Docker对这些执行的运作流程基本如下：

<center>
<img src="http://www.plantuml.com/plantuml/svg/SybFpaujKj2rK_3CJKnFLR1IIClCI-62S82Yplbv9OcPUK0bLWf5fNbS23v8IrvnIL5fSabcVXvGEhKYDRcq91LD2TJKvFpStCmI0000" />
</center>

Docker针对每个命令都会执行上述流程，Docker首先会从镜像运行一个容器，然后执行一个命令，再进行提交为一个镜像层。

每个Dockerfile的第一条指令都是`FROM`，`FROM`指定一个存在的镜像，后续指令都基于该镜像进行，这个镜像成为基础镜像。

`RUN`指令会在当前镜像运行的容器中，运行指定的命令，默认情况下RUN指令会在shell中使用 `/bin/sh -c` 来执行。

`EXPOSE`指令会设定容器内的程序将会使用容器的指定端口。

构建镜像很简单，在构建目录，执行`sudo docker build -t="weipeng2k/static_web" .`，该命令将会在当前目录下找寻Dockerfile文件，并开始构建镜像。
