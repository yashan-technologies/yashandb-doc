Generic Description
----

EXPLAIN is used to display the execution plan of a SQL statement.

When the execution plan for the SQL statement already exists in the plan cache, it is read and output directly; otherwise, the optimizer generates a plan and saves it to the plan cache before reading and outputting it.

Execution plans can only be displayed for DML type SQL statements, and all constraints for executing the SQL statement must be met.

Statement Definition
----

**explain::=**

```ebnf
= EXPLAIN [PLAN [SET PROJ ON] FOR] sql_statement.
```

### PLAN FOR

This is for compatibility and can be omitted. EXPLAIN PLAN FOR = EXPLAIN.

### SET PROJ ON

This is used to enable HEAP table projection printing.

> **Caution**: 
>
> HEAP table projection printing functionality is a laboratory feature and **not recommended for use in a production environment** as it may affect system stability.

### sql\_statement

The SQL statement for which to display the execution plan, with a length not exceeding 2M.

***Example***

```sql
-- SQL statement in Standalone Deployment 
EXPLAIN
WITH q_area(ano,aname) AS
(SELECT area_no ano,area_name aname FROM area WHERE area_no IN ('01','04'))
SELECT bno,bname,aname FROM (
SELECT a.branch_no bno,a.branch_name bname,b.aname aname FROM branches a,q_area b WHERE a.area_no=b.ano AND a.area_no='01'
UNION
SELECT a.branch_no bno,a.branch_name bname,b.aname aname FROM branches a,q_area b WHERE a.area_no=b.ano AND a.area_no='04'
);

-- SQL statement in ISC Distributed Cluster Deployment 
EXPLAIN
SELECT *
FROM (SELECT * FROM area WHERE area_no IN ('01','02')
      UNION
      SELECT * FROM area WHERE area_no IN ('01','03')
     );
```

Output Definition
----

Output 1. The execution plan for Standalone Deployment HEAP table is as follows:

```sql
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3782799764                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  VIEW                          |                      |            |      1000|     1652( 0)|                                |
|  2 |   HASH DISTINCT                |                      |            |      1000|     1652( 0)|                                |
|  3 |    VIEW                        |                      |            |   2512000|     1576( 0)|                                |
|  4 |     UNION ALL                  |                      |            |   2512000|     1446( 0)|                                |
|  5 |      NESTED LOOPS INNER        |                      |            |   1256000|      687( 0)|                                |
|* 6 |       TABLE ACCESS FULL        | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|* 7 |       VIEW                     |                      |            |       314|      151( 0)|                                |
|  8 |        TABLE ACCESS BY INDEX ROWID| AREA                 | SALES      |          |             |                                |
|* 9 |         INDEX RANGE SCAN       | SYS_C_13             | SALES      |      7840|      151( 0)|                                |
| 10 |      NESTED LOOPS INNER        |                      |            |   1256000|      687( 0)|                                |
|*11 |       TABLE ACCESS FULL        | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|*12 |       VIEW                     |                      |            |       314|      151( 0)|                                |
| 13 |        TABLE ACCESS BY INDEX ROWID| AREA                 | SALES      |          |             |                                |
|*14 |         INDEX RANGE SCAN       | SYS_C_13             | SALES      |      7840|      151( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   6 - Predicate : filter("A"."AREA_NO" = '01')                 
   7 - Predicate : filter("B"."ANO" = '01')                     
   9 - Predicate : access("AREA"."AREA_NO" IN ('01', '04'))     
  11 - Predicate : filter("A"."AREA_NO" = '04')                 
  12 - Predicate : filter("B"."ANO" = '04')                     
  14 - Predicate : access("AREA"."AREA_NO" IN ('01', '04'))    
```

Output 2. The execution plan for Standalone Deployment TAC table is as follows:

