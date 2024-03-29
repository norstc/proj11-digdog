---
title: "Eclipse"
weight: 10
---

## 安装

下载压缩包后直接解压就行了

Eclipse IDE for Enterprise Java and Web Developers

## 当前版本

基于JDK 21

2024、3、29： eclipse-jee-2023-12-R-win32-x86_64.zip

查看老工程的eclipse版本： 工作空间路径\.metadata\version.ini

## 设置

ssh（git 拉取代码必须）

第一次启动后， 直接import git 项目会提示ssh错误
先配置ssh
Windows -> preferences -> ssh2 -> key management -> load existing key -> 

选择  C:\Users\用户名\.ssh\id_rsa

点击 apply and  close

encode（编码，必须）

## 从本地仓库拉取代码

Windows 10， 配置ssh

C:\Users\账号名\.ssh

新建一个  config 文件
内容如下

```conf
KexAlgorithms +diffie-hellman-group1-sha1
HostKeyAlgorithms +ssh-rsa
PubkeyAcceptedKeyTypes +ssh-rsa
```

## 小经验

### 编辑器里中文后如果有标点符号，字体变形

默认的字体不支持中文符号

windows -> preferences -> general -> appereance -> colors and fonts --> basic -> text font -> edit
选择 微软雅黑 字体，

### junit 只执行某个 test

方法 1: 鼠标单击希望执行的函数名, 选中该函数, 右键菜单, run as -> junit test

方法 2: 使用 outline, 在函数列表中, 点击某个函数, 右键菜单, run as -> junit test

方法 3: 利用 ctrl+o 快捷键, 打开弹窗式 outline, 选中某个函数, 然后右键菜单, run as -> junit test

### 安装 spring tools

Help -> Eclipse market -> 搜索 spring tools -> 安装 spring tools 4 4.13.1.RELEASE （ 这个需要升级eclipse 才能安装）
缺省eclipse 2020-06版本 只会安装Spring tools 3 3.9.15

Spring Tools 3 (Standalone Edition)

Attention - End of life: The Spring Tools 3 are no longer maintained and no longer updated for newer Eclipse versions. Please upgrade to Spring Tools 4 for Eclipse.

The Spring Tools 3 contain the previous generation Spring tooling for Eclipse, mostly focused on working with Spring apps configured using XML and providing validation support for various additional Spring projects, like Spring AOP, Spring Webflow, Spring Integration, etc.

This cannot be installed in addition to the new Spring Tools 4.

### 在 eclipse 里查看 SpringSecurityFilterChain

进入 debug 页面
执行 ctx.getBean(DefaultSecurityFilterChain.class), 可以看到 15 个 filter

### 文件过滤器

左边栏 Project Explorer 右边 漏斗标记 Filters and customization

去掉 Pre-set filters -> .* resources 的勾

可以显示默认隐藏的 .gitignore 文件

### eclipse不能自动识别注解 @Slf4j

@Slf4j可以避免在类中反复写

```java
private final Logger logger = LoggerFactory.getLogger(当前类名.class);
```

但是eclipse 无法自动识别@Slf4j

先找到maven中依赖库的位置

用户名\.m2\repository\org\projectlombok\lombok\1.18.30

命令行进入该目录

执行

```shell
用户名\.m2\repository\org\projectlombok\lombok\1.18.30>java -jar lombok-1.18.30.jar
```

会显示一个安装界面,会自动找到有效的eclipse路径, 如果没找到就手动选择,

直接install即可, 提示安装成功后

退出eclispe, 重新打开eclipse进入项目,

project-> clean

### windows系统中安装多个版本的jdk

分目录安装
比如
d:/jdk8
d:/jdk21

然后在系统环境变量path中增加两个目录的bin目录

注意， 这两个目录的顺序， 靠前的生效，

如果需要切换jdk版本就把两个目录的顺序调整一下即可
