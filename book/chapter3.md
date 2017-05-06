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
