## 1 前言

对年轻人来说最烦的事情是什么？我认为是做重复的事情。每天在同样的地方，做同样的工作，写同样的代码。毫无新意，而且难有长进，然后一不留神才发现已经老了。

因为实在不喜欢每天写同样枯燥的代码，所以写了一个spring 的依赖包，尽可能的将常用到的功能，封装成框架内的服务。目前框架内功能还比较少，我想只要坚持个一两年，以后的功能应该会越来越丰富。

当然本文档的核心还是后端开发规范，这个小工具是作为可选项，建议你们自己构建自己的工具包。

## 2 pom

在 `pom.xml`中引入unicorn的依赖

>pom.xml

```xml
        <dependency>
            <groupId>cn.kerrysmec.mpaas</groupId>
            <artifactId>unicorn</artifactId>
            <version>0.1.0</version>
        </dependency>
```

## 3 启动类

!>启动类是spring boot程序启动的入口，spring boot是可以独立于容器运行的，我们知道如果一个jar包可以独立运行，需要一个带有`main`函数的主类，在spring boot里启动类就是主类，默认创建的spring boot工程只有一个命名为`项目名称Application`的类，该类就是启动类也就是主类。

如果想要在spring boot程序中使用到`unicorn`，只需要在启动类`xxxApplication.java`上增加以下注解

```java
@EnableUnicorn
```

到此就完成里unicorn所有的配置，总结下，总共就两点

1. **引入依赖**
2. **在启动类加上@EnableUnicorn的注解**

