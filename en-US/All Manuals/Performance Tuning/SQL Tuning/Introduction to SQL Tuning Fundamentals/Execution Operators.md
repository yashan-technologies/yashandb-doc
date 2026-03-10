## Data Query Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
| ---------------- | ------------------------------------------------------------ | ---------- | ---------- |
| SUBQUERY              | Indicates that a row subquery execution plan exists; column subqueries will be converted to a result plan. | √                   | √                     |
| VIEW                  | Indicates a view; necessary to add when a row-level query at the same layer contains a FROM subquery or user view. | √                   | ×                     |
| CONNECT BY            | Hierarchical recursive query; returns all records associated with the current record using the connect by condition in a depth-first manner. The returned record set is structured as a tree with the current record as the root, based on prior id=father_id. | √                   | ×                     |
| SELECT STATEMENT      | Indicates a query statement.                                     | √                   | √                     |
| INSERT STATEMENT      | Indicates an insert statement.                                   | √                   | √                     |
| UPDATE STATEMENT      | Indicates a data update statement.                               | √                   | √                     |
| DELETE STATEMENT      | Indicates a data delete statement.                               | √                   | √                     |
| MERGE STATEMENT       | Indicates a bulk insert statement; can complete all INSERT operations with one full table scan when conditions are met. | √                   | √                     |
| UNION ALL             | Merges results from multiple SELECT statements and returns them. | √                   | √                     |
| AGGREGATE             | Performs aggregate operations on ungrouped data.                | √                   | √                     |
| FOR UPDATE            | Adds a row-level lock when querying certain row data to prevent other users from updating this data. | √                   | √                     |
| RESULT                | After predicate restructuring, an independent plan node carrying FILTER and projection needs to be hosted on the plan tree. | √                   | √                     |
| WINDOW                | Data filtering limit. Used to limit the number of rows returned in the result set and has the ability to skip an offset. | √                   | √                     |
| WINDOW SORT           | Indicator of window function; can group and sort simultaneously, grouping based on sorting and returning original information rather than grouped information (one record per group). | √                   | √                     |
| WINDOW HASH           | Indicator of window function; can group and sort simultaneously using hash for grouping, returning original information rather than grouped information (one record per group). | ×                   | √                     |
| WINDOW NOSORT         | Indicator of window function; this operator groups ordered data and calculates window function return values, returning original information rather than grouped information (one record per group). If the data order provided by lower operators meets the ordering requirements of the window function, this operator can be selected. | √                   | √                     |
| FIRST ROW             | Retrieves the first record obtained from the lower scanning operator. | √                   | √                     |
| COUNT                 | Returns the number of selected rows in the table.               | √                   | √                     |
| COUNT STOPKEY         | Returns the number of selected rows in the table using ROWNUM as a limiting condition. | √                   | √                     |
| RESULT COLLECTOR      | Indicates batch execution and the opening of pipeline parallelism. | √                   | ×                     |
| UNPIVOT               | Converts columns to row data.                                   | √                   | ×                     |

## Table Scan Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
|-----------------------------| ------------------------------------------------------- | ---------- | ---------- |
| TABLE ACCESS FULL                   | Full table scan.                                         | √                   | √                     |
| TABLE ACCESS BY INDEX ROWID         | Retrieves the rowids of corresponding data blocks based on the index (returns to the table based on rowid). | √                   | √                     |
| TABLE ACCESS BY USER ROWID          | Finds data blocks using a specified rowid.              | √                   | √                     |
| TEMP TABLE ACCESS                   | Scans temporary materialization area of shared CTE.     | √                   | √                     |

## Index Scan Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
|----------------------------| --------------------------------------------------------- | ---------- | ----- |
| INDEX UNIQUE SCAN                   | Unique index scan; only applicable when the WHERE condition is an equality check. | √                   | √                     |
| INDEX RANGE SCAN                    | Index range scan; when the scanned object is a unique index, the predicate condition must be a range query (between, \<, >); when the scanned object is a non-unique index, there are no restrictions. An index range scan may return multiple records. | √                   | √                     |
| INDEX RANGE SCAN DESCENDING         | Performs index range scan in descending order.            | √                   | √                     |
| INDEX FULL SCAN                     | Full index scan; scans all indexed rows of all leaf blocks of the target index. | √                   | √                     |
| INDEX FULL SCAN DESCENDING          | Performs full index scan in descending order.              | √                   | √                     |
| INDEX FAST FULL SCAN                | Fast full index scan; similar to full index scan, but the scan results are not ordered. Can perform a physical read rather than logical index read and allows parallel reading of indices. | √                   | √                     |
| INDEX FULL SCAN (MIN/MAX)           | Full index scan for minimum/maximum. In non-grouping cases, optimizes for min, max functions on index fields, returning only one record. | √                   | √                     |
| INDEX RANGE SCAN (MIN/MAX)         | Index range scan for minimum/maximum. In non-grouping cases, optimizes for min, max functions on index fields, returning only one record. | √                   | √                     |
| INDEX SKIP SCAN                     | Skip index scan; skips leading columns of the index, using non-leading columns for index scanning. | √                   | √                     |
| INDEX SKIP SCAN DESCENDING          | Performs index skip scan in descending order.              | √                   | √                     |
| SPATIAL INDEX SCAN                  | R-tree index scan; only applicable for index fields with spatial relationship functions like ST_Contains, ST_Intersects, etc. | √                   | ×                     |
| BITMAP OR                           | Performs set representation using bitmap for index scanning. | √                   | ×                     |