```sql
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3782799764                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  COL TO ROW                    |                      |            |          |             |                                |
|  2 |   RESULT                       |                      |            |      1000|     1652( 0)|                                |
|  3 |    HASH GROUP                  |                      |            |      1000|     1652( 0)|                                |
|  4 |     RESULT                     |                      |            |   2512000|     1576( 0)|                                |
|  5 |      UNION ALL                 |                      |            |   2512000|     1446( 0)|                                |
|  6 |       NESTED LOOPS INNER       |                      |            |   1256000|      687( 0)|                                |
|* 7 |        TABLE ACCESS FULL       | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|* 8 |        RESULT                  |                      |            |       314|      151( 0)|                                |
|  9 |         TABLE ACCESS BY INDEX ROWID| AREA                 | SALES      |          |             |                                |
|*10 |          INDEX RANGE SCAN      | SYS_C_58             | SALES      |      7840|      151( 0)|                                |
| 11 |       NESTED LOOPS INNER       |                      |            |   1256000|      687( 0)|                                |
|*12 |        TABLE ACCESS FULL       | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|*13 |        RESULT                  |                      |            |       314|      151( 0)|                                |
| 14 |         TABLE ACCESS BY INDEX ROWID| AREA                 | SALES      |          |             |                                |
|*15 |          INDEX RANGE SCAN      | SYS_C_58             | SALES      |      7840|      151( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Projection: RemoteTable[1][CHAR, 4], RemoteTable[1][VARCHAR, 200], RemoteTable[1][VARCHAR, 60]
   2 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   3 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   4 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   5 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   6 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[1, 0][VARCHAR, 60]
   7 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200]
       Predicate : access("A"."AREA_NO" = '01')                 
   8 - Projection: Tuple[0, 1][VARCHAR, 60]                     
       Predicate : filter(Tuple[0, 0] = '01')                   
  10 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
       Predicate : access("AREA"."AREA_NO" IN ('01', '04'))     
  11 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[1, 0][VARCHAR, 60]
  12 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200]
       Predicate : access("A"."AREA_NO" = '04')                 
  13 - Projection: Tuple[0, 1][VARCHAR, 60]                     
       Predicate : filter(Tuple[0, 0] = '04')                   
  15 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
       Predicate : access("AREA"."AREA_NO" IN ('01', '04'))        
```

Output 3. The execution plan for Standalone Deployment LSC table is as follows:

```sql
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3782799764                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  COL TO ROW                    |                      |            |          |             |                                |
|  2 |   RESULT                       |                      |            |      1000|     2252( 0)|                                |
|  3 |    HASH GROUP                  |                      |            |      1000|     2252( 0)|                                |
|  4 |     RESULT                     |                      |            |   2512000|     2176( 0)|                                |
|  5 |      UNION ALL                 |                      |            |   2512000|     2046( 0)|                                |
|  6 |       NESTED LOOPS INNER       |                      |            |   1256000|      987( 0)|                                |
|* 7 |        TABLE ACCESS FULL       | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|* 8 |        RESULT                  |                      |            |       314|      451( 0)|                                |
|* 9 |         TABLE ACCESS FULL      | AREA                 | SALES      |      7840|      451( 0)|                                |
| 10 |       NESTED LOOPS INNER       |                      |            |   1256000|      987( 0)|                                |
|*11 |        TABLE ACCESS FULL       | BRANCHES             | SALES      |      4000|      445( 0)|                                |
|*12 |        RESULT                  |                      |            |       314|      451( 0)|                                |
|*13 |         TABLE ACCESS FULL      | AREA                 | SALES      |      7840|      451( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   1 - Projection: RemoteTable[1][CHAR, 4], RemoteTable[1][VARCHAR, 200], RemoteTable[1][VARCHAR, 60]
   2 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   3 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   4 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   5 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][VARCHAR, 60]
   6 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[1, 0][VARCHAR, 60]
   7 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200]
       Predicate : access("A"."AREA_NO" = '01')                 
   8 - Projection: Tuple[0, 1][VARCHAR, 60]                     
       Predicate : filter(Tuple[0, 0] = '01')                   
   9 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
       Predicate : access("AREA"."AREA_NO" IN ('01', '04'))     
  10 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200], Tuple[1, 0][VARCHAR, 60]
  11 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][VARCHAR, 200]
       Predicate : access("A"."AREA_NO" = '04')                 
  12 - Projection: Tuple[0, 1][VARCHAR, 60]                     
       Predicate : filter(Tuple[0, 0] = '04')                   
  13 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
       Predicate : access("AREA"."AREA_NO" IN ('01', '04'))   
```

Output 4. The execution plan for distributed TAC/LSC tables is as follows:

