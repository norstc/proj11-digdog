---
title: Java 环境配置
weight: 3
---

## 下载jdk

选择openjdk  21.0.2

https://jdk.java.net/21/

### Windows 10

windows/x61: openjdk-21.0.2_windows-x64_bin.zip

解压到 ; D:\jdk-21.0.2

配置环境变量： 右键windows 开始 -》 系统-》 关于 -》 高级系统设置 -》 系统属性 -》 高级-》 环境变量

JAVA_HOME=D:\jdk-21.0.2

PATH 增加 D:\jdk-21.0.2\bin

打开cmd 使用 java -version 查看Java版本

```shell
java -version
openjdk version "21.0.2" 2024-01-16
OpenJDK Runtime Environment (build 21.0.2+13-58)
OpenJDK 64-Bit Server VM (build 21.0.2+13-58, mixed mode, sharing)

```

### linux

Linux/X64： openjdk-21.0.2_linux-x64_bin.tar.gz

拷贝到 /home/admin/openjdk-21.0.2_linux-x64_bin.tar.gz

解压到 /home/admin

```shell
cd /home/admin
tar -xvzf openjdk-21.0.2_linux-x64_bin.tar.gz
```

然后修改全部用户公用的配置文件 `vim /etc/profile`

```profile
export JAVA_HOME=/home/admin/jdk-21.0.2
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

使用 `java --version`

```shell
java -version
openjdk version "21.0.2" 2024-01-16
OpenJDK Runtime Environment (build 21.0.2+13-58)
OpenJDK 64-Bit Server VM (build 21.0.2+13-58, mixed mode, sharing)
```

## 问题

1. 提示 GLIBC 版本低

```shell
java --version
Error: dl failure on line 541
Error: failed /home/admin/jdk-21.0.2/lib/server/libjvm.so, because /lib64/libc.so.6: version `GLIBC_2.6' not found (required by /home/admin/jdk-21.0.2/lib/server/libjvm.so)
```

系统glibc版本过低， 需要更新， 建议直接升级linux版本， 不要继续用centos了
