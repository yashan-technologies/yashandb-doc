SA_COMPONENTS包提供了一组内置子程序，用于创建、修改、删除策略组件，策略组件包括级别和范围。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE\_LEVEL

```plsql
SA_COMPONENTS.CREATE_LEVEL (
    policy_name IN VARCHAR,
    level_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
此存储过程用于为LBAC安全策略创建级别。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| level_num| 级别值，取值区间为[0,9999] |
| short_name| 短名称|
| long_name| 长名称|

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.CREATE_LEVEL ('lbac_policy', 10, 'GENERAL', 'GENERAL');
END;
/

```

## DROP\_LEVEL

```plsql
SA_COMPONENTS.DROP_LEVEL (
  policy_name IN VARCHAR,
  level_num IN INTEGER);
```
此存储过程用于删除LBAC安全策略的级别。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| level_num| 级别值 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.DROP_LEVEL ('lbac_policy', 10);
END;
/

```

## CREATE\_COMPARTMENT

```plsql
SA_COMPONENTS.CREATE_COMPARTMENT (
    policy_name IN VARCHAR,
    comp_num IN INTEGER,
    short_name IN VARCHAR,
    long_name IN VARCHAR);
```
此存储过程用于为LBAC安全策略创建范围。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| comp_num| 范围值，取值区间为[0,9999] |
| short_name| 短名称 |
| long_name| 长名称 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.CREATE_COMPARTMENT ('lbac_policy', 10, 'MNG', 'MNG');
END;
/

```

## DROP\_COMPARTMENT

```plsql
SA_COMPONENTS.DROP_COMPARTMENT (
  policy_name IN VARCHAR,
  comp_num IN INTEGER);
```
此存储过程用于删除LBAC安全策略的范围。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| comp_num| 范围值 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_COMPONENTS.DROP_COMPARTMENT ('lbac_policy', 10);
END;
/

```