```sql
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3958538062                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  DISTRIBUTED COORDINATOR       |                      |            |          |             |                                |
|  2 |   COL TO ROW                   |                      |            |          |             |                                |
|  3 |    RESULT                      |                      |            |      1000|      959( 0)|                                |
|  4 |     HASH DISTINCT              |                      |            |      1000|      959( 0)|                                |
|  5 |      RESULT                    |                      |            |     15680|      958( 0)|                                |
|  6 |       UNION ALL                |                      |            |     15680|      957( 0)|                                |
|  7 |        PX N2I REMOTE           | QUEUE_0              |            |      7840|      478( 0)|                                |
|* 8 |         TABLE ACCESS FULL      | AREA                 | SALES      |      7840|      451( 0)|                                |
|  9 |        PX N2I REMOTE           | QUEUE_1              |            |      7840|      478( 0)|                                |
|*10 |         TABLE ACCESS FULL      | AREA                 | SALES      |      7840|      451( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   2 - Projection: RemoteTable[1][CHAR, 2]                      
   3 - Projection: Tuple[0, 0][CHAR, 2]                         
   4 - Projection: Tuple[0, 0][CHAR, 2]                         
   5 - Projection: Tuple[0, 0][CHAR, 2]                         
   6 - Projection: Tuple[0, 0][CHAR, 2]                         
   7 - Projection: Tuple[0, 0][CHAR, 2]                         
       PX RemoteInfo: (RANDOM SENDER -> RANDOM RECEIVER : 3->1 [3][4][5]->[2])
   8 - Projection: Tuple[0, 0][CHAR, 2]                         
       Predicate : access("AREA"."AREA_NO" IN ('01', '02'))     
   9 - Projection: Tuple[0, 0][CHAR, 2]                         
       PX RemoteInfo: (RANDOM SENDER -> RANDOM RECEIVER : 3->1 [3][4][5]->[2])
  10 - Projection: Tuple[0, 0][CHAR, 2]                         
       Predicate : access("AREA"."AREA_NO" IN ('01', '03'))    
```

### SQL hash value

The hash number of the SQL statement, corresponding to the hash_value field in the V$SQL view.

### Optimizer

The name of the optimizer used by YashanDB, where ADOPT_C indicates CBO.

### Id

The execution step number, executed in reverse order from bottom to top in a binary tree structure.

The following is an example of the execution binary tree:

![](./image/25.png)

### Operation type

The execution operator defines the actions for each step in the execution plan.

As seen in outputs 1 and 2, INDEX RANGE SCAN indicates execution of an index range scan to fetch the ROWID, while TABLE ACCESS BY INDEX ROWID reads the row data based on the ROWID, proceeding up to the 0 step operator.

As seen in outputs 3 and 4, TABLE ACCESS FULL indicates a full table scan, retrieving records RESULT, before applying the UNION ALL algorithm on the two RESULTS, and continuing up to the 0 step operator.

The PX operators can be categorized as BROADCAST, HASH, RANDOM, etc., indicating how data is redistributed between nodes. Each PX operator will list the sending conditions. N2I means multiple nodes are sending data to one node; specific sending and receiving nodes can be seen in the projection information.

### Name

The name of the database objects operated on, such as table or index names when scanning.

For PX operators, the Name column indicates the unique identifier used in the communication process.

### Owner

The owner of the operation objects. For example, in output 1, area and branches tables, along with the SYS_C_16 index, have their Owner as the SALES user.

In output 2, the area and branches tables have their Owner as the SALES_LSC user.

In output 3, the area table has its Owner as the SALES user.

### Rows

The number of records scanned by the operator.

### Cost(%CPU)

The estimated CPU cost incurred by executing the operator.

### Partition Info

For partitioned tables, this column shows the partitions scanned by the operator.

***Example***1 for Standalone Deployment Heap tables

```sql
-- Create the sales table as a range partitioned table and create a local index on the year field.
CREATE TABLE sales
(year CHAR(4) NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4),
product CHAR(5),
quantity NUMBER DEFAULT 0 NOT NULL,
amount NUMBER(10,2) DEFAULT 0 NOT NULL,
salsperson CHAR(10))
PARTITION BY RANGE(year)
(PARTITION p_sales_1 VALUES LESS THAN ('2001'),
PARTITION p_sales_2 VALUES LESS THAN ('2011'),
PARTITION p_sales_3 VALUES LESS THAN (2022));
 
CREATE INDEX idx_sales ON sales(year) LOCAL;
 
INSERT INTO sales VALUES ('2001','01','0201','11001',30,500,'0201010011');
INSERT INTO sales VALUES ('2015','11','0101','11001',20,300,'');
INSERT INTO sales VALUES ('2021','10','0101','11001',20,300,'');
INSERT INTO sales VALUES ('2000','12','0102','11001',20,300,'');
INSERT INTO sales VALUES ('2015','03','0102','11001',20,300,'');
INSERT INTO sales VALUES ('2020','05','0101','11001',40,600,'');
COMMIT;
 
-- View the execution plan
EXPLAIN
SELECT a.area_name,
b.branch_name,
s.product,
s.amount
FROM branches b,
area a,
(SELECT branch,
product,SUM(amount) amount
FROM sales
WHERE year>'2005'
GROUP BY branch,product) s
WHERE s.branch=b.branch_no
AND b.area_no=a.area_no;
```

