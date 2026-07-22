```ebnf
any_value = ANY_VALUE "(" expr ")".
```

ANY_VALUE函数用于优化含有GROUP BY子句的查询，可返回给定参数[expr](../通用SQL语法/expr.md)的一个不确定值。对于因查询列与GROUP BY指定的列无关而导致语句执行失败的情况，可将expr赋值为此列名，函数返回首行数据。

本函数遵循如下规则：

- 本函数作为聚集函数使用。

- 若使用不含GROUP BY子句的语句查询表中列数据，本函数无作用。

- 函数会根据以下规则确定返回值的数据类型：

    - 若expr为TINYINT或SMALLINT类型，函数返回值为INT类型。

    - 若expr为INT类型，函数返回值为BIGINT类型。
  
    - 若expr为TINYINT UNSIGNED或SMALLINT UNSIGNED类型，函数返回值为INT UNSIGNED类型。
  
    - 若expr为INT UNSIGNED或BIGINT UNSIGNED类型，函数返回值为BIGINT UNSIGNED类型。

    - 若expr为CHAR类型，函数返回值为VARCHAR类型。

    - 若expr为其它类型，函数返回值类型与expr类型一致。

示例（单机HEAP表）

```sql
-- 使用含有GROUP BY子句的语句查询area_name列
SELECT area_name,max(DHQ) FROM area GROUP BY area_no;

[1:8]YAS-04316 not a single-group group function                 
  
-- 将area_name列赋给ANY_VALUE函数
SELECT ANY_VALUE(area_name),max(DHQ) FROM area GROUP BY area_no;

ANY_VALUE(area_name)                                          max(DHQ)
------------------------------------------------------------- ----------------------------------------------------------------
EastChina                                                          Shanghai
WestChina                                                          Chengdu
SouthChina                                                          Guangzhou
NorthChina                                                          Beijing
CentralChina                                                          Wuhan

5 rows fetched.
```
