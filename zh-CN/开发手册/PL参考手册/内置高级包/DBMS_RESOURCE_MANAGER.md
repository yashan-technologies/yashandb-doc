DBMS_RESOURCE_MANAGER包提供了一组[资源管理](../../../数据库管理/资源管理/00资源管理)的存储过程/函数，用于创建和删除资源使用组、资源映射及相关操作。

> **Note**:
>
> - 调用DBMS_RESOURCE_MANAGER高级包下的所有子程序时，都需要以SYS用户连接数据库，否则报错。
> - 该高级包不适用于共享集群/分布式集群部署。
> - 存算一体分布式集群部署下，高级包DBMS_RESOURCE_MANAGER下的所有过程/函数只允许逐条调用，且只能在CN节点上操作。

## CREATE_CONSUMER_GROUP

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP IN VARCHAR(64),
    COMMENT        IN VARCHAR(2000) DEFAULT NULL);
```

该程序用于创建一个资源使用组。

系统包含如下默认资源使用组：

- SYS_GROUP：包括系统用户sys和系统进程，不允许修改与删除SYS_GROUP的相关配置与映射关系。
- DEFAULT_CONSUMER_GROUP：不存在资源映射关系的资源使用者默认划分至该组。

YashanDB最多支持用户自定义创建126个资源使用组。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| CONSUMER_GROUP      | 资源使用组，名称唯一且符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)，允许使用系统保留关键字，但不满足[双引号的通用规则](../../SQL参考手册/基本SQL元素/双引号)               |
| COMMENT      | 注释 |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP1',
    'GROUP FOR CPU RESOURCE1');
-- 缺省注释
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP2');
-- 注释信息为空
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP3',
    NULL);
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP4',
    COMMENT => 'GROUP FOR CPU RESOURCE4');
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP5');
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP6',
    COMMENT => NULL);
```

## CREATE_PLAN

```plsql
DBMS_RESOURCE_MANAGER.CREATE_PLAN (
    PLAN          IN   VARCHAR(64), 
    COMMENT       IN   VARCHAR(2000) DEFAULT NULL);
```

该程序用于创建资源计划，目前仅支持创建一级计划，不支持创建子计划。

系统包含如下默认资源计划：

- TOALL: 一级资源计划，包含关联内部进程和DEFAULT_CONSUMER_GROUP的指令。
- SYS_GROUP: 二级资源计划，属于TOALL的子计划，包含关联系统用户sys和系统进程的指令。

|  参数| 描述|
| --- | --- |
| PLAN | 资源计划名，名称唯一且符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)，允许使用系统保留关键字，但不满足[双引号的通用规则](../../SQL参考手册/基本SQL元素/双引号)  |
| COMMENT | 注释 |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN1',
    'PLAN FOR CPU RESOURCE1');
-- 缺省注释
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN2');
-- 注释信息为空
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN3',
    NULL);
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN4',
    COMMENT => 'PLAN FOR CPU RESOURCE4');
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN5');
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN6',
    COMMENT => NULL);
```

## CREATE_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN                    IN VARCHAR(64),
    GROUP_OR_SUBPLAN        IN VARCHAR(64),
    MGMT_P1                 IN NUMBER DEFAULT NULL,
    MAX_UTILIZATION_LIMIT   IN NUMBER DEFAULT NULL,
    UTILIZATION_LIMT        IN NUMBER DEFAULT NULL,
    SHARES                  IN NUMBER DEFAULT NULL,
    PARALLEL_SERVER_LIMIT   IN NUMBER DEFAULT NULL,
    SPA_LIMIT               IN NUMBER DEFAULT NULL,
    SPA_LIMIT_RESERVED      IN NUMBER DEFAULT NULL,
    SESSION_SPA_LIMIT       IN NUMBER DEFAULT NULL,
    EXECUTION_QUEUE_TIMEOUT IN NUMBER DEFAULT NULL
    CONCURRENCY_LIMIT       IN NUMBER  DEFAULT NULL);
```

