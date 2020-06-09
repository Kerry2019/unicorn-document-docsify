## 1. 熔断 CircuitBreaker

> pom.xml

```xml
		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-circuitbreaker</artifactId>
			<version>0.13.2</version>
		</dependency>
```

这个库提供了一个基于 ConcurrentHashMap 的 `CircuitBreakerRegistry` ，CircuitBreakerRegistry 是线程安全的，并且是原子操作。开发者可以使用 CircuitBreakerRegistry 来创建和检索 CircuitBreaker 的实例 ，开发者可以直接使用默认的全局`CircuitBreakerConfig` 为所有 CircuitBreaker 实例创建 CircuitBreakerRegistry ，如下所示：

```java
CircuitBreakerRegistry circuitBreakerRegistry = CircuitBreakerRegistry.ofDefaults();
```

当然开发者也可以提供自己的 CircuitBreakerConfig ，然后根据自定义的 CircuitBreakerConfig 来创建一个 CircuitBreakerRegistry 实例，进而创建 CircuitBreaker 实例。

> 示例代码

```java
@RestController
public class CircuitBreakerController {
    /**
     * 1、创建自定义 CircuitBreakerConfig
     */
    CircuitBreakerConfig circuitBreakerConfig = CircuitBreakerConfig
            .custom()
            .failureRateThreshold(20f)
            .waitDurationInOpenState(Duration.ofSeconds(50))
            .ringBufferSizeInHalfOpenState(10)
            .ringBufferSizeInClosedState(10)
            .recordExceptions(RuntimeException.class)
            .ignoreExceptions(IOException.class)
            .enableAutomaticTransitionFromOpenToHalfOpen()
            .build();
    /**
     * 2、创建 CircuitBreakerRegistry
     */
    CircuitBreakerRegistry circuitBreakerRegistry = CircuitBreakerRegistry.of(circuitBreakerConfig);

    CircuitBreaker circuitBreaker2 = CircuitBreaker.ofDefaults("CircuitBreaker2");


    /**
     * 一个熔断器
     *
     * @param number
     * @return
     */
    @GetMapping("/cb-one/{number}")
    public Integer one(@PathVariable("number") Integer number) {
        /**
         * 熔断器
         */
        CircuitBreaker circuitBreaker = circuitBreakerRegistry.circuitBreaker("CircuitBreaker");
        /**
         * 重置熔断器 reset
         */
        if (number == 0) {
            circuitBreaker.reset();
        }
        /**
         * 熔断器装饰
         */
        CheckedFunction0<Integer> decoratedSupplier = CircuitBreaker
                .decorateCheckedSupplier(circuitBreaker, () -> {
                    if (number == 1) {
                        throw new RuntimeException("熔断器 报错！");
                    }
                    return number;
                });
        /**
         * 返回 Try
         */
        Try<Integer> resultTry = Try.of(decoratedSupplier);
        return resultTry.get();
    }

    /**
     * 多个熔断器
     *
     * @param number
     * @return
     */
    @GetMapping("/cb-more/{number}")
    public Integer more(@PathVariable("number") Integer number) {
        CircuitBreaker circuitBreaker1 = circuitBreakerRegistry.circuitBreaker("CircuitBreaker1");

        /**
         * 重置熔断器 reset
         */
        if (number == 0) {
            circuitBreaker1.reset();
            circuitBreaker2.reset();
        }
        /**
         * 熔断器装饰 1
         */
        CheckedFunction0<Integer> decoratedSupplier1 = CircuitBreaker
                .decorateCheckedSupplier(circuitBreaker1, () -> {
                    if (number == 1) {
                        throw new RuntimeException("第一个熔断器 报错！");
                    }
                    return number;
                });
        /**
         * 熔断器装饰 2
         */
        CheckedFunction1<Integer, Integer> decoratedSupplier2 = CircuitBreaker
                .decorateCheckedFunction(circuitBreaker2, (input) -> {
                    if (number == 2) {
                        throw new RuntimeException("第二个熔断器 报错！");
                    }
                    return number;
                });
        /**
         * 装饰者模式 依次执行熔断器1、熔断器2 ...
         * 返回 Try
         */
        Try<Integer> resultTry = Try
                .of(decoratedSupplier1)
                .mapTry(decoratedSupplier2);

        return resultTry.get();
    }
}
```

