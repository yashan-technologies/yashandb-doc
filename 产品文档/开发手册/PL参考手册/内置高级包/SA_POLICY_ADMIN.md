SA_POLICY_ADMIN包提供了一组内置子程序，用于设置、移除表与LBAC安全策略的关联关系。

- 该高级包仅适用于HEAP表。

- 执行该高级包的用户需具备LBAC_DBA角色。

## APPLY\_TABLE\_POLICY

```plsql
SA_POLICY_ADMIN.APPLY_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    table_options IN VARCHAR DEFAULT NULL,
    label_function IN VARCHAR DEFAULT NULL,
    predicate IN VARCHAR DEFAULT NULL);
```
此存储过程用于为表关联LBAC安全策略。

| 参数 | 描述 |
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| schema_name| 用户名 |
| table_name| 表名 |
| table_options| 强制控制选项名，默认值NULL</br>强制控制选项清查阅[SA_SYSDBA](SA_SYSDBA.html#defaultoptions)中的相关说明</br>参数值若为NULL，则值为[SA_SYSDBA](SA_SYSDBA)中CREATE_POLICY时指定的default_options值；若default_options值也为NULL则table_options为“READ_CONTROL, WRITE_CONTROL”|
| label_function| 保留参数 |
| predicate| 保留参数 |

示例（单机HEAP表、共享集群）

```plsql
BEGIN
    SA_POLICY_ADMIN.APPLY_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', 'READ_CONTROL');
END;
/

```

## REMOVE\_TABLE\_POLICY

```plsql
SA_POLICY_ADMIN.REMOVE_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    drop_column IN BOOLEAN DEFAULT FALSE);
```
此存储过程用于解除表与指定LBAC安全策略的关联关系。

| 参数 | 描述 |
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| schema_name| 用户名 |
| table_name| 表名 |
| drop_column | 是否删除标签列，默认值FALSE |

示例（单机HEAP表、共享集群）

```plsql
BEGIN
    SA_POLICY_ADMIN.REMOVE_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', true);
END;
/

```