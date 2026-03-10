本视图显示审计记录信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| SESSIONID | BIGINT | 审计会话ID |
| OS_USER | VARCHAR(128) | 数据库会话的操作系统用户的名称 |
| HOST_NAME | VARCHAR(128) | 主库名称 |
| INSTANCE_ID | SMALLINT | 实例ID |
| DBID | INTEGER | 数据ID |
| AUTHENTICATION_TYPE | VARCHAR(256) | 会话用户的身份验证信息 |
| DBUSERNAME | VARCHAR(128) | 登录用户名称 |
| CLIENT_PROGRAM_NAME | VARCHAR(84) | 客户端名称 |
| STATEMENT_ID | BIGINT | 语句ID，无实际意义 |
| EVENT_TIMESTAMP | TIMESTAMP | 时间 |
| ACTION | VARCHAR(64) | 审计项名称 |
| RETURN_CODE | INTEGER | 错误码值，审计对应的事件执行成功则为0 |
| THREAD_ID | BIGINT | 线程ID |
| SCN | BIGINT | SCN |
| SQL_TEXT | CLOB | SQL内容 |
| CURRENT_USER | VARCHAR(128) | 执行语句的当前用户 |
| UNIFIED_AUDIT_POLICIES | VARCHAR(4000) | 审计策略名称 |
| NODE_NAME | VARCHAR(68) | 物理节点名称，单机记为"unknown" |
| OBJECT_SCHEMA | VARCHAR(68) | 审计的对象所属的用户名 |
| OBJECT_NAME | VARCHAR(68) | 审计的对象名 |
| SYSTEM_PRIVILEGE_USED | VARCHAR(1024) | 权限名称 |
| SQL_BINDS | VARCHAR(4000) | 绑定参数的值 |
| TRANSACTION_ID | BIGINT | 事务ID |
| ROLE | VARCHAR(64) | 登录用户角色类型 |
| YLS_POLICY_NAME | VARCHAR(64) | 行访问控制策略名称 |
| YLS_GRANTEE | VARCHAR(64) | 应用行访问控制策略的用户 |
| YLS_MAX_READ_LABEL | VARCHAR(4000) | 用户的最大读标签 |
| YLS_MAX_WRITE_LABEL | VARCHAR(4000) | 用户的最大写读标签 |
| YLS_MIN_WRITE_LABEL | VARCHAR(64) | 用户的最小写读标签 |
| YLS_STRING_LABEL | VARCHAR(4000) | 标签内容 |
| YLS_LABEL_COMPONENT_TYPE | VARCHAR(12) | 组件类型 |
| YLS_LABEL_COMPONENT_NAME | VARCHAR(64) | 组件名称 |
| RLS_INFO | CLOB | 执行语句中表关联的行访问控制策略名称 |
