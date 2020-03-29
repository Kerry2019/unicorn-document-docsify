RESTful是本文档推崇的一种API 设计风格，所有推荐使用下面的方式做API响应。

## 1 成功响应

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

## 2 失败响应

> 状态码

状态码有 4xx 或 5xx ，如：500 INTERNAL SERVER ERROR、401 Unauthorized、404 NOT FOUND等。

在框架中我们自定义了一个`状态码700`，我们定义该异常是可以直接将异常内容反馈用户的，例如：“用户名不能为空”，“该商品已售罄，请下次再来”，等等。

> 响应结果

对于需要向用户返回出错信息的。我们定义，在返回的信息中将error作为键名，出错信息作为键值即可。

```json
{
    "error": "告知用户的错误原因"
}
```

## 3 自定义异常类

刚刚说过，我们自定义了一个`状态码700`，那么对应的异常类是`com.smec.mpaas.unicorn.comm.exception.RPaasBusinessException`。

>RPaasBusinessException.java

```java
/**
 * RESTful 异常类
 */
public class RPaasBusinessException extends RuntimeException {
    /**
     * 自定义异常，状态码
     */
    public static final int STATUS_CODE=700;

    public RPaasBusinessException(){

    }
    public RPaasBusinessException(String message){
        super(message);
    }
    
}
```

同样也定义了异常响应的类。

> RErrorResponse.java

```java
/**
 * RESTful Error 返回类
 */
public class RErrorResponse implements Serializable {
    private static final long serialVersionUID = 1L;
    private String error;

    public static RErrorResponse error(String error){
        return new RErrorResponse(error);
    }
    public RErrorResponse(){
    }

    public RErrorResponse(String error){
        this.error=error;
    }

    public String getError() {
        return error;
    }

    public void setError(String error) {
        this.error = error;
    }
}
```

## 4 自定义异常处理

框架内封装了统一的异常处理，这么做是为了追求代码的美观性，在抛出异常后，将自动返回包含错误信息的响应类。对应于RESTful的规范处理，通过抛出`RPaasBusinessException`异常，统一返回`RErrorResponse`的响应类。

下面是controller中的一个示例:

```java
 /**
     * 新增用户
     */
    @PostMapping("/addUser")
    public JpaUser addUser(@RequestBody JpaUser jpaUser){
        if(jpaUserDao.countByUsername(jpaUser.getUsername())>0){
            throw new RPaasBusinessException(jpaUser.getUsername()+"账号已存在");
        }
        jpaUserDao.save(jpaUser);
        return jpaUser;
    }
```
当触发RPaasBusinessException，返回结果为：
```json
状态码 700

{
    "error": "Kerry账号已存在"
}
```

> Spring框架拦截顺序

这种统一异常处理是使用`@ControllerAdvice`实现的，要值得注意的是，作为框架拦截的一种表现，@ControllerAdvice的顺序晚于AOP，但是要早于拦截器和过滤器。就是说，在处理接口的`后置过滤器`和`后置拦截器`中，就不能用这种方式抛出RPaasBusinessException。

**ServletContextListener> Filter > Interception > AOP > 具体执行的方法 > AOP > `@ControllerAdvice` > Interception > Filter > ServletContextListener**

