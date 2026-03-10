This view displays the execution plan information for all nodes in an ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID         | INTEGER     | Group ID                                                         |
| GROUP_NODE_ID    | INTEGER     | Node ID within the group                                         |
| ADDRESS          | RAW(8)     | SQL address                                                      |
| HASH_VALUE       | BIGINT      | The hash value of SQL, calculated from the SQL text             |
| SQL_ID           | VARCHAR(13) | The unique ID value that identifies a SQL statement, obtained through hashing/encryption of the SQL text |
| PLAN_HASH_VALUE  | BIGINT      | A unique identifier for an execution plan                        |
| CHILD_ADDRESS     | RAW(8)     | Child cursor address                                             |
| CHILD_NUMBER      | INTEGER     | Child cursor number                                             |
| TIMESTAMP        | DATE        | The time when the execution plan was created                    |
| OPERATION        | VARCHAR(256) | Description information after executing the plan explain         |
| OPTIONS          | VARCHAR(256) | Description information alongside operation after executing the plan explain |
| OBJECT_NODE      | VARCHAR(256) | The representation information of the table or view             |
| OBJECT#         | BIGINT      | Table ID                                                        |
| OBJECT_OWNER     | VARCHAR(64) | The username of the owner of the table or index                 |
| OBJECT_NAME      | VARCHAR(64) | The name of the table or index                                   |
| OBJECT_ALIAS     | VARCHAR(256) | The alias of the table                                          |
| OBJECT_TYPE      | VARCHAR(64) | The type of the object<br>\* TABLE FUNCTION<br>\* TABLE       |
| OPTIMIZER        | VARCHAR(64) | Reserved field                                                  |
| PARTITION_INFO   | VARCHAR(64) | The partition information of the execution plan                 |
| ID               | INTEGER     | The ID of each execution plan                                    |
| PARENT_ID        | INTEGER     | The ID of the next execution plan                                |
| DEPTH            | INTEGER     | The depth of the current execution plan in the overall plan tree (root node depth is 0) |
| POSITION         | INTEGER     | The processing order of all operations with the same PARENT_ID  |
| SEARCH_COLUMNS    | BIGINT      | The number of indexed columns with start and stop keys (i.e., the number of columns with matching predicates) |
| COST             | BIGINT      | Estimated operation cost based on cost method                   |
| CARDINALITY      | BIGINT      | The estimated number of rows produced by the operation based on the optimizer's cost method |
| BYTES            | BIGINT      | The estimated number of bytes produced by the operation based on the optimizer's cost method |
| OTHER_TAG        | VARCHAR(256) | Description of the content in the other column                  |
| PARTITION_START  | INTEGER     | The starting series of partitions being accessed                 |
| PARTITION_STOP   | INTEGER     | The stopping series of partitions being accessed                 |
| PARTITION_ID     | INTEGER     | Partition ID                                                    |
| OTHER            | VARCHAR(4000) | Description of the content in the other column                  |
| CPU_COST         | BIGINT      | Estimated CPU consumption caused by the operation based on the optimizer's cost method |
| IO_COST          | BIGINT      | Estimated IO cost caused by the operation based on the optimizer's cost method |
| TEMP_SPACE       | BIGINT      | Estimated temporary space usage based on the optimizer's cost method |
| ACCESS_PREDICATES | VARCHAR(4000) | Predicates used to locate row data access                       |
| FILTER_PREDICATES | VARCHAR(4000) | Predicates used to filter rows before generating them           |
| PROJECTION       | VARCHAR(4000) | Reserved field                                                  |
| TIME             | BIGINT      | Estimated runtime of the operation based on the optimizer's cost method |
| QBLOCK_NAME      | VARCHAR(64) | The name of each query block                                   |
| REMARKS          | VARCHAR(4000) | Remarks                                                         |