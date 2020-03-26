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