<span id="PART" name="PART" class="yaslink"></span>

## Partition Scan Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
|-----------------------------|-----------| ---------- | ---------- |
| PART SCAN ITERATOR                  | A group of partition scans.           | √                   | √                     |
| PART SCAN ALL                       | Scans all partitions.                 | √                   | √                     |
| PART SCAN SINGLE                    | Scans a single partition.            | √                   | √                     |
| PART SCAN COMBINED ITERATOR         | Scans specified secondary partitions.  | √                   | √                     |

## AC Scan Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
|---------|----------| ---------- | ---------- |
| AC SCAN          | AC Scan.    | ×                   | √                      |
| EXPAND           | Expands AC scanned data. | ×                   | √                      |

## Group/Sort Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
|--------------------|----------------------------------| ---------- | ------ |
| SORT                        | Generated sorting plan based on actual conditions. | √                   | ×                     |
| ORDER BY STOPKEY            | Sorting plan for the TOP data based on actual conditions. | √                   | √                     |
| SORT ORDER BY               | Sorting plan generated by the order by statement. | ×                   | √                     |
| HASH DISTINCT               | Removes duplicates using hash algorithm. | √                   | √                     |
| SORT DISTINCT               | Removes duplicates using sorting algorithm. | √                   | √                     |
| SORTED DISTINCT             | Removes duplicates using sorting algorithm on already sorted data. | √                   | √                     |
| SORT DISTINCT STOPKEY       | Removes duplicates using sorting algorithm on already sorted data, and returns the TOP. | √                   | √                     |
| HASH GROUP                  | Groups data using hash algorithm. | √                   | √                     |
| SORT GROUP                  | Groups data using sorting algorithm. | √                   | ×                     |
| SORT GROUP BY STOPKEY       | Groups data using sorting algorithm and returns the TOP. | √                   | ×                     |
| GROUP                       | Groups sorted or grouped data.           | √                   | √                     |
| SORT GROUPING SETS          | Groups by multiple columns using sorting algorithm and performs union operation on results. | √                  | √                     |
| HASH GROUPING SETS          | Groups by multiple columns using hash algorithm and performs union operation on results. | √                  | √                     |

## HASH JOIN Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
| -------------------- | ------------------------------------------------------------ | ---------- | ---------- |
| HASH JOIN OUTER           | HASH JOIN left outer join. Based on the left table; when join conditions are met for both tables, data from both is returned. If not met, left table data + nulls for right table is returned. | √                   | √                     |
| HASH JOIN FULL OUTER      | HASH JOIN full outer join. When join conditions for both tables are met, data from both is returned. If not met, additional data from left table + nulls for right and vice versa is returned. | √                   | √                     |
| HASH JOIN SEMI            | HASH JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. Filter in / exists often converted to SEMI JOIN. | √                   | √                     |
| HASH JOIN ANTI            | HASH JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. Filter not in / not exists often converted to ANTI JOIN. | √                   | √                     |
| HASH JOIN ANTI NA         | HASH JOIN left anti join that is aware of null values. | √                   | √                     |
| HASH JOIN                  | HASH JOIN inner join. Materializes the left table; the right table finds data meeting join conditions through the HASH algorithm. Only supports equality queries. | √                   | √                     |
| HASH RIGHT OUTER          | HASH JOIN right outer join. Based on the right table; when join conditions for both tables are met, data from both is returned. If not met, right table data + nulls for left table is returned. | √                   | √                     |
| HASH RIGHT SEMI           | HASH JOIN right semi join. If a row from the right table meets specified conditions and appears in the left table, it is retained in the result set. | √                   | √                     |
| HASH RIGHT ANTI           | HASH JOIN right anti join. If a row from the right table meets specified conditions and does not appear in the left table, it is retained in the result set. | √                   | √                     |
| JOIN FILTER CREATE            | The creation end of RUNTIME FILTER is typically located on the build side of join operations. RUNTIME FILTER are a query optimization technique primarily used in distributed query execution to reduce data transfer volume. JOIN FILTER is a significant type of runtime filter, which dynamically generates filtering conditions during join operations and pushes these conditions upstream to operators for early data filtering. | √                   | √                    |
| JOIN FILTER USE           | The application end of RUNTIME FILTER is usually situated on the probe side of join operations. RUNTIME FILTER are a query optimization technique primarily used in distributed query execution to reduce data transfer volume. JOIN FILTER is a significant type of runtime filter, which dynamically generates filtering conditions during join operations and pushes these conditions upstream to operators for early data filtering.  | √                   | √                    |


