本视图显示表权限信息。

|  字段| 类型| 说明|
|----------------|--------------|--------------------|
| GRANTEE        | VARCHAR(81)  | 授予权限的用户名          |
| TABLE_CATALOG | VARCHAR(512) | 架构所属的目录的名称，此值始终为def |
| TABLE_SCHEMA  | VARCHAR(64)  | 表所在的模式名             | 
| TABLE_NAME    | VARCHAR(64)  | 表的名称               |
| PRIVILEGE_TYPE | VARCHAR(64)  | 授予的权限              | 
| IS_GRANTABLE  | VARCHAR(3)   | 是否可以授予权限           |
