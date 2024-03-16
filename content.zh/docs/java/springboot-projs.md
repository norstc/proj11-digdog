---
title: Springboot projects
weight: 1
---

## 命令行程序 spring boot console application

编译成一个独立的jar包， 可以在安装jre的机器上用命令行方式运行，比如

```shell
java -jar filename.jar arg1 arg2 arg3
```

建立项目

1. web页面 https://start.spring.io/， 建立完下载，然后导入
2. Eclipse 使用new project -》 spring boot -> spring starter project

主要选择项：

构建工具 maven
jdk ： 21
Spring boot： 3.2.x(这里选的3.2.3)

springboot 提供的依赖全都不需要选

项目打开后， 右键项目 -> run as -> maven build -> clean package

打包出可执行程序： \eclipse202312-ws\dig-java\p01-cmd\target\p01-cmd-0.0.1-SNAPSHOT.jar

进入该目录， 使用命令行执行

```shell
java -jar p01-cmd-0.0.1-SNAPSHOT.jar
```

spring boot的启动流程：

```xml
Class that can be used to bootstrap and launch a Spring application from a Java mainmethod. By default class will perform the following steps to bootstrap yourapplication: 
•Create an appropriate ApplicationContext instance (depending on yourclasspath)
•Register a CommandLinePropertySource to expose command line arguments asSpring properties
•Refresh the application context, loading all singleton beans
•Trigger any CommandLineRunner beans

```

implements CommandLineRunner

为啥用CommandLineRunner？

springboot是一个框架，内置很多初始化的东西， 使用 CommandLineRunner 可以确保springboot 把其他设置都初始化完，比如数据初始化， 建立数据库连接， 执行校验等

CommandLineRunner 是一个interface， 实现它时需要实现run方法，有三种方法使用CommandLineRunner

1. 主程序 implement

    ```java
    package com.digdog.p01cmd;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class P01CmdApplication implements CommandLineRunner{

        private static Logger LOG = LoggerFactory.getLogger(P01CmdApplication.class);
        public static void main(String[] args) {
            LOG.info("starting application");
            SpringApplication.run(P01CmdApplication.class, args);
            for (int i = 0 ; i < args.length; i++) {
                LOG.info("args[{}]: {}", i, args[i]);
            }
            LOG.info("Application finished");
        }
        @Override
        public void run(String... args) throws Exception {
            // TODO Auto-generated method stub
            LOG.info("runner started");
            for (int i = 0 ; i < args.length; i++) {
                LOG.info("runners args[{}]: {}", i, args[i]);
            }
            LOG.info("Runner finished");
        }

    }
    ```

2. 使用component

    ```java
    package com.digdog.p01cmd;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.stereotype.Component;

    @Component
    public class MyCommandLineRunner implements CommandLineRunner{

        private static Logger LOG = LoggerFactory.getLogger(MyCommandLineRunner.class);
        
        @Override
        public void run(String... args) throws Exception {
            // TODO Auto-generated method stub
            LOG.info("use CommandLineRunner as a Component");
            for (int i = 0 ; i < args.length; i++) {
                LOG.info("MyCommandLineRunner args[{}]: {}", i, args[i]);
            }
        }

    }
    ```

3. 使用bean

    ```java
    package com.digdog.p01cmd;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.core.annotation.Order;

    @Order(value=2)
    public class CommandLineRunnerBean implements CommandLineRunner{

        private static Logger LOG = LoggerFactory.getLogger(CommandLineRunnerBean.class);
        
        @Override
        public void run(String... args) throws Exception {
            // TODO Auto-generated method stub
            LOG.info("use CommandLineRunner as a bean");
            for (int i = 0 ; i < args.length; i++) {
                LOG.info("Bean args[{}]: {}", i, args[i]);
            }
        }

    }

    ```

默认执行顺序是 component， bean， 主程序， 当有多个 CommandLineRunner impletation 时，可以用 @Order(value=2) 确定执行顺序

CommandLineRunner异常处理：

run方法遇到异常会直接抛出， 所以应该尽量在run 方法内捕获并处理异常， 否则会直接导致程序死掉。处理方法有两种

1. 传统的try/catch

    ```java
    @Override
    public void run(String... args) throws Exception {
        // TODO Auto-generated method stub
        LOG.info("runner started");
        for (int i = 0 ; i < args.length; i++) {
            LOG.info("runners args[{}]: {}", i, args[i]);
        }
        try {
            LOG.info("error: {}", args[66]);
        }
        catch(Exception e) {
            LOG.info("error: {}",e.getMessage());
        }
        
        LOG.info("Runner finished");
    }
    ```

2. 使用spring 提供的ExitCodeGenerator， springboot 会自动判断是否有ExitCodeGenerator, 如果有， 那么遇到异常时会自动执行ExitCodeGenerator

```java

```

SpringBoot 还提供了一个 ApplicationRunner，

```java
/*
 * Copyright 2012-2023 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot;

import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;

/**
 * Interface used to indicate that a bean should <em>run</em> when it is contained within
 * a {@link SpringApplication}. Multiple {@link ApplicationRunner} beans can be defined
 * within the same application context and can be ordered using the {@link Ordered}
 * interface or {@link Order @Order} annotation.
 *
 * @author Phillip Webb
 * @since 1.3.0
 * @see CommandLineRunner
 */
@FunctionalInterface
public interface ApplicationRunner extends Runner {

	/**
	 * Callback used to run the bean.
	 * @param args incoming application arguments
	 * @throws Exception on error
	 */
	void run(ApplicationArguments args) throws Exception;

}

```

也是继承自 Runner 接口

```java
/*
 * Copyright 2012-2023 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot;

/**
 * Marker interface for runners.
 *
 * @author Tadaya Tsuyukubo
 * @see ApplicationRunner
 * @see CommandLineRunner
 */
interface Runner {

}
```

## Web程序- REST API

## 数据库程序 - MySQL

## 消息服务器ActiveMQ
