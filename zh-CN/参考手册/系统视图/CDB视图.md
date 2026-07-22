CDB视图与DBA视图基本同构，CDB视图具有独有的CON_ID（容器ID）字段用于标识数据来源，其他字段描述可参考DBA视图。

CDB视图仅在容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）中有意义，在根容器上查询CDB视图可获取全局信息，而在PDB中查询CDB视图则仅统计当前PDB的信息。

|  视图名称| 功能说明|
|--------------------------------|--------------------------------------------------------------------------|
| CDB_ALL_TABLES |  所有的表，包括系统表             |
| CDB_CDB_RSRC_PLANS           |  所有容器资源计划信息       |
| CDB_CDB_RSRC_PLAN_DIRECTIVES |  所有容器资源计划指令信息                 |
| CDB_CONSTRAINTS |  所有容器信息             |
| CDB_CONS_COLUMNS | 所有表中列所属的约束信息              |
| CDB_DATA_FILES | 所有数据文件信息              |
| CDB_INDEXES | 所有的索引    |
| CDB_IND_COLUMNS | 所有的索引列              |
| CDB_IND_EXPRESSIONS | 所有的函数索引表达式信息              |
| CDB_OBJECTS | 所有的对象              |
| CDB_PROFILES | 所有用户配置文件              |
| CDB_ROLES | 所有的角色              |
| CDB_ROLE_PRIVS | 所有基于角色授权的记录              |
| CDB_SYS_PRIVS | 所有基于系统特权授权的记录              |
| CDB_TABLES | 所有的表，包括系统表              |
| CDB_TABLESPACES | 所有的表空间              |
| CDB_TAB_COLS |  所有表中的所有列，包括系统内部创建的虚拟列信息         |
| CDB_TAB_COLUMNS |  所有表中的所有列             |
| CDB_TAB_PRIVS |  所有基于对象特权授权的记录             |
| CDB_TS_QUOTAS | 所有的用户对应的表空间配额，以及配额的使用情况              |
| CDB_USERS |  所有的用户             |
