



## 1 创建spring boot工程

第一步你需要创建一个spring boot的工程，如果你知道怎么创建或者你已经有了一个spring boot的工程，你可以跳过该部分内容。

1. 打开IntelliJ IDEA 点击 `File->New->Project`  选择左边的`Spring Initializr`点击Next
2. 输入`GroupId` 和`ArtifactId`

!> `GroupId`一般以`公司`为单位，一个公司一个GroupId，在项目上就是一个客户一个GroupId，切勿对同一个客户不同项目指定不同的GroupId

!> `ArtifactId`是用来标识应用，如果应用较大，可以用项目名称作为ArtifactId，如果该应用只是一个模块，可以以`项目名称-模块名称`作为ArtifactId

3. 点击`Next`进入附加模块选择界面，选择`Web`模块即可，点击`Finish`完成spring boot的创建

## 2 pom依赖

除了引入unicorn依赖以外，还要引入jpa框架的依赖和连接oracle数据库的ojdbc依赖，以及辅助开发的lombok。

>pom.xml
```xml
		<!--data-jpa-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
			<version>2.1.1.RELEASE</version>
		</dependency>
		<!--ojdbc-->
		<dependency>
			<groupId>com.oracle</groupId>
			<artifactId>ojdbc6</artifactId>
			<version>6</version>
		</dependency>
		<!--unicorn-->
		<dependency>
			<groupId>cn.kerrysmec.mpaas</groupId>
			<artifactId>unicorn</artifactId>
			<version>0.1.0-SNAPSHOT</version>
		</dependency>
		<!--druid连接池-->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid-spring-boot-starter</artifactId>
			<version>1.1.10</version>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.16.20</version>
		</dependency>
```

## 3 配置文件

application.properties 文件中配置的内容包括：jpa、unicorn、druid连接池和监控等。

```properties
server.port=8002
#jpa
spring.jpa.database=oracle
spring.jpa.show-sql=true
#接口安全
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=simple
mpaas.unicorn.security.header-name=uid
mpaas.unicorn.swagger.enable=true
mpaas.unicorn.swagger.title=测试标题
mpaas.unicorn.swagger.description=测试描述
mpaas.unicorn.swagger.version=1
#数据源
spring.datasource.url={url}
spring.datasource.username={username}
spring.datasource.password={password}
spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver
#druid连接池
spring.datasource.druid.initial-size=15
spring.datasource.druid.max-active=100
spring.datasource.druid.min-idle=15
spring.datasource.druid.max-wait=60000
spring.datasource.druid.time-between-eviction-runs-millis=60000
spring.datasource.druid.min-evictable-idle-time-millis=300000
spring.datasource.druid.test-on-borrow=false
spring.datasource.druid.test-on-return=false
spring.datasource.druid.test-while-idle=true
spring.datasource.druid.validation-query=SELECT 1 FROM DUAL
spring.datasource.druid.validation-query-timeout=1000
spring.datasource.druid.keep-alive=true
spring.datasource.druid.remove-abandoned=true
spring.datasource.druid.remove-abandoned-timeout=180
spring.datasource.druid.log-abandoned=true
spring.datasource.druid.pool-prepared-statements=true
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=20
spring.datasource.druid.filters=stat,wall,slf4j
spring.datasource.druid.use-global-data-source-stat=true
spring.datasource.druid.preparedStatement=false
spring.datasource.druid.maxOpenPreparedStatements=100
spring.datasource.druid.connect-properties.mergeSql=true
spring.datasource.druid.connect-properties.slowSqlMillis=5000
#druid监控
# WebStatFilter配置，说明请参考Druid Wiki，配置_配置WebStatFilter
spring.datasource.druid.web-stat-filter.enabled= true
spring.datasource.druid.web-stat-filter.url-pattern=/*
spring.datasource.druid.web-stat-filter.exclusions=*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*
spring.datasource.druid.web-stat-filter.session-stat-enable=true
spring.datasource.druid.web-stat-filter.session-stat-max-count=1000
# StatViewServlet配置，说明请参考Druid Wiki，配置_StatViewServlet配置
spring.datasource.druid.stat-view-servlet.enabled=true
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*
spring.datasource.druid.stat-view-servlet.reset-enable=false
spring.datasource.druid.stat-view-servlet.login-username=kerry
spring.datasource.druid.stat-view-servlet.login-password=password
```

## 4 pojo类