The output results for the above are (the notation \[1,2\] indicates scanning partitions 1 to 2):

```sql
-- For HEAP table, TAC table will include an additional COL TO ROW operator
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3362858118                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  NESTED LOOPS INNER            |                      |            |      1000|      461( 0)|                                |
|  2 |   NESTED LOOPS INNER           |                      |            |      1000|      311( 0)|                                |
|  3 |    VIEW                        |                      |            |      1000|      161( 0)|                                |
|* 4 |     HASH GROUP                 |                      |            |      1000|      161( 0)|                                |
|  5 |      PART SCAN ITERATOR        |                      |            |     33000|      160( 0)| [1,2]                          |
|  6 |       TABLE ACCESS BY INDEX ROWID| SALES                | SALES      |          |             |                                |
|* 7 |        INDEX RANGE SCAN        | IDX_SALES            | SALES      |     33000|      160( 0)|                                |
|  8 |    TABLE ACCESS BY INDEX ROWID | BRANCHES             | SALES      |          |             |                                |
|* 9 |     INDEX UNIQUE SCAN          | SYS_C_125            | SALES      |         1|      149( 0)|                                |
| 10 |   TABLE ACCESS BY INDEX ROWID  | AREA                 | SALES      |          |             |                                |
|*11 |    INDEX UNIQUE SCAN           | SYS_C_123            | SALES      |         1|      149( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   4 - Predicate : group expressions("SALES"."BRANCH", "SALES"."PRODUCT")
   7 - Predicate : access("SALES"."YEAR" > '2005')              
   9 - Predicate : access("B"."BRANCH_NO" = "S"."BRANCH")       
  11 - Predicate : access("A"."AREA_NO" = "B"."AREA_NO")               
```

***Example***2 for ISC Distributed Cluster Deployment LSC tables

