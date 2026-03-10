本视图显示所有表的排序信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 表所属用户名 |
| TABLE_NAME | VARCHAR(64) | 表名  |
| SORT_METHOD | VARCHAR(4) | 排序方式<br>\*   DESC<br>\*   ASC<br> |
| NULLS_FIRST | VARCHAR(1) | 空值是否在前 |
| SORT_TYPE | VARCHAR(11) | 排序方法<br/>\*   SORT_NORMAL<br/>\*   SORT_ZSORT<br/>\*   SORT_HSORT<br/> |
| SORT_MCOL | VARCHAR(1) | 是否排序 |
