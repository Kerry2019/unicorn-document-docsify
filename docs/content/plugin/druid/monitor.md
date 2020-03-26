开启监控，只需要基于之前的配置文件基础，增加下面的配置：

```properties
# WebStatFilter配置
spring.datasource.druid.web-stat-filter.enabled= 
spring.datasource.druid.web-stat-filter.url-pattern=
spring.datasource.druid.web-stat-filter.exclusions=
spring.datasource.druid.web-stat-filter.session-stat-enable=
spring.datasource.druid.web-stat-filter.session-stat-max-count=
spring.datasource.druid.web-stat-filter.principal-session-name=
spring.datasource.druid.web-stat-filter.principal-cookie-name=
spring.datasource.druid.web-stat-filter.profile-enable=

# StatViewServlet配置
spring.datasource.druid.stat-view-servlet.enabled=
spring.datasource.druid.stat-view-servlet.url-pattern=
spring.datasource.druid.stat-view-servlet.reset-enable=
spring.datasource.druid.stat-view-servlet.login-username=
spring.datasource.druid.stat-view-servlet.login-password=
spring.datasource.druid.stat-view-servlet.allow=
spring.datasource.druid.stat-view-servlet.deny=

# Spring监控配置
spring.datasource.druid.aop-patterns= 
```



| 配置                                   | 缺省值 | 说明                                                        |
| -------------------------------------- | ------ | ----------------------------------------------------------- |
| web-stat-filter.enabled                | false  | 是否启用StatFilter                                          |
| web-stat-filter.url-pattern            |        | 过滤器生效路径                                              |
| web-stat-filter.exclusions             |        | 过滤忽略的url                                               |
| web-stat-filter.session-stat-enable    |        | 是否开启session监控                                         |
| web-stat-filter.session-stat-max-count |        | 监控session最大数量                                         |
| web-stat-filter.principal-session-name |        | 在session中能识别用户的sessionName                          |
| web-stat-filter.principal-cookie-name  |        | 在cookie中能识别用户的cookieName                            |
| web-stat-filter.profile-enable         |        | 是否启用监控单个Url调用的sql列表                            |
| stat-view-servlet.enabled              | false  | 是否启用StatViewServlet（监控页面）                         |
| stat-view-servlet.url-pattern          |        | 访问页面                                                    |
| stat-view-servlet.reset-enable         |        | 是否允许手动重置监控数据                                    |
| stat-view-servlet.login-username       |        | 监控页登录账号                                              |
| stat-view-servlet.login-password       |        | 监控页登录密码                                              |
| stat-view-servlet.allow                | *      | 访问监控页白名单                                            |
| stat-view-servlet.deny                 |        | 访问监控页黑名单                                            |
| aop-patterns                           |        | Spring监控AOP切入点，如x.y.z.service.*,配置多个英文逗号分隔 |

当我们都配置开启了监控，并且 `stat-view-servlet.url-pattern=/druid/*`，此时项目的Druid监控页面地址是：http://ip:port/druid 。在输入账号/密码成功登录后，监控页面主要包含下面的模块：

1. **数据源**：显示数据源和连接池的基本信息。
2. **SQL监控**： 对执行的MySQL语句进行记录，并记录执行时间、事务次数等。
3. **SQL防火墙** ：对SQL进行预编译，并统计该条SQL的数据指标。
4. **Web应用**：对发布的服务进行监控，统计访问次数，并发数等全局信息
5. **URI监控**：对访问的URI进行统计，记录次数，并发数，执行jdbc数等
6. **Session监控**：对用户请求后保存在服务器端的session进行记录，识别出每个用户访问了多少次数据库等。
7. **Spring监控**（按需配置）：利用aop对各个内容接口的执行时间、jdbc数进行记录。