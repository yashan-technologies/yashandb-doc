```ebnf
last_insert_id = LAST_INSERT_ID "(" [expr] ")" .
```

LAST_INSERT_ID函数用于获取当前会话最后一次INSERT中产生的AUTOINCREMENT列的值，返回值类型为BIGINT UNSIGNED类型。

若增加入参expr，LAST_INSERT_ID函数返回expr转换为BIGINT UNSIGNED后的值，转换失败会返回Invalid number错误。

>**Caution**:
>
> BIGINT UNSIGNED是YashanDB 23.4新增的数据类型，成功执行本函数要求客户端（包括yasql、驱动接口等）高于23.4及23.4配套版本。

**expr**

[通用表达式](../通用SQL语法/expr.md)，须为可转化为BIGINT UNSIGNED类型的类型。

- 当expr的值为浮点型时，函数会进行四舍五入。

- 当expr的值为NULL时，函数返回NULL。

- 当expr的值为空字符串时，函数返回0。

- 不允许对BLOB和TEXT类型数据使用此函数。

示例（单机HEAP表）

```sql
drop table if exists product_export;

Succeed.

create table product_export (id bigint PRIMARY KEY auto_increment, name varchar(20));

Succeed.

insert into product_export (name) values('xiaoming');

1 row affected.

select last_insert_id() res from dual;

                  res 
--------------------- 
                    1

1 row fetched.

select id,name from product_export;

                   id name                                                             
--------------------- ---------------------------------------------------------------- 
                    1 xiaoming                                                        

1 row fetched.
```