该程序用于创建资源计划指令。

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 资源计划名 |
| GROUP_OR_SUBPLAN        | 通用参数 | 资源使用组 |
| MGMT_P1                 | CPU资源管理参数 | CPU共享模式下的使用份额，须为[1,100]中的整数，默认值NULL表示最小值1 |
| MAX_UTILIZATION_LIMIT   | CPU资源管理参数 | CPU最大使用上限，须为[1,100]中的整数，默认值NULL表示最大值100 |
| UTILIZATION_LIMIT       | CPU资源管理参数 | 与字段MAX_UTILIZATION_LIMIT相同表现 |
| SHARES                  | CPU资源管理参数 | 与字段MGMT_P1相同表现 |
| PARALLEL_SERVER_LIMIT   | 并行执行资源管理参数 | 资源使用组可以使用的最大并行资源百分比，须为[0,100]中的整数，默认值NULL表示最大值100 |
| SPA_LIMIT               | 内存管理参数 | 资源使用组占用用户内存的上限百分比（单位：%），公共内存使用不在限制内。须为[1,100]的整数，默认值NULL表示最大值100 |
| SPA_LIMIT_RESERVED      | 内存管理参数 | 资源使用组内用户内存预留一定比例作为各会话私有部分（单位：%）。预留范围内的用户内存，会话无需向资源使用组申请。须为[0,100]的整数，默认值NULL表示最小值0 |
| SESSION_SPA_LIMIT       | 内存管理参数 | 会话占用资源组可用内存的上限百分比（单位：%）。须为[1,100]的正整数，默认值NULL表示最大值100 |
| EXECUTION_QUEUE_TIMEOUT | 执行调度管理参数 | 当资源不足时在队列中等待的超时时间，单位是s。须为[-1, 4294967295]的整数，-1表示无限超时，默认值NULL等同最小值-1 |
| CONCURRENCY_LIMIT | 执行调度管理参数 | 资源使用组内允许同时执行资源密集型SQL的数量，需为[0, 2147483647]中的整数，NULL表示不限制 |

使用说明：

- 当同时设置MGMT_P1和SHARES参数时，以shares参数为准。
- 当同时设置MAX_UTILIZATION_LIMIT和UTILIZATION_LIMIT参数时，以UTILIZATION_LIMIT参数为准。
- 最大CPU使用率计算公式为`MAX_UTILIZATION_LIMIT * CPU个数`，例如某用户映射的资源使用组中MAX_UTILIZATION_LIMIT为10，环境中各节点的CPU个数为2，则该用户在每个节点的最大CPU使用率为20%。
- 为保障SYS_GROUP具备足够的资源可用，当该组的共享资源（MGMT_P1）较少或较多时，系统会在用户输入时做动态调整。若SYS_GROUP的MGMT_P1全局占比小于40%或大于60%时，系统会将SYS_GROUP的MGMT_P1动态调整为剩余资源组的MGMT_P1总和，此时SYS_GROUP的MGMT_P1值可能会超过100（可查看视图DBA_RSRC_PLAN_DIRECTIVES的MGMT_P1字段，全局占比计算公式：`SYS_GROUP的MGMT_P1 / 所有资源使用组的MGMT_P1总和)`）。
- 较低版本的数据库升级后，如果原本已将MGMT_P1或MAX_UTILIZATION_LIMIT参数配置为0，实际表现与当前版本中将其对应配置为100相同。

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 不配置可选参数
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2',
    10,
    20,
    30,
    40,
    50);
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN3',
    GROUP_OR_SUBPLAN => 'RESGROUP3',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20,
    UTILIZATION_LIMIT => 30,
    SHARES => 40,
    PARALLEL_SERVER_LIMIT => 50);
-- 只配置MGMT_P1和MAX_UTILIZATION_LIMIT参数
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20);
-- 只配置PARALL_SERVER_LIMIT参数
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5',
    PARALLEL_SERVER_LIMIT => 50);
-- 指定参数名后的参数只能继续指定参数名，以下示例会报错
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN6',
    GROUP_OR_SUBPLAN => 'RESGROUP6',
    10);

YAS-04253 PL/SQL compiling errors:
[1:104] YAS-00003 invalid parameter, reason: param => input order error
```

## SET_CONSUMER_GROUP_MAPPING

```plsql
DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE      IN VARCHAR(64),
    VALUE          IN VARCHAR(64),
    CONSUMER_GROUP IN VARCHAR(64) DEFAULT NULL);
```

该程序用于把资源使用者映射到资源使用组，无法修改SYS用户与SYS_GROUP组的映射关系。

如果不存在映射，将创建到对应资源组的映射；如果已经存在映射，则尝试更新映射到新的资源组。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| ATTRIBUTE       | 映射属性<br/>仅支持USER，即资源使用者以用户为维度 |
| VALUE          | 待映射的用户名，必须为已存在的用户            |
| CONSUMER_GROUP | 待映射的资源使用组，为NULL时表示删除对应映射                                    |

如果映射的资源使用组没有对应的计划指令，则表现与DEFAULT_CONSUMER_GROUP保持一致；当创建新的计划指令时，需要用户重新登录会话才能生效。

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 缺省CONSUMER_GROUP参数，效果等同于DELETE_CONSUMER_GROUP_MAPPING
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1');
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1',
    'RESGROUP1');
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE => 'USER',
    VALUE => 'SALES2',
    CONSUMER_GROUP => 'RESGROUP2');
```

