This view displays all partition index information.

|Field |Type |Description |
| --- | --- | --- |
| OWNER | VARCHAR(64) | The username of the partition index owner |
| INDEX\_NAME | VARCHAR(64) | The name of the index |
| TABLE\_NAME | VARCHAR(64) | The name of the table to which the index belongs |
| PARTITIONING\_TYPE | VARCHAR(5) | The partition type<br>*   RANGE<br>*   HASH<br>*   LIST<br>*   SYSTEM (for internal database use only) |
| SUBPARTITIONING\_TYPE | VARCHAR(5) | The subpartition type<br/>*   RANGE<br/>*   HASH<br/>*   LIST |
| PARTITION\_COUNT | BIGINT | The number of partitions |
| PARTITIONING\_KEY\_COUNT | INTEGER | The number of partition keys |
| SUBPARTITIONING\_KEY\_COUNT | INTEGER | The number of subpartition keys |
| LOCALITY | VARCHAR(6) | The partition index type<br>* LOCAL<br>* GLOBAL |
| DEF\_TABLESPACE\_NAME | VARCHAR(64) | The default tablespace name when adding a partition |
| DEF\_PCT\_FREE | INTEGER | The default PCTFREE when adding a partition, in percentage |
| DEF\_INI\_TRANS | INTEGER | The default INI\_TRANS when adding a partition, in number (initialization transactions per page) |
| DEF\_MAX\_TRANS | INTEGER | The default MAX TRANS when adding a partition, in number (maximum transactions allowed per page) |
| DEF\_LOGGING | CHAR(1) | The default LOGGING attribute when adding a partition, with values of Y or N |
