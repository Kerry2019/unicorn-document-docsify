### 1 自定义

为了保证框架的灵活性，除了提供已经实现功能的`oam`和`adfs`两种接入单点登录方式以外，还可以选用另外一种`custom`方式,自定义实现单点登录。

> 自定义接入单点登录需要做两个操作：在配置文件中申明，并且实现`com.smec.mpaas.unicorn.adapter.MPaasSSOAuthentication`接口。

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
     * @throws MPaasBusinessException
     */
     UserProfile ssoAuth(Map<String, String> header, Map<String, String> cookies) throws MPaasBusinessException;
}
```

### 2 实现

在写实现MPaasSSOAuthentication接口的类时，需要加上`@Component`等注解，目的是为了将实现类的bean注入到IOC容器中。下面有一段示例代码。

```java
@Component
public class MPaasSSOAuthenticationImpl implements MPaasSSOAuthentication{
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


