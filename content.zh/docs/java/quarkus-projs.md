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

## 消息服务器activemq - amqp

建立项目： 使用quarkus 的web向导： https://code.quarkus.io/?e=org.amqphub.quarkus%3Aquarkus-qpid-jms&extension-search=origin:platform%20activemq

按activemq 搜索出 AMQP 1.0JMS Client - Apache Qpid JMS

download zip -> p02-activemq.zip

建立工作文件夹 ： E:\zjh\quarkus-ws

将 p02-activemq.zip 解压到  E:\zjh\quarkus-ws

进入改目录， 启动 vscode 

```shell
E:\zjh\quarkus-ws\p02-activemq>code .
```

默认项目里pom里带的依赖是amqp协议的

```xml
    <dependency>
      <groupId>org.amqphub.quarkus</groupId>
      <artifactId>quarkus-qpid-jms</artifactId>
    </dependency>
```

修改配置文件
E:\zjh\quarkus-ws\p02-activemq\src\main\resources\application.properties

```conf
#config activemq
#必须要amqp协议， 不能是tcp
quarkus.qpid-jms.url=amqp://192.168.1.200:5672
quarkus.qpid-jms.username=admin
quarkus.qpid-jms.password=admin
```

使用qurkusmain 命令行程序做入口， 直接给消息服务器发消息

```java
import io.quarkus.runtime.QuarkusApplication;
import io.quarkus.runtime.annotations.QuarkusMain;
import jakarta.inject.Inject;
import jakarta.jms.ConnectionFactory;
import jakarta.jms.JMSContext;

@QuarkusMain
public class ActivemqMain implements QuarkusApplication {

    @Inject
    ConnectionFactory connectionFactory;

    @Override
    public int run(String... args) throws Exception {
        // 发送消息命令行：
        // 第一个参数是队列名称
        // 第二个参数是内容
        if (args.length < 2) {
            System.out.println("we need que name and content");
            return 1;
        } else {
            for (int i = 0; i < args.length; i++) {
                System.out.println("args " + i + " : " + args[i]);
            }
        }
        try {
            JMSContext jmsContext = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
            jmsContext.createProducer().send(
                    jmsContext.createQueue(args[0]),
                    args[1]);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }

        return 0;

    }

}
```

## 消息服务器activemq - artemis

artemis是activemq的新版本， 有redhat贡献， 目前号称是新一代推荐使用消息系统

下载zip包后解压， 解压， 后直接启动 D:\apache-artemis-2.32.0\bin>.\artemis

可以看到提示页面，

```shell
 create       Create a new broker instance.
```

需要使用create 新增一个 broker 实例

```shell
D:\apache-artemis-2.32.0\bin>.\artemis  create mybroker
Creating ActiveMQ Artemis instance at: D:\apache-artemis-2.32.0\bin\mybroker

--user:
What is the default username?
admin

--password: is mandatory with this configuration:
What is the default password?


--allow-anonymous | --require-login:
Allow anonymous access?, valid values are Y, N, True, False
Y

Auto tuning journal ...
done! Your system can make 0.05 writes per millisecond, your journal-buffer-timeout will be 21004000

You can now start the broker by executing:

   "D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis" run

Or you can setup the broker as Windows service and run it in the background:

   "D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis-service.exe" install
   "D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis-service.exe" start

   To stop the windows service:
      "D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis-service.exe" stop

   To uninstall the windows service
      "D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis-service.exe" uninstall
```

然后启动broker

```shell
"D:\apache-artemis-2.32.0\bin\mybroker\bin\artemis" run

2024-03-19 15:40:26,684 INFO  [org.apache.activemq.artemis] AMQ241004: Artemis Console available at http://localhost:8161/console

```

默认会自动建立web console， 直接访问 ： http://localhost:8161/console

登录admin/admin

重新建立一个项目
https://code.quarkus.io/?g=org.digdog&a=p03-activemq-artemis&j=21&e=org.amqphub.quarkus%3Aquarkus-qpid-jms&extension-search=origin:platform%20artemis

默认仍然是用qpid 客户端， 需要修改下pom 引入 artemis 依赖库

