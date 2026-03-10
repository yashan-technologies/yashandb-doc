This view displays index statistics.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_CATALOG    | VARCHAR(3)   | The catalog name of the table where the index is located |
| TABLE_SCHEMA     | VARCHAR(64)  | The schema name of the table where the index is located  |
| TABLE_NAME       | VARCHAR(64)  | The name of the table where the index is located          |
| NON_UNIQUE       | BIGINT       | Whether the index is non-unique                         |
| INDEX_SCHEMA     | VARCHAR(64)  | The schema name to which the index belongs               |
| INDEX_NAME       | VARCHAR(64)  | The name of the index                                |
| SEQ_IN_INDEX     | BIGINT       | The order of the index column                           |
| COLUMN_NAME      | VARCHAR(64)  | The name of the index column                          |
| COLLATION        | VARCHAR(1)   | The collation of the index column                      |
| CARDINALITY      | BIGINT       | The cardinality of the index column                      |
| SUB_PART         | BIGINT       | The index prefix length                                   |
| PACKED           | VARCHAR(1)   | The packing method                                        |
| NULLABLE         | VARCHAR(3)   | Whether the index column contains null values          |
| INDEX_TYPE       | VARCHAR(8)   | The type of the index                                     |
| COMMENT           | VARCHAR(4000) | Comments                                              |
| INDEX_COMMENT     | VARCHAR(4000) | Comments on the index                                 |