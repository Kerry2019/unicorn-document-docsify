虽然本文档中推崇 API设计使用RESTful规范，但对应于某些无法使用RESTful的特殊场景，也提供了另外一种封装类的实现方式。

## 1 封装类

`WResponse`是框架内定义的一个统一响应封装类。

该响应方式，无论成功或失败，状态码都是`200`，通过`code`属性值来判断。WResponse定义了两个code。`ok`和`error`。`ok`表示接口调用成功，没有任何错误，数据能正确返回。`error`表示接口调用失败，可能是系统错误或者业务错误，没有数据返回，错误信息可以从`errorMsg`中获取。`data`表示接口返回的业务数据，可以是对象，也可以是列表。

```java
/**
 * wrapper response ，统一响应封装类
 */
public class WResponse implements Serializable{
    private static final long serialVersionUID = 1L;

    public static final String CODE_OK = "ok";
    public static final String CODE_ERR = "error";

    private String code;
    private String errorMsg;
    private Object data;

    public WResponse(String code, String errorMsg) {
        this.code = code;
        this.errorMsg = errorMsg;
    }

    public WResponse(){
        this(CODE_OK,null);
    }

    public static WResponse fail(String errorMsg){
        return new WResponse(CODE_ERR, errorMsg);
    }
    public static WResponse ok() {
        return new WResponse();
    }

    public WResponse setData(Object data) {
        this.data = data;
        return this;
    }

    public WResponse data(Object data) {
        return this.setData(data);
    }
	//getter and setter
    ...
}
```

> 响应示例

在spring boot应用开发中，你写的controller 里的方法应该是以下格式:

```java
@RestController
public class MpaasUserController {
    @RequestMapping(value = "/uri", method = RequestMethod.GET)
    public WResponse api() {
        //你的逻辑代码
        //....
        return WResponse.ok().data(result);
    }
}
```

## 2 自定义异常类

刚刚说过，我们自定义了一个`状态码700`，那么对应的异常类是`com.smec.mpaas.unicorn.comm.exception.WPaasBusinessException`。

>WPaasBusinessException.java

```java
/**
 * Wrapper Response Exception
 */
public class WPaasBusinessException extends RuntimeException {

    public WPaasBusinessException(){
    }
    public WPaasBusinessException(String message){
        super(message);
    }
    
}
```

## 3 自定义异常处理

框架内封装了统一的异常处理，这么做是为了追求代码的美观性，在抛出异常后，将自动返回包含错误信息的响应类。对应于RESTful的规范处理，通过抛出`WPaasBusinessException`异常，统一返回`WResponse`的响应类。

下面是controller中的一个示例:

```java
 /**
     * 新增用户
     */
    @PostMapping("/addUser")
    public WResponse addUser(@RequestBody JpaUser jpaUser){
        if(jpaUserDao.countByUsername(jpaUser.getUsername())>0){
            throw new WPaasBusinessException(jpaUser.getUsername()+"账号已存在");
        }
        jpaUserDao.save(jpaUser);
        return WResponse.ok();
    }
```
当触发WPaasBusinessException，返回结果为：
```json
状态码 200

{
    "code":"error",
    "errorMsg":"Kerry账号已存在"
}
```

