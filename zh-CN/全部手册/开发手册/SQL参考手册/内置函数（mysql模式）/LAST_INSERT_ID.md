```ebnf+diagram
last_insert_id::= LAST_INSERT_ID "(" [expr] ")" 
```

LAST_INSERT_ID函数用于获取当前会话最后一次INSERT中产生的AUTOINCREMENT列的值，返回值类型为BIGINT UNSIGNED类型。

若增加入参expr，LAST_INSERT_ID函数返回expr转换为BIGINT UNSIGNED后的值，转换失败会返回Invalid number错误。

>**Caution**:
>
> BIGINT UNSIGNED是YashanDB 23.4新增的数据类型，成功执行本函数要求客户端（包括yasql、驱动接口等）高于23.4及23.4配套版本。

**expr**

[通用表达式](../通用SQL语法/expr)，须为可转化为BIGINT UNSIGNED类型的类型。

- 当expr的值为浮点型时，函数会进行四舍五入。

- 当expr的值为NULL时，函数返回NULL。

- 当expr的值为空字符串时，函数返回0。

示例（单机HEAP表）

```sql
DROP TABLE IF EXISTS product_export;


CREATE TABLE product_export (id BIGINT PRIMARY KEY auto_increment, name VARCHAR(20));


INSERT INTO product_export (name) VALUES('xiaoming');


SELECT LAST_INSERT_ID() FROM dual;

     last_insert_id() 
--------------------- 
                    1


SELECT id,name FROM product_export;

                   ID NAME                                                             
--------------------- ---------------------------------------------------------------- 
                    1 xiaoming                                                        

```
