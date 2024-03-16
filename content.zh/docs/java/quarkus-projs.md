---
title: Quarkus projects
weight: 2
---

Quarkus 是Red hat 主导的Java 框架， 主打 云原生， 号称提供更小包体积， 更快的启动速度， 由此给Java生态在云技术当道的现在一个新的选择。

安装Quarkus， windows 10，  使用choco 命令行直接安装

```shell
choco install quarkus
```

安装完后可以查看版本

```shell
quarkus --version
3.8.2
```

后续如果有升级，可以用Choco直接升

```shell
choco upgrade quarkus
```

## 命令行程序

项目初始化： 使用quarkus的命令行工具

```shell
#建立项目 org.acme:command-mode-quickstart， --no-code 表示是一个空项目
quarkus create app org.acme:command-mode-quickstart --no-code
#进入项目目录
cd command-mode-quickstart
#打开编辑器， 这里用vscode
code .
```

VSCode有一个很好用的插件Quarkus, 安装一下

右键项目 src， 新建 Java 类 HelloWorldMain

```java
import io.quarkus.runtime.QuarkusApplication;
import io.quarkus.runtime.annotations.QuarkusMain;

@QuarkusMain
public class HelloWorldMain implements QuarkusApplication {

    @Override
    public int run(String... args) throws Exception {
        // TODO Auto-generated method stub
        // throw new UnsupportedOperationException("Unimplemented method 'run'");
        if (args.length > 0) {
            for (int i = 0; i < args.length; i++) {
                System.out.println("args : " + i + "->" + args[i]);
            }

        }

        return 0;
    }

}
```

quarkus 提供了开发模式

```shell
quarkus dev
```

可以进入调试页面， 一个简单的命令行页面， 目测没啥用

打包程序用

```shell
quarkus build
```

这个命令会在 target目录下打包出一个jar文件， 但是不能直接运行， 能直接运行的在
target\quarkus-app

这个目录是quarkus为了云特意设计的执行包， 主要是将依赖剥离出来， 可以获得更快的编译速度， 启动时间。
执行jar

```shell
java -jar target\quarkus-app\quarkus-run.jar
```
