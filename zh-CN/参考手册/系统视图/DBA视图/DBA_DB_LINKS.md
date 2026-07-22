本视图显示所有数据库链接信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| OWNER            | VARCHAR(64)  | DBLINK所属用户的名称 |
| DB\_LINK         | VARCHAR(128) | DBLINK名称 |
| USERNAME         | VARCHAR(128) | 访问DBLINK所使用的用户名 |
| PASSWORD         | RAW(256)     | 访问DBLINK所使用的密文密码                   |
| CREDENTIAL\_NAME | VARCHAR(128) | DBLINK凭证名称<sup>*</sup> |
| CREDENTIAL\_OWNER | VARCHAR(128) | DBLINK凭证所属用户的名称<sup>*</sup> |
| HOST             | VARCHAR(256) | DBLINK所连接的目标数据库的主库信息 |
| CREATED          | DATE         | DBLINK的创建时间 |
| HIDDEN           | VARCHAR(3)   | 表示DBLINK是否被隐藏<sup>*</sup> |
| SHARD\_INTERNAL  | VARCHAR(3)   | 表示DBLINK是否用于支持存算一体分布式数据库<sup>*</sup> |
| VALID            | VARCHAR(3)   | 表示DBLINK是否有效。YES表示有效，NO表示无效 |
| INTRA\_CDB       | VARCHAR(3)   | 表示DBLINK是否跨容器<sup>*</sup> |

描述中带\*号的列为预留列，内部值恒为空。