我们就通过这段代码来讲解知识点吧，本代码中定义了两个接口。

> cb-one  一个熔断器

1、关于 `CircuitBreakerConfig` 的定义为：

- 故障率阈值百分比是20%，超过这个阈值，断路器就会打开；
- 断路器保持打开的时间为50秒，在到达设置的时间之后，断路器会进入到 half open 状态
- 当断路器处于 half open 状态时，环形缓冲区的大小为10；
- 当断路器关闭时，环形缓冲区的大小为10；
- 断路器认定为故障的异常为 RuntimeException ;
- 断路器不认定为故障的异常为 IOException；
- 允许断路器自动由打开状态转换为半开状态 ；

2、如果是自定义，正常创建熔断器对象的过程是 “`CircuitBreakerConfig` -> `CircuitBreakerRegistry`->`CircuitBreaker` ”。

3、因为在Controller中，我们是针对每次请求来访问熔断器，所以`CircuitBreakerConfig` 和`CircuitBreakerRegistry`应当是全局变量，而不能是局部变量。而CircuitBreaker则可以定义在局部方法中。

4、`circuitBreaker.reset()`方法可以重置熔断器的故障统计。



> cb-more 多个熔断器

1、定义了两个熔断器，一个和上文定义的一样，另一个是使用 CircuitBreaker.ofDefaults，因为该方法内部还是会实例CircuitBreakerConfig和CircuitBreakerRegistry，所以该CircuitBreaker只能在全局变量中赋值。所以，如果想要使用`ofDefaults`，建议使用 `CircuitBreakerRegistry.ofDefaults()`。

2、熔断器使用了装饰者模式，开发者可以使用 CircuitBreaker.decorateCheckedSupplier(), CircuitBreaker.decorateCheckedRunnable() 或者 CircuitBreaker.decorateCheckedFunction() 来装饰 Supplier / Runnable / Function 或者 CheckedRunnable / CheckedFunction，然后使用 Try.of(…) 或者 Try.run(…) 来进行调用操作，也可以使用 map、flatMap、filter、recover 或者 andThen 进行链式调用，但是调用这些方法断路器必须处于 CLOSED 或者 HALF_OPEN 状态。

> 熔断器的状态监听

状态监听可以获取到熔断器当前的运行数据，例如：

```java
CircuitBreaker.Metrics metrics = circuitBreaker.getMetrics();
// 获取故障率
float failureRate = metrics.getFailureRate();
// 获取调用失败次数
int failedCalls = metrics.getNumberOfFailedCalls();
```



## 2. 重试 Retry

请求失败重试也是一个常见功能，Resilience4j 中对此也提供了支持，首先引入重试相关依赖：

> pom.xml

```xml
		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-retry</artifactId>
			<version>0.13.2</version>
		</dependency>
```

> 示例代码

```java
@RestController
public class RetryController {
    private static final DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss");
    RetryConfig retryConfig = RetryConfig.<String>custom()
            .maxAttempts(3)
            .retryExceptions(RuntimeException.class)
            .ignoreExceptions(IOException.class)
            .retryOnResult(s -> s.contains("Kerry"))
            .waitDuration(Duration.ofSeconds(3))
            .build();

    /**
     * 重试
     *
     * @param word
     * @return
     */
    @GetMapping("/retry/{word}")
    public String retry(@PathVariable("word") String word) {
        Retry retry = Retry.of("Retry", retryConfig);
        CheckedFunction0<String> decoratedSupplier = Retry
                .decorateCheckedSupplier(retry, () -> {
                    System.out.println("时分秒：" + LocalDateTime.now().format(dateTimeFormatter));
                    return "Hello!" + word;
                });
        Try<String> result = Try.of(decoratedSupplier);
        return result.get();
    }
}
```

继续通过代码来讲解知识点。

1、如果自定义创建Retry实例的话，需要通过“ `RetryConfig`->`Retry` ”。

2、关于`RetryConfig`的定义为：

1. 最大重试次数为3次；
2. 被认定需要重试的异常为 RuntimeException;
3. 被忽略，不需要重试的异常为 IOException;
4. retryOnResult方法传入的是个Predicate，如果返回true则触发重试；
5. 每次重试的间隔为3秒；



## 3. 流控 RateLimiter

> pom.xml

```xml
		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-ratelimiter</artifactId>
			<version>0.13.2</version>
		</dependency>
```

