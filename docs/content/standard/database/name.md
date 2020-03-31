oracle不区分大小写，所有命名都统一用大写，多个单词用下划线 _ 来分隔。

1. `表 table`：命名组成：“模块名_系统名”，英文单词皆单数。

2. `序列 sequence`：命名组成：“表名_S”，是在对应表名后面加上 _S

3. `主键 primary key`：表名+“_PKN”，`PK`是Primary Key 的简称，`N`是数字1、2、3...n，表示多个主键的序号。

4. `索引index`：表名+“_INDEXN”，`INDEX`表示索引，同样`N`是数字1、2、3...n，表示多个索引的序号。

5. `表字段 cloumn`：主键一律命名 `ID`，其他字段命名不要使用数据库关键词。

6. `视图 view`：视图命令后缀一律是 `_V`。

7. `函数 function`：函数前缀一律是`FUNC_`。

8. `存储过程 procedure`：存储过程前缀一律是`PROC_`。

9. `包 package`：包后缀一律是`_PKG`。

10. `数据库连接 dblink`：由 `远程服务器名_`+`数据库名_`+`_link`组成。

11. `pl/sql`

    * 传入参数： `p_` + 名称，如 p_org_code。
    * 输出参数：`o_` +名称，如果 o_org_code。
    * 变量：`v_` + 名称，如 v_org_code。
    * 游标变量：`v_` + 名称 + `_cur`，如 v_org_cur。
    * 隐式游标参数：将游标名中`_cur`改成`_row`，如 for v_org_row in v_org_cur。

    

