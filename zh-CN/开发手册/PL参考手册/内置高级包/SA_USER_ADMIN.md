SA_USER_ADMIN包提供了一组内置子程序，用于设置、移除用户与LBAC标签策略的关联关系。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## SET_USER_LABELS

```plsql
SA_USER_ADMIN.SET_USER_LABELS (
    policy_name     IN VARCHAR,
    user_name       IN VARCHAR,
    max_read_label  IN VARCHAR,
    max_write_label IN VARCHAR DEFAULT NULL,
    min_write_label IN VARCHAR DEFAULT NULL,
    def_label       IN VARCHAR DEFAULT NULL,
    row_label       IN VARCHAR DEFAULT NULL);
```
此存储过程用于为用户关联LBAC安全标签，从而建立用户与LBAC标签策略的关联关系。

执行成功后，用户的LBAC标签策略信息可通过[DBA_SA_USER_LABELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_USER_LABELS)获取。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全标签所属策略的名称 |
| user_name| 目标用户的名称 |
| max_read_label| 设置目标用户的最大读标签，即该用户被赋予的最高级别的读访问权限，需使用标签内容（label_value）进行指定，可通过[DBA_SA_LABELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_LABELS.md)的LABEL字段获取标签内容 |
| max_write_label| 设置目标用户的最大写标签，即该用户被赋予的最高级别的写访问权限，需使用标签内容（label_value）进行指定，可通过[DBA_SA_LABELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_LABELS.md)的LABEL字段获取标签内容<br />设置时需满足如下规则：<br/>* 等级：max_write_label的等级必须与max_read_label的等级相等<br/>* 范围：max_write_label的范围必须是max_read_label范围的子集<br/><br />若该参数为空，则默认复用max_read_label的值 |
| min_write_label| 设置目标用户的最小写标签，即该用户被赋予的最低级别的写访问权限，需使用等级短名称进行指定，可通过[DBA_SA_LEVELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_LEVELS.md)的SHORT_NAME字段获取等级短名称<br />若该参数为空，则默认为该策略中的最小等级 |
| def_label| 默认的会话标签，需使用范围短名称进行指定，应设置为max_read_label范围的子集，可通过[DBA_SA_COMPARTMENTS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_COMPARTMENTS.md)的SHORT_NAME字段获取范围短名称<br />若该参数为空，则默认复用max_read_label的范围 |
| row_label| 行标签，包含的等级的值不能大于def_label中的等级的值，包含的范围内容是def_label范围的子集 |

在DBA_SA_USER_LABELS视图中，默认的会话标签（def_label参数值）将以DEFAULT_READ_LABEL字段和DEFAULT_WRITE_LABEL字段进行展现。

- DEFAULT_READ_LABEL：用户默认的读标签，其等级值取def_label的等级值、其范围取max_read_label和def_label的范围内容交集。

- DEFAULT_WRITE_LABEL：用户默认的写标签，标签中的等级值为def_label标签的等级制，标签中的范围内容是max_write_label的范围内容和def_label的范围内容的子集。

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_USER_ADMIN.SET_USER_LABELS ('lbac_policy', 'sales', 'GENERAL:MNG');
END;
/

PL/SQL Succeed.
```

## DROP_USER_ACCESS

```plsql
SA_USER_ADMIN.DROP_USER_ACCESS (
    policy_name IN VARCHAR,
    user_name IN VARCHAR);
```
此存储过程用于解除用户与指定LBAC标签策略的关联关系。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC标签策略 |
| user_name| 用户名 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_USER_ADMIN.DROP_USER_ACCESS ('lbac_policy', 'sales');
END;
/

PL/SQL Succeed.
```