```sql
-- The sales table is a range partitioned table
CREATE DUPLICATED TABLE sales
(year CHAR(4) NOT NULL,
month CHAR(2) NOT NULL,
branch CHAR(4),
product CHAR(5),
quantity NUMBER DEFAULT 0 NOT NULL,
amount NUMBER(10,2) DEFAULT 0 NOT NULL,
salsperson CHAR(10))
PARTITION BY RANGE(year)
(PARTITION p_sales_1 VALUES LESS THAN ('2001'),
PARTITION p_sales_2 VALUES LESS THAN ('2011'),
PARTITION p_sales_3 VALUES LESS THAN (2022));
  
INSERT INTO sales VALUES ('2001','01','0201','11001',30,500,'0201010011');
INSERT INTO sales VALUES ('2015','11','0101','11001',20,300,'');
INSERT INTO sales VALUES ('2021','10','0101','11001',20,300,'');
INSERT INTO sales VALUES ('2000','12','0102','11001',20,300,'');
INSERT INTO sales VALUES ('2015','03','0102','11001',20,300,'');
INSERT INTO sales VALUES ('2020','05','0101','11001',40,600,'');
COMMIT;

-- View the execution plan for the following statement, \[1,2\] indicates scanning partitions 1 to 2
EXPLAIN
SELECT a.area_name,
b.branch_name,
s.product,
s.amount
FROM branches b,
area a,
(SELECT branch,
product,SUM(amount) amount
FROM sales
WHERE year>'2005'
GROUP BY branch,product) s
WHERE s.branch=b.branch_no
AND b.area_no=a.area_no;
 
PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 3362858118                                      
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  DISTRIBUTED COORDINATOR       |                      |            |          |             |                                |
|  2 |   COL TO ROW                   |                      |            |      1000|      734( 0)|                                |
|  3 |    PX N2I REMOTE               | QUEUE_0              |            |      1000|      734( 0)|                                |
|* 4 |     HASH JOIN INNER            |                      |            |      1000|      730( 0)|                                |
|  5 |      JOIN FILTER USE           |                      |            |    100000|      271( 0)|                                |
|  6 |       PART SCAN ALL            |                      |            |    100000|      271( 0)| [0,20]                         |
|* 7 |        TABLE ACCESS FULL       | AREA                 | SYS        |    100000|      271( 0)|                                |
|* 8 |      JOIN FILTER CREATE        |                      |            |      1000|      450( 0)|                                |
|* 9 |       PX N2N REMOTE            | QUEUE_1              |            |      1000|      450( 0)|                                |
|*10 |        HASH JOIN INNER         |                      |            |      1000|      446( 0)|                                |
| 11 |         PART SCAN ALL          |                      |            |    100000|      272( 0)| [0,20]                         |
| 12 |          TABLE ACCESS FULL     | BRANCHES             | SYS        |    100000|      272( 0)|                                |
| 13 |         RESULT                 |                      |            |      1000|      169( 0)|                                |
| 14 |          HASH GROUP            |                      |            |      1000|      169( 0)|                                |
|*15 |           PX I2N REMOTE        | QUEUE_2              |            |     33000|      166( 0)|                                |
| 16 |            PART SCAN ITERATOR  |                      |            |     33000|      153( 0)| [1,2]                          |
|*17 |             TABLE ACCESS FULL  | SALES                | SYS        |     33000|      153( 0)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   2 - Projection: RemoteTable[3][VARCHAR, 60], RemoteTable[3][VARCHAR, 200], RemoteTable[3][CHAR, 5], RemoteTable[3][NUMBER]
   3 - Projection: Tuple[0, 0][VARCHAR, 60], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][CHAR, 5], Tuple[0, 3][NUMBER]
       PX RemoteInfo: (RANDOM SENDER -> RANDOM RECEIVER : 3->1 [3][4][5]->[2])
   4 - Projection: Tuple[0, 1][VARCHAR, 60], Tuple[1, 1][VARCHAR, 200], Tuple[1, 2][CHAR, 5], Tuple[1, 3][NUMBER]
       Predicate : access(Tuple[0, 0] = Tuple[1, 0])            
   5 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
   6 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
   7 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 60]
       Predicate : RUNTIME FILTER(RUNTIME USE(0): "A"."AREA_NO")
   8 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][CHAR, 5], Tuple[0, 3][NUMBER]
       Predicate : RUNTIME FILTER(RUNTIME CREATE(0): Tuple[0, 0])
   9 - Projection: Tuple[0, 0][CHAR, 2], Tuple[0, 1][VARCHAR, 200], Tuple[0, 2][CHAR, 5], Tuple[0, 3][NUMBER]
       PX RemoteInfo: (HASH SENDER -> RANDOM RECEIVER : 3->3 [3][4][5]->[3][4][5])
       Predicate : access(Tuple[0, 0])                          
  10 - Projection: Tuple[0, 1][CHAR, 2], Tuple[0, 2][VARCHAR, 200], Tuple[1, 1][CHAR, 5], Tuple[1, 2][NUMBER]
       Predicate : access(Tuple[0, 0] = Tuple[1, 0])            
  11 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 1][CHAR, 2], Tuple[0, 2][VARCHAR, 200]
  12 - Projection: Tuple[0, 0][CHAR, 4], Tuple[0, 2][CHAR, 2], Tuple[0, 1][VARCHAR, 200]
  13 - Projection: Tuple[0, 1][CHAR, 4], Tuple[0, 2][CHAR, 5], Tuple[0, 0][NUMBER]
  14 - Projection: SUM(Tuple[0, 0])[NUMBER], Tuple[0, 1][CHAR, 4], Tuple[0, 2][CHAR, 5]
       Group Expression: (Tuple[0, 1], Tuple[0, 2])             
  15 - Projection: Tuple[0, 0][NUMBER, (10, 2)], Tuple[0, 1][CHAR, 4], Tuple[0, 2][CHAR, 5]
       PX RemoteInfo: (HASH SENDER -> RANDOM RECEIVER : 1->3 [3]->[3][4][5])
       Predicate : access(Tuple[0, 1])                          
  16 - Projection: Tuple[0, 0][NUMBER, (10, 2)], Tuple[0, 1][CHAR, 4], Tuple[0, 2][CHAR, 5]
  17 - Projection: Tuple[0, 5][NUMBER, (10, 2)], Tuple[0, 2][CHAR, 4], Tuple[0, 3][CHAR, 5]
       Predicate : access("SALES"."YEAR" > '2005')                    
```

### Predicate

Predicate information includes:

* access: limiting predicates used to reduce the size of the result set, such as index limiting predicates and HashJoin limiting predicates.
* filter: filtering predicates that perform filtering operations on each record in the result set.
* projection: projection information referencing lower-level operators. The notation Tuple[sourceId, attrId] indicates the attrId projection of the sourceId data source of the lower-level operator, both starting from 0.