# 六、Docker-Compose

> &#x20;       Compose 定位是 「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」，其前身是开源项目 Fig。
>
>
>
> &#x20; 在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。 Compose 恰好满足了这样的需求。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

## 两个重要的概念： <a href="#rissd" id="rissd"></a>

* 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
* 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

## 关于安装 <a href="#zcrkg" id="zcrkg"></a>

> &#x20;       有的教程上面说v1和v2版本执行的指令分别是docker-compose和docker compose（有和没有中间横线的区别），再此我在下方总结了一下。

1. 独立安装的Compose，不论是V1还是V2版本，指令都是docker-compose（中间是短横线）
2. 插件安装的Compose，在V2版本，指令是docker compose（中间是空格），最新版的docker安装时会自动以插件的形式安装docker compose
3. 支持同时采用两种方式安装Compose，安装后可以同时使用docker-compose和docker compose

**提示: 具体安装教程请翻阅互联网**

## 模板文件 <a href="#drhat" id="drhat"></a>

&#x20;       在构建docker-compose服务时如果不指定文件，默认的模板文件名称为 docker-compose.yml，格式为 YAML 格式。

### 示例： <a href="#zrpuc" id="zrpuc"></a>

```docker
version: '3.0'

networks:
  docker-compose-demo-net:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.1.0/24
          gateway: 192.168.1.1


services:
  docker-compose-demo01:
    build:
      #构建的地址
      context: /usr/local/docker-compose-demo
      dockerfile: Dockerfile
    image: docker-compose-demo
    container_name: docker-compose-demo01
    #选择网络
    networks:
      - docker-compose-demo-net
    #选择端口
    ports:
      - 8081:8080/tcp
    restart: always

  docker-compose-demo02:
    build:
      #构建的地址
      context: /usr/local/docker-compose-demo
      dockerfile: Dockerfile
    image: docker-compose-demo
    container_name: docker-compose-demo02
    #选择网络
    networks:
      - docker-compose-demo-net
    #选择端口
    ports:
      - 8082:8080/tcp
    restart: always

  nginx:
    image: nginx:latest
    container_name: nginx-demo
    networks:
      - docker-compose-demo-net
    ports:
      - 80:80/tcp
    restart: always
    volumes:
      - /usr/local/docker-compose-demo/nginx.conf:/etc/nginx/nginx.conf:rw
```

注意：每个服务都必须通过image制定镜像或者使用build指令配置DockerFile文件。

## Docker-Compose运行指令 <a href="#fntyx" id="fntyx"></a>

#### 1. 启动容器 <a href="#xttni" id="xttni"></a>

```bash
 docker-compose up
```

#### 2. 指定模板文件启动 <a href="#gqcu1" id="gqcu1"></a>

```bash
docker-compose -f xxxx.yml up
```

#### 3. 启动容器并在后台运行 <a href="#hh0x0" id="hh0x0"></a>

会将构建的容器状态打印出来

```bash
docker-compose -f xxx.yml up -d
```

#### 4. 停止容器 <a href="#rm0zi" id="rm0zi"></a>

停止并删除docker-compose中定义的所有容器、网络、卷。

```bash
docker-compose -f xxx.yml down
```

#### 5. 查看服务日志 <a href="#nukd6" id="nukd6"></a>

```bash
docker-compose logs [service_name]
```

* service\_name：可选

#### 6. 查看运行中容器的内容 <a href="#b40bb" id="b40bb"></a>

```bash
docker-compose ps
```

#### 7. 重新构建容器 <a href="#co1ht" id="co1ht"></a>

```bash
docker-compose build
```

#### 8. 列出服务 <a href="#thp1t" id="thp1t"></a>

```bash
docker-compose config --services
```

#### 9. 执行特定服务内的命令 <a href="#zkxmg" id="zkxmg"></a>

```bash
docker-compose exec [service_name] [command]
```

其实上面的命令和基本的docker ps命令类似，只是命令为docker-compose