## UPDATE_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN                    IN VARCHAR(64),
    GROUP_OR_SUBPLAN        IN VARCHAR(64),
    MGMT_P1                 IN NUMBER DEFAULT NULL,
    MAX_UTILIZATION_LIMIT   IN NUMBER DEFAULT NULL,
    UTILIZATION_LIMT        IN NUMBER DEFAULT NULL,
    SHARES                  IN NUMBER DEFAULT NULL,
    PARALLEL_SERVER_LIMIT   IN NUMBER DEFAULT NULL,
    SPA_LIMIT               IN NUMBER DEFAULT NULL,
    SPA_LIMIT_RESERVED      IN NUMBER DEFAULT NULL,
    SESSION_SPA_LIMIT       IN NUMBER DEFAULT NULL,
    EXECUTION_QUEUE_TIMEOUT IN NUMBER DEFAULT NULL
    CONCURRENCY_LIMIT       IN NUMBER  DEFAULT NULL);
```

该程序用于更新资源计划指令。

|  参数| 参数分类| 描述|
| :---------------------- | :------------- | :----------------------------------------------------------- |
| PLAN                    | 通用参数 | 资源计划名 |
| GROUP_OR_SUBPLAN        | 通用参数 | 资源使用组 |
| MGMT_P1                 | CPU资源管理参数 | CPU共享模式下的使用份额，须为[1,100]中的整数 |
| MAX_UTILIZATION_LIMIT   | CPU资源管理参数 | CPU最大使用上限，须为[1,100]中的整数 |
| UTILIZATION_LIMIT       | CPU资源管理参数 | 与字段MAX_UTILIZATION_LIMIT相同表现 |
| SHARES                  | CPU资源管理参数 | 与字段MGMT_P1相同表现 |
| PARALLEL_SERVER_LIMIT   | 并行执行资源管理参数 | 资源使用组可以使用的最大并行资源百分比，须为[0,100]中的整数 |
| SPA_LIMIT               | 内存管理参数 | 资源使用组占用用户内存的上限百分比（单位：%），公共内存使用不在限制内。须为[1,100]的整数 |
| SPA_LIMIT_RESERVED      | 内存管理参数 | 资源使用组内用户内存预留一定比例作为各会话私有部分（单位：%）。预留范围内的用户内存，会话无需向资源使用组申请。须为[0,100]的整数 |
| SESSION_SPA_LIMIT       | 内存管理参数 | 会话占用资源组可用内存的上限百分比（单位：%）。须为[1,100]的整数 |
| EXECUTION_QUEUE_TIMEOUT | 执行调度管理参数 | 当资源不足时在队列中等待的超时时间，单位是s。须为[-1, 4294967295]的整数，-1表示无限超时，默认值NULL等同最小值-1 |
| CONCURRENCY_LIMIT | 执行调度管理参数 | 资源使用组内允许同时执行资源密集型SQL的数量，需为[0, 2147483647]中的整数，NULL表示不限制 |

使用说明：

- 可选参数不设置值或输入为NULL时，不对该参数做任何处理。

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 不配置可选参数
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2',
    10,
    20,
    30,
    40,
    50);
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN3',
    GROUP_OR_SUBPLAN => 'RESGROUP3',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20,
    UTILIZATION_LIMIT => 30,
    SHARES => 40,
    PARALLEL_SERVER_LIMIT => 50);
-- 只配置MGMT_P1和MAX_UTILIZATION_LIMIT参数
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20);
-- 只配置PARALL_SERVER_LIMIT参数
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5',
    PARALLEL_SERVER_LIMIT => 50);
```

## DELETE_CONSUMER_GROUP_MAPPING

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE IN VARCHAR(64),
    VALUE     IN VARCHAR(64));
```

该程序用于删除资源使用者到资源使用组的映射，无法删除SYS用户与SYS_GROUP组的映射关系。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| ATTRIBUTE | 映射属性，仅支持USER |
| VALUE     | 映射关系中的用户名，不能为SYS用户            |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1');
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE => 'USER',
    VALUE => 'SALES2');
```

## DELETE_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN             IN VARCHAR(64),
    GROUP_OR_SUBPLAN IN VARCHAR(64));
```

该程序用于删除资源计划指令。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| PLAN       | 资源计划名 |
| GROUP_OR_SUBPLAN | 资源使用组 |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN3',
    'RESGROUP3');    
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5');
```

## DELETE_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN (
    PLAN IN VARCHAR(64));
```

该程序用于删除资源计划，无法删除正在生效的资源计划及内置资源计划TOALL和SYS_GROUP。

|  参数| 描述|
| :--- | :--- |
| PLAN | 资源计划名 |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN3');
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN5');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN6');
```

## DELETE_CONSUMER_GROUP

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP IN VARCHAR(64));
```

该程序用于删除一个资源使用组。

|  参数| 描述|
| :------------- | :--------- |
| CONSUMER_GROUP | 资源使用组 |

示例（单机部署、存算一体分布式集群部署）

```plsql
-- 忽略参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP3');
-- 指定参数名
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP5');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP6');
```
