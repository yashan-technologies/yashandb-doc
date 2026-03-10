本视图描述数据库中的临时表空间在对应实例的临时空间相关使用信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| TABLESPACE_NAME | VARCHAR(64) | 表空间名称 |
| TABLESPACE_SIZE | NUMBER | 表空间的总大小（单位：字节） |
| ALLOCATED_SPACE | NUMBER | 已分配的总空间（单位：字节） |
| FREE_SPACE | NUMBER | 当前可用空间（单位：字节） |
| SHARED | VARCHAR(16) | 临时表空间类型<br>\*   SHARED：共享临时表空间<br>\*   LOCAL_ON_ALL：本地临时表空间 |
| INST_ID | NUMBER  | 实例ID |