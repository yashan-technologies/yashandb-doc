Objects
-----
|Specification Name |Specification Type |Specification Value |
| ---------------------------- | -------- | --------- |
| User Count                | Maximum Value       | 10240 (including built-in database users)         |
| Table Count               | Maximum Value       | No limit                                          |
| Object Name Length        | Maximum Value       | 64Bytes                                          |
| Role Count                | Maximum Value       | 9640 (excluding built-in database roles)         |
| Private Temporary Table Count | Maximum Per Session | 64                                               |
| Password Length           | Maximum Value       | 127                                              |
| Total Flashback Restore Point Count    | Maximum Value       | 8192                                           |

<span id="table" name="table"></span>

Tables
-----

|Specification Name |Specification Type |Specification Value |
| ------------ | -------- | ------------------------ |
| Column Count               | Maximum Value       | 4096                                             |
| Record Count               | Maximum Value       | No limit                                         |
| Row Length                 | Maximum Value       | Row Store: 64512Bytes<br>Column Store: 32000KB  |
| Column Length              | Maximum Value       | 8000Bytes                                       |
| LOB Column Length          | Maximum Value       | No limit                                         |

Indexes
-----
|Specification Name |Specification Type |Specification Value |
| ------------ | -------- | --------- |
| Indexed Column Count                | Maximum Value       | 32                                             |
| Indexed Key Length (including internal format) | Maximum Value | Depends on data block size (i.e., DB_BLOCK_SIZE):<br>* 8K: 6400Bytes<br>* 16K: 12960Bytes<br>* 32K: 26510Bytes |
| Index Count for a Single Table     | Maximum Value       | 255                                            |
| Index Levels                        | Maximum Value       | 24                                             |

AC (Access Control)
-----
|Specification Name |Specification Type |Specification Value |
| ------------ | -------- | --------- |
| Column Count                        | Maximum Value       | 31                                             |
| Column Length                       | Maximum Value       | 8000Bytes                                      |
| AC Count for a Single Table        | Maximum Value       | 255                                            |

undo
-----
|Specification Name |Specification Type |Specification Value |
| ------------------- | -------- | ------------------------------ |
| Undo Segment Count                | Maximum Value       | 1024                                           |
| Undo Segment Count                | Minimum Value       | 8                                              |

Transactions
------
|Specification Name |Specification Type |Specification Value |
| ------------------- | -------- | ------------------------------ |
| Transaction Count                 | Maximum Value       | No limit                                       |
| Autonomous Transaction Nesting Levels | Maximum Value   | No limit                                       |

Sequence 
-----
|Specification Name |Specification Type |Specification Value |
| -------------- | ------------------- | -------- |
| Sequence Value                     | Maximum Value       | 1e28 - 1                                       |
| Sequence Value                     | Minimum Value       | -1e27 + 1                                      |
| Sequence Increment                 | Maximum Value       | Absolute value of increment must be less than the difference between maximum and minimum sequence values |
| Sequence Buffer Count              | Maximum Value       | For cyclic sequence, buffer count must not exceed the number of values in one cycle; non-cyclic sequences are unlimited |
| Sequence Buffer Count              | Minimum Value       | 2                                              |

Data Partition 
-----
|Specification Name |Specification Type |Specification Value |
| ------------ | -------- |---------------------------------------------|
| Partition Count                    | Maximum Value       | Maximum number of partitions that can be created in partitioned tables/indexes<br>1M - 1 |
| Partition Column Count             | Maximum Value       | 16<br> Interval partition only supports single-column partition |

