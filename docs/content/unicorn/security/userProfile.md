unicorn将获取当前用户信息的静态方法，都写在`com.smec.mpaas.unicorn.comm.pojo.UserProfileThread`类中。如果接口安全认证正常，可以通过下列方法获取当前用户的账号，否则获取到的账号是 `anonymous`。

> 获取当前账号

```java
String uid=UserProfileThread.getCurrentUser();
```

> 获取当前用户类

```java
UserProfile user=UserProfileThread.getUserProfile();
```

> UserProfile.java

```java
public class UserProfile {
    public static final String ANONYMOUS_USER ="anonymous";
    public static final UserProfile ANONYMOUS_OBJ = new UserProfile(ANONYMOUS_USER,true);
    /**
     * 用户唯一编号
     */
    private String uid;
    /**
     * 用户名，保存显示名称
     */
    private String userName;
    /**
     * 用户token
     */
    private String token;
    /**
     * 额外属性
     */
    private Map<String, String> attributes;
    /**
     * 标识该用户是否是匿名登录(也就是未登录)
     */
    private boolean anonymous = true;

    public UserProfile() {}
    public UserProfile(String uid,boolean anonymous) {
        this.uid = uid;
        this.anonymous=anonymous;
    }
    public UserProfile(String uid,String userName,String token,boolean anonymous) {
        this.uid = uid;
        this.userName=userName;
        this.token=token;
        this.anonymous=anonymous;
    }
    public static String getAnonymousUser() {
        return ANONYMOUS_USER;
    }
    public static UserProfile getAnonymousObj() {
        return ANONYMOUS_OBJ;
    }
    //getter、setter方法
    ...
}

```

