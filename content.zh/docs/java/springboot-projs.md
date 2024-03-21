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

## 消息服务器ActiveMQ

建立项目： 使用spring initializr https://start.spring.io/

Maven
java
springboot 3.2.3
jar
jdk21

依赖项选择
Spring for Apache Activemq 5

使用命令行方式，第一个参数是队列名， 第二个参数是文本消息

```java
package com.digdog.p02activemqopenwire;

import org.apache.activemq.ActiveMQConnectionFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.jms.connection.CachingConnectionFactory;
import org.springframework.jms.core.JmsTemplate;
import org.springframework.jms.core.MessageCreator;

import jakarta.jms.JMSException;
import jakarta.jms.Message;
import jakarta.jms.Session;
import jakarta.jms.TextMessage;

@SpringBootApplication
public class P02ActivemqOpenwireApplication implements CommandLineRunner {

 private static Logger LOG = LoggerFactory.getLogger(P02ActivemqOpenwireApplication.class);

 public static void main(String[] args) {
  SpringApplication.run(P02ActivemqOpenwireApplication.class, args);
 }

 @Override
 public void run(String... args) throws Exception {
  if (args.length < 2) {
   LOG.info("need two args");
   return;
  } else {
   LOG.info("started running");
   String queName = args[0];
   String message = args[1];

   LOG.info("que is {}, message is {}", queName, message);

   // 消息服务器地址
   String BROKER_URL = "tcp://192.168.1.200:61616";
   String BROKER_USERNAME = "admin";
   String BROKER_PASSWORD = "admin";

   // ActiveMQConnectionFactory:
   ActiveMQConnectionFactory activeMQConnectionFactory = new ActiveMQConnectionFactory();
   activeMQConnectionFactory.setBrokerURL(BROKER_URL);
   activeMQConnectionFactory.setUserName(BROKER_USERNAME);
   activeMQConnectionFactory.setPassword(BROKER_PASSWORD);

   // CachingConnectionFactory
   // 使用这个后 springboot 会自动管理， 发完消息后，不会自动停止， 需要手动ctrl+c 停止程序
   CachingConnectionFactory cachingConnectionFactory = new CachingConnectionFactory(activeMQConnectionFactory);
   cachingConnectionFactory.setSessionCacheSize(10);

   // JmsTemplate
   JmsTemplate jmsTemplate = new JmsTemplate();
   jmsTemplate.setConnectionFactory(cachingConnectionFactory);

   // MessageCreator
   MessageCreator messageCreator = new MessageCreator() {

    @Override
    public Message createMessage(Session session) throws JMSException {
     TextMessage textMessage = session.createTextMessage(message);
     return textMessage;
    }

   };
   jmsTemplate.setTimeToLive(10);

   // 发送消息
   jmsTemplate.send(queName, messageCreator);
  }

  LOG.info("end running");
 }

}

```

## Web程序

实现一个简单的黑名单IP地址列表页，使用springboot 结合MySQL, themleaf 做一个简单web app

### 数据库 - MySQL

首先是数据库建表，最简单的方式做一个表， 只有两个字段， 第一个id， 自增长， 主键， 第二个 ip地址， unique（去重）

```sql
CREATE TABLE `em_black_ip_ng` (
  `id` int NOT NULL AUTO_INCREMENT,
  `black_ip_ng` varchar(255) CHARACTER SET utf8mb3 COLLATE utf8mb3_unicode_ci NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `black_ip_ng` (`black_ip_ng`)
) ENGINE=InnoDB AUTO_INCREMENT=6867 DEFAULT CHARSET=utf8mb3 COLLATE=utf8mb3_unicode_ci;
```

然后常用的sql语句，增删改查， 先用sql 玩一下

```sql
-- ip查询
-- 查询全部
select * from em_black_ip_ng;
-- 查询指定ip
select * from em_black_ip_ng where black_ip_ng = '82.156.34.174';

-- 新增ng 黑名单ip
-- 注意这里的ignore， 未包含的会新增，包含的会自动跳过
insert ignore into em_black_ip_ng (black_ip_ng) values ('39.144.50.62');

-- 删除 ip
delete from em_black_ip_ng where id = 3307;

-- 修改ip 82.156.34.174
select * from em_black_ip_ng where id = 6859;
update em_black_ip_ng set black_ip_ng = '82.156.34.174' where id = 6859;
```

