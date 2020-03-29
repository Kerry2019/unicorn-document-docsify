“辅助模式”并不是框架所推崇的，“标准模式”是需要自己去写解析当前用户信息的实现类。但我因为偷懒，将平时开发常用到的几种实现类封装进来，作为“辅助模式”。如果你们的单点登录方式正好和我遇到的一样，也可以直接使用对应的模式。

## 1 simple

> 接入`Simple`模式，只需要在`application.properties`中配置即可，无需其他操作。

```properties
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=simple
mpaas.unicorn.security.header-name=uid
mpaas.unicorn.security.public-route=公共路由地址
```

!> Simple模式处理方式，是指在调用接口时，在http请求的Header中直接加上用户标识字段。这是个别项目简易的处理方式，在经过单点登录之后，在接口的Header中都加上一个键值对，如： `键为uid，值为用户工号`。如果键不为uid，可以通过修改`mpaas.unicorn.comm.security.header-name`来指定键。

unicorn会由过滤器获取当前请求头中的uid对应的值，并将工号存在当前请求的上下文里面。



## 2 jwks

> 接入jwks模式的单点登录，同样只需要在`application.properties`中配置即可。

```properties
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=jwks
mpaas.unicorn.security.header-name=Header中存放access_token的键名
mpaas.unicorn.security.jwks-uri=jwks公钥集服务器地址
mpaas.unicorn.security.public-route=公共路由地址
```

!> `JWKS`（JSON Web Key Set）是JSON Web的密钥集。当我们的单点登录系统通过JWT规范（当前实现RS256加密）来签发token时，客户端可通过公钥来解析token。该模式要求我们提供包含公钥集的远程服务器URI，然后从远程服务器上拿到公钥，并解析当前用户信息。

目前大部分项目都使用基于OAuth2 的单点登录，单点登录认证成功后会获取到一个标识用户信息的令牌 -- access_token，遵守了 JWT 的标准。当在调用后端接口时，要求在Header中带上access_token，这里建议存放access_token的键为`token`，即 `键为token，值为access_token`，不过键同样可以通过`mpaas.unicorn.security.header-name`来指定。

在JWT标准中，access_token包含三段字符串，第一段包含加密算法和公钥的来源，第二段包含用户信息和过期时间等，第三段则是对前两段的私钥加密。所以在解析之后，拿到的是包含第二段用户信息的Map<String,Object>，如果将Map转换为用户信息类UserProfile，也同样需要实现一个接口`com.smec.mpaas.unicorn.comm.adapter.JWKSEnhanceUserProfile`。

```java
public interface JWKSEnhanceUserProfile {

    /**
     * 将包含用户标识的Map对象，增强成自定义内容的UserProfile
     * @param originMap
     * @return
     * @throws RPaasBusinessException
     */
    UserProfile enhance(Map<String,Object> originMap)throws RPaasBusinessException;
}
```



> 实现示例

在写实现JWKSEnhanceUserProfile接口的类时，需要加上`@Component`等注解，目的是为了将实现类的bean注入到IOC容器中。下面有一段示例代码。

```java
@Component
public class JWKSEnhanceUserProfileImpl implements JWKSEnhanceUserProfile {
    /**
     * 从AD域中员工标识邮箱中，提取工号作为主键
     * @param originMap
     * @return
     * @throws RPaasBusinessException
     */
    @Override
    public UserProfile enhance(Map<String,Object> originMap)throws RPaasBusinessException{
            String uid = (String) originMap.get("upn");
            String username = (String) originMap.get("unique_name");
            UserProfile userProfile = new UserProfile(uid.substring(0, uid.indexOf("@")), false);
            userProfile.setUserName(username);
            return userProfile;
    }
}
```

