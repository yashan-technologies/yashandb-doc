Table flashback essentially restores tables/table partitions and their dependent objects from the [Recycle Bin](../基本数据库管理/回收站管理). This feature is therefore subject to Recycle Bin constraints (e.g., the Recycle Bin must be enabled) and requires the target objects to remain in the Recycle Bin.  

The SQL statement is FLASHBACK TABLE [PARTITION|SUBPARTITION] TO BEFORE ……, detailed syntax can be found in [FLASHBACK](../../开发手册/SQL参考手册/SQL语句/FLASHBACK.html#tableclauses).  

For truncated tables or partitions, if new data is inserted after truncating, executing the table flashback operation will place this new data into the recycle bin.



When flashing back tables/partitions, data recovery follows these rules: 

- If the object to be recovered is a table that has been dropped, you need to check whether the original table name is occupied. If it is, you must rename the table during the Flashback operation; otherwise, an error will occur.

- If an object has been dropped multiple times, each Flashback operation will only restore one DROP operation in reverse chronological order. That is, the first Flashback will restore only the most recent DROP. If the previous Flashback operation did not rename the object (retaining the original table name), subsequent Flashback operations will no longer be able to restore the object using the original name.

- If an object has been truncated multiple times, each Flashback operation will only restore one TRUNCATE operation in reverse chronological order. That is, the first Flashback will restore only the most recent TRUNCATE operation.

- Objects always adhere to hierarchical dependencies:

    - Partitions that entered the Recycle Bin via TRUNCATE TABLE cannot be restored individually. Subpartitions that entered via TRUNCATE PARTITION of a primary partition cannot be restored individually.

    - Dependent objects (indexes, triggers, etc.) cannot be restored separately. They are automatically recovered with their parent table/partition if still present in the Recycle Bin.



## Prerequisites

- The user executing the table flashback operation must have DBA privilege or FLASHBACK-related privilege.

- Ensure the target table/partition remains in the Recycle Bin. Use the [DBA_RECYCLEBIN view](../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) (or corresponding USER_RECYCLEBIN/ALL_RECYCLEBIN views) to verify.

- For dropped tables, confirm if the original table name is occupied. If occupied, rename during flashback. 

## Examples

### Flashback Drop Operation

***Example*** for Heap tables

```sql
-- Environment setup (enable Recycle Bin, drop sample tables)
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
FLASHBACK TABLE BIN$2393 TO BEFORE DROP;
-- Or use the original name of the table for flashback
FLASHBACK TABLE finance_info TO BEFORE DROP;
-- Or specify a new name for the table using the RENAME TO command
FLASHBACK TABLE employees TO BEFORE DROP RENAME TO employees_recycle;

-- 3. Verify if the relevant objects retain the system-generated recycle bin name. If the name has not restored, please modify it manually using the ALTER INDEX statement
SELECT INDEX_NAME FROM USER_INDEXES WHERE TABLE_NAME = 'FINANCE_INFO';

INDEX_NAME
---------------------------------
IDX_FINANCE_INFO_1
```

### Flashback Truncate Operation

***Example*** for Heap tables

```sql
-- Environment setup (enable Recycle Bin, truncate sample data, insert new data)
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;

YEAR      MONTH     BRANCH    REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
--------- --------- --------- ------------- ----------- -----------
2001      01        0201               2888        2000         300
2021      01        0201              28888       24000        3000
2021      01        0101              38888       34000        4000

TRUNCATE TABLE finance_info;

INSERT INTO finance_info VALUES ('2024','01','0201',28808,12000,1000);
COMMIT;

-- 1. Check if the recycle bin contains the product table
SELECT ORIGINAL_NAME,OPERATION,RECYCLEBIN_TIME FROM DBA_RECYCLEBIN WHERE original_name = 'FINANCE_INFO';

ORIGINAL_NAME                                                    OPERATION                 RECYCLEBIN_TIME
---------------------------------------------------------------- ------------------------- -----------------------------------------
FINANCE_INFO                                                     TRUNCATE                  2024-12-13:16:25:16                                           

-- 2. Flashback table
FLASHBACK TABLE finance_info TO BEFORE TRUNCATE;

SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;

YEAR      MONTH     BRANCH    REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
--------- --------- --------- ------------- ----------- -----------
2001      01        0201               2888        2000         300
2021      01        0201              28888       24000        3000
2021      01        0101              38888       34000        4000


-- 4. Check Recycle Bin entries for finance_info and compare timestamps
SELECT ORIGINAL_NAME,OPERATION,RECYCLEBIN_TIME FROM DBA_RECYCLEBIN WHERE original_name = 'FINANCE_INFO';

ORIGINAL_NAME                                                    OPERATION                 RECYCLEBIN_TIME
---------------------------------------------------------------- ------------------------- -----------------------------------------
FINANCE_INFO                                                     TRUNCATE                  2024-12-13:16:27:05 
```