<span id="Syntax" name="Syntax"></span>
Syntax
------
|Specification Name |Specification Type |Specification Value |
|-----------------------| ------------ |---------------------------------------------------------------------------------------------------|
| Length of a Single SQL Statement    | Maximum Value       | 2MB - 1 (including spaces and special characters) |
| yasql Single Line Character Count  | Maximum Value       | 4000                                          |
| Input Length of Constant Strings in SQL Statement | Maximum Value | 65534                                          |
| Output Length of String Type       | Maximum Value       | 65534                                          |
| Number of Tables Participating in JOIN in SQL Statement | Maximum Value | 128                                           |
| Number of Lines in Stored Procedure | Maximum Value       | 64K                                            |
| Number of Parameters in Stored Procedure | Maximum Value  | 4095                                           |
| Number of UDF Parameters           | Maximum Value       | 4095                                           |
| Number of Package Elements             | Maximum Value       | 1024                                           |
| Number of Partition Columns in Window Function | Maximum Value   | 1000                                           |
| Number of ORDER BY Columns in Window Function | Maximum Value | 1000                                         |
| Number of Window Functions          | Maximum Value       | 128                                            |
| Maximum Projection Column Length (including columns split from column store) | Maximum Value | 4096                                          |
| Subquery Nesting Count             | Maximum Value       | 128                                            |
| Degree of Parallelism              | Maximum Value       | 255                                            |
| Row Specification of Materialized Area | Maximum Value   | 63KB                                           |
| Number of Subqueries or SELECT Clauses | Maximum Value    | 170                                            |
| Length of Sorting Key in Materialized Area | Maximum Value  | 20K                                            |
| Number of Stages Required to Execute a Single SQL Statement | Maximum Value | 128<br> Maximum number of stages for executing a single SQL statement is influenced by MAX_PARALLEL_WORKERS configuration parameter<br> Reference formula: MIN(MAX_PARALLEL_WORKERS, 128) |
| Aggregation Function Materialized Area | Maximum Value   | 32KB<br> When the result of an aggregation function is variable-length data, it will be buffered in the aggregation materialized area, which is limited to 32K |
| Row Length Specification of Table Function | Maximum Value | 32KB                                           |

Context
-----
|Specification Name |Specification Type |Specification Value |
|------------------------- | ------- | ------------------ |
| Number of Context Buffers per Session  | Maximum Value       | 10000                                          |

Online DDL
------

To ensure business continuity, YashanDB supports online execution of partial DDL. To minimize the impact on system operation, online DDL operations are also recommended to be executed during off-peak business hours, and attention should be paid to the functionality constraints during online execution of DDL. Please refer to [SQL Reference](../../Development Guide/SQL Reference Manual/SQL Statements/00SQL Statements) for detailed functionality specification constraints.

The following is the list of supported online DDLs and the constraints for online execution when using HEAP tables in OLTP scenarios.

|Object Type |Operation Type |Constraints for Online Execution |
| ---------------| ---------- | ------------------------- |
| Index             | Create Index (CREATE INDEX) | - Online index operation must use ONLINE keyword in DDL syntax<br>- Column indexes and RTree indexes are not allowed to be created online<br>- Indexes for temporary tables cannot be created online |
|                   | Rebuild Index (ALTER INDEX REBUILD) | - Online index operation must use ONLINE keyword in DDL syntax<br>- Column indexes and RTree indexes are not allowed to be rebuilt online |
|                   | Drop Index (DROP INDEX)   | No restrictions                                |
|                   | Rename Index (ALTER INDEX RENAME TO) | No restrictions                                |
| Table             | Rename                | No restrictions                                |
|                   | Truncate Table (TRUNCATE TABLE) | No restrictions                                |
|                   | Shrink Space (ALTER TABLE SHRINK SPACE) | No restrictions                                |
|                   | Enable/Disable Row Movement (ALTER TABLE ENABLE/DISABLE ROW MOVEMENT) | No restrictions                                |
|                   | Add/Drop Supplemental Log (ALTER TABLE ADD/DROP SUPPLEMENTAL LOG) | No restrictions                                |
|                   | Drop Constraint (ALTER TABLE drop_constraint<br>- Drop Foreign Key Constraint<br>- Drop NOT NULL Constraint<br>- Drop CHECK Constraint) | No restrictions                                |
|                   | Drop Primary Key (ALTER TABLE DROP PRIMARY KEY) | No restrictions                                |
|                   | Add Primary Key Column (ADD PRIMARY KEY USING INDEX (CREATE INDEX ONLINE)) | - When adding primary key column, index creation must be specified online<br>- ALTER TABLE ADD PRIMARY KEY() is an OFFLINE operation |
| Partition         | Add/Drop Partition (ALTER TABLE ADD/DROP PARTITION) | Online addition of HASH partition is not supported |
|                   | Modify Partition Rule (ALTER TABLE SET) | Only INTERVAL and RANGE partitions may be converted into each other |
|                   | Truncate Partition (ALTER TABLE TRUNCATE PARTITION) | No restrictions                                |
| Column            | Add/Drop Column (ALTER TABLE ADD/DROP COLUMN) | - Default value for sequence or LOB data cannot be added<br>- VIRTUAL columns cannot be added or removed |
|                   | Modify Column Default Value (ALTER TABLE MODIFY COLUMN DEFAULT) | No restrictions                                |
|                   | Rename Column (ALTER TABLE RENAME) | No restrictions                                |
|                   | Increase Column Type Length or Precision | Only allows increasing length or precision within the same major data category; decreasing length or precision is not allowed |