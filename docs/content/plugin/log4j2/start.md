> log框架优点

还有多少人在打印日志时还在用`System.out.println`？我们平时本地写一些简单代码偶尔使用，但是在实际的项目上，是应该不会让你这么做的。`打印`本身也是一种资源消耗，在生产环境做无意义的日志输出，即是资源的浪费，也会导致日志文件冗余而庞大。所以很多人在本地测试时写了很多的`System.out.println`，到了上线前又一个个注释掉，其实spring boot早就有Log框架来处理这类需求。

log框架就是帮助开发人员进行日志输出管理的API类库。它最重要的特点就可以配置文件灵活的设置日志信息的优先级、日志信息的输出目的地以及日志信息的输出格式。log框架除了可以记录程序运行日志信息外还有一重要的功能就是用来显示调试信息。

当前流行的log框架有`logback`、`log4j`和`log4j2`三种，spring boot 官方默认的log框架是logback，集成在`spring-boot-starter-web`下。但目前性能更好的是log4j2，所以本文以log4j2为例讲解。

log4j2提供了AsyncAppender和AsyncLogger以及全局异步，开启方式如下

- 同步模式：默认配置即为同步模式，即没有使用任何AsyncAppender和AsyncLogger
- 全局异步：配置按照同步方式配，通过添加jvm启动参数即可开启全局异步，无需修改配置和应用
- 混合异步：使用异步Logger和同步Logger的混合配置，且不开启全局异步，即Logger配置中部分AsyncLogger，部分Logger



> 引入log

如果使用默认的`logback`框架，在引入`spring-boot-starter-web`即可，无需其他操作。如果是其他框架，则需要先排查`logback`的依赖包，再进入对应的框架依赖，如下：

```xml
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--log4j2-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
```



> lombok

lombok通常用来解决代码冗余的，同样可以用在log框架中，如果不想每次都写private  final Logger logger = LoggerFactory.getLogger(XXX.class); 可以添加类的注解@Slf4j，然后在类中就可以直接使用log对象。

```xml
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.0</version>
        </dependency>
```

