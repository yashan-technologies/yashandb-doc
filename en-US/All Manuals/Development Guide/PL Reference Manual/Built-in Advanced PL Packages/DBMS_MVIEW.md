The DBMS_MVIEW package provides a set of built-in stored procedures for materialized view refresh-related operations.

There is no functionality related to materialized views in ISC Distributed Cluster Deployment.

## REFRESH

```plsql
DBMS_MVIEW.REFRESH(
	tab IN VARCHAR,
    method IN VARCHAR,
    refresh_after_errors IN BOOLEAN DEFAULT FALSE,
    purge_option IN	INTEGER DEFAULT 1,
    parallelism IN INTEGER DEFAULT 0,
    atomic_refresh IN BOOLEAN DEFAULT TRUE,
    nested IN BOOLEAN DEFAULT FALSE,
    out_of_place IN BOOLEAN DEFAULT FALSE);
```

The REFRESH procedure is used to manually refresh data in a materialized view.

|Parameter |Description |
| :------------------- | :----------------------------------------------------------- |
| tab                  | The name of the materialized view.                          |
| method               | The refresh method for the materialized view, defaults to C (full refresh), can be omitted. |
| refresh_after_errors | Compatibility parameter, compatible with list only.         |
| purge_option         | Compatibility parameter, compatible with list only.         |
| parallelism          | Degree of parallelism compatibility parameter, compatible with list only. |
| atomic_refresh       | When TRUE, the refresh behavior is delete entire table + insert into select; when FALSE, the refresh behavior is truncate entire table + insert into select. |
| nested               | Nested refresh, compatibility parameter, compatible with list only. |
| out_of_place         | Compatibility parameter, compatible with list only.         |

***Example*** for Heap tables

```plsql
-- Create the materialized view and specify it as manual refresh mode
CREATE MATERIALIZED VIEW TEST_MVIEW 
REFRESH ON DEMAND
AS SELECT * FROM area;

-- Execute the advanced package to refresh the materialized view data
EXEC DBMS_MVIEW.REFRESH('TEST_MVIEW');
```