```xml
<?xml version="1.0"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.digdog</groupId>
  <artifactId>p03-activemq-artemis</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <properties>
    <compiler-plugin.version>3.12.1</compiler-plugin.version>
    <maven.compiler.release>21</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <quarkus.platform.artifact-id>quarkus-bom</quarkus.platform.artifact-id>
    <quarkus.platform.group-id>io.quarkus.platform</quarkus.platform.group-id>
    <quarkus.platform.version>3.8.2</quarkus.platform.version>
    
    <surefire-plugin.version>3.2.5</surefire-plugin.version>

    <!-- 跳过测试 -->
    <skipTests>true</skipTests>
    <skipITs>true</skipITs>

    <!-- artemis -->
    <quarkus-artemis.version>3.2.1</quarkus-artemis.version>

  </properties>
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>${quarkus.platform.group-id}</groupId>
        <artifactId>${quarkus.platform.artifact-id}</artifactId>
        <version>${quarkus.platform.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
       <!-- artemis -->
      <dependency>
        <groupId>io.quarkiverse.artemis</groupId>
        <artifactId>quarkus-artemis-bom</artifactId>
        <version>${quarkus-artemis.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>

    </dependencies>
  </dependencyManagement>
  <dependencies>
   <!-- artemis -->
    <dependency>
      <groupId>io.quarkiverse.artemis</groupId>
      <artifactId>quarkus-artemis-jms</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-arc</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>${quarkus.platform.group-id}</groupId>
        <artifactId>quarkus-maven-plugin</artifactId>
        <version>${quarkus.platform.version}</version>
        <extensions>true</extensions>
        <executions>
          <execution>
            <goals>
              <goal>build</goal>
              <goal>generate-code</goal>
              <goal>generate-code-tests</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>${compiler-plugin.version}</version>
        <configuration>
          <compilerArgs>
            <arg>-parameters</arg>
          </compilerArgs>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>${surefire-plugin.version}</version>
        <configuration>
          <systemPropertyVariables>
            <java.util.logging.manager>org.jboss.logmanager.LogManager</java.util.logging.manager>
            <maven.home>${maven.home}</maven.home>
          </systemPropertyVariables>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-failsafe-plugin</artifactId>
        <version>${surefire-plugin.version}</version>
        <executions>
          <execution>
            <goals>
              <goal>integration-test</goal>
              <goal>verify</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <systemPropertyVariables>
            <native.image.path>${project.build.directory}/${project.build.finalName}-runner</native.image.path>
            <java.util.logging.manager>org.jboss.logmanager.LogManager</java.util.logging.manager>
            <maven.home>${maven.home}</maven.home>
          </systemPropertyVariables>
        </configuration>
      </plugin>
    </plugins>
  </build>
  <profiles>
    <profile>
      <id>native</id>
      <activation>
        <property>
          <name>native</name>
        </property>
      </activation>
      <properties>
        <skipITs>false</skipITs>
        <quarkus.package.type>native</quarkus.package.type>
      </properties>
    </profile>
  </profiles>
</project>
```

然后配置文件， 使用artemis的端口

E:\zjh\eclipse202312-ws\dig-java\p03-activemq-artemis\src\main\resources\application.properties

这里的61616端口， 并不是actvivemq classic（老版activemq的端口）不能连老版的， 必须是连新的artemis服务器

```conf
#artemis 端口
quarkus.artemis.url=tcp://localhost:61616
quarkus.artemis.username=admin
quarkus.artemis.password=admin
```

主程序

```java
import io.quarkus.runtime.QuarkusApplication;
import io.quarkus.runtime.annotations.QuarkusMain;
import jakarta.inject.Inject;
import jakarta.jms.ConnectionFactory;
import jakarta.jms.JMSContext;

@QuarkusMain
public class ArtemisMain implements QuarkusApplication {

    @Inject
    ConnectionFactory connectionFactory;

    @Override
    public int run(String... args) throws Exception {
        //
        if (args.length < 2) {
            System.out.println("need 2 args");
            return 1;
        } else {
            for (int i = 0; i < args.length; i++) {
                System.out.println("args: " + i + "-> " + args[i]);
            }
        }

        try {
            JMSContext jmsContext = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
            jmsContext.createProducer().send(jmsContext.createQueue(args[0]), args[1]);
        } catch (Exception e) {
            System.out.println(e.getMessage());
            return 1;
        }

        return 0;
    }

}
```

使用quarkus build 打包

```shell
quarkus build
```

使用quarkus-run 执行， p1p 是队列名， p2p是消息

```shell
java -jar .\target\quarkus-app\quarkus-run.jar p1p p2p
```

执行结果类似

```shell
2024-03-19 15:43:37,343 INFO  [io.quarkus] (main) p03-activemq-artemis 1.0.0-SNAPSHOT on JVM (powered by Quarkus 3.8.2) started in 0.664s.
2024-03-19 15:43:37,349 INFO  [io.quarkus] (main) Profile prod activated. 
2024-03-19 15:43:37,349 INFO  [io.quarkus] (main) Installed features: [artemis-jms, cdi]
args: 0-> p1p
args: 1-> p2p
2024-03-19 15:43:37,842 INFO  [io.quarkus] (main) p03-activemq-artemis stopped in 0.005s
```

可以通过web concole页面查看artemis里收到了消息

http://localhost:8161/console/artemis/artemisBrowseQueue?tab=artemis&nid=root-org.apache.activemq.artemis-0.0.0.0-addresses-p1p-queues-anycast-p1p

## 消息服务器activemq - classic - openwire- 61616

结束， 不学quarkus了， 还是继续用spring boot吧。