## MERGE JOIN Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
| ---------- | ------------------------------------------------------------ | ---------- | --------- |
| MERGE SORT            | Sub-plan for MERGE JOIN, used for sorting keys participating in MERGE JOIN. | √                   | √                     |
| MERGE JOIN OUTER      | MERGE JOIN left outer join. Supports equality and range conditions for sorting. When join conditions are met, data from both tables is returned; otherwise, left table data + nulls for right table is returned. | √                   | √                     |
| MERGE JOIN FULL OUTER | MERGE JOIN full outer join. Supports equality and range conditions for sorting. When join conditions are met, data from both tables is returned; otherwise, additional left table data + nulls for right and vice versa is returned. | √                   | √                     |
| MERGE JOIN SEMI       | MERGE JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. Filter in / exists often converted to SEMI JOIN. | √                   | √                     |
| MERGE JOIN ANTI       | MERGE JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. Filter not in / not exists often converted to ANTI JOIN. | √                   | √                     |
| MERGE JOIN ANTI NA    | MERGE JOIN left anti join that is aware of null values. | √                   | √                     |
| MERGE JOIN            | MERGE JOIN inner join. Sorts and materializes the left and right tables, then merges based on the join conditions, supporting both equality and range sorting. | √                   | √                     |

## NEST LOOP Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
| --------------------- | ------------------------------------------------------------ | ---------- | ---------- |
| NESTED LOOPS LEFT OUTER    | NESTED LOOP JOIN left outer join. Based on the left table; when join conditions are met for both tables, data from both is returned. If not met, left table data + nulls for right table is returned. | √                   | √                     |
| NESTED LOOPS FULL OUTER    | NESTED LOOP JOIN full outer join. When join conditions for both tables are met, data from both is returned. If not met, additional data from left table + nulls for right and vice versa is returned. | √                   | √                     |
| NESTED LOOPS SEMI          | NESTED LOOP JOIN left semi join. If a row from the left table meets specified conditions and appears in the right table, it is retained in the result set. | √                   | √                     |
| NESTED LOOPS ANTI          | NESTED LOOP JOIN left anti join. If a row from the left table meets specified conditions and does not appear in the right table, it is retained in the result set. | √                   | √                     |
| NESTED LOOPS ANTI NA       | NESTED LOOP JOIN left anti join that is aware of null values. | √                   | √                     |
| NESTED LOOPS INNER          | NESTED LOOP inner join. Searches for data meeting join conditions through looping over both tables. | √                   | √                     |
| NESTED INDEX LOOPS LEFT OUTER | NESTED INDEX LOOPS left outer join. Similar to NESTED LOOPS LEFT OUTER, but right table uses index scan. | √                   | √                     |
| NESTED INDEX LOOPS SEMI     | NESTED INDEX LOOPS left semi join. Similar to NESTED LOOPS SEMI, but right table uses index scan. | √                   | √                     |
| NESTED INDEX LOOPS ANTI     | NESTED INDEX LOOPS left anti join. Similar to NESTED LOOPS ANTI, but right table uses index scan. | √                   | √                     |
| NESTED INDEX LOOPS          | NESTED INDEX LOOPS inner join. Similar to NESTED LOOPS, but right table uses index scan. | √                   | √                     |

## Auxiliary Functionality Operators

|Operator Name |Meaning |Applicable Row Mode |Applicable Column Mode |
| ----------------------- | ------------------------------------------------------- | ---------- | ---------- |
| LOAD TABLE CONVENTIONAL      | During the recent insertion of data, the database executed real-time statistics collection. | √                   | √                     |
| PX COORDINATOR               | Identifier for parallel execution in Standalone Deployment; indicates that all operators below this identifier are executed in parallel. | √                   | √                     |
| DISTRIBUTED COORDINATOR      | Identifier for parallel execution in ISC Distributed Cluster Deployment; indicates that all operators below this identifier are executed in parallel. | √                   | √                     |
| PX REMOTE                    | Indicates that data interactions include cross-node transmissions. | √                   | √                     |
| PX LOCAL                     | Indicates that data interactions are only local transmissions. | √                   | √                     |
| MERGE                        | Data merging; combines multiple sorted data sources into one. | √                   | √                     |
| ROW TO COL                   | Converts row calculations to column calculations.          | √                   | √                     |
| COL TO ROW                   | Converts column calculations to row calculations.          | √                   | √                     |
| MATERIAL                     | Materializes the input data source.                        | √                   | √                     |