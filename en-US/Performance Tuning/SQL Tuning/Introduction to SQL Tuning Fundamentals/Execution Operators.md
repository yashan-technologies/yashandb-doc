In YashanDB, all execution operators default to single-row processing. To enable batch execution, please contact our technical support.

> **Note**:
>
> When an execution operator uses parallel batch processing, it will be marked with an additional `(B)` indicator. For example, `SELECT STATEMENT(B)` indicates a query statement that is executed in parallel batch mode.

## Data Query Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
| ---------------- | ------------------------------------------------------------ | ---------- | ---------- |
| SUBQUERY              | Indicates that a row subquery execution plan exists; column subqueries will be converted to a result plan. | All              | ×                    |
| VIEW                  | Indicates a view; necessary to add when a row-level query at the same layer contains a FROM subquery or user view. | Row                | √                    |
| CONNECT BY            | Hierarchical recursive query; returns all records associated with the current record using the connect by condition in a depth-first manner. The returned record set is structured as a tree with the current record as the root, based on prior id=father_id. | Row                | ×                     |
| SELECT STATEMENT      | Indicates a query statement.                                     | All                     | √ |
| INSERT STATEMENT      | Indicates an insert statement.                                   | All                     | ×        |
| UPDATE STATEMENT      | Indicates a data update statement.                               | All                     | √ |
| DELETE STATEMENT      | Indicates a data delete statement.                               | All                     | ×        |
| MERGE STATEMENT       | Indicates a bulk insert statement; can complete all INSERT operations with one full table scan when conditions are met. | All                     | √ |
| UNION ALL             | Merges results from multiple SELECT statements and returns them. | All                     | √ |
| AGGREGATE             | Performs aggregate operations on ungrouped data.                | All                     | √ |
| FOR UPDATE            | Adds a row-level lock when querying certain row data to prevent other users from updating this data. | All                     | ×        |
| RESULT                | After predicate restructuring, an independent plan node carrying FILTER and projection needs to be hosted on the plan tree. | All                     | √ |
| WINDOW                | Data filtering limit. Used to limit the number of rows returned in the result set and has the ability to skip an offset. | All                     | √ |
| WINDOW SORT           | Indicator of window function; can group and sort simultaneously, grouping based on sorting and returning original information rather than grouped information (one record per group). | All                     | ×        |
| WINDOW HASH           | Indicator of window function; can group and sort simultaneously using hash for grouping, returning original information rather than grouped information (one record per group). | All                     | √ |
| WINDOW NOSORT         | Indicator of window function; this operator groups ordered data and calculates window function return values, returning original information rather than grouped information (one record per group). If the data order provided by lower operators meets the ordering requirements of the window function, this operator can be selected. | All                     | ×        |
| FIRST ROW             | Retrieves the first record obtained from the lower scanning operator. | All                     | ×        |
| COUNT                 | Returns the number of selected rows in the table.               | All                     | √ |
| COUNT STOPKEY         | Returns the number of selected rows in the table using ROWNUM as a limiting condition. | All                     | √ |
| RESULT COLLECTOR      | Indicates batch execution and the opening of pipeline parallelism. | Row                     | √ |
| UNPIVOT               | Converts columns to row data.                                   | Row                     | ×        |

