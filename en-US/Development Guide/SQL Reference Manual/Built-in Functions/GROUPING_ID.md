```ebnf
grouping_id = GROUPING_ID "(" expr {"," expr  } ")".
```

When performing grouping and sorting calculations with GROUP BY, if you need to further extend aggregate calculations using ROLLUP or GROUPING SETS, you can use the GROUPING function to determine whether a NULL value in the query result represents a column value or a ROLLUP aggregation marker. A result of 1 indicates an aggregated value, while 0 indicates a normal column value. 

The return type of this function is NUMBER. When there is only one parameter [expr](../General SQL Syntax/expr) of GROUPING_ID, the result is the same as GROUPING.

This function supports 1 to 126 parameters. An error will be reported if the number of parameters is outside this range.

This function must be used in conjunction with the `GROUP BY`, `GROUP BY ROLLUP`, `GROUP BY CUBE`, or `GROUP BY GROUPING SETS` statements, and DISTINCT cannot be used within the function.

The [expr](../General SQL Syntax/expr) can be any data type except UDT, cannot be NULL, and cannot be an aggregate function.

***Example***

In this example, the GROUPING value as 1 indicates an aggregate calculation on the name column, while a value as 3 indicates an aggregate calculation on both dept and name.

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
