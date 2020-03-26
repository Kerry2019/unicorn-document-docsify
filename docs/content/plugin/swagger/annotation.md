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


