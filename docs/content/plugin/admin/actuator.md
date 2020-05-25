`actuator`是`spring boot`项目中非常强大一个功能，有助于对应用程序进行监视和管理，通过 `restful api` 请求来监管、审计、收集应用的运行情况，针对微服务而言它是必不可少的一个环节。

`endpoints`是`actuator` 的核心部分，它用来监视应用程序及交互，`spring-boot-actuator`中已经内置了非常多的 `endpoints（health、info、beans、httptrace、shutdown等等）`，同时也允许我们自己扩展自己的端点。

以下是一些非常有用的actuator endpoints列表：

| Endpoint ID    | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| auditevents    | 显示应用暴露的审计事件 (比如认证进入、订单失败)              |
| info           | 显示应用的基本信息                                           |
| health         | 显示应用的健康状态                                           |
| metrics        | 显示应用多样的度量信息                                       |
| loggers        | 显示和修改配置的loggers                                      |
| logfile        | 返回log file中的内容(如果logging.file或者logging.path被设置) |
| httptrace      | 显示HTTP足迹，最近100个HTTP request/repsponse                |
| env            | 显示当前的环境特性                                           |
| flyway         | 显示数据库迁移路径的详细信息                                 |
| liquidbase     | 显示Liquibase 数据库迁移的纤细信息                           |
| shutdown       | 让你逐步关闭应用                                             |
| mappings       | 显示所有的@RequestMapping路径                                |
| scheduledtasks | 显示应用中的调度任务                                         |
| threaddump     | 执行一个线程dump                                             |
| heapdump       | 返回一个GZip压缩的JVM堆dump                                  |

