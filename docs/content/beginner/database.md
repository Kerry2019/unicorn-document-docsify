本章示例代码实现的功能比较简单，我们先创建一张用户表，然后开发对该表增删改查的接口，ORM框架使用的是Spring Data JPA。



> 创建table
``` sql
create table JPA_USER
(
  id                NUMBER not null,
  username          VARCHAR2(50),
  chinese_name      VARCHAR2(100),
  email             VARCHAR2(50),
  birthday          DATE,
  department_id     NUMBER,
  object_version    NUMBER not null,
  created_by        VARCHAR2(50),
  created_date      DATE,
  last_updated_by   VARCHAR2(50),
  last_updated_date DATE
)
```
> 创建sequence
``` sql
create sequence JPA_USER_S
minvalue 1
maxvalue 9999999999999999999999999999
start with 1
increment by 1
cache 20;
```

> 主键

JPA_USER_PK1

> 索引

```sql
create index JPA_USER_INDEX1 on JPA_USER (DEPARTMENT_ID)
```

该索引为了示例，实际表数据较少，没必要创建索引。