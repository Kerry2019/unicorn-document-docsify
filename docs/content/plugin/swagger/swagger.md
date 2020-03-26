## 1 代码即文档
`Springfox Swagger`有可能不是最好的接口文档生成工具，但它让我深刻地接受了`代码即文档`*My Code is Self-Documenting*理念。

作为一名程序员，以前总觉得写代码就应该很纯粹，简洁明了，不喜欢为了个别很少用的功能，写了一堆的类和接口。Spring Boot的项目，我可能不写Service层，把业务代码都写在Controller层；也不会为@RequestBody单独写一个请求类，而是用 Map<String,Object> 来代替。代码写起来很顺畅，但可阅读性不高，后续运维项目的人就需要大费周折了。

另外写完接口之后，不可避免的都要写接口文档，用来描述对外提供的接口服务。我一般都是单独为接口文档写一个markdown文件，并且标注版本号，发给前端或其他开发人员。当每次调整完接口后，都需要升版本号，给这些人重新发一份新的接口文档。

!>这就是一件很头疼的事情：后端开发人员即要维护`代码`，又要维护`接口文档`，而且还要保证二者是同步更新的。

如果`代码能够自动生成接口文档`该多好！这就是`Springfox Swagger`。要求我们在写代码时基于Swagger的规范，在服务发布后会自动生成接口文档。虽说会让代码看起来不再简洁，但省去了维护接口文档，而且Swagger的规范会让我们的代码可阅读性更高。

## 2 快速开始

`Unicorn`里面集成了Springfox Swagger，所以如果想要开启Swagger使用，只需要在`application.properties`中将`mpaas.unicorn.swagger.enable`设为`true`即可。

另外还可以配置当前项目接口文档的标题、描述和当前版本号信息。

```properties
mpaas.unicorn.swagger.enable=true
mpaas.unicorn.swagger.title=接口文档标题
mpaas.unicorn.swagger.description=接口文档描述
mpaas.unicorn.swagger.version=接口文档当前版本号
```

在运行Spring Boot项目后，访问 http://ip:port/swagger-ui.html#/ 就能看到在线的接口文档。

!>在项目上生产环境时，为了防止接口调用暴露，建议将`enable`属性设为`false`，关闭swagger配置。

如果当前项目开启了`接口安全`，Swagger文档将自动配置了Authorized，注入了键为`mpaas.unicorn.security.header-name`的Header参数。

## 3 swagger注解

> `@Api` 

用在类上，说明该类的作用。可以标记一个 Controller 类做为 swagger 文档资源。

| 属性名称       | 备注                                             |
| -------------- | ------------------------------------------------ |
| value          | url的路径值                                      |
| tags           | 如果设置这个值、value的值会被覆盖                |
| description    | 对api资源的描述@Deprecated                       |
| basePath       | 基本路径可以不配置@Deprecated                    |
| position       | 如果配置多个Api 想改变显示的顺序位置@Deprecated  |
| produces       | For example, "application/json, application/xml" |
| consumes       | For example, "application/json, application/xml" |
| protocols      | Possible values: http, https, ws, wss.           |
| authorizations | 高级特性认证时配置                               |

> `@ApiOperation`

用在Controller 类的方法上，说明方法的作用，每一个url资源的定义。

| **属性名称**      | **备注**                                                     |
| ----------------- | ------------------------------------------------------------ |
| value             | url的路径值                                                  |
| tags              | 如果设置这个值、value的值会被覆盖                            |
| description       | 对api资源的描述                                              |
| basePath          | 基本路径可以不配置                                           |
| position          | 如果配置多个Api 想改变显示的顺序位置@Deprecated              |
| produces          | For example, "application/json, application/xml"             |
| consumes          | For example, "application/json, application/xml"             |
| protocols         | Possible values: http, https, ws, wss.                       |
| authorizations    | 高级特性认证时配置                                           |
| hidden            | 配置为true 将在文档中隐藏                                    |
| response          | 方法的返回值的类型class                                      |
| responseContainer | 这些对象是有效的 "List", "Set" or "Map".，其他无效           |
| responseReference | 指定对响应类型的引用。指定的引用可以是本地的，也可以是远程的*将按原样使用，并覆盖任何指定的response()类。 |
| responseHeaders   | 响应旁边提供的可能标题列表。                                 |
| code              | http的状态码 默认 200                                        |
| httpMethod        | "GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" and "PATCH" |
| extensions        | 扩展属性                                                     |
| notes             | 注释说明                                                     |

