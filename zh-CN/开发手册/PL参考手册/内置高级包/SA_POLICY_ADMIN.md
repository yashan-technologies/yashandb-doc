SA_POLICY_ADMIN包提供了一组内置子程序，用于设置、移除表与LBAC标签策略的关联关系。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## APPLY_TABLE_POLICY

```plsql
SA_POLICY_ADMIN.APPLY_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    table_options IN VARCHAR DEFAULT NULL,
    label_function IN VARCHAR DEFAULT NULL,
    predicate IN VARCHAR DEFAULT NULL);
```
此存储过程用于将LBAC标签策略应用至目标表，同时会在目标表中新增一列记录每行数据的标签（列名将采用目标LBAC标签策略中指定的column_name）。

策略应用后，相应的读写控制将立即生效。

> **Note**:
>
> 建议在建表初期**尚未插入任何数据前**，先完成行访问控制相关配置。若在应用策略前目标表中已存在数据，应用策略并不会自动为历史数据分配标签值。此类无标签的数据行仅允许sys用户读写。如需允许其他用户访问这些行，必须通过sys用户执行UPDATE语句为其设定标签值。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待应用的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| schema_name| 目标表的所属用户 |
| table_name| 目标表的名称 |
| table_options| [强制控制选项](SA_SYSDBA.md#defaultoptions)，多个名称间使用逗号（`,`）隔开，默认值NULL</br>强制控制选项清查阅[SA_SYSDBA中的相关说明</br>参数值若为NULL，则值为[SA_SYSDBA](SA_SYSDBA)中CREATE_POLICY时指定的default_options值；若default_options值也为NULL则table_options为“READ_CONTROL, WRITE_CONTROL” |
| label_function| 保留参数 |
| predicate| 保留参数 |

示例（HEAP表）

```plsql
BEGIN
    SA_POLICY_ADMIN.APPLY_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', 'READ_CONTROL');
END;
/

PL/SQL Succeed.
```

## REMOVE_TABLE_POLICY

```plsql
SA_POLICY_ADMIN.REMOVE_TABLE_POLICY (
    policy_name IN VARCHAR,
    schema_name IN VARCHAR,
    table_name IN VARCHAR,
    drop_column IN BOOLEAN DEFAULT FALSE);
```
此存储过程用于解除表与指定LBAC标签策略的关联关系。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC标签策略的名称 |
| schema_name| 用户名 |
| table_name| 表名 |
| drop_column | 是否删除标签列，默认值FALSE |

示例（HEAP表）

```plsql
BEGIN
    SA_POLICY_ADMIN.REMOVE_TABLE_POLICY ('lbac_policy', 'sales', 'sales_info', true);
END;
/

PL/SQL Succeed.
```
