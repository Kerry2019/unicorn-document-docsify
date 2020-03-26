## 1 成功

> 状态码

状态码为2xx，这里建议都用200返回。

> 响应结果

针对不同操作，服务器向用户返回的结果应该符合以下规范。

- GET /collection：返回资源对象的列表（数组）
- GET /collection/resource：返回单个资源对象
- POST /collection：返回新生成的资源对象
- PUT /collection/resource：返回完整的资源对象
- PATCH /collection/resource：返回完整的资源对象
- DELETE /collection/resource：返回一个空文档

## 2 失败

> 状态码

状态码有 4xx 或 5xx ，如：500 INTERNAL SERVER ERROR、401 Unauthorized、404 NOT FOUND等。

> 响应结果

对于需要向用户返回出错信息的。我们定义，在返回的信息中将error作为键名，出错信息作为键值即可。

```json
{
    "error": "告知用户的错误原因"
}
```
