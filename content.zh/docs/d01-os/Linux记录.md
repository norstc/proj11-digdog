---
weight: 2
title: Linux
---

以下操作都是在BC linux的操作， 属于Redhat， centOS系列， 其他linux版本不一定都能适用。

## 查看版本

查看cpu

```shell
cat /proc/cpuinfo
```

查看linux的版本

发行版本

```shell
cat /etc/issue

\S
Kernel \r on an \m
```

内核版本

```shell
cat /proc/version

Linux version 4.19.90-2107.6.0.0192.8.oe1.bclinux.x86_64 (mockbuild@compute1) (gcc version 7.3.0 (GCC)) #1 SMP Fri Mar 24 10:04:47 CST 2023
```  

发行版

```shell
lsb_release –a
-bash: lsb_release：未找到命令
```

如果没有lsb_release

可以看

```shell
cat /etc/redhat-release

BigCloud Enterprise Linux For Euler release 21.10 (LTS-SP2)
```

## 用户管理

创建新用户admin，并建立一个初始目录 /home/admin

```shell
useradd -m admin
```

查看添加情况

```shell
less /etc/passwd

#其中会新增一行
admin:x:1001:1001::/home/admin:/bin/bash
```

设置一个密码

```shell
passwd admin
```

## 查看当期路径下所有目录的大小du

```shell
du -h --max-depth=1
```

说明：

-h : --human-readable

## 查看进程 ps

看java的进程号

```shell
ps -ef|grep java
```



查看tomcat的进程号

ps –ef | grep tomcat

查看 用户test1的进程

ps -u test1

查看所有用户的所有进程

ps -aux
