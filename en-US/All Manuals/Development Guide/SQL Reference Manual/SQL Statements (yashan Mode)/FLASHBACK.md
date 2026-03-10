## General Description

FLASHBACK is used to implement historical data flashback for databases or tables.

## Statement Definition

**flashback::=**

```ebnf+diagram
syntax::= FLASHBACK (database_clauses|table_clauses)
```

**[database\_clauses](#databaseclauses)::=**

```ebnf+diagram
syntax::= DATABASE (TO (SCN scn|TIME timestamp|RESTORE POINT restore_point_name))
```

**[table\_clauses](#tableclauses)::=**

```ebnf+diagram
syntax::= TABLE table_name (TO (SCN scn|Timestamp timestamp| BEFORE (DROP [RENAME TO new_name]|TRUNCATE)) | (PARITITION|SUBPARTITION) part_name TO BEFORE TRUNCATE)
```

<span id="databaseclauses" name="databaseclauses" class="yaslink"></span>

### 1. database\_clauses

This statement is used to perform a full database flashback.

FLASHBACK DATABASE must comply with the following rules:

- Only applicable to Standalone Deployment.
- Ensure that the [full database flashback](ALTER DATABASE.html#flashbackdatabaseclauses) functionality is enabled.
- Currently, file-related DDL (such as tablespace DDL, data file DDL, etc.) and NOLOGGING objects cannot be flashed back.
- Operations on archived files and REDO files cannot be flashed back.
- Cold data physical files in lsc tables will not be cleaned after flashback and need to be deleted manually.
- To flash back to a specified restore point, you must first [CREATE RESTORE POINT](CREATE RESTORE POINT).
- When performing a full database flashback, the database must be in NOMOUNT state. After flashback is complete, you must [ALTER DATABASE OPEN RESETLOGS](ALTER DATABASE.html#startupclauses) to reset the redo timeline.

#### 1.1. TO SCN|TIME

This statement is used to flash back the database to a specified SCN|TIME point.

***Example*** for Standalone Deployment

```sql
-- Query V$FLASHBACK_DATABASE_LOG to get the oldest flashback time point
SELECT OLDEST_FLASHBACK_SCN,OLDEST_FLASHBACK_TIME FROM V$FLASHBACK_DATABASE_LOG;

-- Determine the flashback time point based on the retrieved oldest time point
FLASHBACK DATABASE TO SCN 625007001600000000;
FLASHBACK DATABASE TO TIME TIMESTAMP;
```

#### 1.2. TO RESTORE POINT

This statement is used to flash back the database to a specified restore point. You can obtain existing restore point information through the V$RESTORE_POINT view.

***Example*** for Standalone Deployment

```sql
-- Query V$RESTORE_POINT to get current restore point information
SELECT * FROM V$RESTORE_POINT;

-- Decide which restore point to flash back to based on the information in the view
FLASHBACK DATABASE TO RESTORE POINT p202412011100;
```

<span id="tableclauses" name="tableclauses" class="yaslink"></span>

### 2. table\_clauses

This statement is used to flash back historical data of a table.

FLASHBACK TABLE must comply with the following rules:

- Only applicable to HEAP tables.
- FLASHBACK TO SCN is not allowed in scenarios where DDL that changes the table structure occurred from the specified SCN point to the present, such as:
    - UPDATE TABLE, MOVE TABLE, TRUNCATE TABLE
    - Adding constraints to the table
    - MODIFY COLUMN, DROP COLUMN
    - ADD, DROP, MERGE, SPLIT, COALESCE, TRUNCATE partition or subpartition
- FLASHBACK cannot be performed within a transaction.
- The target table for FLASHBACK cannot have dependencies on other tables.
- FLASHBACK does not maintain ROWID.
- FLASHBACK only maintains indexes that exist at the current moment:
    - Indexes that were not created at the SCN point but exist at the current moment: Data for such indexes will be flashed back, but the index metadata will be based on the current moment.
    - Indexes that existed at the SCN point but do not exist at the current moment: Such indexes and their data will not be recovered.
- FLASHBACK is not allowed to be performed on data that would violate constraints.
- In scenarios where FLASHBACK is applied to multiple tables, it must either all succeed or all fail.
- FLASHBACK does not restore statistics.

#### 2.1. TO SCN|TIMESTAMP

This statement is used to flash back the data of the table to the specified SCN|TIMESTAMP point. This functionality does not require enabling the recycle bin.

Data flashed back using this functionality may change ROWID, therefore the ROW MOVEMENT switch for the table needs to be enabled beforehand (refer to the syntax described in [ALTER TABLE](ALTER TABLE)); otherwise, an error will be returned.

If the table to be flashed back has foreign key constraints, you must first remove the foreign key constraints from other tables before performing the flashback; otherwise, an error will be returned.

TO SCN|TIMESTAMP flashback is not applicable to subpartitions, meaning that it cannot flash back a subpartition to a specified SCN|TIMESTAMP point.

***Example*** for Heap tables

```sql
-- Enable ROW MOVEMENT switch for finance_info
ALTER TABLE finance_info ENABLE ROW MOVEMENT;

-- A record that exists in the finance_info table
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000   
 
-- Get the current SCN
SELECT SYSTIMESTAMP res FROM dual;
RES                                           
-----------------------------------------------
2023-12-17 14:10:28.736908   

-- Delete this record and commit
DELETE FROM finance_info WHERE year='2021' AND month='02';
COMMIT;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------

-- Use FLASHBACK to flash back historical data (flash back by SCN)
FLASHBACK TABLE finance_info TO TIMESTAMP  TIMESTAMP('2023-12-17 14:10:28.736908');
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000   

-- Get the last modification SCN (using ROWSCN to get)
SELECT rowscn FROM finance_info WHERE year='2021' AND month='02';
               ROWSCN 
--------------------- 
   408883147271815168

-- Delete the record again
DELETE FROM finance_info WHERE year='2021' AND month='02';
COMMIT;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------

-- FLASHBACK to flash back historical data (flash back by SCN)
FLASHBACK TABLE finance_info TO SCN 408883147271815168;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000  
```

#### 2.2. BEFORE

This statement is used to flash back a deleted table to its state before deletion or to flash back the data of a truncated table. This functionality requires enabling the recycle bin (set the [configuration parameter](ALTER SYSTEM) RECYCLEBIN_ENABLED to ON).

##### 2.2.1. DROP

This statement is used to flash back a deleted table, including its structure and data.

In practice, the name of the deleted table may have already been reused or occupied by other objects. In this case, you need to specify the RENAME TO keyword to rename the restored table; otherwise, an error YAS-02013 will be returned.

***Example*** for Heap tables

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

DROP TABLE finance_info;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;
[1:15]YAS-02012 table or view does not exist

FLASHBACK TABLE finance_info TO BEFORE DROP;
-- If the name finance_info has already been used, it needs to be renamed
-- FLASHBACK TABLE finance_info TO BEFORE DROP RENAME TO finance_info_new;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL 
----- ----- ------ ------------- ----------- ----------- 
2001  01    0201            2888        2000         300
2021  01    0201           28888       24000        3000
2021  01    0101           38888       34000        4000
2021  02    0101           37778       33000        6000
```

##### 2.2.2. TRUNCATE

This statement is used to flash back the data of a truncated table. Regardless of whether multiple truncates have occurred, only the data from the last truncate can be flashed back.

***Example*** for Heap tables

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

TRUNCATE TABLE sales_info_range;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 

FLASHBACK TABLE sales_info_range TO BEFORE TRUNCATE;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info_range;
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 
2001  01    0201   11001              30         500 0201010011   
2000  12    0102   11001              20         300              
2015  11    0101   11001              20         300              
2015  03    0102   11001              20         300              
2021  10    0101   11001              20         300              
2021  05    0101   11001              40         600  
```

#### 2.3. PARTITION|SUBPARTITION

This statement is used to flash back the data of a truncated partition or subpartition. This functionality requires enabling the recycle bin (set the [configuration parameter](ALTER SYSTEM) RECYCLEBIN_ENABLED to ON). Empty partitions will also enter the recycle bin when truncated and can be flashed back.

In practical applications, if a partition or subpartition is truncated and new data is inserted afterward, performing a flashback will place this "new data" into the recycle bin. Additionally, if there is a global index primary key or unique key that the table is dependent on, an error will occur.

After directly truncating the entire partition table, individual partitions cannot be flashed back. Similarly, after truncating a primary partition, specific subpartitions within that primary partition cannot be flashed back either.

If a partition or subpartition is dropped, it can no longer be flashed back.

***Example*** for Heap tables

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

ALTER TABLE sales_info TRUNCATE PARTITION p_sales_info_1;
ALTER TABLE sales_info TRUNCATE SUBPARTITION  P_SALES_INFO_2_SP_SALES_INFO_1;

SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION (p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON    
----- ----- ------ --------- ----------- ----------- ------------- 

INSERT INTO sales_info VALUES ('2021','06','0402','11001',40,600,'');
COMMIT;

FLASHBACK TABLE sales_info PARTITION p_sales_info_1 TO BEFORE TRUNCATE;
FLASHBACK TABLE sales_info SUBPARTITION P_SALES_INFO_2_SP_SALES_INFO_1 TO BEFORE TRUNCATE;
-- After flashback, the newly added data is placed in the recycle bin, and only the previous data exists in the p_sales_info_1 partition
SELECT year,month,branch,product,quantity,amount,salsperson FROM sales_info PARTITION (p_sales_info_1);
YEAR  MONTH BRANCH PRODUCT      QUANTITY      AMOUNT SALSPERSON
----- ----- ------ --------- ----------- ----------- -------------
2021  10    0402   11001              20         300
```
