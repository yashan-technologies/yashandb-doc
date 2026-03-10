本视图显示所有database link在沙箱进程yex_server上创建的对象的相关统计信息。

| 字段 | 类型 | 描述 |
|-------------------|-------------|--|
| EXT_OBJ_ID | INTEGER | 对象ID |
| EXT_OBJ_NAME | VARCHAR(64) | 对象名称 |
| EXT_OBJ_USERID | INTEGER | 对象USERID |
| EXT_OBJ_VALID | BOOLEAN | 对象是否有效，TRUE表示有效，FALSE表示无效 |
| EXT_OBJ_REF | INTEGER | 对象被引用的计数值 |