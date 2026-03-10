After the table data is modified by UPDATE or DELETE, it can be traced back to recent historical data through the flashback query functionality. For more details, please refer to the [flashback_query_clause](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/SELECT.html#flashbackqueryclause) of the SELECT statement.

The point in time for flashback queries is determined by the undo retention period (UNDO_RETENTION). It is recommended to set this parameter to 86400 seconds (24 hours) or longer.

Flashback queries are not applicable to ISC Distributed Cluster Deployment.

***Example*** for Standalone Deployment and YAC Deployment

```sql
-- A record that exists in the area table
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou       
 
-- Get the current time
SELECT SYSTIMESTAMP res FROM dual;
RES                                            
----------------------------------------------------------------
2023-12-17 14:14:08.498126     
 
-- Delete this record and commit
DELETE FROM area WHERE area_no='03';
COMMIT;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
 
 
-- Query historical data using flashback
SELECT area_no,area_name,DHQ FROM area AS OF TIMESTAMP TIMESTAMP('2023-12-17 14:14:08.498126')
WHERE area_no='03';
AREA_NO AREA_NAME          DHQ         
------- ------------------ ------------
03      SouthChina             Guangzhou     
```
