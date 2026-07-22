This view displays information about all partition tables.

|Field |Type |Description |
| --- | --- | --- |
| OWNER | VARCHAR(64) | The username of the partition table |
| TABLE\_NAME | VARCHAR(64) | Table name |
| PARTITIONING\_TYPE | VARCHAR(9) | Partition type<br>*   RANGE<br>*   HASH<br>*   LIST<br>*   SYSTEM (for internal database use only) |
| SUBPARTITIONING\_TYPE | VARCHAR(9) | Subpartition type<br/>*   NONE non-composite partition<br/>*   RANGE<br>*   HASH<br>*   LIST |
| PARTITION\_COUNT | BIGINT | Number of partitions |
| PARTITIONING\_KEY\_COUNT | INTEGER | Number of columns in the partition key |
| SUBPARTITIONING\_KEY\_COUNT | INTEGER | Number of columns in the subpartition key |
| DEF\_TABLESPACE\_NAME | VARCHAR(64) | Default tablespace name for the partition |
| DEF\_PCT\_FREE | INTEGER | Default PCTFREE for the partition |
| DEF\_INI\_TRANS | INTEGER | Default INIT TRANS for the partition |
| DEF\_MAX\_TRANS | INTEGER | Default MAX TRANS for the partition |
| DEF\_LOGGING | CHAR(1) | Default LOGGING attribute for the partition |
| INTERVAL | VARCHAR(1000) | INTERVAL partition key value |
