本视图展示字典缓存中的状态信息。

|  字段| 类型| 说明|
| :-------------------- | :---------- | :-------------------- |
| USER_ID              | INTEGER     | 字典缓存对象的用户ID       |
| OBJECT_ID            | BIGINT      | 字典缓存对象的ID           |
| NAME                  | VARCHAR(68) | 对象名称                   |
| VERSION               | INTEGER     | 版本信息，用于数据库内部判断对象的新老情况                   |
| TYPE                  | INTEGER     | 字典缓存对象的[对象类型](../DBA视图/DBA_OBJECTS)             |
| IN_RECYCLE_BIN      | INTEGER     | 该对象是否待回收           |
| VALID                 | INTEGER     | 表示该对象在字典缓存中是否有效<br/> \* 1：表示字典缓存对象有效<br/> \* 0：表示字典缓存对象无效。字典缓存对象无效时，该对象对应的缓存区域可能随时被回收，回收后如需重新访问该对象，需要把字典缓存对象重新加载到缓存<br/> \* NULL：表示字典缓存对象尚未加载<br/>当VALID为0或NULL时，REF_COUNT、MEMORY_CONTEXT_USED、LOAD_SCN三个字段无参考意义 |
| PVT                   | INTEGER     | 表示该对象是否是私有临时表对象，当对象是私有临时表对象时，REF_COUNT、MEMORY_CONTEXT_USED、LOAD_SCN三个字段无参考意义 |
| REF_COUNT            | INTEGER     | 当前字典缓存对象的引用计数 |
| MEMORY_CONTEXT_USED | BIGINT      | 当前字典缓存对象的上下文内存使用字节数                       |
| LOAD_SCN             | BIGINT      | 当前字典缓存对象加载时的SCN号                                |
| NOLOGGING_INSTANCE_ID| TINYINT    | 当前字典缓存对象开启nologging的实例ID，若对象非nologging表，默认为-1|