> `@ApiImplicitParam`

用在`@ApiImplicitParams`注解中，指定一个请求参数的各个方面

| **属性名称**  | **备注**         |
| ------------- | ---------------- |
| paramType     | 参数放在哪个地方 |
| name          | 参数名称         |
| value         | 参数代表的含义   |
| dataType      | 参数类型         |
| dataTypeClass | 参数类型         |
| required      | 是否必要         |
| defaultValue  | 参数的默认值     |

> `@ApiImplicitParams`

只有`value`一个属性，用来装多个`@ApiImplicitParam`。

> `@ApiParam`

用在接收参数中，指定一个请求参数的信息。

| **属性名称** | **备注**                 |
| ------------ | ------------------------ |
| name         | 要绑定到的请求参数的名称 |
| value        | 参数的简单描述           |
| required     | 是否必要                 |
| defaultValue | 参数的默认值             |

> `@ApiModel`

描述一个Model的信息（这种一般用在post创建的时候，使用`@RequestBody`这样的场景，请求参数无法使用 `@ApiImplicitParam`注解进行描述的时候。

| 属性名称      | 备注                                                         |
| ------------- | ------------------------------------------------------------ |
| value         | 名称                                                         |
| description   | 描述                                                         |
| parent        | 父类 （class）                                               |
| discriminator | 官方描述：支持模型继承和多态性。这是用作鉴别器的字段名。基于这个领域，可以断言需要使用哪种子类型。 |
| subTypes      | 子类型 {（class）}                                           |
| reference     | 官方描述：指定对相应类型定义的引用，覆盖指定的任何其他元数据 |

> `@ApiModelProperty`

用在实体类属性上。

| 属性名称 | 备注             |
| -------- | ---------------- |
| value    | 字段说明         |
| name     | 重写属性名       |
| dataType | 重写属性数据类型 |
| example  | 示例说明         |
| hidden   | 是否隐藏         |
| required | 是否必填         |
| notes    | 注释说明         |

> `@ApiResponse`

响应配置的使用方式。

| 属性名称          | 备注                             |
| ----------------- | -------------------------------- |
| code              | http的状态码                     |
| message           | 描述                             |
| response          | 默认响应类 Void                  |
| reference         | 参考ApiOperation中配置           |
| responseHeaders   | 参考 ResponseHeader 属性配置说明 |
| responseContainer | 参考ApiOperation中配置           |

> `@ApiResponses`

只有`value`一个属性，用来装多个`@ApiResponse`。



## 4 示例:model

以一个简单的`JpaUser`类为例

```java
@ApiModel(description = "用户个人信息表")
@Entity
@Table(name = "jpa_user")
@EntityListeners(AuditingEntityListener.class)
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

    @ApiModelProperty(value = "出生日期",example = "1995-08-18")
    @JsonFormat(pattern="yyyy-MM-dd",timezone = "GMT+8")
    private Date birthday;

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

	// getter、setter
	
}
```

## 5 示例:controller

```java
@Api(tags = {"用户操作接口"})
@RestController
@RequestMapping
@ApiResponses({@ApiResponse(code = MPaasBusinessException.STATUS_CODE, message = "自定义业务异常", response = ErrorResponse.class)})
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
    public List<JpaUser> findAllUser() {
        return userService.findAllUser();
    }

    /**
     * 根据用户名查询用户
     *
     * @param username
     * @return
     */
    @ApiOperation(value = "根据用户名查询用户")
    @ApiImplicitParam(paramType = "path", name = "username", value = "用户名", dataType = "string")
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





