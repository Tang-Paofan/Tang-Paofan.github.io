---
title: Docker-Command
mathjax: false
date: 2025-07-29 22:25:26
tags:
categories:
---

Docker 常用命令
<!-- less -->

```bash
docker pull nginx:latest

docker run nginx

# 端口绑定（端口映射） -p 宿主端口:docker端口
# 将宿主端口转发到docker端口
docker run -p 80:80 nginx

# 挂载卷（文件绑定） -v 宿主目录:docker目录
# 持久化保存
docker run -v %cd%:/user/zzl

# 命名卷挂载
docker volume create nginx_volume
docker run -v nginx_volume:/user/zzl
# 查找挂载卷宿主机路径
docker volume inspect nginx_volume

# 进入容器
docker exec -it ${Container ID} bash

# 启动后直接进入容器，退出命令行后容器被删除（--rm）
docker run -it --rm nginx

# 设置环境变量
docker run -e MONGO_INITDB_ROOT_USERNAME=tech mongon

# 运行后直接进入容器内部，推出后容器被删除，但是镜像还在
docker run -it --rm alpine

# 只要容器停止就重启（宿主机停电，或者崩溃），手动停止也不会重启
docker run -d --restart always nginx

# 只要容器停止就重启（宿主机停电，或者崩溃），与always类似，但是手动停止就不会重启
docker run -d --restart unless-stopped nginx

docker stop containerid
docker start containerid

# 根据当前目录下Dockerfile 构建镜像
docker build -t ${container_name} .

# Docker创建子网
docker network create netword1
```