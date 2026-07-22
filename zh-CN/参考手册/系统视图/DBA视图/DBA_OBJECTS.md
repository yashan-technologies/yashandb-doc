本视图显示数据库中的所有对象信息。

|  字段| 类型| 说明|
| --- | --- |-------------------------------------------------------------------|
|  OWNER | VARCHAR(64) | 对象所属用户名                      |
| OBJECT\_NAME | VARCHAR(64) | 对象名称                         |
| SUBOBJECT\_NAME | VARCHAR(64) | 子对象名称                        |
| OBJECT\_ID | BIGINT | 对象ID                         |
| DATA\_OBJECT\_ID | BIGINT | 对象Segment ID                 |
| OBJECT\_TYPE | VARCHAR(18) | 对象类型<br/>\* 1：TABLE<br/>\* 2：VIEW<br/>\* 3：DYNAMIC\_VIEW<br/>\* 4：INDEX<br/>\* 5：SEQUENCE<br/>\* 6：AC<br/>\* 7：TABLE PARTITION<br/>\* 8：INDEX PARTITION<br/>\* 9：LOB<br/>\* 10：LOB PARTITION<br/>\* 11：SYNONYM<br/>\* 12：UDF<br/>\* 13：PROCEDURE<br/>\* 14：PACKAGE<br/>\* 15：TRIGGER<br/>\* 16：AC PARTITION<br/>\* 17：AUDIT POLICY<br/>\* 18：JOB<br/>\* 19：PACKAGE BODY<br/>\* 20：TYPE<br/>\* 21：TYPE BODY<br/>\* 22：LIBRARY<br/>\* 23：TABLE SUBPARTITION<br/>\* 24：INDEX SUBPARTITION<br/>\* 25：LOB SUBPARTITION<br/>\* 26：MATERIALIZED VIEW<br/>\* 27：DATABASE LINK<br/>\* 28：OUTLINE<br/>\* 29：FIXED TABLE<br/>\* 30：FIXED VIEW<br/>\* 31：DIRECTORY<br/>\* 32：LBAC POLICY<br/>\* 33：CONTEXT<br/>\* 34：CONTAINER |
| CREATED | DATE | 对象创建时间                       |
| LAST\_DDL\_TIME | DATE | 最后一次修改对象的时间                  |
| TIMESTAMP | VARCHAR(1) | 预留字段                         |
| STATUS | VARCHAR(7) | 对象状态<br>\*   VALID<br>\*   INVALID          |
| TEMPORARY | VARCHAR(1) | 对象是否是临时的（Y/N）                |
| GENERATED | VARCHAR(1) | 对象名称是否为系统生成的（Y/N）            |
| SECONDARY | VARCHAR(1) | 预留字段，值为N                     |
| NAMESPACE | INTEGER | 对象名字空间                       |
| SHARING | CHAR(4) | 预留字段，值为NONE                  |
| EDITIONABLE | VARCHAR(1) | 预留字段                         |
| DATABASE\_MAINTAINED | VARCHAR(1) | 是否DATABASE MAINTAINED<br>\*   Y：数据库系统创建的对象<br>\*   N：用户创建的对象   DBA_OBJECTS    |
| APPLICATION | VARCHAR(1) | 预留字段                         |
| DUPLICATED | CHAR(1) | 表示是否为复制对象（Y/N）               |
| SHARDED | CHAR(1) | 表示是否为分布对象（Y/N）               |
