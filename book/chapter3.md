# Docker入门

## Docker运行信息

使用`sudo docker info`，将会发送命令给DockerDaemon，返回Docker运行的信息。

```sh
Containers: 2
 Running: 0
 Paused: 0
 Stopped: 2
Images: 2
Server Version: 17.03.1-ce
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 10
 Dirperm1 Supported: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 4ab9917febca54791c5f071a9d1f404867857fcc
runc version: 54296cf40ad8143b62dbcaa1d90e520a2136ddfe
init version: 949e6fa
Security Options:
 apparmor
 seccomp
  Profile: default
Kernel Version: 4.8.0-49-generic
Operating System: Ubuntu 16.04.2 LTS
OSType: linux
Architecture: x86_64
CPUs: 8
Total Memory: 31.36 GiB
Name: weipeng2k-workstation
ID: 5ZXX:RICR:KQKY:BFOY:QPRK:W4TB:TJQL:R74Q:ABZB:X3CV:GVWF:YSGB
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
WARNING: No swap limit support
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```

> 该命令会返回所有容器和镜像的数量

> 存储器的驱动等配置信息

## 运行容器

使用`sudo docker run -i -t ubuntu /bin/bash`，`docker run`命令提供了Docker容器的创建到启动的功能。

> `sudo docker run -i -t ubuntu:16.04 /bin/bash`，可以选择启动ubuntu镜像中tag为16.04的版本。

`-i`标志，保证容器STDIN是开启的，`-t`使得创建的容器分配了一个tty终端。

> 由于运行的ubuntu的`sources.list`中是美国地址，在进行软件安装的时候非常慢，因此我们基于`ubuntu`构建一个使用中国仓库的`ubuntu`。

> 在`src/chapter3`中创建一个目录`ubuntu`

> `cp /etc/apt/sources.list .`

> `vi Dockerfile`


`Dockerfile`内容为：

```sh
FROM ubuntu:16.04

COPY sources.list /etc/apt/
```

> 创建了一个镜像，从官方`ubuntu:16.04`派生，将当前的`sources.list`拷贝到`/etc/apt/`目录下，替换旧有的`sources.list`

> 该`sources.list`内容是来自cn的。

下面使用`sudo docker build -t "weipeng2k/ubuntu:16.04" .`，创建一个镜像。

然后使用`sudo docker run -i -t weipeng2k/ubuntu:16.04 /bin/bash`运行一个容器，登录其中，安装一下工具。

> 可以看到是从中国仓库中下载，速度很快。

```sh
root@34fe3f085d18:/# apt-get install net-tools
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  net-tools
0 upgraded, 1 newly installed, 0 to remove and 9 not upgraded.
Need to get 175 kB of archives.
After this operation, 725 kB of additional disk space will be used.
Get:1 http://cn.archive.ubuntu.com/ubuntu xenial/main amd64 net-tools amd64 1.60-26ubuntu1 [175 kB]
Fetched 175 kB in 0s (653 kB/s)  
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package net-tools.
(Reading database ... 4764 files and directories currently installed.)
Preparing to unpack .../net-tools_1.60-26ubuntu1_amd64.deb ...
Unpacking net-tools (1.60-26ubuntu1) ...
Setting up net-tools (1.60-26ubuntu1) ...
root@34fe3f085d18:/# ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:ac:11:00:02  
          inet addr:172.17.0.2  Bcast:0.0.0.0  Mask:255.255.0.0
          inet6 addr: fe80::42:acff:fe11:2/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9771 errors:0 dropped:0 overruns:0 frame:0
          TX packets:5376 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:14717960 (14.7 MB)  TX bytes:362791 (362.7 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

使用`sudo docker ps`可以查看运行的容器，如果容器已经停止了将不会展示，但是可以通过`sudo docker ps -a`查看所有的容器。

```sh
$ sudo docker ps -a
CONTAINER ID        IMAGE                    COMMAND             CREATED             STATUS                      PORTS               NAMES
34fe3f085d18        weipeng2k/ubuntu:16.04   "/bin/bash"         38 minutes ago      Exited (0) 15 minutes ago                       vigilant_shannon
61fba4e07526        ubuntu                   "/bin/bash"         About an hour ago   Exited (0) 50 minutes ago                       laughing_rosalind
93cca381d115        ubuntu                   "bash"              4 days ago          Exited (0) 4 days ago                           jolly_kirch
3e8954daff1b        hello-world              "/hello"            4 days ago          Exited (0) 4 days ago                           agitated_einstein
```

可以看到很多有用的信息，比如容器ID，来自哪个镜像，以及容器执行的命令和状态。

## 容器命名

之前通过`docker ps`看到的的列表，`NAMES`属性非常怪异，因为是Docker随机生成的，如果我们需要指定名称可以通过`sudo docker --name container_name -i -t weipeng2k/ubuntu:16.04 uname -a`来进行。

```sh
$ sudo docker run --name container_name weipeng2k/ubuntu:16.04 uname -a
Linux 91b9e178ca14 4.8.0-49-generic #52~16.04.1-Ubuntu SMP Thu Apr 20 10:55:59 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
$ sudo docker ps -a
CONTAINER ID        IMAGE                    COMMAND             CREATED             STATUS                      PORTS               NAMES
91b9e178ca14        weipeng2k/ubuntu:16.04   "uname -a"          16 seconds ago      Exited (0) 16 seconds ago                       container_name
34fe3f085d18        weipeng2k/ubuntu:16.04   "/bin/bash"         44 minutes ago      Exited (0) 21 minutes ago                       vigilant_shannon
61fba4e07526        ubuntu                   "/bin/bash"         About an hour ago   Exited (0) 56 minutes ago                       laughing_rosalind
93cca381d115        ubuntu                   "bash"              4 days ago          Exited (0) 4 days ago                           jolly_kirch
3e8954daff1b        hello-world              "/hello"            4 days ago          Exited (0) 4 days ago                           agitated_einstein
```
> 一般来说指定名称非常有用，我们可以通过名称替代ID。

## 重新启动

Docker容器在命令执行完毕后，就会自动退出，如果我们想重新启动容器，可以通过`sudo docker start container_name`来完成容器的再启动。

```sh
$ sudo docker start -i container_name
Linux 91b9e178ca14 4.8.0-49-generic #52~16.04.1-Ubuntu SMP Thu Apr 20 10:55:59 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

