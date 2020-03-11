> 接口抛出异常

当我们在开发时，针对一些业务异常（如：登录接口中，账号/密码不能为空），或者try catch 到的一些系统异常，并需要立即返回响应结果的情况，可以使用到这里定义的异常规范`com.smec.mpaas.unicorn.exception.MPaasBusinessException`。

这么做是为了追求代码的美观性，在抛出异常后，将自动返回包含错误信息的Response响应类。
下面是controller中的一个示例:

```java
 /**
     * 新增用户
     * @param jpaUser
     * @return
     */
    @PostMapping("/addUser")
    public Response addUser(@RequestBody JpaUser jpaUser){
        if(jpaUserDao.countByUsername(jpaUser.getUsername())>0){
            throw new MPaasBusinessException(jpaUser.getUsername()+"账号已存在");
        }
        jpaUserDao.save(jpaUser);
        return Response.ok();
    }
```
当触发MPaasBusinessException，返回结果为：
```java
{
    "code": "error",
    "errorMsg": "Kerry账号已存在",
    "data": null
}
```

> MPaasBusinessException详解

`MPaasBusinessException` 是unicorn提供的一个标准的业务异常类型，很大层面上是为了追求代码的简洁规范。统一异常处理是通过`@ControllerAdvice`来实现的。
```java
@ControllerAdvice
public class DefaultExceptionHandler {
    @ExceptionHandler(MPaasBusinessException.class)
    @ResponseBody
    public Response businessExceptionHandler(Exception e){
        return Response.fail(e.getMessage());
    }
}
```

> Spring框架拦截顺序

要值得注意的是，作为框架拦截的一种表现，@ControllerAdvice的顺序晚于AOP，但是要早于拦截器和过滤器。就是说，在处理接口的`后置过滤器`和`后置拦截器`中，就不能用这种方式抛出`MPaasBusinessException`。

**ServletContextListener> Filter > Interception > AOP > 具体执行的方法 > AOP > `@ControllerAdvice` > Interception > Filter > ServletContextListener**

