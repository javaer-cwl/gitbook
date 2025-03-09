# 七、Docker-Compose 实战

本篇实战文章会将DockerFile实战章节中的项目通过Docker-Compose来实现完成。

## 一、项目回顾 <a href="#pl1pf" id="pl1pf"></a>

### 1. 准备文件 <a href="#uiczx" id="uiczx"></a>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### 2. DockerFile2内容 <a href="#gao04" id="gao04"></a>

```docker
FROM fabletang/jre8-alpinea

# 设定时区、中文

# 工作目录
WORKDIR /AppService

# 定义一个环境变量，后面看看通过java服务能否获取到
ENV TEST=测试


# 复制jar到工作目录中
COPY docker_file_demo-0.0.1.jar ./docker_file_demo_1.0.jar
COPY application-test.yml ./application-test.yml



CMD ["java", "-jar", "./docker_file_demo_1.0.jar","--spring.profiles.active=test"]
```

### 3. application-test.yml <a href="#g6lt3" id="g6lt3"></a>

```yaml
applicationName: 你获取的是外部的配置文件
```

### 通过docker-compose构建服务 <a href="#jgl5m" id="jgl5m"></a>

## 二、Docker-Compose模板文件 <a href="#dzxb5" id="dzxb5"></a>

### 1. docker-compose.yml <a href="#eiqwp" id="eiqwp"></a>

```yaml
version: '3.0'

services:
  docker-compose-demo01:
    build:
      #构建的地址
      context: ./
      dockerfile: DockerFile2
    image: docker-compose-demo:1.0
    ports:
      - 8081:8080
    restart: always
    volumes:
      - /AppService/docker/docker_file/docekr_file_test/application-test.yml:/AppService/application-test.yml
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### 2. 测试结果 <a href="#nvboi" id="nvboi"></a>

```
[root@VM-4-14-centos docekr_file_test]# curl 127.0.0.1:8081/test1
你获取的是外部的配置文件
[root@VM-4-14-centos docekr_file_test]# 
```
