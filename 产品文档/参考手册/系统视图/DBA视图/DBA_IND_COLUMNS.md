本视图显示所有的索引列信息。

| 字段  | 类型 | 说明  |
| --- | --- | --- |
| INDEX_OWNER | VARCHAR(64) | 索引的用户名 |
| INDEX_NAME | VARCHAR(64) | 索引名 |
| TABLE_OWNER | VARCHAR(64) | 表的用户名 |
| TABLE_NAME | VARCHAR(64) | 表名 |
| COLUMN_NAME | VARCHAR(64) | 列名  |
| COLUMN_POSITION | INTEGER | 列在索引中的位置 |
| COLUMN_LENGTH | INTEGER | 列的长度（单位：字节） |
| DESCEND | VARCHAR(4) | 列是否降序存储<br>\*   DESC：降序<br>\*   ASC：升序 |