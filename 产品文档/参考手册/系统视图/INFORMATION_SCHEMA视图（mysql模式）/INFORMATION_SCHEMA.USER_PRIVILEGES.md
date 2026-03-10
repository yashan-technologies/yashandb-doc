本视图显示用户权限信息。

| 字段             | 类型           | 说明                  |
|----------------|--------------|---------------------|
| GRANTEE        | VARCHAR(81)  | 授予权限的账户名称           |
| TABLE_CATALOG | VARCHAR(512) | 架构所属的目录的名称，此值始终为def |
| PRIVILEGE_TYPE | VARCHAR(64)  | 授予的权限               | 
| IS_GRANTABLE  | VARCHAR(3)   | 是否可以授予权限  |
 