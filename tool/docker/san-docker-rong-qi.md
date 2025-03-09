# 三、Docker 容器

## 创建(启动)容器 <a href="#ygv0l" id="ygv0l"></a>

&#x20;       有人把 “从镜像创建容器” 和 ”从停止状态的容器启动“ 都叫做启动容器。这里我做了区分，把”从镜像创建容器“ 过程叫做创建容器。

## docker run命令 <a href="#kyn62" id="kyn62"></a>

通过docker run命令可以从镜像中创建一个容器或成为实例。

例如：

```bash
docker run -d  --name 别名 imageName
```

ps: -d 表示后台运行

注意： docker run命令可以添加很多参数，比如映射端口、映射路径等等。

## run命令参数 <a href="#mx1fs" id="mx1fs"></a>

1. **-d：**&#x4EE5;后台模式运行容器。
2. **-i：**&#x5141;许你与容器的标准输入进行交互。
3. **-t：**&#x5206;配一个伪终端 (TTY)。
4. **--name：**&#x4E3A;容器指定一个自定义名称。
5. **--rm：**&#x5BB9;器退出时自动删除容器。
6. -**e：**&#x8BBE;置环境变量。
7. **--network：**&#x8FDE;接容器到指定的网络。
8. **-p**：将容器端口映射到主机端口。
9. -**-volume** 或 -v：将主机文件或目录挂载到容器中。
10. **--link：**&#x8FDE;接一个容器到另一个容器。
11. **--restart：**&#x8BBE;置容器的重启策略。
12. **--user：**&#x6307;定容器运行的用户名或 UID。
13. **--privileged：**&#x7ED9;容器赋予特权。
14. **--entrypoint**：覆盖容器的默认入口点。
15. **--env-file：**&#x4ECE;文件中读取环境变量。
16. **--label：**&#x4E3A;容器添加元数据标签。
17. **--detach-keys：**&#x8986;盖分离键的设置。
18. **--log-driver：**&#x6307;定容器的日志驱动程序。
19. **--log-opt：**&#x8BBE;置容器的日志选项。
20. **--ulimit：**&#x8BBE;置容器的 ulimit 值。

## 常用命令参数 <a href="#v8xuu" id="v8xuu"></a>

1. **--name** ： 为容器指定一个自定义名称

```bash
docker run --name myRedis redis:laste
```

2. **-d：**&#x4EE5;后台模式运行容器。

```bash
docker run -d --name myRedis redis:laste
```

3. **--rm：容器退出时自动删除容器。**

&#x20;       这个也挺有用的，有时候我们临时启动一个工具包镜像就可以这样

```bash
docker run -t --rm imageName
```

运行后交互式终端操作，退出时删除容器

4. -**e：设置环境变量**

&#x20;       比如mysql镜像需要设置环境变量用于配置root账号和密码

```bash
docker run -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql
```

5. -**-volume** 或 -v：将主机文件或目录挂载到容器中。(对于一些镜像应该强制使用）

&#x20;       比如把mysql的数据文件映射到宿主机中，这样数据不会随着容器销毁而丢失

```bash
docker run -d -v /host-data:/container-data myapp
```

&#x20;       -v /宿主机:/容器

6. **--restart：**&#x8BBE;置容器的重启策略。

```bash
docker run --restart=always -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql
```

7. **-p**：将容器端口映射到主机端口。（最常用）

```bash
docker run --restart=always -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql
```

&#x20;       -p 宿主机指定一个端口:容器内服务端口

8. 将正在运行的容器删除

```bash
 docker rm -f xxxxx
```

## 容器操作 <a href="#tlddz" id="tlddz"></a>

1. 查看容器

```bash
docker ps
docker ps -a
```

2. 停止容器

```bash
docker stop containerId
```

3. 删除容器

&#x20;       删除容器时请确保不会数据丢失，数据文件已映射到宿主机上

```bash
docker rm containerId
```

4. 启动容器

```bash
docker start containerId
```

5. 进入容器

```bash
docker exec -it 容器 /bin/bash
docker exec -it 容器 bash
```

5. 退出容器

```bash
exei
```

6. 复制文件到宿主机

```bash
docker cp 容器id或name:/容器内路径 宿主机路径
```

7. 从宿主机复制文件到容器内

```bash
docker cp 宿主机文件  容器:/路径
```

## 执行容器内服务的命令 <a href="#uyyh9" id="uyyh9"></a>

&#x20;         有时候我们需要执行容器内服务的命令，比如nginx重新加载配置文件。可以通过重启容器来完成，但是这样不够优雅，这会导致服务短暂的无法访问，如果配置文件有问题会导致无法启动。

1. 命令格式

```bash
docker exec -it 容器名称或id  服务命令 参数
```

2. 例如：nginx，// 检查配置文件是否正常

```bash

[root@xxxxx ~]#docker exec -it nginx nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

&#x20;       这里出现两个nginx，第一个nginx表示容器名称，也可以替换为容器的id，第二个nginx是容器内nginx服务

3. 重新加载nginx

```bash
docker exec -it nginx nginx -s reload
```

## 容器导入导出 <a href="#yb2xa" id="yb2xa"></a>

1. 导出容器

&#x20;       这样将导出容器快照到本地文件。

```bash
docker export 7691a814370e > ubuntu.tar
```

2. 导入容器

```bash
docker import my_container.tar my_imported_image
```

&#x20;       这将创建一个新的镜像，名称为 my\_imported\_image，其文件系统是从之前导出的 tar 文件中导入的。

## 从容器提交新镜像 <a href="#efeep" id="efeep"></a>

1. 提交容器为镜像

```bash
docker commit <container_id> <new_image_name>
```

&#x20;       这中方式是将容器保存为新的镜像而而不是为本地文件。

## 注意 <a href="#btmag" id="btmag"></a>

&#x20;       关于镜像的导入导出、容器的导入导出、容器提交新镜像。这三种区别

* 镜像的导入导出： 是将镜像导出为tar本地文件，可以方便移动到其它服务器上
* 容器的导入导出： 这是将正在运行的容器导出为本地文件，在其它服务器上可以导入为镜像
* 容器提交为镜像： 这是在本机上操作，将正常运行的容器提交为一个镜像。
