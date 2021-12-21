# Dockerfile

## 最常用五个指令

### FROM

指定当前镜像基于哪个镜像。

`FROM alpine` 说明这个镜像基于 `alpine`，如果只有这么一句，那么就是 `alpine`的复制。

### WORKDIR

指定接下来的 `shell` 语句运行在哪个路径下。

`WORKDIR /app` 后续 `shell` 命令都会在 `/app`目录下运行，如果没有这个目录，那就会创建这样一个目录。

### COPY

把宿主机某个目录下文件拷贝到镜像中某个目录下。

`COPY src/ /app`

### RUN

运行 `shell` 语句

`RUN echo 321 >> 1.txt`

### CMD

也是运行对应 `shell` 脚本。

### RUN 和 CMD 的不同

| RUN                        | CMD                                |
| -------------------------- | ---------------------------------- |
| 脚本是在**构建镜像时**运行 | 脚本是在**容器真正运行时**才会运行 |

## 其它指令

### ADD

功能和`COPY`相同，都是进行文件拷贝。但是使用ADD还可以进行**网络资源**的拷贝，还自带**解**压。

一般使用 `COPY`就够了。

### ENTRYPOINT

二者后的指令都可以是 `shell`命令形式，或者是数组形式。搭配起来用的效果如图所示。

总结：

1. 如果 `ENTRYPOINT`不是数组形式，那就是以 `ENTRYPOINT`为准。
2. 如果是 `ENTRYPOINT` 数组形式，那就将 `ENTRYPOINT` + `CMD`拼接起来。



![image-20211124144459080](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211124144459080.png)

### EXPOSE

暴露内网端口。

`EXPOSE 80`即暴露80端口，就是通过 `localhost:80`可以访问。

配合上 `docker run -P ...` (是大P) ，会将容器中的EXPOSE端口（80）随机映射到宿主机的端口。

实际操作一下。

![image-20211124151310355](https://ryze-halo-blog.oss-cn-beijing.aliyuncs.com/halo-blog/image-20211124151310355.png)

上图表示外网通过 32776 访问，内网通过 80 访问

### VOLUME

指定挂载的文件。一般是匿名挂载

`VOLUME ["/data1","/data2"]` 将 `"/data1","/data2"`这两个文件夹挂载到宿主机上。

### ENV

指定容器的环境变量

`ENV A=10` 或者 `ENV A 10`

### ARG

构建参数。只有在容器构建时才有效。所以下面代码不会有输出：

```dockerfile
ARG A=10
CMD echo $A
```

但是可以指定`ARG`后，再赋值给环境变量，这样就有效了。

```dockerfile
ARG B=11
ENV A $B
CMD echo $A
```

B只是默认是11，但它可以被修改：

`docker build -t test --build-arg B=12 .`这样B就被修改为12了。

### USER

用于指定RUN CMD等指令运行时的用户身份，不指定是root。
用法:

`USER  用户名:用户组`

`USER  用户id:组id`

### LABEL

指定元数据信息。

元数据是什么？？？

```dockerfile
LABEL k="v" k1="v1"
```

### ONBUILD

`ONBUILD`是当前镜像构建的时候不会执行，基于当前镜像的镜像构建的时候才会执行。

`ONBUILD`后面就是一些 `Dockerfile`指令。

`ONBUILD ENV C=100`

### STOPSIGNAL

指定容器停止信号。

### HEALTHCHECK

### SHELL

指定容器中的`shell`是那种`shell`，`/bin/bash`









