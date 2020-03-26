路径又称"终点"（endpoint），表示API的具体网址。

1. 服务器层面上，建议将API部署在专用域名之下，或主域名下。如：*https://api.example.com* 或 *https://example.org/api/*。

2. 建议将API的版本号放入URL中，如：*https://api.example.com/v1/* 。另一种做法是，将版本号放在HTTP头信息中，但不如放入URL方便和直观。Github采用这种做法。直接修改线上接口，会导致调用方报错，建议通过升版本号实现。

3. 在RESTful架构中，每个网址代表一种资源，所以网址中不能有动词，只能有`名词`。名词可以是与数据库的表格名对应，也可以是与业务系统，或业务功能名称对应。

4. 如果名词是与数据库的表格名对应的，API中的名词应该使用`复数`，其他情况则没必要。

5. 按照资源的逻辑`层级`，对 URL 进行嵌套。

6. 路径中的`资源名称`一律使用`小写字母`，如果名词由多个单词组成，则使用连字符”`-`“来连接单词。

7. API参数传递时，无论是基于path还是body，`参数`命名都统一用驼峰式命名。

    

   举个经典的例子，有一个API提供动物园（zoo）的信息，还包括各种动物园里面动物的信息，则它的GET接口路径应该设计成下面这样。

   ```http
   <!-- 查询所有动物园信息 -->
   https://api.example.com/v1/zoo-mg/zoos
   
   <!-- 查询某个{ID}动物园的所有动物信息 -->
   https://api.example.com/v1/zoo-mg/zoos/{ID}/animals 
   
   <!-- 分页查询某个{ID}动物园的猫科动物信息 -->
   https://api.example.com/v1/zoo-mg/zoos/{ID}/animals?class=猫科&page=1&pageSize=10
   ```

   