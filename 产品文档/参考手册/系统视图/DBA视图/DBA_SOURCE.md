本视图显示所有存储过程信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| OWNER | VARCHAR(64) | 对象所属的用户 |
| NAME | VARCHAR(64) | 对象名称 |
| TYPE | VARCHAR(17) | 对象类型 |
| TEXT | CLOB | 存储过程源码 |
| ORIGIN_CON_ID | VARCHAR(1) | 保留字段，值为NULL |
| OBJ# | BIGINT | 对象ID |
| VERSION | INTEGER | 对象版本号 |