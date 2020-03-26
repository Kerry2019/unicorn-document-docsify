

`Unicorn`里面集成了Springfox Swagger，所以如果想要开启Swagger使用，只需要在`application.properties`中将`mpaas.unicorn.swagger.enable`设为`true`即可。

另外还可以配置当前项目接口文档的标题、描述和当前版本号信息。

```properties
mpaas.unicorn.swagger.enable=true
mpaas.unicorn.swagger.title=接口文档标题
mpaas.unicorn.swagger.description=接口文档描述
mpaas.unicorn.swagger.version=接口文档当前版本号
```

在运行Spring Boot项目后，访问 http://ip:port/swagger-ui.html 就能看到在线的接口文档。

!>在项目上生产环境时，为了防止接口调用暴露，建议将`enable`属性设为`false`，关闭swagger配置。

如果当前项目开启了`接口安全`，Swagger文档将自动配置了Authorized，注入了键为`mpaas.unicorn.security.header-name`的Header参数。


