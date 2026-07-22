本视图显示SCHEMA权限信息。

|  字段| 类型| 说明|
|----------------|--------------|---------------------|
| GRANTEE        | VARCHAR(81)  | 授予权限的用户名称           |
| TABLE\_CATALOG | VARCHAR(3) | 架构所属的目录的名称，此值始终为def |
| TABLE\_SCHEMA  | VARCHAR(64)  | SCHEMA所在的模式名        |
| PRIVILEGE\_TYPE | VARCHAR(64)  | 授予的权限               | 
| IS\_GRANTABLE  | VARCHAR(3)   | 是否可以授予权限            |
