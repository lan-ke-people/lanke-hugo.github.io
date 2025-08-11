---
title: "Linux运维常用命令速查"
author: "烂柯"
description: "Linux服务器运维常用命令大全：进程管理、服务部署、资源监控"
date: 2022-10-15
lastmod: 2022-10-20

tags: [
    "进程管理",
    "服务部署",
    "后台运行",
    "资源监控",
    "端口管理"
]
categories: [
    "Linux运维",
    "服务管理",
    "系统监控"
]
keywords: [
    "服务启动",
    "进程监控",
    "端口占用",
    "资源查看"
]
---

## 服务启动命令
```bash
# Java应用
nohup java -jar XXX.jar &

# Nacos
nohup sh startup.sh -m standalone &

# Redis
./redis-server redis.conf &

# Nginx
./sbin/nginx

# RocketMQ
nohup sh mqnamesrv &
nohup sh mqbroker -n 127.0.0.1:9876 -c broker.conf &

# 关闭RocketMQ
sh mqshutdown broker
sh mqshutdown namesrv
```

## 进程管理命令
```bash
# 查看进程
ps -aux | grep java

# 终止进程
kill -9 [PID]
```

## 端口与网络
```bash
# 查看端口占用
netstat -ntulp | grep 3306

# 查看进程端口
netstat -nap | grep [PID]
```

## 资源监控
```bash
# 内存使用
free -h

# 磁盘使用
df -h
```