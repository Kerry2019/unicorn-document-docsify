

以一个简单项目为例，带大家上手使用，ORM框架使用的是Spring Data JPA。

## 1 SQL脚本准备
> 创建table
``` sql
create table JPA_USER
(
  id                NUMBER not null,
  username          VARCHAR2(50),
  chinese_name      VARCHAR2(100),
  email             VARCHAR2(50),
  birthday          DATE,
  department_id     NUMBER,
  object_version    NUMBER not null,
  created_by        VARCHAR2(50),
  created_date      DATE,
  last_updated_by   VARCHAR2(50),
  last_updated_date DATE
)
```
> 创建sequence
``` sql
create sequence JPA_USER_S
minvalue 1
maxvalue 9999999999999999999999999999
start with 1
increment by 1
cache 20;
```

## 2 引入pom依赖

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
			<groupId>com.smec.mpaas</groupId>
			<artifactId>unicorn</artifactId>
			<version>0.0.1-SNAPSHOT</version>
		</dependency>
		<!--lombok（@Slf4j/@Getter/@Setter等）-->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.16.20</version>
		</dependency>
```

## 3 创建pojo类
针对 jpa_user 表，创建pojo类。
> JpaUser.java
```java
@Entity
@Table(name = "jpa_user")
@EntityListeners(AuditingEntityListener.class)
@Data
public class JpaUser {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE,generator = "jpa_user_s")
    @SequenceGenerator(name = "jpa_user_s",sequenceName = "jpa_user_s",allocationSize = 1)
    private long id;

    @Column(name = "username")
    private String username;

    @Column(name = "chinese_name")
    private String chineseName;

    private String email;

    /**
     * 网上说 1、@DateTimeFormat用于入参格式化；2、@JsonFormat用于出参格式化
     * 但实践后发现当前版本 @JsonFormat 支持入参和出参格式化
     * 而且当和 @DateTimeFormat 同时存在时，@JsonFormat 入参格式化的优先级更高
     * 建议！！！只用 @JsonFormat 一个注解
     */
    @JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")
    private Date birthday;

    @Column(name = "department_id")
    private long departmentId;

    /**
     * 是javax.persistence.Version，不是 org.springframework.data.annotation.Version
     */
    @Version
    private long objectVersion;

    @CreatedBy
    @Column(name = "created_by")
    private String createdBy;

    @CreatedDate
    @Column(name = "created_date")
    @JsonFormat(pattern="yyyy.MM.dd HH:mm:ss",timezone = "GMT+8")
    private Date createdDate;

    @LastModifiedBy
    @Column(name = "last_updated_by")
    private String lastUpdatedBy;

    @LastModifiedDate
    @Column(name = "last_updated_date")
    @JsonFormat(pattern="yyyy.MM.dd HH:mm:ss",timezone = "GMT+8")
    private Date lastUpdatedDate;
    
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


## 4 创建dao类

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

## 5 创建controller类
创建一个UserController，有增删查三个接口，有两个地方用到Unicorn规范的。
1. Response（com.smec.mpaas.unicorn.pojo），统一响应类。
2. MPaasBusinessException（com.smec.mpaas.unicorn.exception），统一业务异常类。

>UserController.java
```java
@RestController
@RequestMapping(("/user"))
public class UserController {
    @Autowired
    private JpaUserDao jpaUserDao;

    /**
     * 查询所有用户
     * @return
     */
    @GetMapping("/findAllUser")
    public Response findAllUser()
    {
        return Response.ok().data(jpaUserDao.findAll());
    }

    /**
     * 新增用户
     * @param jpaUser
     * @return
     */
    @PostMapping("/addUser")
    public Response addUser(@RequestBody JpaUser jpaUser){
        if(jpaUserDao.countByUsername(jpaUser.getUsername())>0){
            throw new MPaasBusinessException(jpaUser.getUsername()+"账号已存在");
        }
        jpaUserDao.save(jpaUser);
        return Response.ok();
    }

    /**
     * 根据 账号username 删除用户
     * @param username
     * @return
     */
    @GetMapping("/deleteUserByUsername/{username}")
    @Transactional
    public Response deleteUserByUsername(@PathVariable("username")String username){
        jpaUserDao.deleteByUsername(username);
        return Response.ok();
    }

}
```

## 6 application文件
我只希望查询的接口允许匿名用户访问，其他的都需要经过三菱的ADFS的token验证，如下配置。
>application.properties
```properties
server.port=8002
#数据源
spring.datasource.url=数据库连接
spring.datasource.username=用户
spring.datasource.password=密码
spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver
#jpa
spring.jpa.database=oracle
spring.jpa.show-sql=true
#接口安全
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=adfs
mpaas.unicorn.security.header-name=token
mpaas.unicorn.security.public-route=/user/findAllUser
```
## 7 启动并测试

1、调用`新增用户`接口（携带token）：
> 请求参数
```json
{
	"username":"Kerry",
	"chineseName":"吴晨瑞",
	"email":"kerry.wu@definesys.com",
	"birthday":"1995-08-18",
	"departmentId":1
}
```
>响应结果
```json
//第一次新增
{
    "code": "ok",
    "errorMsg": null,
    "data": null
}

//第二次新增
{
    "code": "error",
    "errorMsg": "Kerry账号已存在",
    "data": null
}
```
2、调用`新增用户`接口（未携带token）：
> 请求参数
```json
{
	"username":"Kerry",
	"chineseName":"吴晨瑞",
	"email":"kerry.wu@definesys.com",
	"birthday":"1995-08-18",
	"departmentId":1
}
```
>响应结果
```json
{
    "code": "error",
    "errorMsg": "Unauthorized"
}
```

3、调用`查询用户`接口：
>响应结果
```json
{
    "code": "ok",
    "errorMsg": null,
    "data": [
        {
            "id": 41,
            "username": "Kerry",
            "chineseName": "吴晨瑞",
            "email": "kerry.wu@definesys.com",
            "birthday": "1995-08-18",
            "departmentId": 1,
            "objectVersion": 0,
            "createdBy": "Kerry",
            "createdDate": "2020.03.08 18:15:14",
            "lastUpdatedBy": "Kerry",
            "lastUpdatedDate": "2020.03.08 18:15:14",
            "departmentName": null
        }
    ]
}
```

