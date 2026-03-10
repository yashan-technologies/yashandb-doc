```ebnf+diagram
median::= MEDIAN "(" [ALL] expr ")" [ OVER "(" [query_partition_clause] ")" ]
```

MEDIAN函数计算给定参数[expr](../通用SQL语法/expr)的中位数，仅支持expr类型为[数值型](../数据类型（yashan模式）/数值型)或者[日期时间型](../数据类型（yashan模式）/日期时间型)，使用其他数据类型会返回错误。

本函数不支持向量化计算。

MEDIAN窗口函数不能与DISTINCT以及ORDER BY语句一起使用。

函数返回值类型：

| 数据类型  | 返回值类型 |
| --------- | ---------- |
| TINYINT   | NUMBER     |
| SMALLINT  | NUMBER     |
| INT       | NUMBER     |
| BIGINT    | NUMBER     |
| FLOAT     | FLOAT      |
| DOUBLE    | DOUBLE     |
| NUMBER    | NUMBER     |
| DATE      | DATE       |
| TIMESTAMP | TIMESTAMP  |
| TIME      | TIME       |
| INTERVAL  | INTERVAL   |

单行计算中，当expr的值为空时函数返回NULL；expr为字面NULL时报错。expr使用绑定参数，绑定字面NULL，返回结果NULL。

多行计算中，函数将忽略expr值为空的行。当所有行均为空时，计算结果为NULL。

**ALL**

表示对所有行计算中位数。

**query_partition_clause**

窗口函数通用语法。

expr只支持INT、NUMBER与时间类型。partition by的参数支持除LOB、JSON、XMLTYPE、UDT外的其它数据类型。

示例（HEAP表）

```sql
-- 创建表exam并插入数据
CREATE TABLE exam(id INT,score INT);
INSERT INTO exam VALUES(1,99);
INSERT INTO exam VALUES(2,80);
INSERT INTO exam VALUES(3,80);

SELECT id, score FROM exam;
          ID        SCORE
------------ ------------
           1           99
           2           80
           3           80

SELECT MEDIAN(score) res FROM exam;
         RES
------------
         80

SELECT MEDIAN(score) OVER () res FROM exam;
                RES
-------------------
                 80
                 80
                 80

SELECT MEDIAN(score) OVER (PARTITION BY id) res FROM exam;
                 RES
--------------------
                  99
                  80
                  80

SELECT MEDIAN(null) OVER() FROM sys.dual;
[1:15]YAS-04322 invalid datatype
```
