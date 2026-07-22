This view displays the databucket file information for all LSC tablespaces.

|Field |Type |Description |
| --- | --- | --- |
| ID | INTEGER | databucket ID |
| NAME | VARCHAR(255) | databucket name |
| TYPE | INTEGER | databucket type<br>\* 0: LOCAL_FS type |
| TS# | INTEGER | ID number of the tablespace to which the databucket belongs |
| SLOT | INTEGER | Serial number of the databucket within the belonging tablespace |
| URL | VARCHAR(255) | Storage path of the databucket |
| USED\_SIZE | BIGINT | Used space of the databucket (unit: bytes) |
| MAX\_SIZE | BIGINT | Maximize availability space of the databucket (unit: bytes) |
| READONLY | BOOLEAN | Whether the databucket is read-only |