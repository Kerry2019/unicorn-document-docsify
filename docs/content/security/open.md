### 参数配置
!>在企业应用中，绝大部分服务都不允许随意调用，无论网页端、移动端还是其他应用，都需要接入统一的`单点登录`。在调用后台接口时同样需要携带单点登录之后的凭证，一方面是为了保障`调用接口安全`，另一方面也是为了服务器端能获取到当前请求用户的`身份信息`。

对于接口安全这类通用服务，unicorn框架同样提供了服务封装，我们可以在`application.properties`中开启服务。

```properties
#示例
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=adfs
mpaas.unicorn.security.header-name=token
mpaas.unicorn.security.public-route=/user/findAllUser,/user/findUserByUsername
```
>`mpaas.unicorn.security`前缀下有四个参数：
1. `open`：Boolean类型，表示开启接口安全。默认值是false，当为true时开启接口安全。
2. `mode`：String类型，表示单点登录模式。目前有三个值选项 `oam/adfs/custom` ，默认值是oam，具体的接入方式后续文档中说明。
3. `header-name`：String类型，表示将用户信息凭证存放接口header中键的名字。默认值是uid。
4. `public-route`：String类型，表示在已经开启接口安全的前提下，需要绕过接口安全，设置成公共接口的路由匹配。当有`多个值`时可以用逗号隔开，并且支持写`正则表达式`。

### 接口返回

在开启接口安全前提下，并没有符合`application.properties`中定义的安全认证规则，则接口响应的状态码是 `401 Unauthorized`，返回结果如下：
```json
{
    "code": "error",
    "errorMsg": "Unauthorized"
}
```

