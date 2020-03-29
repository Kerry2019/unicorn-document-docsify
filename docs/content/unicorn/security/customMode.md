这里称为“标准模式”，实际上只是让引用方写自定义的方法，实现解析用户上下文的接口。主要是为了保证框架的灵活性，因为每个项目的单点登录方式实在各式各样。

> 自定义接入单点登录需要做两个操作：
>
> 1、在配置文件中申明。
>
> 2、实现`com.smec.mpaas.unicorn.adapter.MPaasSSOAuthentication`接口。

application.properties 配置如下：

```properties
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=custom
mpaas.unicorn.security.public-route=公共路由地址
```

MPaasSSOAuthentication.java 接口定义如下：

```java
public interface MPaasSSOAuthentication {
    /**
     * 单点登录认证
     *
     * @param header
     * @param cookies
     * @return UserProfile 用户信息封装类
     * @throws RPaasBusinessException
     */
     UserProfile ssoAuth(Map<String, String> header, Map<String, String> cookies) throws RPaasBusinessException;
}
```

> 实现示例

在写实现MPaasSSOAuthentication接口的类时，需要加上`@Component`等注解，目的是为了将实现类的bean注入到IOC容器中。下面有一段示例代码。

```java
@Component
public class MPaasSSOAuthenticationImpl implements RPaasBusinessException{
    @Autowired
    private JwtUtil jwtUtil;

    @Override
    public UserProfile ssoAuth(Map<String, String> header, Map<String, String> cookies) throws MPaasBusinessException{
        String token=header.get("token");
        Map<String,String> userMap=jwtUtil.praseToken(token);
        return new UserProfile(userMap.get("employeeCode"),userMap.get("employeeName"),token,false);
    }
}
```

