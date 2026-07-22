SA_LABEL_ADMIN包提供了一组内置子程序，用于创建、删除LBAC标签策略中的标签。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE_LABEL

```plsql
SA_LABEL_ADMIN.CREATE_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER,
    label_value IN VARCHAR,
    data_label IN BOOLEAN DEFAULT TRUE);
```
此存储过程用于为已有的LBAC标签策略创建标签。

> **Note**:
>
> 标签会占用一定的[会话栈内存、堆内存](../../../概念手册/实例架构/内存体系.md)，如需创建大量的标签，建议合理配置相应的内存参数WORK_AREA_POOL_SIZE、WORK_AREA_STACK_SIZE以及WORK_AREA_HEAP_SIZE的值以便预分配更多的内存空间。

|  参数| 描述|
| :--- |:-----------------------------|
| policy_name        | 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| label_tag| 标签的标识值，全库唯一，取值区间为[1,99999999]<br/>若通过[CHAR_TO_LABEL](../../SQL参考手册/内置函数/CHAR_TO_LABEL.md)函数自动创建标签，其标签标识值区间为[1000000000,4000000000] |
| label_value| 标签内容，由等级、范围的短名称组成<br />同一类型的短名称间使用逗号（`,`）隔开，不同类型的短名称间使用冒号（`:`）隔开，格式为：`等级1短名,…,等级n短名:范围1短名,…,范围m短名` |
| data_label| 标识该标签是否为数据标签，默认为TRUE              |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');
END;
/

PL/SQL Succeed.
```

## DROP_LABEL

```plsql
SA_LABEL_ADMIN.DROP_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER);
```
此存储过程用于从已有的LBAC标签策略中删除标签。

> **Caution**:
>
> 删除标签前请确保目标标签未被任何数据行使用（可通过[DBA_SA_TABLE_POLICIES](../../../参考手册/系统视图/DBA视图/DBA_SA_TABLE_POLICIES)视图查看目标策略已应用于哪些表，再查看此类表的策略列值是否存在目标标签值），否则标签删除后对应的数据行将仅允许sys用户读写。

|  参数| 描述|
| :--- | :---- |
| policy_name        | 待操作的目标LBAC标签策略的名称，可通过[DBA_SA_POLICIES视图](../../../参考手册/系统视图/DBA视图/DBA_SA_POLICIES.md)的POLICY_NAME字段获取策略名称 |
| label_tag| 目标策略中待删除的标签的标识值，可通过[DBA_SA_LABELS视图](../../../参考手册/系统视图/DBA视图/DBA_SA_LABELS.md)的LABEL_TAG字段获取标签的标识值 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
  SA_LABEL_ADMIN.DROP_LABEL ('lbac_policy', 1001);
END;
/

PL/SQL Succeed.
```
