This view displays the databucket file information of all LSC tablespaces for all nodes in the ISC distributed cluster.

|Field |Type |Description |
| --- | --- | --- |
| GROUP\_ID | INTEGER | Group ID |
| GROUP\_NODE\_ID | INTEGER | Node ID within the group |
| ID | INTEGER | databucket ID |
| NAME | VARCHAR(255) | Databucket name |
| TYPE | INTEGER | Databucket type <br>\* 0: LOCAL_FS type |
| TS# | INTEGER | ID of the tablespace to which the databucket belongs |
| SLOT | INTEGER | Serial number of the databucket within the tablespace |
| URL | VARCHAR(255) | Databucket storage path |
| USED\_SIZE | BIGINT | Used space of the databucket (unit: bytes) |
| MAX\_SIZE | BIGINT | Databucket maximize availability space (unit: bytes) |
| READONLY | BOOLEAN | Whether the databucket is read-only |