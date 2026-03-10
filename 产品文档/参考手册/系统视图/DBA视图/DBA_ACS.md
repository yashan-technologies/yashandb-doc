本视图显示所有的AC。

| 字段  | 类型          | 说明                                                                                  |
| --- |-------------|-------------------------------------------------------------------------------------|
| OWNER | VARCHAR(64)   | AC的用户名                                              |
| AC_NAME         | VARCHAR(64)   | AC名                                                    |
| COLS            | INTEGER       | AC列数量                                                |
| INCOLS          | INTEGER       | AC的左侧范围列数量                                      |
| OUTCOLS         | INTEGER       | AC的右侧范围列数量                                      |
| BOUND           | BIGINT        | AC的边界大小                                            |
| SORTTYPE        | VARCHAR(12)   | AC的排序方式<br/>*  ACOL_ORDER<br/>*  ACOL_UNORDER<br/> |
| FILTER          | VARCHAR(4000) | AC数据过滤的条件                                        |
| TABLE_OWNER     | VARCHAR(64)   | 表的用户名                                              |
| TABLE_NAME      | VARCHAR(64)   | 表名                                                    |
| PARTITIONED     | VARCHAR(1)    | 是否为分区AC<br>\*   Y：分区AC<br>\*   N：不是分区AC        |
| SHARDED         | VARCHAR(1)    | 是否为分布AC<br/>\*   Y：分布AC<br/>\*   N：不是分布AC      |
| DUPLICATED      | VARCHAR(1)    | 是否为复制AC<br/>\*   Y：复制AC<br/>\*   N：不是复制AC      |
| TABLESPACE_NAME | VARCHAR(64)   | 表空间名字                                              |
