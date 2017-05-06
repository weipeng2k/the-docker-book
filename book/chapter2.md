# 安装Docker

> Docker支持大多数的平台，但是对Linux平台支持较好，推荐使用Ubuntu或者RHEL，后面的例子都是用的是Ubuntu

> Ubuntu，有奔头

## Ubuntu下安装DockerCE

一般使用`16.04 LTS`作为基准环境，这个版本推出了一年多了。由于Docker在最近做了调整，分化除了**DockerCE**和**DockerEE**，两个分支，我们使用免费的**DockerEE**

> 版本分化，价值体现，收费先行，建设生态，sun微电子，前车之鉴

> 官方的Docker进行镜像下载，随着阿里云成为Docker的官方伙伴，而变得快了？至少给我的下载感觉提升了不少。

* 设置仓库

  * 先安装一些软件<br>
`sudo apt-get -y install apt-transport-https ca-certificates curl`
  * 添加仓库key<br>
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
  * 添加仓库<br>
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
  * 更新仓库<br>
`sudo apt-get update`

> `lsb_release -cs`可以看ubuntu发行版的代号。

* 安装DockerCE

`sudo apt-get install docker-ce`

* 验证

`sudo docker run hello-world`

> 该过程会联通Registry下载hello-world，并启动，最终输出到你的终端。

## Docker守护进程

Docker以`root`权限运行守护进程，只有使用`root`，才能够处理普通用户无法完成的操作，比如：挂载文件系统。守护进程监听`/var/run/docker.sock`这个Unix套接字文件，来获取来自客户端的Docker请求。

可以运行`sudo service docker stop`，停止当前的docker守护进程，然后使用`sudo dockerd -H tcp://0.0.0.0:2375` 来完成守护进程的启动。默认情况下，Docker的客户端与DockerDaemon之间的通信是不进行认证的。

> 这时，docker守护进程将会在监听本机所有网卡上的2375端口，用来响应命令。

> 回顾一下之前提到的DockerDaemon的架构，它处于架构的正中心，负责响应命令，完成镜像的下载、构建以及容器的创建工作。

* 启动DockerDaemon<br>
  `sudo service docker start`
* 重启DockerDaemon<br>
  `sudo service docker restart`
* 停止DockerDaemon<br>
  `sudo service docker stop`

> service实际上是运行在`/etc/init.d`中的`docker`脚本，其中必须要支持`start`、`restart`、`stop`和`status`。
