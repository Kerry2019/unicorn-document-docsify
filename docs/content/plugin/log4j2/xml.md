log的配置有两种方式，一种是通过xml文件来配置，另一种是通过application的配置文件。我个人偏向于application配置文件，因为实在不想再回到spring mvc的恐惧。本段先讲xml的配置，下段再讲配置文件。

简单来说是在classpath目录下创建log4j2.xml（命名无要求），用于配置日志的输出规则，然后在配置文件中指定xml文件路径。



> log4j2.xml缺省默认配置文件:

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <Configuration status="WARN">
   <Appenders>
     <Console name="Console" target="SYSTEM_OUT">
       <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
     </Console>
   </Appenders>
   <Loggers>
     <Root level="error">
       <AppenderRef ref="Console"/>
     </Root>
   </Loggers>
 </Configuration>
```

> 1、根节点Configuration

有两个属性:status和monitorinterval,有两个子节点:Appenders和Loggers(表明可以定义多个Appender和Logger).

* status:用来指定log4j本身的打印日志的级别.

* monitorinterval:用于指定log4j自动重新配置的监测间隔时间，单位是s,最小是5s.

> 2、Appenders节点

常见的有三种子节点:Console、RollingFile、File。

1. console:节点用来定义输出到控制台的Appender.

   1.1. name:指定Appender的名字.

   1.2. target:SYSTEM_OUT 或 SYSTEM_ERR,一般只设置默认:SYSTEM_OUT

   1.3. PatternLayout:输出格式，不设置默认为:%m%n.

2. File:节点用来定义输出到指定位置的文件的Appender.

　　2.1. name:指定Appender的名字.

　　2.2. fileName:指定输出日志的目的文件带全路径的文件名.

　　2.3. PatternLayout:输出格式，不设置默认为:%m%n.

3. RollingFile:节点用来定义超过指定大小自动删除旧的创建新的的Appender.

　　3.1. name:指定Appender的名字.

　　3.2. fileName:指定输出日志的目的文件带全路径的文件名.

　　3.3. PatternLayout:输出格式，不设置默认为:%m%n.

　　3.4. filePattern:指定新建日志文件的名称格式.

　　3.5. Policies:指定滚动日志的策略，就是什么时候进行新建日志文件输出日志.

　　3.6. TimeBasedTriggeringPolicy:Policies子节点，基于时间的滚动策略，interval属性用来指定多久滚动一次，默认是1 hour。modulate=true用来调整时间：比如现在是早上3am，interval是4，那么第一次滚动是在4am，接着是8am，12am...而不是7am.

　　3.7. SizeBasedTriggeringPolicy:Policies子节点，基于指定文件大小的滚动策略，size属性用来定义每个日志文件的大小.

　　3.8. DefaultRolloverStrategy:用来指定同一个文件夹下最多有几个日志文件时开始删除最旧的，创建新的(通过max属性)。

> 3、Loggers节点

常见的有两种:Root和Logger.

1. Root：节点用来指定项目的根日志，如果没有单独指定Logger，那么就会默认使用该Root日志输出

　　1.1. level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF.

　　1.2. AppenderRef：Root的子节点，用来指定该日志输出到哪个Appender.

2. Logger：节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。

　　2.1. level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF.

　　2.2. name:用来指定该Logger所适用的类或者类所在的包全路径,继承自Root节点.

　　2.3. AppenderRef：Logger的子节点，用来指定该日志输出到哪个Appender,如果没有指定，就会默认继承自Root.如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。

> 4、关于日志level.

共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF.

* All:最低等级的，用于打开所有日志记录.

* Trace:是追踪，就是程序推进以下，你就可以写个trace输出，所以trace应该会特别多，不过没关系，我们可以设置最低日志级别不让他输出.

* Debug:指出细粒度信息事件对调试应用程序是非常有帮助的.

* Info:消息在粗粒度级别上突出强调应用程序的运行过程.

* Warn:输出警告及warn以下级别的日志.

* Error:输出错误信息日志.

* Fatal:输出每个严重的错误事件将会导致应用程序的退出的日志.

* OFF:最高等级的，用于关闭所有日志记录.

**程序会打印高于或等于所设置级别的日志，设置的日志等级越高，打印出来的日志就越少**。



> 比较完整的log4j2.xml配置模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
 <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
 <!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
 <!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
 <configuration status="WARN" monitorInterval="30">
     <!--先定义所有的appender-->
     <appenders>
     <!--这个输出控制台的配置-->
         <console name="Console" target="SYSTEM_OUT">
         <!--输出日志的格式-->
             <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
         </console>
     <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用-->
     <File name="log" fileName="log/test.log" append="false">
        <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
     </File>
     <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
         <RollingFile name="RollingFileInfo" fileName="${sys:user.home}/logs/info.log"
                      filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">
             <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
             <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
             <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
             <Policies>
                 <TimeBasedTriggeringPolicy/>
                 <SizeBasedTriggeringPolicy size="100 MB"/>
             </Policies>
         </RollingFile>
         <RollingFile name="RollingFileWarn" fileName="${sys:user.home}/logs/warn.log"
                      filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log">
             <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
             <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
             <Policies>
                 <TimeBasedTriggeringPolicy/>
                 <SizeBasedTriggeringPolicy size="100 MB"/>
             </Policies>
         <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件，这里设置了20 -->
             <DefaultRolloverStrategy max="20"/>
         </RollingFile>
         <RollingFile name="RollingFileError" fileName="${sys:user.home}/logs/error.log"
                      filePattern="${sys:user.home}/logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log">
             <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
             <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
             <Policies>
                 <TimeBasedTriggeringPolicy/>
                 <SizeBasedTriggeringPolicy size="100 MB"/>
             </Policies>
         </RollingFile>
     </appenders>
     <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
     <loggers>
         <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
         <logger name="org.springframework" level="INFO"></logger>
         <logger name="org.mybatis" level="INFO"></logger>
         <root level="all">
             <appender-ref ref="Console"/>
             <appender-ref ref="RollingFileInfo"/>
             <appender-ref ref="RollingFileWarn"/>
             <appender-ref ref="RollingFileError"/>
         </root>
     </loggers>
 </configuration>
```

