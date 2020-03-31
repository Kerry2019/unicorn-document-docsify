项目上使用的`sequence`是数据库系统按照一定规则自增的数字序列，因为自增所以不会重复。所以一般用来代理表的主键，唯一标识。



我们规范要求每新建一张表，都要对应的创建一个sequence。并且sequence有命名要求，是对应的表名后面加上”`_s`“。

```sql
-- Create sequence 
create sequence 表名_S
minvalue 1
maxvalue 9999999999999999999999999999
start with 1
increment by 1
cache 20;
```

数据库都是oracle数据库，所有本章的所有内容介绍，都基于oracle数据库的环境来进行。

## 1 表字段

为了统一规范，我们创建表时，最好统一主键命名，和五个基础的系统字段：

1. `id`：主键，非空number类型，根据后续定义的sequence来增长。
2. `object_version`：版本号，行记录每次的更新操作都会加一，可用于`乐观锁`的实现。
3. `created_by`：行记录的创建人。
4. `created_date`：行记录的创建时间。
5. `last_updated_by`：行记录的最后修改人。
6. `last_updated_date`：行记录的最后修改时间。

```sql
-- Create table
create table 表名
(
  id                NUMBER not null,
    
  --- 其他业务字段 ---
    
  object_version    NUMBER not null,
  created_by        VARCHAR2(50),
  created_date      DATE,
  last_updated_by   VARCHAR2(50),
  last_updated_date DATE
)
```



## 2 注释

在建完表之后，要求给表中的每个字段加上中文`注释`。表结构是一个项目中最重要的数据结构，我们必须保证每个字段的含义清晰明了，否则时间长了总会忘却。对于有对应`值列表`的字段，在注释还要求记录对应值列表的编码。



给表字段写注释可以直接在数据库客户端上操作，也可以通过sql来:

```sql
comment on column schema名.表名.字段名
  is '注释内容';
  
--示例
comment on column REMES.MNT_NOTICE_ACCOUNT_LINES.status
  is '分公司结算流程状态（值列表MNT_NOTICE_ACCOUNT_LINES_STATUS）';
```

## 3 其他

表中还需要创建**主键**。

根据实际情况来决定是否要创建**索引**。

具体主键和索引的命名规则看其他章节。



## 4 sequence

> `sequence`是数据库系统按照一定规则自增的数字序列，因为自增所以不会重复。所以一般用来代理表的主键，唯一标识。



我们规范要求每新建一张表，都要对应的创建一个sequence。并且sequence有命名要求，是对应的表名后面加上”`_s`“。

```sql
-- Create sequence 
create sequence 表名_S
minvalue 1
maxvalue 9999999999999999999999999999
start with 1
increment by 1
cache 20;
```

