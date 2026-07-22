本视图显示数据库所有同义词信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER | VARCHAR(64) | 同义词所属的用户名 |
| SYNONYM\_NAME | VARCHAR(64) | 同义词名称 |
| TABLE\_OWNER | VARCHAR(64) | 被引用对象所属的用户名 |
| TABLE\_NAME | VARCHAR(64) | 被引用对象的名称 |
| DB\_LINK | VARCHAR(64) | 引用database link的名称 |
| DATABASE\_MAINTAINED | VARCHAR(1)  | 是否由数据库系统创建<br>\*   Y：是 <br>\*   N：否，由用户创建                         |
