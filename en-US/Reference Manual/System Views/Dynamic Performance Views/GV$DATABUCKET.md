This view displays the databucket (data bucket) file information for all LSC tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| GROUP_ID | NUMBER | Group ID |
| GROUP_NODE_ID | NUMBER | Node ID within the group |
| INST_ID | NUMBER | Instance ID |
| ID | INTEGER | databucket ID |
| NAME | VARCHAR(255) | Databucket Name |
| TYPE | INTEGER | Databucket Type<br>\* 0: LOCAL_FS type |
| TS# | INTEGER | ID of the tablespace to which the databucket belongs |
| SLOT | INTEGER | Sequence number of the databucket within the tablespace |
| URL | VARCHAR(255) | Storage path of the databucket |
| USED_SIZE | BIGINT | Used space of the databucket (unit: bytes) |
| MAX_SIZE | BIGINT | Databucket maximize availability space (unit: bytes) |
| READONLY | BOOLEAN | Whether the databucket is read-only |