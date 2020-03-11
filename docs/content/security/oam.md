> 接入`OAM`模式的单点登录，只需要在`application.properties`中配置即可，无需其他操作。

```properties
mpaas.unicorn.security.open=true
mpaas.unicorn.security.mode=oam
mpaas.unicorn.security.header-name=uid
mpaas.unicorn.security.public-route=公共路由地址
```

实际上`OAM`是unicorn默认开启的模式，`mpaas.unicorn.security.mode`和`mpaas.unicorn.security.header-name`可以不写。

!> 当前项目上的OAM单点登录处理方式，是在单点登录成功后，在请求接口的Header中都加上一个键值对 -- `键为uid，值为用户工号`。如果键不为uid，可以通过修改`mpaas.unicorn.security.header-name`来指定键。

unicorn会由过滤器获取当前请求头中的uid对应的值，并将工号存在当前请求的上下文里面。