在Oracle数据库中有多个用户，每个用户一般对应一个`schema`，每个schema之间相互独立。我们项目上是按照业务条线来划分schema，例如：维保业务的schema名是mnt，安装业务是inst，融合所有业务的是fusion。如果在当前schema中，想要使用其他schema里面的资源（table、view、sequence等等）该怎么做呢？下面有两种常见办法。

> 资源授权

```sql
-- 将当前schema下mnt_demo表，授权 “查、增、删、改” 权限给fusion
grant select,insert,delete,update on mnt_demo to fusion;

-- 将当前schema下mnt_demo_s sequence，授权 “查” 权限给fusion
grant select on mnt_demo_s to fusion;
```

直接在当前schema下，将当前资源的相关权限，授权给指定的schema即可。但是在被授权的schema中访问资源时，需要加上所属schema的前缀。如示例中，mnt_demo表原属于mnt的schema下，如果fusion要访问，就必须如下：

```sql
select * from mnt.mnt_demo;
```

有没有办法去掉这个前缀呢？有点，那就是`同义词`。

> 同义词

!>Oracle `synonym` `同义词`是数据库当前用户通过给另外一个用户的对象创建一个别名，然后可以通过对别名进行查询和操作，等价于直接操作该数据库对象。Oracle同义词常常是给表、视图、函数、过程、包等制定别名，可以通过CREATE 命令进行创建、ALTER 命令进行修改、DROP 命令执行删除操作。

```sql
-- 创建了一个public的同义词，所有schema都可以查询（仅限于查询的权限）
create public synonym mnt_demo同义词名 for mnt_demo;

-- 把当前同义词的其他权限授权给fusion
grant select,insert,delete,update on mnt_demo同义词名 to fusion;
```

此时在fusion中访问mnt_demo就可以直接用：

```sql
select * from mnt_demo同义词名;
```

> 同义词循环链

在使用同义词时，最常出现的错误就是`同义词循环链错误`。出现这个错误的原因是：存在同义词，但同义词没有相应的对象（该对应的表等对象被删了或找不到）。

处理这种问题，我们先查询 public 同义词所在的表：

```sql
select * from dba_synonyms t where t.synonym_name='同义词名称';
```

能查询到该同义词对应的schema和资源名称，验证是否存在。如果的确有问题，就删除该同义词，重新创建。删除同义词的sql如下：

```sql
DROP PUBLIC SYNONYM 同义词名称;
```