针对 jpa_user 表，创建pojo类，并加上swagger的相关注解。
> JpaUser.java
```java
@ApiModel(description = "用户个人信息表")
@Entity
@Table(name = "jpa_user")
@EntityListeners(AuditingEntityListener.class)
@Data
public class JpaUser {
    @ApiModelProperty(hidden = true)
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE,generator = "jpa_user_s")
    @SequenceGenerator(name = "jpa_user_s",sequenceName = "jpa_user_s",allocationSize = 1)
    private long id;

    @ApiModelProperty(value = "用户名",example = "Kerry",required = true)
    @Column(name = "username")
    private String username;

    @ApiModelProperty(value = "姓名",example = "吴晨瑞",required = true)
    @Column(name = "chinese_name")
    private String chineseName;

    @ApiModelProperty(value = "邮箱",example = "kerry.wu@definesys.com",required = true)
    private String email;

    /**
     * 网上说 1、@DateTimeFormat用于入参格式化；2、@JsonFormat用于出参格式化
     * 但实践后发现当前版本 @JsonFormat 支持入参和出参格式化
     * 而且当和 @DateTimeFormat 同时存在时，@JsonFormat 入参格式化的优先级更高
     * 建议！！！只用 @JsonFormat 一个注解
     */
    @ApiModelProperty(value = "出生日期",example = "1995-08-18")
    @JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")
    private Date birthday;

    @ApiModelProperty(hidden = true)
    @Column(name = "department_id")
    private long departmentId;
    /**
     * 是javax.persistence.Version，不是 org.springframework.data.annotation.Version
     */
    @ApiModelProperty(hidden = true)
    @Version
    private long objectVersion;

    @ApiModelProperty(hidden = true)
    @CreatedBy
    @Column(name = "created_by")
    private String createdBy;

    @ApiModelProperty(hidden = true)
    @CreatedDate
    @Column(name = "created_date")
    @JsonFormat(pattern="yyyy.MM.dd HH:mm:ss",timezone = "GMT+8")
    private Date createdDate;

    @ApiModelProperty(hidden = true)
    @LastModifiedBy
    @Column(name = "last_updated_by")
    private String lastUpdatedBy;
    
    @ApiModelProperty(hidden = true)
    @LastModifiedDate
    @Column(name = "last_updated_date")
    @JsonFormat(pattern="yyyy.MM.dd HH:mm:ss",timezone = "GMT+8")
    private Date lastUpdatedDate;
    /**
     * @Transient 表示该属性并非数据库的表字段映射，ORM框架将忽略该属性
     * 记住是 javax.persistence.Transient，不是 org.springframework.data.annotation.Transient
     */
    @ApiModelProperty(hidden = true)
    @Transient
    private String departmentName;
}

```


我们可以看到 `@CreatedBy`、`@LastModifiedBy` 这两个注解都是获取当前发起Http请求的用户名，是需要自己实现接口的，我们从unicorn里面获取。

1. 首先需要在启动类增加注解 `@EnableJpaAuditing`。
2. 然后实现AuditorAware接口，`UserProfileThread.getCurrentUser()`方法来自于unicorn依赖包中 `com.smec.mpaas.unicorn.pojo.UserProfileThread` 。


> AuditorConfig.java
```java
@Configuration
public class AuditorConfig implements AuditorAware<String> {

    /**
     * 返回操作员标志信息
     *
     * @return
     */
    @Override
    public Optional<String> getCurrentAuditor() {

        return Optional.of(UserProfileThread.getCurrentUser());
    }

}
```

## 5 dao层

> JpaUserDao.java
```java
public interface JpaUserDao extends JpaRepository<JpaUser,Long> {

    /**
     * 根据 username 删除
     * @param username
     */
    void deleteByUsername(String username);

    /**
     * 根据 username 查询数量
     * @param username
     * @return
     */
    long countByUsername(String username);

}
```

## 6 controller层

建议不要在Controller层实现业务代码，而在Service层来实现。那么Controller层能干嘛呢？映射API的路由，定义API输入输出格式，还有配置Swagger文档。

自从了解到Swagger`代码即文档`的概念，我愈发相信文档也是代码的一部分，很有必要在controller层预留出足够的位置，用来写接口文档。

