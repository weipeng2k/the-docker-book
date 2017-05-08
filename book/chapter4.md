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
