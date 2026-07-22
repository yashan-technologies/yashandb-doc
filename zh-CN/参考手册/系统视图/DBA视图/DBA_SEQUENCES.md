本视图显示数据库中所有的SEQUENCE信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| SEQUENCE\_OWNER | VARCHAR(64) | SEQUENCE所属用户的ID |
| SEQUENCE\_NAME | VARCHAR(64) | SEQUENCE名称 |
| MIN\_VALUE | NUMBER | SEQUENCE的最小值 |
| MAX\_VALUE | NUMBER | SEQUENCE的最大值 |
| INCREMENT\_BY | NUMBER | SEQUENCE的增长步长 |
| CYCLE\_FLAG | VARCHAR(1) | 是否在超过最大值之后，循环使用SEQUENCE值<br>\*   0：NO<br>\*   1：YES |
| ORDER\_FLAG | VARCHAR(1) | SEQUENCE值是否顺序产生 |
| CACHE\_SIZE | NUMBER | 每次在内存中缓存的SEQUENCE序列值的长度 |
| LAST\_NUMBER | NUMBER | 最后一个持久化的SEQUENCE值。如果使用了SEQUENCE缓存，那么持久化的SEQUENCE将大于等于最后一个被使用的SEQUENCE |
| DATABASE\_MAINTAINED | VARCHAR(1) | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的SEQUENCE<br>\*   N：用户创建的SEQUENCE |
