In the Standalone Deployment, this view displays the values of all dictionary structures in all TAC tables. When partitions exist, they are presented on a per-partition basis.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID     | NUMBER   | Group ID                    |
| GROUP_NODE_ID| NUMBER   | Node ID within the group    |
| INST_ID      | NUMBER   | Instance ID                 |
| BO           | BIGINT   | Parent table ID             |
| OBJID        | BIGINT   | Current table ID (partition ID) |
| DATAOBJ      | BIGINT   | The data object ID of the current table |
| COL_ID       | SMALLINT | Column ID of the dictionary encoding in the table |
| DICT_ID      | INTEGER  | ID of the dictionary item in the current table (partition) |
| DICT_VALUES  | VARCHAR(8000) | Value of the dictionary item in the current table (partition) |