>UserController.java
```java
@Api(tags = {"用户操作接口"})
@RestController
@RequestMapping("/hr")
@ApiResponses({@ApiResponse(code = RPaasBusinessException.STATUS_CODE, message = "自定义业务异常", response = RErrorResponse.class)})
public class UserController {
    @Autowired
    private UserService userService;

    /**
     * 查询所有用户
     *
     * @return
     */
    @ApiOperation(value = "查询所有用户")
    @ApiResponses(
            value = {
                    @ApiResponse(code = 200, message = "成功", response = JpaUser.class, responseContainer = "List"),
            }
    )
    @GetMapping("/users")
    public List<JpaUser> findAllUser(HttpServletResponse response) {
        return userService.findAllUser();
    }

    /**
     * 根据用户名查询用户
     *
     * @param username
     * @return
     */
    @ApiOperation(value = "根据用户名查询用户")
    @ApiImplicitParam(paramType = "path", name = "username", value = "用户名", dataType = "string",defaultValue = "Kerry")
    @ApiResponses(
            value = {
                    @ApiResponse(code = 200, message = "成功", response = JpaUser.class),
            }
    )
    @GetMapping("/users/{username}")
    public JpaUser findUserByUsername(@PathVariable String username) {

        return userService.findUserByUsername(username);
    }

    /**
     * 新增用户
     *
     * @param jpaUser
     * @return
     */
    @ApiOperation(value = "新增用户", notes = "新增成功后，返回新增用户信息")
    @ApiResponses(
            value = {
                    @ApiResponse(code = 200, message = "成功", response = JpaUser.class),
            }
    )
    @PostMapping("/users")
    public JpaUser addUser(@ApiParam("用户实体对象") @RequestBody JpaUser jpaUser) {
        return userService.addUser(jpaUser);
    }

    /**
     * 根据 账号username 删除用户
     *
     * @param username
     * @return
     */
    @ApiOperation(value = "删除用户", notes = "删除成功后，返回空字符串")
    @ApiResponses(
            value = {
                    @ApiResponse(code = 200, message = "成功"),
            }
    )
    @DeleteMapping("/users/{username}")
    public void deleteUserByUsername(@PathVariable("username") String username) {
        userService.deleteUserByUsername(username);
    }

    /**
     * 根据 账号username 更新用户
     *
     * @param username
     * @return
     */
    @ApiOperation(value = "更新用户部分信息", notes = "更新成功后，返回新的用户信息")
    @ApiResponses(
            value = {
                    @ApiResponse(code = 200, message = "成功", response = JpaUser.class),
            }
    )
    @PatchMapping("/users/{username}")
    public JpaUser updateUserByUsername(@PathVariable("username") String username, @RequestBody JpaUser patchUser) throws IllegalAccessException {
        return userService.updateUserByUsername(username, patchUser);
    }

}
```

## 7 service层

上文说过了，service层主要用来实现controller层中的业务代码，那就直接上代码吧。
>UserService.java
```java
@Service
public class UserService {
    @Autowired
    private JpaUserDao jpaUserDao;

    /**
     * 查询所有用户
     * @return
     */
    public List<JpaUser> findAllUser() {
        return jpaUserDao.findAll();
    }

    /**
     * 查询指定工号的用户
     * @param username
     * @return
     */
    public JpaUser findUserByUsername( String username) {
        return jpaUserDao.findFirstByUsername(username);
    }

    /**
     * 新增用户
     * @param jpaUser
     * @return
     */
    public JpaUser addUser( JpaUser jpaUser) {
        if (jpaUserDao.countByUsername(jpaUser.getUsername()) > 0) {
            throw new RPaasBusinessException(jpaUser.getUsername() + "账号已存在");
        }
        jpaUserDao.save(jpaUser);
        return jpaUser;
    }
    @Transactional
    public void deleteUserByUsername(String username) {
        jpaUserDao.deleteByUsername(username);
    }

    /**
     * 根据工号，更新用户信息
     * @param username
     * @param patchUser
     * @return
     * @throws IllegalAccessException
     */
    public JpaUser updateUserByUsername( String username, JpaUser patchUser) throws IllegalAccessException {
        JpaUser findUser = jpaUserDao.findFirstByUsername(username);
        for (Field field : JpaUser.class.getDeclaredFields()) {
            field.setAccessible(true);
            Optional.ofNullable(field.get(patchUser))
                    .ifPresent(obj -> {
                        try {
                            if (!(obj instanceof Long && Long.valueOf(0).equals(obj))) {
                                field.set(findUser, obj);
                            }
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    });
        }
        jpaUserDao.save(findUser);
        return findUser;
    }

}

```
