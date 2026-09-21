---
title: docker常用命令
date: 2025-09-11 22:34:30
tags: docker
---

# 镜像

- 搜索
  - `docker search mysql`
- 下载
  - `docker pull <镜像名>:<标签>`
- 查看信息（环境变量、作者、分层信息、启动命令）
  - `docker inspect <镜像名或ID>：查看镜像的详细元数据`
- 查看本地镜像列表
  - `docker image ls`
  - `docker images`
- 删除本地镜像
  - `docker rmi <镜像名|镜像id>`
  - `docker rmi -f ceb3c3017a6d`
  - -f 表示强制删除（如果镜像正在被容器使用）
- 利用 Dockerfile 自动化构建自定义镜像
  - `docker build -t my-app:v1.0 .`
  - -t 指定镜像名和标签，末尾的“.”代表当前目录下的Dockerfile
- 为现有镜像打一个新标签
  - `docker tag <原镜像> <新镜像名>:<新标签>`
  - -t 指定镜像名和标签，末尾的“.”代表当前目录下的Dockerfile
- `docker save`：将本地镜像打包导出为一个 .tar 压缩包文件
  - `docker save -o nginx-latest.tar nginx:latest`
- `docker load`: 从 .tar 压缩包文件中加载镜像到本地
  - `docker load -i nginx-latest.tar`
- 推送镜像
  - `docker push myusername/my-app:v1.0`

# 容器

- 创建&启动 `docker run <image name>`
  - -d 后台运行
  - --name 命名
  - -p 指定端口映射
    - `-p <宿主端口>:<容器端口>`
  - -v 指定卷
    - `-v <宿主路径|卷名称>:<容器路径>`
  - -e 传入环境变量
    - `docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d dhi.io/mysql:<tag> mysqld`
  - --rm 用完删除容器
  - -it 进入交互式命令
  - --restar [always|unless-stopped]
- 启动已有容器 `docker start <容器名或ID>`
- 停止容器 `docker stop <容器名或ID>`
- 重启容器 `docker restart <容器名或ID>`
- 强制停止容器 `docker kill <容器名或ID>`
- 容器列表 `docker ps`
  - -a 查看所有容器
- 实时查看容器的 CPU、内存、网络 I/O、磁盘 I/O 等资源使用率 `docker stats`
- 查看容器内正在运行的进程列表。 `docker top <容器名或ID>`
- 删除容器 `docker rm <容器名或ID>`
  - -f 强制删除运行中的容器
- 在运行中的容器内执行新命令（最常用于进入容器的终端）`docker exec`
  - -i: 交互式操作
  - -t: 分配伪终端
  - `docker exec -it my-nginx /bin/bash`
- `docker logs` 查看容器的控制台日志输出
  - -f 滚动查看日志
- `docker cp` 在宿主机和容器之间互相复制文件或文件夹
- `docker inspect <容器名或ID>` 查看容器的底层详细信息（包括网络配置、挂载卷、环境变量等）
  - `docker inspect my-nginx`
- `docker container prune` 一键清理所有已经停止运行的容器（释放空间）
- 其他
- `docker rm -f $(docker ps -aq)`

# 网络

- 创建 `docker network create <name>`
- `docker network list`
- `docker network rm <name>`
- 使用 `docker run -d --network <network-name|host> nginx`

# 卷

- 创建卷 `docker volume create [name]`
- 查看创建卷的真实目录 `docker volume inspect [name]`
- window查看真实目录 `\\wsl$\docker-desktop\mnt\docker-desktop-disk\data\docker\volumes`
- 查看所有卷 `docker volume list`
- 删除卷 `docker volume rm <name>`
- 使用卷 `docker -v <name>:容器目录 nginx`
- 删除所有没有容器使用的卷 `docker volume prune -a`

# Dockerfile

```
FROM nodejs
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 8000
CMD ['node', 'index.js']
```

# docker compose

`docker compose [-f <filename>] up -d [--build]`
`docker compose stop`
`docker compose start`
`docker compose down`

```
services:
  db_mysql:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - 3307:3306
    volumes:
      - D:\mysql_data:/var/lib/mysql

  db_redis:
    image: redis
    ports:
      - 6380:6379

  spring-app:
    build: ./easyled-java
    ports:
      - "8084:8084"
    depends_on:
      - db_mysql
      - db_redis

  h5-app:
    image: nginx
    ports:
      - 8085:80
    volumes:
      - ./default.conf:/etc/nginx/conf.d/default.conf
      - ./easyled-js/dist:/usr/share/nginx/html
    depends_on:
      - spring-app
      - db_redis
      - db_mysql
```

# 示例

## nginx

- `docker run -d -p 80:80 -v /web/html:/usr/share/nginx/html nginx`

## mysql

- `docker run -d --name mysql-container -p 3306:3306 -e MYSQL_ROOT_PASSWORD=your_password -v /data/mysql:/var/lib/mysql mysql:latest`

