Data flashback refers to querying historical data of existing tables at a past point-in-time through UNDO logs, either returning the result set as a flashback query or restoring the table state via FLASHBACK TABLE TO SCN|TIMESTAMP.

The time range for data flashback is determined by the UNDO log lifecycle (i.e., undo retention period), influenced primarily by:

- Undo retention period

   Controlled by the UNDO_RETENTION configuration parameter (default: 300 seconds). Theoretically, UNDO blocks are retained for the specified duration after transaction commit, after which they may be reused.

- UNDO tablespace capacity

   If the UNDO tablespace lacks sufficient resources to allocate new blocks, even unexpired UNDO blocks from committed transactions may be reused.

Therefore, DBAs should comprehensively consider actual business requirements for flashback duration and disk configurations to properly plan [UNDO tablespace](../../Storage Management/Logical Space Management/Tablespace Management/UNDO Tablespace Management) configurations.

## Querying Historical Data of Existing Tables

Flashback queries are commonly used for data auditing, error tracing, and identifying target timestamps for recovery operations. For example, when unexpected data is found, flashback queries can help trace historical data to identify root causes.

For syntax details, refer to the [flashback_query_clause](../../../Development Guide/SQL Reference Manual/SQL Statements/SELECT.md#flashbackqueryclause) in the SELECT statement documentation.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- A record that exists in the area table
SELECT area_no,area_name,DHQ FROM area;
AREA_NO   AREA_NAME                                                        DHQ
--------- ---------------------------------------------------------------- -----------------------------------------
01        EastChina                                                        Shanghai
02        WestChina                                                        Chengdu
03        SouthChina                                                       Guangzhou
04        NorthChina                                                       Beijing
05        CentralChina                                                     Wuhan      
 
-- Get the current time
SELECT SYSTIMESTAMP res FROM dual;
RES                                            
----------------------------------------------------------------
2023-12-17 14:14:08.498126     

-- Get the current SCN 
SELECT CURRENT_SCN FROM V$DATABASE;

          CURRENT_SCN
---------------------
   725539138356854784
 
-- Delete this record and commit
DELETE FROM area WHERE area_no='03';
COMMIT;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
 
-- Query historical data using flashback
-- Method 1: Based on TIMESTAMP
SELECT area_no,area_name,DHQ FROM area AS OF TIMESTAMP TIMESTAMP('2023-12-17 14:14:08.498126') WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     

-- Method 2: Based on SCN 
SELECT area_no,area_name,DHQ FROM area AS OF SCN 725539138356854784 WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     
```

## Restoring Data of Existing Tables

Flashback restoring table data (FLASHBACK TABLE TO SCN|TIMESTAMP) reverts a table's data to a past state, undoing INSERT/DELETE operations performed during that period.

Usage constraints for FLASHBACK TABLE TO SCN|TIMESTAMP:

- Applies only to HEAP tables.

- Cannot be executed within a transaction.

- Target table must have no dependencies. If foreign key constraints exist, remove them from other tables first.

- Table structure must remain unchanged between current time and target flashback time (no DDL operations):
   
   - Adding constraints
   
   - MODIFY/DROP COLUMN

   -ADD/DROP/MERGE/SPLIT/COALESCE/TRUNCATE partitions or subpartitions

   - TRUNCATE TABLE

- Only maintains indexes existing at current time:

   - Indexes created after target time: Data will be restored, but metadata reflects current state.

   - Indexes dropped after target time: Neither index nor data will be restored.

- ROWIDs may change after flashback. Ensure applications do not rely on ROWIDs.

- If flashing back multiple tables, failure of any table aborts all operations.

- Flashback does not directly restore statistics associated with the target table; statistics depend only on the current state at the time of collection.

### Prerequisites

- The user executing the table flashback operation must have DBA privilege or FLASHBACK-related privilege.

- Target table must have ROW MOVEMENT enabled.

### Example

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Enable ROW MOVEMENT for the target table (using finance_info table as example).

   ```sql
   ALTER TABLE finance_info ENABLE ROW MOVEMENT;
   ```

3. View current data of the target table and obtain current timestamp.

   ```sql
   -- View current data of the target table
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info;

   YEAR      MONTH     BRANCH    REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   --------- --------- --------- ------------- ----------- -----------
   2001      01        0201               2888        2000         300
   2021      01        0201              28888       24000        3000
   2021      01        0101              38888       34000        4000
   2021      02        0101              37778       33000        6000

   -- Get the current SCN 
   SELECT SYSTIMESTAMP res FROM dual;

   RES                                           
   -----------------------------------------------
   2023-12-17 14:10:28.736908   

   -- Get the current SCN 
   SELECT CURRENT_SCN FROM V$DATABASE;

             CURRENT_SCN
   ---------------------
      725955893872099328
   ```

4. Delete specific data from the target table and commit the transaction.

   ```sql
   -- Delete data
   DELETE FROM finance_info WHERE year='2021' and month='02';
   COMMIT;

   -- Confirm deletion result
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' and month='02';
   YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   ----- ----- ------ ------------- ----------- -----------
   ```

5. Flashback table data using TIMESTAMP or SCN (choose one method).

   ```sql
   -- Method 1: Flashback via TIMESTAMP
   FLASHBACK TABLE finance_info TO TIMESTAMP TIMESTAMP('2023-12-17 14:10:28.736908');

   -- Method 2: Flashback via SCN
   FLASHBACK TABLE finance_info TO SCN 725955893872099328;

   -- Confirm flashback result
   SELECT year,month,branch,revenue_total,cost_total,fee_total FROM finance_info WHERE year='2021' and month='02';
   YEAR  MONTH BRANCH REVENUE_TOTAL  COST_TOTAL   FEE_TOTAL
   ----- ----- ------ ------------- ----------- -----------
   2021  02    0101           37778       33000        6000   
   ```
