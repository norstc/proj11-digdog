---
weight:3
title: "Docker记录"
---

## 安装

## 使用

尝试一个监控软件的部署

```shell
docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1 
```

- 命令行说明
- docker run -> docker container run 命令, 从一个镜像创建一个容器然后运行， 参考 https://docs.docker.com/reference/cli/docker/container/run/
- -d -> 以后台运行容器，并打印出容器id
- --restart=always -> 重启容器的策略：一直， 表示只要容器终止， docker就会自动拉起一个新的容器，每次重拉起时docker会自动使用一个自增长的延时，开始100ms，然后每次翻倍，直到1分钟
- -p 3001:3001 -> 将容器的端口通过主机端口暴露出来， 第一个是主机端口，第二个是容器端口
- -v uptime-kuma:/app/data -> 将容器中的目录挂载到主机的卷(volume)， 第一个是主机的卷， 第二个是容器目录， 主机的卷是docker维护的，位于主机上的存储空间， 参考 https://www.zsythink.net/archives/4362
- --name uptime-kuma -> 为创建的容器指定一个名称
- louislam/uptime-kuma:1 -> 镜像名

