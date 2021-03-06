关于“熔断”，我想所有人应该都不会感到陌生。2020年是多灾多难的一年，3月份里，我们见证了美股的多次“熔断”，铺天盖地的新闻也让我们了解了这个名词的概念。微服务中的“熔断”同样也很重要，因为微服务大多都彼此关联，一旦某些个服务发生故障，就会导致调用方故障蔓延，造成服务雪崩。这是我们就需要一套合理有效的，服务调用容错解决方案。

大多数人最早接触的Spring Cloud中的熔断器是`Hystrix`，可惜目前的 Hystrix 已经处于维护模式了，从长远来看，处于维护状态的 Hystrix 走下历史舞台只是一个时间问题。而目前Spring Cloud官方建议的替代产品就是我们今天的主角 - `Resilience4j`。resilience 的词义是“快速恢复的能力”，比较契合它的功能，比hystrix“豪猪”好多了。

Resilience4j 是 Spring Cloud Greenwich 版推荐的容错解决方案，它是一个轻量级的容错库，受 Netflix Hystrix 的启发而设计，它专为 Java 8 和函数式编程而设计。Resilience4j 非常轻量级，因为它的库只使用 Vavr （以前称为 Javaslang ），它没有任何其他外部库依赖项。相比之下， Netflix Hystrix 对Archaius 具有编译依赖性，这导致了更多的外部库依赖，例如 Guava 和 Apache Commons 。

而如果使用Resilience4j，你无需引用全部依赖，可以根据自己需要的功能引用相关的模块即可。Resilience4j 提供了一系列增强微服务可用性的功能，主要功能如下：

1. 断路器 `resilience4j-circuitbreaker`：超过故障率的熔断。
2. 限流 `resilience4j-ratelimiter`：指定时间周期内，限制访问次数。
3. 基于信号量的隔离 `resilience4j-bulkhead`：设置最大并发数量。
4. 请求重试 `resilience4j-retry`：针对指定异常，进行重试。
5. 限时 `resilience4j-timelimiter`：限制方法最大执行时长。

关于 Resilience4j的所有组件具体使用说明，请参考[官方文档](https://resilience4j.readme.io/docs/circuitbreaker) 。下文中会以代码为例，来讲解每个组件的使用方式。

还有一种`resilience4j-spring-boot2`的功能，是将Resilience4j的功能打包在一起，给开发人员提供更易于配置的方式使用。在配置文件中申明所需各种功能的自定义Config配置，然后通过注解和aop的方式，在业务代码中使用。但是我不推荐这种方式，因为有bug，而且文档不全，所以文档中就没有写这种方式。更推荐下文中，通过编程式使用各个功能组件，实际需要啥再引用啥。

