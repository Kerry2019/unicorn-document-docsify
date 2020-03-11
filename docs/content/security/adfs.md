> 接入`ADFS`模式的单点登录，同样只需要在`application.properties`中配置即可。
```properties
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=adfs
mpaas.unicorn.security.header-name=Header中存放access_token的键名
mpaas.unicorn.security.public-route=公共路由地址
```

!> `ADFS`是微软提供的一个用户认证技术，支持OAuth2协议的单点登录。在完成企业的ADFS登录认证后，会获取到一个标识用户信息的令牌 -- access_token，遵守了 JWT 的标准。当在调用后端接口时，要求在Header中带上access_token，这里建议存放access_token的键为`token`，即 `键为token，值为access_token`，不过键同样可以通过`mpaas.unicorn.security.header-name`来指定。

在JWT标准中，access_token包含三段字符串，第一段包含加密算法和公钥的来源，第二段包含用户信息和过期时间等，第三段则是对前两段的私钥加密。

unicorn在拿到access_token后，会从公钥服务器拿到指定公钥，验证第三段加密的合法性，以及校验当前token是否过期。在验证通过之后，拿到当前用户的工号和姓名，存在当前请求的上下文里面。

