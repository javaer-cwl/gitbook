# 二、Docker 镜像

## 一、概念 <a href="#cpjlv" id="cpjlv"></a>

什么是镜像？什么是容器？镜像和容器有什么关系？docker官网给的解释

> An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. For example, you may build an image which is based on the ubuntu image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.

> 镜像像是一个只读模板，带有创建Docker容器的指令。通常，一个镜像像是基于另一个镜像的，还需要进行一些额外的定制。例如，您可以构建一个基于ubuntu镜像的镜像，但是安装Apache web服务器和您的应用程序，以及使您的应用程序运行所需的配置细节。

可以把镜像看作一个纯净版的linux系统，体积小。镜像好比模板，通过镜像可以创建多个相同的容器（有点像java中的类和对象的关系）。

## 二、如何获取镜像 <a href="#zg2wa" id="zg2wa"></a>

镜像可以自己制作(DockerFile会说到)，也可以从docker镜像仓库中拉取镜像。

镜像仓库地址：[https://hub.docker.com/](https://hub.docker.com/)

## 三、镜像操作 <a href="#q9yq8" id="q9yq8"></a>

### 1. 查找镜像

```
docker search imageName
```

### 2. 拉取镜像

```
docker pull imageName
```

### 3. 查看镜像

```
docker images
```

### 4. 删除镜像

```
docker rmi imageName
```

### 5. 镜像标签

> imageName:tar 自定义的镜像名称和标签

```
docker tag 860c279d2fec imageName:tar
```

## 四、镜像导入导出 <a href="#qbqal" id="qbqal"></a>

有一个场景，我服务器中是在内外环境下，我需要通过本地拉取镜像后导出文件，拷贝文件到服务器中导入，最后创建容器。

### 1.导出镜像

> 一般导出的文件名为tar结尾

```
docker save -o <tar文件路径> <镜像名称>
```

### 2.导入镜像

> 镜像拷贝到内网环境服务器后

```
docker load -i <tar文件路径>
```
