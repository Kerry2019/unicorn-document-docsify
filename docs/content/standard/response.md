> 接口返回类型

规定所有接口的返回类型都必须为`com.smec.mpaas.unicorn.pojo.Response`类型，也就是在spring boot应用开发中，你写的controller 里的方法都应该是以下格式:

```java
@RestController
public class MpaasUserController {
    @RequestMapping(value = "/uri", method = RequestMethod.GET)
    public Response api() {
        //你的逻辑代码
        //....
        return Response.ok().data(result);
    }
}
```

> Response详解

`Response` 是unicorn提供的一个标准接口返回类型，目的是统一所有项目所有接口的返回，使用unicorn开发，返回类型`必须`为`Response`


Response的定义如下：

```java
public class Response implements Serializable{
    private static final long serialVersionUID = 1L;

    public static final String CODE_OK = "ok";
    public static final String CODE_ERR = "error";

    private String code;
    private String errorMsg;
    private Object data;

    public Response(String code, String errorMsg) {
        this.code = code;
        this.errorMsg = errorMsg;
    }

    public Response(){
        this(CODE_OK,null);
    }

    public static Response fail(String errorMsg){
        return new Response(CODE_ERR, errorMsg);
    }
    public static Response ok() {
        return new Response();
    }
    public Response setData(Object data) {
        this.data = data;
        return this;
    }
    public Response data(Object data) {
        return this.setData(data);
    }

    //getter and setter
    ...
```

Response定义了两个code。`ok`和`error`。`ok`表示接口调用成功，没有任何错误，数据能正确返回。`error`表示接口调用失败，可能是系统错误或者业务错误，没有数据返回，错误信息可以从`errorMsg`中获取。`data`表示接口返回的业务数据，可以是对象，也可以是列表。