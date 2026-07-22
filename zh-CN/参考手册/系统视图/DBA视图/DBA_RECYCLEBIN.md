本视图显示所有进入回收站的对象信息。

|  字段| 类型| 说明|
| --- |-------------| --- |
| OWNER | VARCHAR(64) | 用户名 |
| OBJECT_NAME | VARCHAR(64) | 对象被删除后系统生成的名称 |
| ORIGINAL_NAME | VARCHAR(64) | 对象被删除前的名称 |
| OPERATION | VARCHAR(9)  | 对象进入回收站的方式<br>\*   DROP<br/>*   TRUNCATE |
| TYPE | VARCHAR(18) | 对象类型<br>*   TABLE：表<br/>*  VIEW：视图<br/>*  DYNAMIC_VIEW：动态视图<br/>*  INDEX：索引<br/>*  SEQUENCE：序列<br/>*  AC：访问约束<br/>*  TABLE PARTITION：表分区<br/>*  INDEX PARTITION：索引分区<br/>*  LOB：LOB列<br/>*  LOB PARTITION：LOB列分区<br/>*  SYNONYM：同义词<br/>*  UDF：自定义函数列<br/>*  PROCEDURE：存储过程<br/>*  PACKAGE：高级包<br/>*  PACKAGE BODY：高级包的包体<br/>*  TRIGGER：触发器<br/>*  JOB：定时任务    |
| TS_NAME | VARCHAR(64) | 表空间名称 |
| RECYCLEBIN_TIME | VARCHAR(19) | 进入回收站的时间 |
| RECYCLEBIN_SCN | BIGINT      | 进入回收站的SCN |
| PARTITION_NAME | VARCHAR(64) | 分区名称 |
| TRUNCATE_PART | VARCHAR(3)  | 是否以alter table truncate partition方式进入回收站<br>\*   YES<br/>*   NO |
| RELATED | BIGINT      | 相关对象的ID<br>\*  分区对象：为父级对象的对象ID<br/>*   非分区对象：为总表的对象ID，如非分区索引，分区索引的总体索引 |
| BASE_OBJECT | BIGINT      | 该对象所依附的表的对象ID |
| PURGE_OBJECT | BIGINT      | 该对象可以被回收的依附对象的对象ID |
| BLOCK_COUNT | INTEGER     | 该对象所占segmen的block数 |