RateLimiter 叫“流控”，即控制在指定时间周期内的最大请求数量。它和CircuitBreaker十分相似，也有一个基于内存的 RateLimiterRegistry 和 RateLimiterConfig 可以配置，同样要求二者是定义为全局变量。

> 示例代码

```java
@RestController
public class RateLimiterController {
    private static final DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss");
    RateLimiterConfig rateLimiterConfig = RateLimiterConfig.custom()
            .limitRefreshPeriod(Duration.ofSeconds(10))
            .limitForPeriod(2)
            .timeoutDuration(Duration.ofSeconds(20))
            .build();
    RateLimiterRegistry rateLimiterRegistry=RateLimiterRegistry.of(rateLimiterConfig);

    @GetMapping("/limiter")
    public String test() {
        RateLimiter rateLimiter = rateLimiterRegistry.rateLimiter("RateLimiter");
        
        CheckedFunction0<String> decoratedSupplier = RateLimiter.decorateCheckedSupplier(rateLimiter,
                () -> "时分秒：" + LocalDateTime.now().format(dateTimeFormatter)
        );
        Try<String> result = Try.of(decoratedSupplier);
        return result.get();
    }
}
```

关于`RateLimiterConfig`的定义为：

1. 流控的时间周期是10秒钟刷新重置；
2. 规定时间周期内，最大请求数量是2次；
3. 超过流控限制后，再进来的请求延迟20秒执行；



## 4. 隔离 Bulkhead

> pom.xml

```xml
		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-bulkhead</artifactId>
			<version>0.13.2</version>
		</dependency>
```



熟悉 Java多线程并发编程的同学，应该对信号量`Semaphore`，有所了解，本段的`Bulkhead`就和信号量定义基本类似，限制某瞬间的请求并发数量。和RateLimiter的区别在于，RateLimiter是指定一段时间周期内的请求，而Bulkhead是瞬间的并发请求。

Bulkhead实例的创建也是和CircuitBreaker和RateLimter一样，通过`BulkheadConfig`和`BulkheadRegistry`来创建。



> 实例代码

```java
@RestController
public class BulkheadController {
    private static final DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss");
    BulkheadConfig bulkheadConfig = BulkheadConfig.custom()
            .maxConcurrentCalls(1)
            .maxWaitTime(10000)
            .build();
    BulkheadRegistry bulkheadRegistry = BulkheadRegistry.of(bulkheadConfig);


    @GetMapping("/bulkhead")
    public String test() {
        Bulkhead bulkhead = bulkheadRegistry.bulkhead("Bulkhead");
        CheckedFunction0<String> decoratedSupplier = Bulkhead.decorateCheckedSupplier(bulkhead, () -> {
            try {
                Thread.sleep(5000);
            } catch (Exception e) {
                e.printStackTrace();
            }
            return "时分秒：" + LocalDateTime.now().format(dateTimeFormatter);
        });
        Try<String> result = Try.of(decoratedSupplier);
        return result.get();
    }
}
```

关于`BulkheadConfig`的定义为：

1. 最大请求并发数为1；
2. 尝试进入饱和态的Bulkhead时，线程的最大阻塞时间为10000毫秒；

## 5. 降级 Fallback

`fallback`和前面讲解的组件不同，它不是组件，只是Resilience4j里面都会用到的方法。不管是熔断、重试、流控还是隔离等，一旦触发的限制规则，都可以降级执行我们定义好的降级方法。还记得前面所有的方法在执行后，返回结果都是什么格式的？`Try`。

Try 有 isFailure() 和 isSuccess() ，返回Boolean值，用来判断 Resilience4j 是否成功。

Try接口有个默认的方法recover，用来实现fallback，它首先判断是不是方法调用失败，如果是才执行fallback方法。例如上文的Bulkhead的代码，可以设置降级时返回错误日志。

```java
		... ...
        Try<String> result = Try.of(decoratedSupplier)
                .recover(throwable -> "错误日志为："+throwable.getMessage());
        return result.get();
```

另外，你还会发现Try的很多方法和Stream基本相似，你可以拿它当Stream流来使用。Resilience4j 真不愧是面向函数编程的最佳改造。

这里只介绍这些常用的，还有时控、缓存、健康监控等等功能，等你们有需要的时候自行查看吧。





