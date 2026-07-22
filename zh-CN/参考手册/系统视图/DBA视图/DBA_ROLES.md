本视图显示数据库所有角色信息。

|  字段| 类型| 说明|
| :-- | --- | --- |
| ROLE | VARCHAR(64) | 角色名称 |
| ROLE_ID | INTEGER | 角色ID |
| TYPE | VARCHAR(11) | 角色类型<br/>\*   NORMAL ROLE：普通角色<br/>\*   SYSTEM ROLE：系统级权限角色<br/>\*   OBJECT ROLE：对象级权限角色 |
| SYS_MAINTAINED | VARCHAR(3) | 是否内置角色<br/>\*   YES：数据库内置角色<br/>\*   NO：用户自定义创建的角色 |
| COMMON | VARCHAR(1) | 是否为全局角色<br>\* Y<br/>* N |
