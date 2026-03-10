YLS_ENFORCEMENT包提供了一组内置子程序，用于设置基于标签的行访问控制的开关状态。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## ENABLE\_YLS

```plsql
YLS_ENFORCEMENT.ENABLE_YLS();
```
此存储过程用于打开行访问控制开关。

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
  YLS_ENFORCEMENT.ENABLE_YLS();
END;
/

```

## DISABLE\_YLS

```plsql
YLS_ENFORCEMENT.DISABLE_YLS();
```
此存储过程用于关闭行访问控制开关。

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
  YLS_ENFORCEMENT.DISABLE_YLS();
END;
/

```
