## 1 model层

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

## 2 controller层

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





