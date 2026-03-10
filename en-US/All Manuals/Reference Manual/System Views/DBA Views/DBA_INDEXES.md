This view displays all index information.

|Field |Type |Description |
| --- |-------------| --- |
| OWNER              | VARCHAR(64)   | Username to which the index belongs |
| INDEX_NAME         | VARCHAR(64)   | Index name |
| INDEX_TYPE         | VARCHAR(25)   | Index type<br>*   NORMAL: BTree index<br>*   NORMAL/REV: BTree Reverse index<br>*   FUNCTION-BASED NORMAL: Function BTree index<br>*   FUNCTION-BASED NORMAL/REV: Function BTree Reverse index<br>*   LOB: Lob index<br>*   COLUMNAR: Columnar index<br>*   RTREE: RTree index |
| TABLE_OWNER        | VARCHAR(64)   | Username of the table |
| TABLE_NAME         | VARCHAR(64)   | Table name |
| TABLE_TYPE         | VARCHAR(11)   | Table type<br>*   TABLE |
| UNIQUENESS         | VARCHAR(1)    | Whether it is a unique index<br>*   Y: Unique index<br>*   N: Non-unique index |
| COMPRESSION        | CHAR(8)       | Whether it is compressed<br>* Disable |
| PREFIX_LENGTH      | INTEGER       | Length of the compressed prefix |
| TABLESPACE_NAME    | VARCHAR(64)   | Name of the tablespace where the index is located |
| INI_TRANS          | INTEGER       | Initial number of transactions in the block |
| MAX_TRANS          | INTEGER       | Maximum number of transactions in the block |
| PCT_FREE           | INTEGER       | Percentage of space reserved in the block |
| LOGGING            | CHAR(1)       | Whether to record redo logs<br>*   Y: Record redo<br>*   N: Do not record redo |
| BLEVEL             | INTEGER       | Height of the BTree |
| LEAF_BLOCKS        | BIGINT        | Number of leaf blocks |
| DISTINCT_KEYS      | BIGINT        | Number of unique key values |
| AVG_LEAF_BLOCKS_PER_KEY | BIGINT   | Average number of leaf blocks per key value |
| AVG_DATA_BLOCKS_PER_KEY | BIGINT   | Average number of blocks per key value |
| STATUS             | VARCHAR(8)    | Status<br>*   UNUSABLE: Unusable<br>*   VALID: Usable<br>*   N/A: Partition index, check status in DBA_IND_PARTITIONS |
| NUM_ROWS           | BIGINT        | Number of rows in the index |
| SAMPLE_SIZE        | BIGINT        | Number of rows sampled for statistics |
| LAST_ANALYZED      | DATE          | Time when statistics were last collected |
| PARTITIONED        | VARCHAR(1)    | Whether it is a partition index<br>*   Y: Is a partition index<br>*   N: Is not a partition index |
| TEMPORARY          | VARCHAR(1)    | Whether it is a temporary index<br>*   Y: Is a temporary index<br>*   N: Is not a temporary index |
| GENERATED          | VARCHAR(1)    | Whether it is an index name created automatically by the system<br>*   Y: Index name created automatically by the system<br>*   N: Index name created by the user |
| GLOBAL_STATS       | VARCHAR(3)    | Whether it has global statistics |
| VISIBILITY         | VARCHAR(9)    | Visibility of the index<br>*   VISIBLE: Visible to SQL executors<br>*   INVISIBLE: Not visible to SQL executors |
| DATABASE_MAINTAINED  | VARCHAR(1)    | Whether created by the database system <br>* Y: Yes <br>* N: No (created by the user) |
| CONSTRAINT_INDEX   | VARCHAR(1)    | Whether it is an index created automatically by a constraint<br>*   Y: Index name created automatically by a constraint<br>*   N: Index created by the user |
| ORPHANED_ENTRIES   | VARCHAR(1)    | Whether the global index contains orphan key-value pairs<br>*   Y: Does not contain<br>*   N: Contains |
| FLASHBACK_SCN | BIGINT | The SCN that the current index can be used for flashback queries. If this value is NULL, it is treated as FLASHBACK_SCN equal to the SCN corresponding to the database startup time. Operations such as creating this index (CREATE INDEX), rebuilding this index (ALTER INDEX REBUILD), and setting this index unusable (ALTER INDEX UNUSABLE) will change this value <br/>If the SCN specified by the flashback query is greater than the maximum SCN that the corresponding table and indexes on the table can be used for flashback queries, the system will automatically flashback query based on the corresponding indexes |