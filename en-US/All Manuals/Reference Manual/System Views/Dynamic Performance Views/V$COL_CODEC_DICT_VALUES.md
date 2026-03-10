In Standalone Deployment, this view displays the values of all dictionary structures in all TAC tables. When a partition exists, it is displayed on a partition basis.

|Field |Type |Description |
| --- | --- | --- |
| BO  | BIGINT | Parent table ID |
| OBJID | BIGINT | This table ID (partition ID) |
| DATAOBJ | BIGINT | Data object ID of this table |
| COL_ID  | SMALLINT | Column ID of the dictionary encoding in the table |
| DICT_ID | INTEGER | ID of dictionary item in this table (partition) |
| DICT_VALUES |VARCHAR(8000) | Value of the dictionary item in this table (partition) |