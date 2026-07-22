SA_COMPONENTS包提供了一组内置子程序，用于创建、删除LBAC标签策略的组件，策略组件包括等级和范围。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE_COMPARTMENT

```plsql
SA_COMPONENTS.CREATE_COMPARTMENT (
    policy_name IN VARCHAR,
    comp_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
此存储过程用于为已有的LBAC标签策略创建范围。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| comp_num| 范围的编号，同策略内唯一，取值区间为[0,9999]<br />该值将用于对受保护数据进行分类 |
| short_name| 范围的短名称，长度不得超过64个字符 |
| long_name| 范围的长名称，长度不得超过64个字符 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.CREATE_COMPARTMENT ('lbac_policy', 10, 'MNG', 'MNG');
END;
/

PL/SQL Succeed.
```

## CREATE_LEVEL

```plsql
SA_COMPONENTS.CREATE_LEVEL (
    policy_name IN VARCHAR,
    level_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
此存储过程用于为已有的LBAC标签策略创建等级。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| level_num| 等级的编号，同策略内唯一，取值区间为[0,9999]<br />该值将用于确定受保护数据的敏感度等级，编号越大表示敏感度越高 |
| short_name| 等级的短名称，长度不得超过64个字符 |
| long_name| 等级的长名称，长度不得超过64个字符 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.CREATE_LEVEL ('lbac_policy', 10, 'GENERAL', 'GENERAL');
END;
/

PL/SQL Succeed.
```

## DROP_COMPARTMENT

```plsql
SA_COMPONENTS.DROP_COMPARTMENT (
  policy_name IN VARCHAR,
  comp_num IN INTEGER);
```
此存储过程用于从已有的LBAC标签策略中删除范围。

若该范围已被应用于标签，将无法直接删除。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| comp_num| 目标策略中待删除的范围的编号，可通过[DBA_SA_COMPARTMENTS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_COMPARTMENTS.md)的COMP_NUM字段获取范围编号 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.DROP_COMPARTMENT ('lbac_policy', 10);
END;
/

PL/SQL Succeed.
```


## DROP_LEVEL

```plsql
SA_COMPONENTS.DROP_LEVEL (
  policy_name IN VARCHAR,
  level_num IN INTEGER);
```
此存储过程用于从已有的LBAC标签策略中删除等级。

若该等级已被应用于标签，将无法直接删除。

|  参数| 描述|
| :--- | :---- |
| policy_name| 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| level_num| 目标策略中待删除的等级的编号，可通过[DBA_SA_LEVELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_LEVELS.md)的LEVEL_NUM字段获取等级编号 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.DROP_LEVEL ('lbac_policy', 10);
END;
/

PL/SQL Succeed.
```