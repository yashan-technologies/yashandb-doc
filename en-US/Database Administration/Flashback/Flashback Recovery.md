YashanDB supports flashback recovery based on the entire database or a specified table. For more details, please refer to [FLASHBACK](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/FLASHBACK).

## Full Database Flashback

Full database flashback requires the following conditions:

- It is applicable only to Standalone Deployment.
- Ensure that the [full database flashback](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER DATABASE.html#flashbackdatabaseclauses) functionality is enabled. You can confirm this by querying the FLASHBACK_ON field in the V$DATABASE view.
- The user executing the full database flashback operation must have DBA privilege or FLASHBACK-related privilege.
- The database must be in MOUNT state when executing a full database flashback. After the flashback is complete, you must execute [ALTER DATABASE OPEN RESETLOGS](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/ALTER DATABASE.html#startupclauses) to reset the redo timeline.
- When performing a full database flashback using SCN or timestamp, the time point to which you can flashback is determined by the time the full database flashback functionality was enabled. Relevant information can be obtained from the V$FLASHBACK_DATABASE_LOG view.
- When performing a full database flashback using a restore point, you must first execute [CREATE RESTORE POINT](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE RESTORE POINT) to create the restore point.

Constraints for full database flashback include:

-  Currently, file-type DDL (such as tablespace DDL, data file DDL, etc.) and NOLOGGING objects cannot be flashed back.
- Operations on archive files and REDO files will not be flashed back.

***Example*** for Standalone Deployment

```sql
-- Check the FLASHBACK_ON field in the V$DATABASE view to confirm whether full database flashback is enabled
SELECT FLASHBACK_ON FROM V$DATABASE;

FLASHBACK_ON
-----------------
NO

-- If the field value is NO, it indicates that it is not enabled. You need to execute the following statement to enable full database flashback functionality
ALTER DATABASE FLASHBACK ON;

-- Create the area1 table and insert data
DROP TABLE IF EXISTS area1; 
CREATE TABLE area1
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
INSERT INTO area1 VALUES ('01','EastChina','Shanghai');
INSERT INTO area1 VALUES ('02','WestChina','Chengdu');

-- Create a restore point
CREATE RESTORE POINT p20250101;

-- Mistakenly delete the area1 table
DROP TABLE area1; 

-- Restart the database to the MOUNT stage and flashback the database based on the restore point
-- (Optional) Query V$RESTORE_POINT to get restore point information
SELECT NAME,CREATE_TIME FROM V$RESTORE_POINT;

-- Decide which restore point to flashback to based on the information in the view
FLASHBACK DATABASE TO RESTORE POINT p20250101;

-- Open the database and reset the redo timeline
ALTER DATABASE OPEN RESETLOGS;

-- Query the area1 table information
SELECT area_no,area_name FROM area1;
AREA_NO   AREA_NAME
--------- ----------------------------------------------------------------
01        EastChina
02        WestChina
```

## Table Flashback

Table flashback has the following requirements:

- The user executing the table flashback operation must have DBA privilege or FLASHBACK-related privilege.
- The structure of the table must not have changed during the period from the current timestamp to the target flashback timestamp.
- The time point to recover for table flashback is determined by the undo retention period (UNDO_RETENTION). It is recommended to set this parameter to 86400 seconds (24 hours) or longer.
- The table objects that can be recovered via table flashback must be HEAP tables and cannot be a table under the sys user.  (Tables under the SYS user will never enter the recycle bin after being dropped or truncated.)
- Before performing table flashback via SCN or timestamp, you must manually enable ROW MOVEMENT for the table. The data after the flashback may change rowid, so ensure that applications do not rely on rowid before executing table flashback.
- For dropped tables, ensure that historical data is still stored in the recycle bin to restore the table (including its structure and data).
   - The recycle bin was enabled before dropping.
   - The purge statement was not specified when dropping the table.
   - No purge operation has been performed on the recycle bin during the period from the current timestamp to the target flashback timestamp.
   - The tablespace was not full when dropping the table.
- For truncated tables, ensure that historical data is still stored in the recycle bin to restore the table's data (including truncate table or truncate partition). If new data is inserted during the period from the current timestamp to the truncate timestamp, executing the table flashback operation will place this new data into the recycle bin.
   - The recycle bin was enabled before truncating.
   - No purge operation has been performed on the recycle bin during the period from the current timestamp to the target flashback timestamp.
   - The tablespace was not full when truncating.

### delete operation flashback

***Example*** for Heap tables

```sql
-- Enable ROW MOVEMENT for the finance_info table
ALTER TABLE finance_info ENABLE ROW MOVEMENT;

-- A record exists in the finance_info table
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

 
-- Use FLASHBACK to recover historical data (flashback via SCN)
FLASHBACK TABLE finance_info TO TIMESTAMP  TIMESTAMP('2023-12-17 14:10:28.736908');
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000   

-- Get the last modified SCN (using ROWSCN)
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

-- Use FLASHBACK to recover historical data (flashback via SCN)
FLASHBACK TABLE finance_info TO SCN 408883147271815168;
SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' AND month='02';
YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
----- ----- ------ ------------- ----------- -----------
2021  02    0101           37778       33000        6000  
```

### drop operation flashback

***Example*** for Heap tables

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

DROP TABLE finance_info;
DROP TABLE orders_info;
DROP TABLE sales_info;
DROP TABLE employees;

-- 1. Check if the recycle bin contains the finance_info table
SELECT original_name,object_name FROM DBA_RECYCLEBIN 
WHERE original_name IN ('FINANCE_INFO','ORDERS_INFO','SALES_INFO','EMPLOYEES');
ORIGINAL_NAME                 OBJECT_NAME
----------------------------- -----------------------
SALES_INFO                    BIN$2393
ORDERS_INFO                   BIN$2389
EMPLOYEES                     BIN$2385
FINANCE_INFO                  BIN$2408

-- 2. Flashback table structure and data
FLASHBACK TABLE "BIN$2393" TO BEFORE DROP;
-- Or use the original name of the table for flashback
FLASHBACK TABLE finance_info TO BEFORE DROP;
-- Or specify a new name for the table using the RENAME TO command
FLASHBACK TABLE employees TO BEFORE DROP RENAME TO employees_recycle;

-- 3. Verify if the relevant objects retain the system-generated recycle bin name. If the name has not restored, please modify it manually using the ALTER INDEX statement
SELECT INDEX_NAME
FROM USER_INDEXES
WHERE TABLE_NAME = 'FINANCE_INFO';
INDEX_NAME
---------------------------------
IDX_FINANCE_INFO_1
```

### truncate operation flashback

***Example*** for Heap tables

```sql
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;
TRUNCATE TABLE product;

-- 1. Check if the recycle bin contains the product table
SELECT original_name,object_name FROM DBA_RECYCLEBIN WHERE original_name = 'PRODUCT';
ORIGINAL_NAME                 OBJECT_NAME
----------------------------- -----------------------
PRODUCT                       PRODUCT

-- 2. Flashback table data
FLASHBACK TABLE product TO BEFORE TRUNCATE;

-- 3. Verify if the relevant objects retain the system-generated recycle bin name. If the name has not restored, please modify it manually using the ALTER INDEX statement
SELECT INDEX_NAME
FROM USER_INDEXES
WHERE TABLE_NAME = 'PRODUCT';
INDEX_NAME
--------------------------
SYS_C_133
```
