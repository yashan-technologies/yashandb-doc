SA_LABEL_ADMIN包提供了一组内置子程序，用于创建、修改、删除标签。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## CREATE\_LABEL

```plsql
SA_LABEL_ADMIN.CREATE_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER,
    label_value IN VARCHAR,
    data_label IN BOOLEAN DEFAULT TRUE);
```
此存储过程用于为LBAC安全策略创建安全标签。创建的标签占用一定内存，若创建大量的标签，建议修改相应POOL的配置参数，预分配更多的内存空间。

|  参数| 描述|
| :--- |:-----------------------------|
| policy_name| LBAC安全标签所属策略的名称              |
| label_tag| 标签值<br/>* 手动创建时，取值区间为[1,99999999] <br/>*  自动创建时，取值区间为[1000000000,4000000000]|
| label_value| 标签内容                         |
| data_label| 是否为数据标签，默认值TRUE              |

标签格式：

```txt
级别名 + ':' + [范围名, ...]
```

示例（单机部署、共享集群）

```plsql
BEGIN
    SA_LABEL_ADMIN.CREATE_LABEL ('lbac_policy', 1001, 'GENERAL:MNG');
END;
/

```

## DROP\_LABEL

```plsql
SA_LABEL_ADMIN.DROP_LABEL (
    policy_name IN VARCHAR,
    label_tag IN BINARY_INTEGER);
```
此存储过程用于删除LBAC安全策略的标签。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略的名称 |
| label_tag| 标签值 |

示例（单机部署、共享集群）

```plsql
BEGIN
  SA_LABEL_ADMIN.DROP_LABEL ('lbac_policy', 1001);
END;
/

```
