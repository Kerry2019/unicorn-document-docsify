

## 1 创建spring boot工程
第一步你需要创建一个spring boot的工程，如果你知道怎么创建或者你已经有了一个spring boot的工程，你可以跳过该部分内容。
1. 打开IntelliJ IDEA 点击 `File->New->Project`  选择左边的`Spring Initializr`点击Next
2. 输入`GroupId` 和`ArtifactId`

!> `GroupId`一般以`公司`为单位，一个公司一个GroupId，在项目上就是一个客户一个GroupId，切勿对同一个客户不同项目指定不同的GroupId

!> `ArtifactId`是用来标识应用，如果应用较大，可以用项目名称作为ArtifactId，如果该应用只是一个模块，可以以`项目名称-模块名称`作为ArtifactId

3. 点击`Next`进入附加模块选择界面，选择`Web`模块即可，点击`Finish`完成spring boot的创建

## 2 配置依赖
在 `pom.xml`中引入unicorn的依赖
>pom.xml
```xml
        <dependency>
            <groupId>cn.kerrysmec.mpaas</groupId>
            <artifactId>unicorn</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```
## 3 配置启动类
!>启动类是spring boot程序启动的入口，spring boot是可以独立于容器运行的，我们知道如果一个jar包可以独立运行，需要一个带有`main`函数的主类，在spring boot里启动类就是主类，默认创建的spring boot工程只有一个命名为`项目名称Application`的类，该类就是启动类也就是主类。

如果想要在spring boot程序中使用到`unicorn`，只需要在启动类`xxxApplication.java`上增加以下注解
```java
@EnableUnicorn
```
到此就完成里unicorn所有的配置，总结下，总共就两点
1. 引入依赖
2. 在启动类加上@EnableUnicorn的注解