## 守护式容器

守护式容器不是创建一个交互式的容器，适合运行应用和服务。

在`chapter3/daemon_echo`中创建了一个镜像。

`sudo docker build -t "weipeng2k/daemon_echo" .`构建一个`daemon_echo`。这个容器编译了一个`Test.java`，并运行它，它每隔一秒打印一次`hello, world.`。

```java

```

`sudo docker run -name daemon_echo -d weipeng2k/daemon_echo`，以daemon形式在后台运行，如果没有`-d`，将会在前台运行。

我们先用非daemon方式启动，可以看到它在前台启动，如果使用`ctrl + C`，将退出当前进程。

```sh
$ sudo docker run --name daemon_echo2 weipeng2k/daemon_echo
hello world.
hello world.
hello world.
hello world.
hello world.
hello world.
hello world.
hello world.
hello world.
hello world.
```

而使用`-d`运行，docker进程在后台运行，前台只是返回了一个Docker容器的ID。

可以通过`sudo docker logs -f daemon_echo`观察容器内的日志输出。

## 观察容器

可以通过`sudo docker top daemon_echo`来查看容器内的进程。

```sh
$ sudo docker top daemon_echo
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                18408               18390               0                   22:50               ?                   00:00:00            java Test
```

可以通过`sudo docker exec` 来执行命令，比如：

```sh
$ sudo docker exec -i -t daemon_echo /bin/bash
root@94df9b5f5e8b:~/app# ls
Test.class  Test.java
root@94df9b5f5e8b:~/app# jps
1 Test
43 Jps
root@94df9b5f5e8b:~/app# jps
1 Test
63 Jps

```

> 指定一个守护式容器，运行一个命令，这里启动了一个bash，并且连接上去。

可以通过`sudo docker inspect`来查看容器的详细信息。

```sh
$ sudo docker inspect daemon_echo
[
    {
        "Id": "94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357",
        "Created": "2017-05-06T14:21:45.492458772Z",
        "Path": "java",
        "Args": [
            "Test"
        ],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 143,
            "Error": "",
            "StartedAt": "2017-05-06T14:50:44.525336192Z",
            "FinishedAt": "2017-05-06T14:56:41.952696647Z"
        },
        "Image": "sha256:544097392791b51128724cd0dc317ee50c144981e1119248db4164c338e19f33",
        "ResolvConfPath": "/var/lib/docker/containers/94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357/hostname",
        "HostsPath": "/var/lib/docker/containers/94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357/hosts",
        "LogPath": "/var/lib/docker/containers/94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357/94df9b5f5e8ba82900f9a01bca0e9e66815cdd01ca054421ce943590a58e4357-json.log",
        "Name": "/daemon_echo",
        "RestartCount": 0,
        "Driver": "aufs",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DiskQuota": 0,
            "KernelMemory": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": -1,
            "OomKillDisable": false,
            "PidsLimit": 0,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0
        },
        "GraphDriver": {
            "Name": "aufs",
            "Data": null
        },
        "Mounts": [],
        "Config": {
            "Hostname": "94df9b5f5e8b",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "java",
                "Test"
            ],
            "ArgsEscaped": true,
            "Image": "weipeng2k/daemon_echo",
            "Volumes": null,
            "WorkingDir": "/root/app",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "fa457180ba8f22fc8b5458f8702680b2ef6f431cc04fa279bbc7ba29d3787afe",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": null,
            "SandboxKey": "/var/run/docker/netns/fa457180ba8f",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "59c20d993ec2ced31e66fade31e61cc619b62d46eb9c844f8bbf668c7075ff33",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": ""
                }
            }
        }
    }
]
```

## 删除容器

删除一个容器，可以使用`sudo docker rm `来进行，比如：

```sh
$ sudo docker rm daemon_echo
daemon_echo
```

> 只有停止的容器才能够被删除，如果没有停止，通过`sudo docker stop daemon_echo`来停止一个容器

> sudo docker rm ``sudo docker ps -a -q`` 可以方便的删除所有容器。
