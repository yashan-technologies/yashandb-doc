本视图显示视图信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| TABLE\_CATALOG | VARCHAR(3) | 视图所在的目录名 |
| TABLE\_SCHEMA | VARCHAR(64) | 视图所在的模式名 |
| TABLE\_NAME | VARCHAR(64) | 视图的名称 |
| VIEW\_DEFINITION | LONGTEXT | 视图的定义 |
| CHECK\_OPTION | VARCHAR(4) | 视图的检查选项<br>仅语法兼容，无实际功能含义 |
| IS\_UPDATABLE | VARCHAR(2) | 视图是否可更新的标志。<br>保留字段，默认显示`NO`，无实际含义 |
| DEFINER | VARCHAR(64) | 视图的创建者 |
| SECURITY\_TYPE | VARCHAR(7) | 视图的安全类型 |
| CHARACTER\_SET\_CLIENT |  VARCHAR(32) | 客户端连接时使用的字符集编码 |
| COLLATION\_CONNECTION | VARCHAR(32) | 客户端连接时使用的字符集排序规则 |