## Table Scan Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
|-----------------------------| ------------------------------------------------------- | ---------- | ---------- |
| TABLE ACCESS FULL           | Full table scan.                                         | All                     | √ |
| TABLE ACCESS BY INDEX ROWID | Retrieves the rowids of corresponding data blocks based on the index (returns to the table based on rowid). | All            | ×        |
| TABLE ACCESS BY USER ROWID          | Specify a single ROWID (`ROWID = ?` or `ROWID IN ('9912:0:0:5108:4', '9912:0:0:5108:8')`) to access data in the table. For the ROWID data format, please refer to [ROWID Data Format](../../../Development Guide/SQL Reference Manual/Data Types/ROWID and UROWID Types.md#Format).          | All                     | ×        |
| TABLE ACCESS BY ROWID RANGE   | Specify a range of ROWIDs (`ROWID BETWEEN ... AND ...` or `ROWID > ?`) to access multiple rows of data in a table. For the ROWID data format, please refer to [ROWID Data Format](../../../Development Guide/SQL Reference Manual/Data Types/ROWID and UROWID Types.md#Format).                    | Row                     | √     |
| TEMP TABLE ACCESS                   | Scans temporary materialization area of shared CTE.     | All                     | √ |

## Index Scan Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
|----------------------------| --------------------------------------------------------- | ---------- | ---------- |
| INDEX UNIQUE SCAN                   | Unique index scan; only applicable when the WHERE condition is an equality check. | All                     | ×        |
| INDEX RANGE SCAN                    | Index range scan; when the scanned object is a unique index, the predicate condition must be a range query (between, <, >); when the scanned object is a non-unique index, there are no restrictions. An index range scan may return multiple records. | All                     | ×        |
| INDEX RANGE SCAN DESCENDING         | Performs index range scan in descending order.            | All                     | ×        |
| INDEX FULL SCAN                     | Full index scan; scans all indexed rows of all leaf blocks of the target index. | All                     | ×        |
| INDEX FULL SCAN DESCENDING          | Performs full index scan in descending order.              | All                     | ×        |
| INDEX FAST FULL SCAN                | Fast full index scan; similar to full index scan, but the scan results are not ordered. Can perform a physical read rather than logical index read and allows parallel reading of indices. | All                     | ×        |
| INDEX FULL SCAN (MIN/MAX)           | Full index scan for minimum/maximum. In non-grouping cases, optimizes for min, max functions on index fields, returning only one record. | All                     | ×        |
| INDEX RANGE SCAN (MIN/MAX)         | Index range scan for minimum/maximum. In non-grouping cases, optimizes for min, max functions on index fields, returning only one record. | All                     | ×        |
| INDEX SKIP SCAN                     | Skip index scan; skips leading columns of the index, using non-leading columns for index scanning. | All                     | ×        |
| INDEX SKIP SCAN DESCENDING          | Performs index skip scan in descending order.              | All                     | ×        |
| SPATIAL INDEX SCAN                  | R-tree index scan; only applicable for index fields with spatial relationship functions like ST_Contains, ST_Intersects, etc. | Row                     | ×        |
| BITMAP OR                           | Performs set representation using bitmap for index scanning. | Row                     | ×        |

<span id="PART" name="PART"></span>

## Partition Scan Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
|-----------------------------|-----------| ---------- | ---------- |
| PART SCAN ITERATOR                  | A group of partition scans.           | All                     | √                    |
| PART SCAN ALL                       | Scans all partitions.                 | All                     | √                    |
| PART SCAN SINGLE                    | Scans a single partition.            | All                     | √                    |
| PART COMBINED ITERATOR         | Scans specified secondary partitions.  | All                     | √                    |

## AC Scan Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
|---------|----------| ---------- | ---------- |
| AC SCAN          | AC Scan.    | Column                      | ×        |
| EXPAND           | Expands AC scanned data. | Column                      | ×        |

## Group/Sort Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
|--------------------|----------------------------------| ---------- | ---------- |
| SORT                        | Generated sorting plan based on actual conditions. | Row                     | √                    |
| ORDER BY STOPKEY            | Sorting plan for the TOP data based on actual conditions. | All                     | √                    |
| SORT ORDER BY               | Sorting plan generated by the order by statement. | Column                     | ×        |
| HASH DISTINCT               | Removes duplicates using hash algorithm. | All                     | √                    |
| SORT DISTINCT               | Removes duplicates using sorting algorithm. | All                     | ×        |
| SORTED DISTINCT             | Removes duplicates using sorting algorithm on already sorted data. | All                     | ×        |
| SORT DISTINCT STOPKEY       | Removes duplicates using sorting algorithm on already sorted data, and returns the TOP. | All                     | ×        |
| HASH GROUP                  | Groups data using hash algorithm. | All                     | √                    |
| SORT GROUP                  | Groups data using sorting algorithm. | Row                     | ×        |
| SORT GROUP BY STOPKEY       | Groups data using sorting algorithm and returns the TOP. | Row                     | ×        |
| GROUP                       | Groups sorted or grouped data.           | All                     | ×        |
| SORT GROUPING SETS          | Groups by multiple columns using sorting algorithm and performs union operation on results. | All                     | ×        |
| HASH GROUPING SETS          | Groups by multiple columns using hash algorithm and performs union operation on results. | All                     | √                    |

## HASH JOIN Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
| -------------------- | ------------------------------------------------------------ | ---------- | ---------- |
| HASH JOIN OUTER           | HASH JOIN left outer join. Based on the left table; when join conditions are met for both tables, data from both is returned. If not met, left table data + nulls for right table is returned. | All                     | √                    |
| HASH JOIN FULL OUTER      | HASH JOIN full outer join. When join conditions for both tables are met, data from both is returned. If not met, additional data from left table + nulls for right and vice versa is returned. | All                     | √                    |
| HASH JOIN SEMI            | HASH JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. Filter in / exists often converted to SEMI JOIN. | All                     | √                    |
| HASH JOIN ANTI            | HASH JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. Filter not in / not exists often converted to ANTI JOIN. | All                     | √                    |
| HASH JOIN ANTI NA         | HASH JOIN left anti join that is aware of null values. | All                     | √                    |
| HASH JOIN                  | HASH JOIN inner join. Materializes the left table; the right table finds data meeting join conditions through the HASH algorithm. Only supports equality queries. | All                     | √                    |
| HASH RIGHT OUTER          | HASH JOIN right outer join. Based on the right table; when join conditions for both tables are met, data from both is returned. If not met, right table data + nulls for left table is returned. | All                     | √                    |
| HASH RIGHT SEMI           | HASH JOIN right semi join. If a row from the right table meets specified conditions and appears in the left table, it is retained in the result set. | All                     | √                    |
| HASH RIGHT ANTI           | HASH JOIN right anti join. If a row from the right table meets specified conditions and does not appear in the left table, it is retained in the result set. | All                     | √                    |
| JOIN FILTER CREATE            | The creation end of RUNTIME FILTER is typically located on the build side of join operations. RUNTIME FILTER are a query optimization technique primarily used in distributed query execution to reduce data transfer volume. JOIN FILTER is a significant type of runtime filter, which dynamically generates filtering conditions during join operations and pushes these conditions upstream to operators for early data filtering. | All                     | ×                    |
| JOIN FILTER USE           | The application end of RUNTIME FILTER is usually situated on the probe side of join operations. RUNTIME FILTER are a query optimization technique primarily used in distributed query execution to reduce data transfer volume. JOIN FILTER is a significant type of runtime filter, which dynamically generates filtering conditions during join operations and pushes these conditions upstream to operators for early data filtering.  | All                     | ×                    |

## MERGE JOIN Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
| ---------- | ------------------------------------------------------------ | ---------- | ---------- |
| MERGE SORT            | Sub-plan for MERGE JOIN, used for sorting keys participating in MERGE JOIN. | All                     | ×        |
| MERGE JOIN OUTER      | MERGE JOIN left outer join. Supports equality and range conditions for sorting. When join conditions are met, data from both tables is returned; otherwise, left table data + nulls for right table is returned. | All                     | ×        |
| MERGE JOIN FULL OUTER | MERGE JOIN full outer join. Supports equality and range conditions for sorting. When join conditions are met, data from both tables is returned; otherwise, additional left table data + nulls for right and vice versa is returned. | All                     | ×        |
| MERGE JOIN SEMI       | MERGE JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. Filter in / exists often converted to SEMI JOIN. | All                     | ×        |
| MERGE JOIN ANTI       | MERGE JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. Filter not in / not exists often converted to ANTI JOIN. | All                     | ×        |
| MERGE JOIN ANTI NA    | MERGE JOIN left anti join that is aware of null values. | All                     | ×        |
| MERGE JOIN            | MERGE JOIN inner join. Sorts and materializes the left and right tables, then merges based on the join conditions, supporting both equality and range sorting. | All                     | ×        |

## NEST LOOP Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
| --------------------- | ------------------------------------------------------------ | ---------- | ---------- |
| NESTED LOOPS LEFT OUTER    | NESTED LOOP JOIN left outer join. Based on the left table; when join conditions are met for both tables, data from both is returned. If not met, left table data + nulls for right table is returned. | All                     | √                    |
| NESTED LOOPS FULL OUTER    | NESTED LOOP JOIN full outer join. When join conditions for both tables are met, data from both is returned. If not met, additional data from left table + nulls for right and vice versa is returned. | All                     | √                    |
| NESTED LOOPS SEMI          | NESTED LOOP JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. | All                     | √                    |
| NESTED LOOPS ANTI          | NESTED LOOP JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. | All                     | √                    |
| NESTED LOOPS ANTI NA       | NESTED LOOP JOIN left anti join that is aware of null values. | All                     | √                    |
| NESTED LOOPS INNER          | NESTED LOOP inner join. Searches for data meeting join conditions through looping over both tables. | All                     | √                    |
| NESTED INDEX LOOPS LEFT OUTER | NESTED INDEX LOOPS left outer join. Similar to NESTED LOOPS LEFT OUTER, but right table uses index scan. | All                     | ×        |
| NESTED INDEX LOOPS SEMI     | NESTED INDEX LOOPS left semi join. Similar to NESTED LOOPS SEMI, but right table uses index scan. | All                     | ×        |
| NESTED INDEX LOOPS ANTI     | NESTED INDEX LOOPS left anti join. Similar to NESTED LOOPS ANTI, but right table uses index scan. | All                     | ×        |
| NESTED INDEX LOOPS          | NESTED INDEX LOOPS inner join. Similar to NESTED LOOPS, but right table uses index scan. | All                     | ×        |

## Auxiliary Functionality Operators

|Operator Name |Meaning |Row/Column Mode | Supports Batch Execution |
| ----------------------- | ------------------------------------------------------- | ---------- | ---------- |
| LOAD TABLE CONVENTIONAL      | During the recent insertion of data, the database executed real-time statistics collection. | All                     | ×        |
| PX COORDINATOR               | Identifier for parallel execution in Standalone Deployment; indicates that all operators below this identifier are executed in parallel. | All                     | ×        |
| DISTRIBUTED COORDINATOR      | Identifier for parallel execution in ISC Distributed Cluster Deployment; indicates that all operators below this identifier are executed in parallel. | All                     | ×        |
| PX REMOTE                    | Indicates that data interactions include cross-node transmissions. | All                     | ×        |
| PX LOCAL                     | Indicates that data interactions are only local transmissions. | All                     | ×        |
| MERGE                        | Data merging; combines multiple sorted data sources into one. | All                     | ×        |
| ROW TO COL                   | Converts row calculations to column calculations.          | All                     | ×        |
| COL TO ROW                   | Converts column calculations to row calculations.          | All                     | ×        |
| MATERIAL                     | Materializes the input data source.                        | All                     | ×        |