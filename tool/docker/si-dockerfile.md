# 四、DockerFile

&#x20;      DockerFile是用于构建docker镜像的文本文件，其内通过一条条指令定义构建的镜像。

&#x20;       Dockerfile中的指令可以指定从哪个基础镜像开始构建、复制文件到镜像中、安装软件包、设置环境变量、暴露端口、运行命令等等。每个指令都会在镜像的构建过程中创建一个新的镜像层，这些层构成了最终镜像的结构。

## 前提准备 <a href="#zu2ao" id="zu2ao"></a>

1. 以安装好docker
2. 建议准备一个空的文件夹，在内创建DockerFile 文件(也可以不用这个名称)
3. 如果存在其它文件，可以在DockerFile同级目录下创建 **.dockerignore** 文件，用来排除其它文件

## 其它建议 <a href="#f7u1p" id="f7u1p"></a>

1. 建议使用官方推荐的纯净版镜像，只需要服务环境依赖。减少依赖、文件大小等。
2. 不建议一个镜像内启动多个服务，建议一个镜像一个服务。比如web系统，包含应用、数据库，应分别构建一个镜像使用。
3. DockerFile 内容尽量精减
4. 使用DockerFile 创建的镜像应该【生命周期】短暂。可以随时的创建容器、删除容器。比如我一个java服务打包成镜像，可能在项目初期我会不停的开发和重新构建镜像。

## 指令 <a href="#uipjm" id="uipjm"></a>

### FROM <a href="#r71ho" id="r71ho"></a>

指明使用哪个基础镜像，如果本地不存在在构建的时候会从docker仓库中下载。

```bash
FROM ubuntu
```

建议使用docker官方制作的镜像，这里推荐几个镜像。

* **应用镜像**，如 nginx、redis、mongo、mysql、httpd、tomcat 等；
* **编程语言镜像**，如 node、oraclejdk，openjdk、python、ruby、golang 等。
* **操作系统镜像**，如 ubuntu、debian、centos、fedora、alpine 等。

