This view displays all ACs. 

|Field |Type |Description |
| --- |-------------|-------------------------------------------------------------------------------------|
| OWNER            | VARCHAR(64)   | The username of the AC                                                                |
| AC_NAME          | VARCHAR(64)   | The name of the AC                                                                    |
| COLS             | INTEGER       | The number of columns in the AC                                                       |
| INCOLS           | INTEGER       | The number of left range columns of the AC                                            |
| OUTCOLS          | INTEGER       | The number of right range columns of the AC                                           |
| BOUND            | BIGINT        | The boundary size of the AC                                                           |
| SORTTYPE         | VARCHAR(12)   | The sorting method of the AC<br/>*  ACOL_ORDER<br/>*  ACOL_UNORDER<br/>            |
| FILTER           | VARCHAR(4000) | The filtering conditions for AC data                                                  |
| TABLE_OWNER      | VARCHAR(64)   | The username of the table                                                             |
| TABLE_NAME       | VARCHAR(64)   | The name of the table                                                                  |
| PARTITIONED      | VARCHAR(1)    | Whether it is a partitioned AC<br>\*   Y: partition AC<br>\*   N: not a partitioned AC |
| SHARDED          | VARCHAR(1)    | Whether it is a sharded AC<br/>\*   Y: sharded AC<br/>\*   N: not a sharded AC       |
| DUPLICATED       | VARCHAR(1)    | Whether it is a duplicated AC<br/>\*   Y: duplicated AC<br/>\*   N: not a duplicated AC |
| TABLESPACE_NAME  | VARCHAR(64)   | The name of the tablespace                                                             |