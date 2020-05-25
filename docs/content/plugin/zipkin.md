## 1. zipkin+sleuth 

微服务里面服务之间相互调用很常见，服务调用的监控该如何掌握呢？这里就用到`分布式链路追踪`。相关的产品有cat，zipkin，pinpoint ，skywalking 等，我选用zipkin的原因，还是因为上手简单。

> zipkin

Zipkin 是一个开放源代码分布式的跟踪系统，每个服务向zipkin报告计时数据，zipkin会根据调用关系通过Zipkin UI生成依赖关系图。

Zipkin提供了可插拔数据存储方式：In-Memory、MySql、Cassandra以及Elasticsearch。为了方便在开发环境我直接采用了In-Memory方式进行存储，生产数据量大的情况则推荐使用Elasticsearch。

> sleuth 基本术语

光靠zipkin可不行，真正做微服务追踪的是`spring cloud sleuth`，它在整个分布式系统中能跟踪一个用户请求的过程(包括数据采集，数据传输，数据存储，数据分析，数据可视化)，捕获这些跟踪数据，就能构建微服务的整个调用链的视图，这是调试和监控微服务的关键工具。

SpringCloudSleuth有4个特点

| 特点              | 说明                                                         |
| ----------------- | :----------------------------------------------------------- |
| 提供链路追踪      | 通过sleuth可以很清楚的看出一个请求经过了哪些服务， 可以方便的理清服务局的调用关系 |
| 性能分析          | 通过sleuth可以很方便的看出每个采集请求的耗时， 分析出哪些服务调用比较耗时，当服务调用的耗时 随着请求量的增大而增大时，也可以对服务的扩容提 供一定的提醒作用 |
| 数据分析 优化链路 | 对于频繁地调用一个服务，或者并行地调用等， 可以针对业务做一些优化措施 |
| 可视化            | 对于程序未捕获的异常，可以在zipkpin界面上看到                |

**Span**：基本工作单元，例如，在一个新建的span中发送一个RPC等同于发送一个回应请求给RPC，span通过一个64位ID唯一标识，trace以另一个64位ID表示，span还有其他数据信息，比如摘要、时间戳事件、关键值注释(tags)、span的ID、以及进度ID(通常是IP地址)
 span在不断的启动和停止，同时记录了时间信息，当你创建了一个span，你必须在未来的某个时刻停止它。

**Trace**：一系列spans组成的一个树状结构，例如，如果你正在跑一个分布式大数据工程，你可能需要创建一个trace。

**Annotation**：用来及时记录一个事件的存在，一些核心annotations用来定义一个请求的开始和结束

- cs - Client Sent -客户端发起一个请求，这个annotion描述了这个span的开始
- sr - Server Received -服务端获得请求并准备开始处理它，如果将其sr减去cs时间戳便可得到网络延迟
- ss - Server Sent -注解表明请求处理的完成(当请求返回客户端)，如果ss减去sr时间戳便可得到服务端需要的处理请求时间
- cr - Client Received -表明span的结束，客户端成功接收到服务端的回复，如果cr减去cs时间戳便可得到客户端从服务端获取回复的所有所需时间。



## 2. 集成

服务端很好搭建，如果数据只想放在内存中，在官网下一个zipkin-server-2.xx-exec.jar运行即可。下面讲解客户端的集成。

> pom.xml

```xml
		<!--starter 中集成了zipkin 和 sleuth-->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-zipkin</artifactId>
			<version>2.0.1.RELEASE</version>
		</dependency>
```

> application.properties

```properties
spring.zipkin.base-url=http://ip:port
spring.sleuth.sampler.probability=1
```

然后就没了，是不是更简单。