# 一、安装

## Docker安装

### 1. docker-compose.yml文件

```docker
version: '3.8'

services:
  redis:
    image: redis:latest
    container_name: my_redis
    restart: unless-stopped
    ports:
      - "6379:6379"
    volumes:
      - ./redis-data:/data
    command: ["redis-server", "--requirepass", "cwljxf1025."]
    networks:
      - redis-net

networks:
  redis-net:
    driver: bridge
```

### 2. 启动命令

```
docker-compose -f xxx.yml up -d
```
