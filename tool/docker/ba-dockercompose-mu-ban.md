# 八、Docker-Compose 模板

## 1. Nginx

```docker
version: '3.8'

services:
  nginx:
    image: nginx:latest  # 使用最新版本的 Nginx 镜像
    container_name: nginx_container  # 容器名称
    ports:
      - "80:80"  # 将容器的 80 端口映射到主机的 80 端口
      - "443:443"  # 将容器的 443 端口映射到主机的 443 端口（如果需要 HTTPS）
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf  # 挂载自定义的 Nginx 配置文件
      - ./html:/usr/share/nginx/html  # 挂载静态文件目录
      - ./logs:/var/log/nginx  # 挂载日志目录
    networks:
      - nginx_network  # 使用自定义网络

networks:
  nginx_network:  # 定义自定义网络
```

## 2. MYSQL

```
version: '3.8'

services:
  mysql:
    image: mysql:8.0  # 使用 MySQL 8.0 镜像
    container_name: mysql_container  # 容器名称
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword  # 设置 root 用户的密码
      MYSQL_DATABASE: mydatabase  # 创建一个默认的数据库
      MYSQL_USER: myuser  # 创建一个普通用户
      MYSQL_PASSWORD: mypassword  # 设置普通用户的密码
    ports:
      - "3306:3306"  # 将容器的 3306 端口映射到主机的 3306 端口
    volumes:
      - mysql_data:/var/lib/mysql  # 持久化 MySQL 数据
    networks:
      - mysql_network  # 使用自定义网络

volumes:
  mysql_data:  # 定义数据卷，用于持久化 MySQL 数据

networks:
  mysql_network:  # 定义自定义网络
```
