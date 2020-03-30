## Swagger文档

在浏览器中打开 http://localhost:8002/swagger-ui.html ，就能看到本项目的Swagger接口文档。

不仅可以看到controller层中定义的接口输入输出格式，还可以在线测试接口。要记得生产环境上线时，要在配置文件中关闭Swagger哦。

## Druid连接池

在浏览器中打开 http://localhost:8002/druid ，就能看到本项目的Druid连接池监控页面。刚打开页面时会要求登录验证，输入我们在配置文件中定义的账号/密码即可。

在Druid监控页面上，不仅可以查看数据源和连接池的基本信息，还可以通过SQL监控、SQL防火墙、URI监控等功能，做API和数据库的优化。

## API 测试

所有接口都在Header中携带（ uid = Kerry）

1、调用`新增用户`接口：

POST：http://localhost:8002/hr/users

> 请求参数

```json
{
	"username":"Kerry",
	"chineseName":"吴晨瑞",
	"email":"kerry.wu@definesys.com",
	"birthday":"1995-08-18",
	"departmentId":1
}
```

>响应结果

```json
//第一次新增
状态码 200

{
    "id": 62,
    "username": "Kerry",
    "chineseName": "吴晨瑞",
    "email": "kerry.wu@definesys.com",
    "birthday": "1995-08-18",
    "departmentId": 1,
    "objectVersion": 0,
    "createdBy": "Kerry0325",
    "createdDate": "2020.03.30 23:49:46",
    "lastUpdatedBy": "Kerry0325",
    "lastUpdatedDate": "2020.03.30 23:49:46"
}


//第二次新增
状态码 700

{
    "error": "Kerry账号已存在"
}
```

2、调用`查询用户`接口：

GET：http://localhost:8002/hr/users

>响应结果

```json
状态码 200

[
    {
        "id": 62,
        "username": "Kerry",
        "chineseName": "吴晨瑞",
        "email": "kerry.wu@definesys.com",
        "birthday": "1995-08-18",
        "departmentId": 1,
        "objectVersion": 0,
        "createdBy": "Kerry0325",
        "createdDate": "2020.03.30 23:49:46",
        "lastUpdatedBy": "Kerry0325",
        "lastUpdatedDate": "2020.03.30 23:49:46",
        "departmentName": null
    }
]
```

3、调用`更新用户`接口：

PATCH：http://localhost:8002/hr/users/Kerry

> 请求参数

```json
{
	"chineseName": "吴晨瑞0329"
}
```

> 响应结果

```json
状态码 200

{
    "id": 62,
    "username": "Kerry",
    "chineseName": "吴晨瑞0329",
    "email": "kerry.wu@definesys.com",
    "birthday": "1995-08-18",
    "departmentId": 1,
    "objectVersion": 1,
    "createdBy": "Kerry0325",
    "createdDate": "2020.03.30 23:49:46",
    "lastUpdatedBy": "kerry0325",
    "lastUpdatedDate": "2020.03.30 23:57:28",
    "departmentName": null
}
```

4、调用`删除用户`接口

DELETE：http://localhost:8002/hr/users/Kerry

> 响应结果

```json
状态码 200
```

5、调用接口时，未在Header中携带uid

> 响应结果

```json
状态码 401

{
    "error": "Unauthorized"
}
```

