本视图显示审计信息。

|  字段| 类型| 说明|
|--------------------------|---------------|--------------|
| GROUP_ID                 | NUMBER        | 组ID          |
| GROUP_NODE_ID            | NUMBER        | 组内节点ID       |
| INST_ID                  | NUMBER        | 实例ID         |
| SESSIONID                | BIGINT        | 审计会话ID       |
| OS\_USER                 | VARCHAR(128)  | 操作系统用户名      |
| HOST\_NAME               | VARCHAR(128)  | 服务器用户名       |
| DBID                     | INTEGER       | 数据库ID        |
| AUTHENTICATION\_TYPE     | VARCHAR(256)  | 认证类型         |
| DBUSERNAME               | VARCHAR(128)  | 用户名字         |
| CLIENT\_PROGRAM\_NAME    | VARCHAR(84)   | 客户端程序名       |
| STATEMENT\_ID            | BIGINT        | statement ID |
| EVENT\_TIMESTAMP         | TIMESTAMP(6)     | 事件发生的时间      |
| ACTION                   | INTEGER       | 审计类型，可以查询V$AUDITABLE_SYSTEM_ACTIONS视图确定审计类型对应的审计行为名称。          |
| RETURN\_CODE             | INTEGER       | 返回码          |
| THREAD\_ID               | BIGINT        | 线程ID         |
| SCN                      | BIGINT        | 系统更改序号       |
| SQL\_TEXT                | CLOB          | 原始SQL        |
| CURRENT\_USER            | VARCHAR(128)  | 当前用户         |
| UNIFIED\_AUDIT\_POLICIES | VARCHAR(4000) | 审计策略         |
| OBJECT\_SCHEMA           | VARCHAR(68)   | 对象拥有者        |
| OBJECT\_NAME             | VARCHAR(68)   | 对象名          |
| SYSTEM_PRIVILEGE_USED    | VARCHAR(1024) | 权限列          |
| SQL_BINDS                | VARCHAR(4000) | 绑定参数         |
