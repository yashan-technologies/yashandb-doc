```ebnf+diagram
string_agg::= STRING_AGG "(" [DISTINCT|ALL] string "," separator [order_by_clause] ")"
```

STRING_AGG函数将多行的数据执行拼接操作，并通过分隔符分隔，返回一行CLOB类型的字符串。本函数与[GROUP_CONCAT](./GROUP_CONCAT)函数实现功能类似。

本函数不支持向量化计算。

**DISTINCT**

计算最终拼接结果时，过滤在同一组内出现的重复的行。

**ALL**

默认值，表示不过滤重复的行，对所有行都进行拼接。

**string**

string可以为：

- 通用表达式[expr](../通用SQL语法/expr)

- 查询列为单列且返回行为单行的子查询

string的值须为字符型或可转换为字符型的其他类型（LOB、XMLTYPE类型支持隐式转换），但不允许为JSON、NVARCHAR、NCHAR、NCLOB类型和时区类型。

当string的值为NULL时，函数返回NULL。

**separator**

指定将组内的多行进行拼接时，多行之间加上separator定义的分隔符，separator为character类的常量或常量表达式。

separator必须指定，但可以指定为NULL。

**order_by_clause**

对组内要CONCAT的string排序，其语法与SELECT语句中描述一致。

当ORDER BY后指定的是常量数字时，表示的是string的顺序值。

示例（HEAP表）

```sql
--创建exprs_string_agg表，并插入数据
CREATE TABLE exprs_string_agg (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_string_agg 
VALUES  (1,'小东',10000),(2,'小明',46450),
        (3,'小红',46450 ),(4,'小东',14465),
        (5,'小明',46450),(6,'小东',46450);

--未指定GROUP BY时，将所有行CONCAT，得到一行结果
SELECT STRING_AGG(MONEY,',') AS money FROM exprs_string_agg;
MONEY                                                            
---------------------------------------------------------------- 
1.0E+004,4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004

--group by后各组的多行数据分别CONCAT成一行，得到按组的多行结果
SELECT NAME,STRING_AGG(money,',') AS money FROM exprs_string_agg GROUP BY name;
NAME                       MONEY                                
-------------------------- ------------------------------------ 
小东                     1.0E+004,1.4465E+004,4.645E+004     
小明                     4.645E+004,4.645E+004               
小红                     4.645E+004                          

--使用order by子句排序
SELECT NAME,STRING_AGG(money,',' ORDER BY id DESC) AS money FROM exprs_string_agg GROUP BY name;
NAME                     MONEY                               
------------------------ ------------------------------------
小东                   4.645E+004,1.4465E+004,1.0E+004       
小明                   4.645E+004,4.645E+004                 
小红                   4.645E+004      

--使用DISTINCT关键字去重
SELECT STRING_AGG(DISTINCT name,',') AS names FROM exprs_string_agg;
NAMES
----------------------------------------------------------------
小东,小明,小红
```
