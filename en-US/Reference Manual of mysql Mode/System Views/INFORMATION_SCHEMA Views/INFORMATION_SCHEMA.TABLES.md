This view displays table information.

|Field |Type |Description |
| --- | --- | --- |
| TABLE_CATALOG      | VARCHAR(3)   | The catalog name of the table                            |
| TABLE_SCHEMA       | VARCHAR(64)  | The schema name of the table                             |
| TABLE_NAME         | VARCHAR(64)  | The name of the table                                    |
| TABLE_TYPE         | VARCHAR(10)  | The type of the table (BASE TABLE, VIEW, SYSTEM VIEW)  |
| ENGINE             | VARCHAR(8)   | The storage engine the table belongs to, fixed as YashanDB |
| VERSION            | INT          | The version number of the table, fixed as 10            |
| ROW_FORMAT         | VARCHAR(7)   | The row format used by the table (COMPACT)              |
| TABLE_ROWS         | BIGINT       | The number of rows in the table                          |
| AVG_ROW_LENGTH     | BIGINT       | The average length of each row in the table             |
| DATA_LENGTH        | BIGINT       | The number of bytes occupied by the data in the table. Currently not counted, fixed as 0 |
| MAX_DATA_LENGTH    | BIGINT       | The maximum space allowed for data in the table (unit: bytes). Currently not counted, fixed as 0 |
| INDEX_LENGTH       | BIGINT       | The size of the space occupied by the index. Currently not counted, fixed as 0 |
| DATA_FREE          | BIGINT       | The space in the table not allocated to any object. Currently not counted, fixed as 0 |
| AUTO_INCREMENT      | BIGINT UNSIGNED | The expected value of the next auto-increment value in the table |
| CREATE_TIME        | TIMESTAMP    | The time the table was created                           |
| UPDATE_TIME        | DATE         | The time the table was updated                           |
| CHECK_TIME         | DATE         | The time the table was last checked                      |
| TABLE_COLLATION    | VARCHAR(32)  | The character set and collation used by the table (currently ineffective and cannot be modified) |
| CHECKSUM           | BIGINT       | The checksum of the table<br>Syntax compatible only, no actual functional meaning.        |
| CREATE_OPTIONS     | VARCHAR(11)  | Additional options for the table, including encryption method, row format, partitioning, etc. |
| TABLE_COMMENT      | VARCHAR(4000)| The comment for the table                                |