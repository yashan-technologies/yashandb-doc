This view displays all execution plan information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | NUMBER     | Group ID                                              |
| GROUP_NODE_ID      | NUMBER     | Node ID within the group                              |
| INST_ID            | NUMBER     | Instance ID                                          |
| ADDRESS            | RAW(8)     | SQL address                                          |
| HASH_VALUE        | BIGINT     | Hash value of the SQL, calculated from the SQL text |
| SQL_ID            | VARCHAR(13)| Unique identifier for a SQL statement, calculated using hashing/encryption on the SQL text |
| PLAN_HASH_VALUE  | BIGINT     | A unique identifier for an execution plan            |
| CHILD_ADDRESS     | RAW(8)     | Child cursor address                                  |
| CHILD_NUMBER      | INTEGER    | Child cursor number                                   |
| TIMESTAMP          | DATE       | Time when the execution plan was created              |
| OPERATION          | VARCHAR(256)| Description information after explaining the execution plan |
| OPTIONS            | VARCHAR(256)| Descriptive information with operation after explanation of the execution plan |
| OBJECT_NODE       | VARCHAR(256)| Description information of the table or view        |
| OBJECT#            | BIGINT     | Table ID                                             |
| OBJECT_OWNER      | VARCHAR(64)| Owner username of the table or index                  |
| OBJECT_NAME       | VARCHAR(64)| Name of the table or index                            |
| OBJECT_ALIAS      | VARCHAR(256)| Alias of the table                                   |
| OBJECT_TYPE       | VARCHAR(64)| Object type<br/>* TABLE FUNCTION<br/>* TABLE        |
| OPTIMIZER          | VARCHAR(64)| Reserved field                                       |
| PARTITION_INFO    | VARCHAR(64)| Partition information of the execution plan          |
| ID                 | INTEGER    | ID for each execution plan                            |
| PARENT_ID         | INTEGER    | ID of the next execution plan                         |
| DEPTH              | INTEGER    | Depth of the current execution plan in the plan tree (root node depth is 0) |
| POSITION           | INTEGER    | Processing order of all operations with the same PARENT_ID |
| SEARCH_COLUMNS    | BIGINT     | Number of indexed columns with start and stop keys (i.e., number of columns with matching predicates) |
| COST               | BIGINT     | Estimated operation cost based on cost method        |
| CARDINALITY        | BIGINT     | Estimated number of rows produced by the operation based on the optimizer's cost-based method |
| BYTES              | BIGINT     | Estimated number of bytes produced by the operation based on the optimizer's cost-based method |
| OTHER_TAG         | VARCHAR(256)| Description of the other column's content           |
| PARTITION_START   | INTEGER    | Start of the accessed series of partitions            |
| PARTITION_STOP    | INTEGER    | Stop of the accessed series of partitions             |
| PARTITION_ID      | INTEGER    | Partition ID                                          |
| OTHER              | VARCHAR(4000)| Description of the other column's content           |
| CPU_COST          | BIGINT     | Estimated CPU consumption for the operation based on the optimizer's cost-based method |
| IO_COST           | BIGINT     | Estimated IO cost for the operation based on the optimizer's cost-based method |
| TEMP_SPACE        | BIGINT     | Estimated temporary space usage based on the optimizer's cost-based method |
| ACCESS_PREDICATES | VARCHAR(4000)| Predicates used to locate accessed row data        |
| FILTER_PREDICATES | VARCHAR(4000)| Predicates used to filter rows before they are generated |
| PROJECTION         | VARCHAR(4000)| Reserved field                                       |
| TIME               | BIGINT     | Estimated operation run time based on the optimizer's cost-based method (unit: ms) |
| QBLOCK_NAME       | VARCHAR(64)| Name of each query block                              |
| REMARKS            | VARCHAR(4000)| Remarks                                             |