```ebnf
grouping_id = GROUPING_ID "(" expr {"," expr  } ")".
```

在GROUP BY分组排序计算时，如果需要进一步通过ROLLUP或GROUPING SETS来拓展聚合计算，可以通过GROUPING函数确定查询结果中的NULL是列值还是ROLLUP聚合标记，结果为1时表示为聚合结果，为0时表示正常列值。

本函数的返回值类型为NUMBER，当GROUPING_ID的参数[expr](../通用SQL语法/expr)只有1个时，结果同GROUPING。

本函数支持1 - 126个参数，参数数量不在此范围则报错。

本函数必须与`GROUP BY`、`GROUP BY ROLLUP`、`GROUP BY CUBE`或`GROUP BY GROUPING SETS`语句结合使用，函数内部不能使用DISTINCT。

[expr](../通用SQL语法/expr)可以为除UDT以外的任意数据类型，不能为NULL且不能为聚集函数。

示例

以下示例中，GROUPING返回值1表示对name列的聚合计算，返回值3表示对dept和name的聚合计算。

```sql
CREATE TABLE dept
(ID INTEGER  NOT NULL PRIMARY KEY,
 dept VARCHAR(10),
 name VARCHAR(10) NOT NULL,
 salary INTEGER NOT NULL);
INSERT INTO dept VALUES (1,'A','LI',8000);
INSERT INTO dept VALUES (2,'A','chang',10000);
INSERT INTO dept VALUES (3,'A','tina',6000);
INSERT INTO dept VALUES (4,'A','kate',4000);
INSERT INTO dept VALUES (5,'A','nora',4500);
INSERT INTO dept VALUES (6,'B','tom',7800);
INSERT INTO dept VALUES (7,'B','jerry',6600);
INSERT INTO dept VALUES (8,'C','lisa',4500);
INSERT INTO dept VALUES (9,'C','mona',3200);

SELECT dept, name, SUM(salary) total, GROUPING_ID(dept, name) 
FROM DEPT GROUP BY ROLLUP(dept, name);

DEPT       NAME             TOTAL GROUPING_ID(DEPT,NAME) 
---------- ---------- ----------- ----------- 
A          LI                8000           0 
A                           32500           1 
                            54600           3 
A          chang            10000           0 
A          tina              6000           0 
A          kate              4000           0 
A          nora              4500           0 
B          tom               7800           0 
B                           14400           1 
B          jerry             6600           0 
C          lisa              4500           0 
C                            7700           1 
C          mona              3200           0 

SELECT dept, name, SUM(salary) total, GROUPING_ID(dept, name)
FROM DEPT GROUP BY CUBE(dept, name);

DEPT       NAME             TOTAL GROUPING_ID(DEPT,NAME) 
---------- ---------- ----------- ----------- 
A          LI                8000           0 
A                           32500           1 
           LI                8000           2 
                            54600           3 
A          chang            10000           0 
           chang            10000           2 
A          tina              6000           0 
           tina              6000           2 
A          kate              4000           0 
           kate              4000           2 
A          nora              4500           0 
           nora              4500           2 
B          tom               7800           0 
B                           14400           1 
           tom               7800           2 
B          jerry             6600           0 
           jerry             6600           2 
C          lisa              4500           0 
C                            7700           1 
           lisa              4500           2 
C          mona              3200           0 
           mona              3200           2 

SELECT dept, name, SUM(salary) total, GROUPING_ID(dept, name) 
FROM DEPT GROUP BY dept,name;

DEPT       NAME             TOTAL GROUPING_ID(DEPT,NAME) 
---------- ---------- ----------- ----------- 
A          LI                8000           0 
A          chang            10000           0 
A          tina              6000           0 
A          kate              4000           0 
A          nora              4500           0 
B          tom               7800           0 
B          jerry             6600           0 
C          lisa              4500           0 
C          mona              3200           0 

SELECT dept, name, SUM(salary) total, GROUPING_ID(dept, name)
FROM DEPT GROUP BY GROUPING SETS(dept, name);

DEPT       NAME             TOTAL GROUPING_ID(DEPT,NAME) 
---------- ---------- ----------- ----------- 
           LI                8000           2 
A                           32500           1 
           chang            10000           2 
           tina              6000           2 
           kate              4000           2 
           nora              4500           2 
           tom               7800           2 
B                           14400           1 
           jerry             6600           2 
           lisa              4500           2 
C                            7700           1 
           mona              3200           2 

```
