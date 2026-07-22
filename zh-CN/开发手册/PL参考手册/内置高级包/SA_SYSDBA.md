SA_SYSDBA包提供了一组内置子程序，用于创建、删除LBAC标签策略。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE_POLICY

```plsql
SA_SYSDBA.CREATE_POLICY (
    policy_name IN VARCHAR,
    column_name IN VARCHAR DEFAULT NULL,
    default_options IN VARCHAR DEFAULT NULL);
```
此存储过程用于创建LBAC标签策略。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC标签策略的名称，全库唯一，长度不得超过64字节 |
| column_name| 标签列的名称，需符合列名相应[规范](../../SQL参考手册/基本SQL元素/标识符.md)且不同策略不允许使用同一个列名。使用默认值NULL时，默认为`{policy_name}_COL`<br />当该策略应用于目标表时，系统将自动在目标表中新增一列（列名为该参数值），用于逐行记录每行数据的标签 |
| default_options| 默认的强制控制选项，多个名称间使用逗号（`,`）隔开，默认为NULL<br />若应用该策略时不单独指定强制控制选项，则默认采用该参数值 |

<span id="defaultoptions" name="defaultoptions"></span>
强制控制选项说明：

|  名称| 值| 描述|
| :--- | :---- | :---- |
| READ_CONTROL | 1 | 控制读 |
| INSERT_CONTROL | 2 | 控制插入 |
| UPDATE_CONTROL | 4 | 控制更新 |
| DELETE_CONTROL | 8 | 控制删除 |
| WRITE_CONTROL | 14 | 控制写，包括INSERT_CONTROL、UPDATE_CONTROL和DELETE_CONTROL |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_SYSDBA.CREATE_POLICY ('lbac_policy', 'policy_column', 'READ_CONTROL');
END;
/

PL/SQL Succeed.
```

## DROP_POLICY

```plsql
SA_SYSDBA.DROP_POLICY (
    policy_name IN VARCHAR,
    drop_column BOOLEAN DEFAULT FALSE);
```
此存储过程用于删除LBAC标签策略。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待删除的LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| drop_column| 是否同时删除已关联该策略的表中的标签列，默认为FALSE，表示不删除标签列 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_SYSDBA.DROP_POLICY ('lbac_policy', true);
END;
/

PL/SQL Succeed.
```
