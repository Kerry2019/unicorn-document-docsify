spring boot admin的web 页面中有关于日志的模块，主要的作用有两个：

* **Logfile日志文件**：实时在线查看应用程序的日志文件。
* **Loggers日志级别**：动态设置应用程序，各个包路径的日志输出级别。

这两个功能的确很实用，我特地把这端从spring boot admin中单独拿出来。

正常来说，日志都输出在linux服务器上，开发人员不可能都有权限访问服务器里的文件。他们需要有`只读权限`，便于他们查看服务器日志，ok！admin上的`Logfile`模块就提供了这个功能。

线上系统的日志输出级别基本都是`info`，但是线上遇到bug时，我们更希望日志级别是`debug`，甚至更低，便于我们分析日志信息。但我们总不能为了看详细日志，手动改一下代码里的日志输出级别，然后重新部署吧。这是我们就可以使用admin的`Loggers`功能，你可以设置程序中任何包路径的日志输出级别，修改完后立即动态生效。

> 开启Logfile

Loggers默认就实现了，不需要特别配置，主要是Logfile要麻烦一些：

1. 应用程序的日志是输出成日志文件的。
2. acuator搜集输出的日志文件信息。

如application.properties

```properties
#日志输出文件路径为./log/  （默认文件名 spring.log）
logging.path=./log
#搜集日志文件信息
management.endpint.logfile.external-file= ./log/spring.log
```



