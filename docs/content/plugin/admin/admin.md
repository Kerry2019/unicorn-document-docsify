

应用程序在通过actuator暴露程序自身信息时，我们就可以通过外界监控服务来监控该应用程序。

最为人所知的监控方案是：Prometheus+Grafana ，prometheus强大的监控功能加上grafana丰富的仪表盘，绝对是大厂的首选方案。

但我这里要介绍的是spring boot admin，虽然和前者相比相形见绌，但核心功能基本都实现了。最关键的原因是，spring boot admin不管是服务端搭建，还是客户端使用，实在是太简单了，原生的spring boot支持，下面讲讲客户端接入方案。



>**1、eureka接入方案**

eureka是注册中心，只要 admin 服务端注册在eureka上，原则上来说，它应该可以获取到所有注册到eureka的客户端程序的ip地址，主动去集成admin客户端。

的确是这样，在打开admin 的web页面后，能看到eureka上的所有服务都被集成进来。但基本都处于离线或失败状态，如果想要集成成功，还需要暴露actuator endpoints，并且引入`spring-boot-admin-starter-client`依赖。

pom.xml

```xml
        <!-- eureka -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            <version>2.0.2.RELEASE</version>
        </dependency>
        <!--actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--admin client-->
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
            <version>2.1.0</version>
        </dependency>
```

application.properties

```properties
#eureka
eureka.instance.prefer-ip-address=true
eureka.client.service-url.defaultZone=http://ip:port/eureka
#actuator
management.endpoints.web.exposure.include=*
```



> 2、**非eureka接入方案**

如果不依赖eureka注册中心，该如何集成spring boot admin的监控呢？每个admin客户端手动的指定admin服务端的地址。

pom.xml

```xml
        <!--actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--admin client-->
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
            <version>2.1.0</version>
        </dependency>
```

application.properties

```properties
#eureka
eureka.instance.prefer-ip-address=true
eureka.client.service-url.defaultZone=http://ip:port/eureka
#actuator
management.endpoints.web.exposure.include=*
#spring boot admin
spring.boot.admin.client.url=http://ip:port
```

