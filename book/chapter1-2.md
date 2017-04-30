## Docker组件

Docker的组件分为：

* Docker客户端和服务端
* Docker镜像
* Registry
* Docker容器

基本架构如下：

<center>
![](http://www.plantuml.com/plantuml/svg/POwx3O0m34JxJE6AZWKeQA2XPOCeM10Hsr481TlpaO10IZ-zkxCHyiB84LenCuR6sUDGveTRZIISu1v7kyImPMP7UiICDi9HMyQWLBe3PLb_5g02GuFweDTvMWwDD9z3zn-9TNtm6bdN4wd0hyuLVbqci6Te1m00)
</center>

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
