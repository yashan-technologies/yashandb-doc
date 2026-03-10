SA_SYSDBA包提供了一组内置子程序，用于创建、删除LBAC安全策略。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE\_POLICY

```plsql
SA_SYSDBA.CREATE_POLICY (
    policy_name IN VARCHAR,
    column_name IN VARCHAR DEFAULT NULL,
    default_options IN VARCHAR DEFAULT NULL);
```
此存储过程用于创建LBAC安全策略。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| column_name| 标签列名，使用默认值NULL时对应的column_name值为policy_name + "_COL" |
| default_options| 强制控制选项名组合，默认值NULL |

<span id="defaultoptions" name="defaultoptions" class="yaslink"></span>
强制控制选项说明：

|  名称| 值| 描述|
| :--- | :---- | :---- |
| READ_CONTROL | 1 | 控制读 |
| INSERT_CONTROL | 2 | 控制插入 |
| UPDATE_CONTROL | 4 | 控制更新 |
| DELETE_CONTROL | 8 | 控制删除 |
| WRITE_CONTROL | 14 | 控制写，INSERT_CONTROL、UPDATE_CONTROL、DELETE_CONTROL的组合 | 

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_SYSDBA.CREATE_POLICY ('lbac_policy', 'policy_column', 'READ_CONTROL');
END;
/

```

## DROP\_POLICY

```plsql
SA_SYSDBA.DROP_POLICY (
    policy_name IN VARCHAR,
    drop_column BOOLEAN DEFAULT FALSE);
```
此存储过程用于删除LBAC安全策略。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| drop_column| 是否删除表的标签列，默认值FALSE |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_SYSDBA.DROP_POLICY ('lbac_policy', true);
END;
/

```
