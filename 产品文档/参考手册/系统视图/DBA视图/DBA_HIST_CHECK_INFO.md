本视图显示可访问到的已开启防篡改能力的防篡改对象的信息。
This view shows information about accessible enabled tamper-proof objects.

|  字段  /  Field | 类型  /  Type | 说明  /  Description |
|-------------|-------------|---------------------------------------------------------------------|
| OWNER       | VARCHAR(64) | 该表的用户名                                                              |
| OWNER        | VARCHAR(64)  | The username of the table                                             |
| TABLE_NAME | VARCHAR(64) | 表名                                                                  |
| TABLE_NAME  | VARCHAR(64)  | Table name                                                           |
| MODE        | VARCHAR(8)  | 防篡改模式（目前仅针对只读表模式）<br/>"READONLY" 表示只读表模式<br/>"UNKNOWN" 表示未知模式       |
| MODE         | VARCHAR(8)   | Tamper-proof mode (currently only for read-only table mode)<br/>"READONLY" indicates read-only table mode<br/>"UNKNOWN" indicates unknown mode |
| ACHECK      | VARCHAR(5)  | 防篡改表是否发生篡改：<br/>"TRUE" 表示未发生篡改<br/>"FALSE" 表示已发生篡改 |
| ACHECK       | VARCHAR(5)   | Whether the tamper-proof table has been tampered with:<br/>"TRUE" indicates not tampered<br/>"FALSE" indicates tampered |