还推荐一个镜像， [Alpine](https://hub.docker.com/_/alpine/) 镜像，因为它被严格控制并保持最小尺寸（目前小于 5 MB），但它仍然是一个完整的发行版（我没用过，有机会使用看看）。

### MAINTAINER <a href="#pnk8e" id="pnk8e"></a>

指定镜像的作者信息，包含镜像的所有者和联系人信息

```bash
MAINTAINER <NAME>
```

### RUN <a href="#gsnfd" id="gsnfd"></a>

用于指定构建镜像时运行的命令，两种模式：

1. shell 模式

```bash
RUN <command>
```

2. exec 模式（会拼接为命令）

```bash
RUN [ "executable", "param1", "param2" ] 
```

3. 多条run指令合并为一条

```bash
RUN yum install httpd && yum install ftp
```

为了保持 Dockerfile 文件的可读性，可理解性，以及可维护性，建议将长的或复杂的 RUN 指令用反斜杠 \ 分割成多行。

### EXPOSE <a href="#t67se" id="t67se"></a>

指定运行该镜像的容器使用的端口，可以是多个。

```bash
EXPOSE <PORT>
```

这里指定端口只是告诉我们，容器内部服务会使用的端口。在启动容器时不会向外暴露端口，需要通过-p 参数来指定映射的端口。(我个人感觉这个指令没啥用)

### WORKDIR <a href="#brdnp" id="brdnp"></a>

在容器内部设置工作目录，这样ENTRYPOINT和CMD指定的命令都会在容器中这个目录下进行。

```bash
WORKDIR /AppService
```

### ENV <a href="#dfmf6" id="dfmf6"></a>

设置环境变量而已，无论是后面的其它指令，如 RUN，还是运行时的应用，都可以直接使用这里定义的环境变量。

```bash
1. 格式一
ENV <key> <value>
2. 格式二
ENV <key1>=<value1> <key2>=<value2>...
```

### COPY <a href="#hdd9s" id="hdd9s"></a>

复制文件

```bash
COPY package.json /usr/src/app/
```

可以是容器内的绝对路径，也可以是相对于工作目录的相对路径（工作目录可以用 WORKDIR 指令来指定）。目标路径不需要事先创建，如果目录不存在会在复制文件前先行创建缺失目录。

**注意**：如果源路径为文件夹，复制的时候不是直接复制该文件夹，而是将文件夹中的内容复制到目标路径。

### ADD <a href="#db3mi" id="db3mi"></a>

同样也是复制文件指令，但和COPY不同的是如果源文件是一个tar压缩文件的话，ADD命令会自动加压缩到指定目录中，比如我们复制一个jdk就标胶方便。

```bash
ADD package.json /usr/src/app/
```

注意：

1. 如果源文件是一个URL，docker会去自动去下载
2. 在 COPY 和 ADD 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 COPY 指令，仅在需要自动解压缩的场合使用 ADD。（好像是因为ADD会是镜像构建缓存失效还是啥的，不太清楚。我记录这个结论就行了。）

### CMD <a href="#pkgrk" id="pkgrk"></a>

容器启动命令

```bash
1. shell 格式
CMD <命令>
2. exec 格式
CMD ["可执行文件", "参数1", "参数2"...]
```

### ENTRYPOINT <a href="#wwoam" id="wwoam"></a>

暂无

## 常用指令 <a href="#rqnvt" id="rqnvt"></a>

有一定的顺序

1. FROM: 指定基础镜像
2. WORKDIR: 相当于cd,指定工作目录
3. ENV: 设置环境变量
4. ADD: 复制文件
5. COPY：复制文件，会自动解压tar压缩文件
6. RUN: 构建时执行一些命令
7. CMD: 创建容器时会执行

## 一个简单示例 <a href="#yn2mz" id="yn2mz"></a>

ps: 网上dang的

```docker
# 第一行必须指定基础容器，建议使用aipln类型的小容器
FROM tomcat:8

# 维护者信息(可选)
MAINTAINER xiaojianjun xiaojianjun@tansun.com.cn

# ENV  (可选)环境变量(指定一个环境变量，会被后续 RUN 指令使用，并在容器运行时保持 
ENV JAVA_HOME /opt/java_jdk/bin
ENV PG_VERSION 9.3.4
ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH

# USER (可选) 指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户,前面的RUN 不受影响
# RUN groupadd -r postgres && useradd -r -g postgres postgres 
USER postgres

# WORKDIT 后续的 RUN、CMD、ENTRYPOINT 指令配置容器内的工作目录
WORKDIR /path/to/workdir

# ADD/COPY 将外部文件copy到容器中。区别是ADD可以使用URL，还可以是tar
# COPY只能使用dockerfile所在目录
# ADD <src> <dest>
# COPY <src> <dest>
COPY target/tomcat-release.war /usr/local/tomcat/webapps/

# RUN 镜像的操作指令
# RUN <command> [“executable”, “param1”, “param2”]。
RUN echo “deb http://archive.ubuntu.com/ubuntu/ raring main universe” >> /etc/apt/sources.list
RUN apt-get update && apt-get install -y nginx
RUN mkdir /opt/deploy/
RUN echo “\ndaemon off;” >> /etc/nginx/nginx.conf

# EXPOSE 容器启动后需要暴露的端口
EXPOSE 22 80 8443 8080

# ENTRYPOINT  容器启动后执行命令，不会被docker run提供的参数覆盖，只能有一个ENTRYPOINT,
# 多个ENTRYPOINT，以最后一个为准
#ENTRYPOINT [“executable”, “param1”, “param2”]
#ENTRYPOINT command param param2
ENTRYPOINT echo "helloDocker"  

# 容器启动时执行指令,每个 Dockerfile 只能有一条 CMD 命令
#CMD [“executable”, “param1”, “param2”] 使用 exec 执行，推荐方式。
#CMD command param1 param2 在 /bin/sh 中执行，提供给需要交互的应用。
#CMD [“param1”, “param2”] 提供给 ENTRYPOINT 的默认参数。
CMD /usr/sbin/nginx


# ONBUILD 配置当所创建的镜像作为其他新创建镜像的基础镜像时，所执行的操作指令。例如，Dockerfile 使用如下的内容创建了镜像 image-A。-- 很少使用

# ONBUILD ADD . /app/src
# ONBUILD RUN /usr/local/bin/python-build –dir /app/src
```
