```ebnf
grouping = GROUPING "(" expr  {"," expr}")".
```

在GROUP BY分组排序计算时，可以通过GROUPING函数确定查询结果是否为聚合结果，通常用于ROLLUP、CUBE或GROUPING SETS等拓展聚合计算场景。GROUPING结果的二进制位为1表示该行记录是对对应顺序查询列的聚合结果，为0时表示正常表记录。expr的数量范围为[1,64]。

本函数必须与`GROUP BY`、`GROUP BY ROLLUP`、`GROUP BY CUBE`或`GROUP BY GROUPING SETS`语句结合使用，函数内部不能使用DISTINCT。

expr的数据类型不允许ROWID、BIT、UDT或BLOB类型，expr不能为聚集函数。

示例（HEAP表）

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

SELECT dept, name, SUM(salary) total, GROUPING(dept, name) 
FROM DEPT GROUP BY ROLLUP(dept, name);

dept       name             total  GROUPING(dept, name) 
---------- ---------- ----------- --------------------- 
A          LI                8000                     0 
A                           32500                     1 
                            54600                     3 
A          chang            10000                     0 
A          tina              6000                     0 
A          kate              4000                     0 
A          nora              4500                     0 
B          tom               7800                     0 
B                           14400                     1 
B          jerry             6600                     0 
C          lisa              4500                     0 
C                            7700                     1 
C          mona              3200                     0 

SELECT dept, name, SUM(salary) total, GROUPING(dept, name)
FROM DEPT GROUP BY GROUPING SETS(dept, name);

dept       name             total  GROUPING(dept, name) 
---------- ---------- ----------- --------------------- 
           LI                8000                     2 
A                           32500                     1 
           chang            10000                     2 
           tina              6000                     2 
           kate              4000                     2 
           nora              4500                     2 
           tom               7800                     2 
B                           14400                     1 
           jerry             6600                     2 
           lisa              4500                     2 
C                            7700                     1 
           mona              3200                     2 

SELECT dept, name, SUM(salary) total, GROUPING(dept, name)
FROM DEPT GROUP BY CUBE(dept, name);

dept       name             total  GROUPING(dept, name) 
---------- ---------- ----------- --------------------- 
A          LI                8000                     0 
A                           32500                     1 
           LI                8000                     2 
                            54600                     3 
A          chang            10000                     0 
           chang            10000                     2 
A          tina              6000                     0 
           tina              6000                     2 
A          kate              4000                     0 
           kate              4000                     2 
A          nora              4500                     0 
           nora              4500                     2 
B          tom               7800                     0 
B                           14400                     1 
           tom               7800                     2 
B          jerry             6600                     0 
           jerry             6600                     2 
C          lisa              4500                     0 
C                            7700                     1 
           lisa              4500                     2 
C          mona              3200                     0 
           mona              3200                     2 

SELECT dept, name, SUM(salary) total, GROUPING(dept, name) 
FROM DEPT GROUP BY dept,name;

dept       name             total  GROUPING(dept, name) 
---------- ---------- ----------- --------------------- 
A          LI                8000                     0 
A          chang            10000                     0 
A          tina              6000                     0 
A          kate              4000                     0 
A          nora              4500                     0 
B          tom               7800                     0 
B          jerry             6600                     0 
C          lisa              4500                     0 
C          mona              3200                     0 

```
