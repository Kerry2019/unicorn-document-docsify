不配置log4j2.xml文件，而通过application.properties文件也可以配置，但是选项较少，不过日常的基础功能就已经够了。

> application.properties文件参考：

```pro
logging.config = ＃记录配置文件的位置。例如Logback的`classpath:logback.xml` 
logging.exception-conversion-word =％wEx ＃记录异常时使用的转换字。
logging.file =＃日志文件名称。例如`myapp.log` 
logging.level.* = ＃日志级别严重性映射。例如`logging.level.org.springframework = DEBUG` 
logging.path = ＃日志文件的位置。例如`/var/log` 
logging.pattern.console = ＃输出到控制台的Appender模式。仅支持默认的登录设置。
logging.pattern.file = ＃输出到文件的Appender模式。仅支持默认的登录设置。
logging.pattern.level = ＃日志级别的Appender模式（默认％5p）。仅支持默认的登录设置。
logging.register-shutdown-hook = false＃初始化时为日志系统注册一个关闭钩子
```

> 参数说明

| Spring的配置（可以通过启动是--xxx指定） | 系统属性（可以通过启动是-Dxxx指定） | 注释                                                         |
| --------------------------------------- | ----------------------------------- | ------------------------------------------------------------ |
| `logging.exception-conversion-word`     | `LOG_EXCEPTION_CONVERSION_WORD`     | 记录异常时使用的转换字。                                     |
| `logging.file`                          | `LOG_FILE`                          | 用于默认日志配置（如果已定义）。                             |
| `logging.path`                          | `LOG_PATH`                          | 用于默认日志配置（如果已定义）。                             |
| `logging.pattern.console`               | `CONSOLE_LOG_PATTERN`               | 在控制台上使用的日志模式（stdout）。（仅支持默认的登录设置。） |
| `logging.pattern.file`                  | `FILE_LOG_PATTERN`                  | 在文件中使用的日志模式（如果启用LOG_FILE）。（仅支持默认的登录设置。） |
| `logging.pattern.level`                 | `LOG_LEVEL_PATTERN`                 | 用于呈现日志级别的格式（默认`%5p`）。（仅支持默认的登录设置。） |



> logging.path与logging.file的配置注意事项

- 若只配置logging.path，那么将会在F:\demo文件夹生成一个日志文件为spring.log（ps：该文件名是固定的，不能更改）。如果path路径不存在，会自动创建该文件夹

- 若只配置logging.file，那将会在项目的当前路径下生成一个demo.log日志文件。这里可以使用绝对路径如，会自动在e盘下创建文件夹和相应的日志文件。

  ```
  logging.file=e:\\demo\\demo.log
  ```

- logging.path和logging.file同时配置，不会在这个路径有F:\demo\demo.log日志生成，logging.path和logging.file不会进行叠加（要注意）

- logging.path和logging.file的value都可以是相对路径或者绝对路径

