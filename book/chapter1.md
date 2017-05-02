# 简介

## 开始

虽然隔离技术不彻底，但是被广泛的运行在超大规模的多租户服务部署，轻量级沙盒以及对安全环境要求并不高的隔离环境中。

> 大规模服务部署

> 沙盒环境

> 有安全保证的环境

> 这些环境在内部网络的保护下，安全不是重点考虑的前提下，资源利用将成为主要矛盾

容器技术是精益技术，因为容器需要的开销有限，由于不需要传统虚拟化所依赖的模拟层（emulation layer）和管理层（hypervisor layer），而是使用了操作系统的系统调用，从而降低了容器的开销，进而增加了部署密度。

## Docker简介

Docker是一个能够把开发的应用程序自动部署到容器的开源引擎。

## Docker组件

Docker的组件分为：

* Docker客户端和服务端
* Docker镜像
* Registry
* Docker容器

基本架构如下：

<center>
<img src="http://www.plantuml.com/plantuml/svg/POwx3O0m34JxJE6AZWKeQA2XPOCeM10Hsr481TlpaO10IZ-zkxCHyiB84LenCuR6sUDGveTRZIISu1v7kyImPMP7UiICDi9HMyQWLBe3PLb_5g02GuFweDTvMWwDD9z3zn-9TNtm6bdN4wd0hyuLVbqci6Te1m00" />
</center>

```sh
@startuml
component DockerClient
component DockerDaemon
component Registry
component DockerImage
component DockerContainer

DockerClient --> DockerDaemon : cmd
DockerDaemon --> DockerContainer : manage
Registry --> DockerImage : manage
DockerDaemon --> Registry : pull&push image
@enduml
```

镜像是基于联合文件系统的一种层式结构，由一系列指令一步一步构建出来。可以把镜像理解为容器的“源代码”。

Docker使用Registry来保存用户构建的镜像。

镜像是Docker生命周期中构建或者打包的阶段，而容器则是启动或者执行阶段。

总结起来，Docker容器是：

* 一个镜像格式
> AUFS，一种交付方式

* 一系列标准的操作
> 定义了操作，面向运维

* 一个执行环境
> lxc，运行环境

和集装箱一样，Docker在执行上述操作时，并不关心容器中塞了什么，Docker不关心你要把容器运到何方：可以再自己的笔记本上完成构建容器，上传到Registry，然后下载到一个物理或者虚拟服务器上测试，然后再把容器部署到amazon的ec2上去。

> 想标准集装箱一样，Docker容器方便替换，可以叠加（多层），易于分发，并且通用

## Docker的用途

Docker可以被用来：

* 加速本地开发和构建流程
* 创建隔离的环境来进行测试
* 本机可以使用其构建一个复杂的程序或者架构来进行测试
* 为开发、测试提供了一个轻量的独立沙盒环境
* 提供软件及服务的程序，比如memcached

## Docker的技术特性

Docker的技术特性：

* 文件系统隔离：每个容器都有自己的root文件系统
* 进程隔离：每个容器运行在自己的进程环境中
* 网络隔离：容器间的虚拟网络接口和IP地址都是分开的
* 资源隔离和分组：使用cgroups时cpu和内存等资源独立分配给容器
* 写时复制：文件系统写时复制创建，文件系统分层

> 资源的隔离和规划
>
> 交付形式的统一，能方便做到开箱即用
