---
title: "docker 常用命令"           # 文章标题
author: "烂柯"              # 文章作者
description: "docker 常用命令" # 文章描述信息
date: 2023-08-16                   # 文章编写日期
lastmod: 2023-08-16                # 文章修改日期

tags: [
    "版本管理",
    "开发工具",
    "容器技术"       # 新增
]

categories: [
    "环境配置",
    "容器化部署"     # 新增
]

keywords: [
    "Docker",        # 保留
    "容器化",         # 优化
    "开发环境部署"    # 优化
]
---

1
检查docker版本
docker -v
2
查看docker已有镜像
sudo docker images
3
设置docker开机启动
sudo systemctl enable docker

查看docker服务状态是否启动
sudo systemctl status docker

启动docker服务
sudo systemctl start docker

查看所有容器状态
docker ps -a 

查看正在运行的容器状态
docker ps

可以通过以下命令在容器内重新加载 Nginx 配置：
docker exec <nginx_container_id_or_name> nginx -s reload

使用 docker exec 命令进入容器的交互式 shell：
docker exec -it nginx /bin/bash

重启指定容器：
sudo docker restart <container_name_or_id>

更新容器为开机启动启动
docker update 容器id --restart=always
关闭容器为开机启动
docker update 容器id --restart=no

1 拉去mysql镜像
sudo docker pull mysql:8.0

2 启动mysql容器
# --name指定容器名字 -v目录挂载 -p指定端口映射  -e设置mysql参数 -d后台运行
sudo docker run --name mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql:/etc/mysql/conf.d -v /mydata/mysql/log:/var/log/mysql  -e MYSQL_ROOT_PASSWORD=root  -p 3306:3306 -d mysql:latest

3 使用su - root（切换为root，这样就不用每次都sudo来赐予了）
su - root
4 进入mysql容器
docker exec -it 容器名称|容器id bin/bash
修改完之后再刷新配置flush privileges；
查看所有容器状态
docker ps -a 
docker rm 容器id

启动redis容器
docker run -p 6379:6379 --name redis --privileged=true -v /mydata/redis/data:/data -v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf
