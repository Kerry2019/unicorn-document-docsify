Spring Boot 2.X 版本不再支持配置继承（即所有数据源不能spring.datasource.druid.*的配置 ），多数据源的话每个数据源的所有配置都需要单独配置，否则配置不会生效。应该如下配置：

```properties
# Druid 数据源 1 
...
spring.datasource.druid.one.url=  
spring.datasource.druid.one.username=
spring.datasource.druid.one.password= 
spring.datasource.druid.one.driver-class-name=
spring.datasource.druid.one.max-active=10
spring.datasource.druid.one.max-wait=10000
...

# Druid 数据源 2 
...
spring.datasource.druid.two.url=  
spring.datasource.druid.two.username=
spring.datasource.druid.two.password= 
spring.datasource.druid.two.driver-class-name=
spring.datasource.druid.two.max-active=20
spring.datasource.druid.two.max-wait=20000
```



在 IOC容器中分别注册两个数据源,`@Primary`表示`默认数据源`。

```java
@Primary
@Bean(name="dataSourceOne")
@ConfigurationProperties("spring.datasource.druid.one")
public DataSource dataSourceOne(){
    return DruidDataSourceBuilder.create().build();
}

@Bean(name="dataSourceOne")
@ConfigurationProperties("spring.datasource.druid.two")
public DataSource dataSourceTwo(){
    return DruidDataSourceBuilder.create().build();
}
```



当在dao层连接数据库时，在dao层方法上加上`@Bean(name="数据源名称")`，就能选择指定数据源来执行方法。如果没有加上该注解，则认为使用`默认数据源`。