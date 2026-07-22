This view displays historical SQL execution plans captured in the AWR repository. This view is used together with the DBA_HIST_SQLSTAT view to view historical SQL execution plans in chronological order.

|  Field  | Type  | Description |
| --- | --- | --- |
| DBID | NUMBER | Database ID |
| SQL_ID | VARCHAR(13) | SQL identifier |
| PLAN_HASH_VALUE | NUMBER | Execution plan hash value |
| ID | NUMBER | Plan line ID |
| OPERATION | VARCHAR(30) | Operation name |
| OPTIONS | VARCHAR(30) | Operation options |
| OBJECT_NODE | VARCHAR(128) | Object node |
| OBJECT# | NUMBER | Object number |
| OBJECT_OWNER | VARCHAR(128) | Object owner |
| OBJECT_NAME | VARCHAR(128) | Object name |
| OBJECT_ALIAS | VARCHAR(261) | Object alias |
| OBJECT_TYPE | VARCHAR(20) | Object type |
| OPTIMIZER | VARCHAR(20) | Optimizer mode |
| PARENT_ID | NUMBER | Parent operation ID |
| DEPTH | NUMBER | Depth |
| POSITION | NUMBER | Position |
| SEARCH_COLUMNS | NUMBER | Number of search columns |
| COST | NUMBER | Cost |
| CARDINALITY | NUMBER | Cardinality |
| BYTES | NUMBER | Bytes |
| OTHER_TAG | VARCHAR(35) | Other tag |
| PARTITION_START | VARCHAR(64) | Partition start |
| PARTITION_STOP | VARCHAR(64) | Partition stop |
| PARTITION_ID | NUMBER | Partition ID |
| OTHER | VARCHAR(4000) | Other information |
| CPU_COST | NUMBER | CPU cost |
| IO_COST | NUMBER | I/O cost |
| TEMP_SPACE | NUMBER | Temporary space |
| ACCESS_PREDICATES | VARCHAR(4000) | Access predicates |
| FILTER_PREDICATES | VARCHAR(4000) | Filter predicates |
| PROJECTION | VARCHAR(4000) | Projection columns |
| TIME | NUMBER | Estimated time |
| QBLOCK_NAME | VARCHAR(128) | Query block name |
| REMARKS | VARCHAR(4000) | Remarks |
| TIMESTAMP | DATE | Timestamp |