### 后端java:  Model + Repository + Controller

mysql表大概知道后就可以写java了

使用spring initializr 建立一个项目， 主要选择 web， jdbc，thymeleaf就可以

先是model

```java
package com.stt.euopmock.model;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="em_black_ip_ng")
public class BlackIpNginx {
 @Id
 @Column(name="id")
 @GeneratedValue(strategy=GenerationType.IDENTITY)
 private Long id;
 
 //nginx配置的黑名单ip
 private String blackIpNg;

 public Long getId() {
  return id;
 }

 public void setId(Long id) {
  this.id = id;
 }

 public String getBlackIpNg() {
  return blackIpNg;
 }

 public void setBlackIpNg(String blackIpNg) {
  this.blackIpNg = blackIpNg;
 }

 @Override
 public String toString() {
  return "BlackIpNginx [id=" + id + ", blackIpNg=" + blackIpNg + "]";
 }
 
 

}

```

然后是repository, 这里直接用springboot 内置的的CrudRepository, 简单的查询都是自动翻译， 不需要写sql了

```java
package com.stt.euopmock.repository;

import java.util.List;

import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import com.stt.euopmock.model.BlackIpNginx;

@Repository
public interface BlackIpNginxRepository extends CrudRepository<BlackIpNginx,Long>{
 //查询所有ip， 
 List<BlackIpNginx> findAll();
 
 //查询所有ip， 按id排序，从大到小, 注意第一个by， findall后面先跟一个by才能用orderby
 List<BlackIpNginx> findAllByOrderById();
  

}

```

最后是controller

```java
package com.stt.euopmock.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.stt.euopmock.model.BlackIpNginx;
import com.stt.euopmock.repository.BlackIpNginxRepository;

@Controller
@RequestMapping("/blackipnginx")
public class BlackIpNginxController {
 
 @Autowired
 private BlackIpNginxRepository blackIpNginxRepository;
 
 //打开ip列表页
 @GetMapping("/blackipnginxlist")
 public String getBlackIpNginxList(Model model) {
  List<BlackIpNginx> blackIpNginxList = blackIpNginxRepository.findAllByOrderById();
  model.addAttribute("blackIpNginxList",blackIpNginxList);
  return "blackipnginx/blackipnginxlist";
 }

}
```



### 前端Thymeleaf

前端使用thymeleaf 模板框架， 自动翻译html返回给浏览器

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
 <title>BlackIpNginx</title>
 <meta http-equiv="Refresh" content="600"/>

 <div th:replace="fragments/header :: header-css"></div>

</head>
<body>

 <div th:replace="fragments/header :: header"></div>

 <div class="container">
  <nav class="navbar navbar-expand navbar-dark bg-dark"
   aria-label="Second navbar example">
   <div class="container-fluid">
    <a class="navbar-brand" href="#">BlackIpNginx</a>
    <button class="navbar-toggler" type="button"
     data-bs-toggle="collapse" data-bs-target="#navbarsExample02"
     aria-controls="navbarsExample02" aria-expanded="false"
     aria-label="Toggle navigation">
     <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarsExample02">
     <ul class="navbar-nav me-auto">
      <li class="nav-item"><a class="nav-link"
       aria-current="page" href="/blackipnginx/blackipnginxlist">BlackIpNginxList</a></li>
     </ul>
     <form>
      <input class="form-control" type="text" placeholder="Search"
       aria-label="Search">
     </form>
    </div>
   </div>
  </nav>

  <table class="table table-stripted">
   <thead>
    <tr>
     <th scope="col">行号</th>
     <th scope="col">Ip</th>
     
    </tr>
   </thead>
   <tbody>
    <tr th:each="blackIpNginx:${blackIpNginxList}">
     <th scope="row" th:text="${blackIpNginx.id}"></th>
     <td th:text="${blackIpNginx.blackIpNg}"></td>
    </tr>
   </tbody>
  </table>
 </div>
 <!-- /.container -->

 <div th:replace="fragments/footer :: footer"></div>

</body>
</html>
```
