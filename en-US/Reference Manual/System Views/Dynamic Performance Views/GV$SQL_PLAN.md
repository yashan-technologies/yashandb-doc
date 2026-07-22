This view displays all execution plan information.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID           | NUMBER     | Group ID                                              |
| GROUP_NODE_ID      | NUMBER     | Node ID within the group                              |
| INST_ID            | NUMBER     | Instance ID                                          |
| ADDRESS            | RAW(8)     | SQL address                                          |
| HASH\_VALUE        | BIGINT     | Hash value of the SQL, calculated from the SQL text |
| SQL\_ID            | VARCHAR(13)| Unique identifier for a SQL statement, calculated using hashing/encryption on the SQL text |
| PLAN\_HASH\_VALUE  | BIGINT     | A unique identifier for an execution plan            |
| CHILD\_ADDRESS     | RAW(8)     | Child cursor address                                  |
| CHILD\_NUMBER      | INTEGER    | Child cursor number                                   |
| TIMESTAMP          | DATE       | Time when the execution plan was created              |
| OPERATION          | VARCHAR(256)| Description information after explaining the execution plan |
| OPTIONS            | VARCHAR(256)| Descriptive information with operation after explanation of the execution plan |
| OBJECT\_NODE       | VARCHAR(256)| Description information of the table or view        |
| OBJECT#            | BIGINT     | Table ID                                             |
| OBJECT\_OWNER      | VARCHAR(64)| Owner username of the table or index                  |
| OBJECT\_NAME       | VARCHAR(64)| Name of the table or index                            |
| OBJECT\_ALIAS      | VARCHAR(256)| Alias of the table                                   |
| OBJECT\_TYPE       | VARCHAR(64)| Object type<br/>* TABLE FUNCTION<br/>* TABLE        |
| OPTIMIZER          | VARCHAR(64)| Reserved field                                       |
| PARTITION\_INFO    | VARCHAR(64)| Partition information of the execution plan          |
| ID                 | INTEGER    | ID for each execution plan                            |
| PARENT\_ID         | INTEGER    | ID of the next execution plan                         |
| DEPTH              | INTEGER    | Depth of the current execution plan in the plan tree (root node depth is 0) |
| POSITION           | INTEGER    | Processing order of all operations with the same PARENT_ID |
| SEARCH\_COLUMNS    | BIGINT     | Number of indexed columns with start and stop keys (i.e., number of columns with matching predicates) |
| COST               | BIGINT     | Estimated operation cost based on cost method        |
| CARDINALITY        | BIGINT     | Estimated number of rows produced by the operation based on the optimizer's cost-based method |
| BYTES              | BIGINT     | Estimated number of bytes produced by the operation based on the optimizer's cost-based method |
| OTHER\_TAG         | VARCHAR(256)| Description of the other column's content           |
| PARTITION\_START   | INTEGER    | Start of the accessed series of partitions            |
| PARTITION\_STOP    | INTEGER    | Stop of the accessed series of partitions             |
| PARTITION\_ID      | INTEGER    | Partition ID                                          |
| OTHER              | VARCHAR(4000)| Description of the other column's content           |
| CPU\_COST          | BIGINT     | Estimated CPU consumption for the operation based on the optimizer's cost-based method |
| IO\_COST           | BIGINT     | Estimated IO cost for the operation based on the optimizer's cost-based method |
| TEMP\_SPACE        | BIGINT     | Estimated temporary space usage based on the optimizer's cost-based method |
| ACCESS\_PREDICATES | VARCHAR(4000)| Predicates used to locate accessed row data        |
| FILTER\_PREDICATES | VARCHAR(4000)| Predicates used to filter rows before they are generated |
| PROJECTION         | VARCHAR(4000)| Reserved field                                       |
| TIME               | BIGINT     | Estimated operation run time based on the optimizer's cost-based method (unit: ms) |
| QBLOCK\_NAME       | VARCHAR(64)| Name of each query block                              |
| REMARKS            | VARCHAR(4000)| Remarks                                             |