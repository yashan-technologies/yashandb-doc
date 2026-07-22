DBMS_RESOURCE_MANAGER包提供了一组[资源管理](../../../数据库管理/资源管理/00资源管理)的存储过程/函数，用于创建和删除资源计划、资源指令、资源映射及相关操作。

> **Note**:
>
> - 仅SYS用户可调用该高级包的存储过程/函数。
> - 存算一体分布式集群部署下，该高级包的存储过程/函数只允许在CN节点上逐条调用。

该高级包的存储过程/函数适用于以下对象：

| 作用域 | 相关子程序 | 适用对象 |
|--------------------|--------------------------------------|--------------------|
| 容器数据库全局资源管理 | [CREATE_CDB_PLAN](#CREATE_CDB_PLAN)<br />[CREATE_CDB_PLAN_DIRECTIVE](#CREATE_CDB_PLAN_DIRECTIVE)<br />[CREATE_CDB_PROFILE_DIRECTIVE](#CREATE_CDB_PROFILE_DIRECTIVE)<br />[SET_PDB_PROFILE](#SET_PDB_PROFILE)<br />[UPDATE_CDB_DEFAULT_DIRECTIVE](#UPDATE_CDB_DEFAULT_DIRECTIVE)<br />[UPDATE_CDB_PLAN_DIRECTIVE](#UPDATE_CDB_PLAN_DIRECTIVE)<br />[UPDATE_CDB_PROFILE_DIRECTIVE](#UPDATE_CDB_PROFILE_DIRECTIVE)<br />[DELETE_CDB_PLAN_DIRECTIVE](#DELETE_CDB_PLAN_DIRECTIVE)<br />[DELETE_CDB_PROFILE_DIRECTIVE](#DELETE_CDB_PROFILE_DIRECTIVE)<br />[DELETE_CDB_PLAN](#DELETE_CDB_PLAN) | 单机部署的容器数据库（需在根容器上执行；若为单机主备部署则需在主根容器上执行） |
| * 非容器数据库资源管理<br/><br/>* 容器数据库本地资源管理 | [CREATE_CONSUMER_GROUP](#CREATE_CONSUMER_GROUP)<br />[CREATE_PLAN](#CREATE_PLAN)<br />[CREATE_PLAN_DIRECTIVE](#CREATE_PLAN_DIRECTIVE)<br />[SET_CONSUMER_GROUP_MAPPING](#SET_CONSUMER_GROUP_MAPPING)<br />[UPDATE_PLAN_DIRECTIVE](#UPDATE_PLAN_DIRECTIVE)<br />[DELETE_CONSUMER_GROUP_MAPPING](#DELETE_CONSUMER_GROUP_MAPPING)<br />[DELETE_PLAN_DIRECTIVE](#DELETE_PLAN_DIRECTIVE)<br />[DELETE_PLAN](#DELETE_PLAN)<br />[DELETE_CONSUMER_GROUP](#DELETE_CONSUMER_GROUP) | * 非容器数据库<br/><br/>* PDB（不含种子容器） |

<span id="CREATE_CDB_PLAN" name="CREATE_CDB_PLAN"></span>

## CREATE_CDB_PLAN

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
    PLAN             IN    VARCHAR(64),
    COMMENT          IN    VARCHAR(2000) DEFAULT NULL);
```

该程序用于创建CDB资源计划，目前仅支持创建一级计划，不支持创建子计划。

在容器数据库中，系统包含默认资源计划DEFAULT_CDB_PLAN，其指令各项资源管理参数均为默认值且默认关联所有PDB。



|  参数| 描述|
| --- | --- |
| PLAN | CDB资源计划名，名称唯一且符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)，允许使用系统保留关键字，但不满足[双引号的通用规则](../../SQL参考手册/基本SQL元素/双引号)  |
| COMMENT | 注释 |



示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
   PLAN => 'RES_CDB_PLAN',
   COMMENT => 'RESOURCE PLAN FOR CDB MANAGEMENT');
```

<span id="CREATE_CDB_PLAN_DIRECTIVE" name="CREATE_CDB_PLAN_DIRECTIVE"></span>

## CREATE_CDB_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

该程序用于创建CDB资源计划指令，并同时将该指令与指定PDB进行关联。  

资源计划指令只能与一个PDB进行关联，如需为多个PDB配置相同的资源配额可使用[CDB资源模板指令](#CREATE_CDB_PROFILE_DIRECTIVE)。  

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 现有CDB资源计划的名称 |
| PLUGGABLE_DATABASE    | 通用参数              | PDB名称                    |
| COMMENT               | 通用参数              | 注释信息                     |
| SHARES                | CPU/IOPS资源管理参数 | CPU/IOPS的使用份额（单位：%），取值范围是[1,100]中的整数或NULL值。默认值NULL表示最小值1。 |
| UTILIZATION_LIMIT     | CPU/IOPS资源管理参数        | CPU/IOPS的使用最大上限（单位：%），取值范围是[1,100]中的整数或NULL值。默认值NULL表示最大值100。 |
| PARALLEL_SERVER_LIMIT | 并行执行资源管理参数  | 可用的最大并行资源百分比（单位：%），取值范围是[0,100]中的整数或NULL值。默认值NULL表示最大值100。 |
| MEMORY_LIMIT          | 内存管理参数          | 可用内存的上限百分比（单位：%），取值范围是[MEMORY_MIN,100]中的整数或NULL值，该值允许超配（即所有容器的MEMORY_LIMIT之和大于100）。默认值NULL表示最大值100。 |
| MEMORY_MIN            | 内存管理参数          | 可用内存的下限百分比（单位：%），取值范围是[1,MEMORY_LIMIT]中的整数或NULL值。默认值NULL表示最小值1。  |



使用说明：

- CPU资源上限值计算公式为`UTILIZATION_LIMIT * CPU个数`，例如某用户映射的资源使用组中MAX_UTILIZATION_LIMIT为10，环境中各节点的CPU个数为2，则该用户在每个节点的最大CPU使用率为20%。

- IOPS资源项及其上限值计算公式分别为：
  
    - 每秒读写字节数上限：计算公式为`UTILIZATION_LIMIT * MAX_BPS`，MAX_BPS参数默认为0，即不对相应资源进行限制。

    - 每秒IO操作次数上限：计算公式为`UTILIZATION_LIMIT * MAX_IOPS`，MAX_IOPS参数默认为0，即不对相应资源进行限制。

- PDB的内存资源上下限值计算公式为CDB_MAX_MEMORY_SIZE乘以对应百分比，PDB的所需内存量则以内存相关参数配置值为标准（而非当前实际使用量），包括DATA_BUFFER_SIZE、SHARE_POOL_SIZE、VM_BUFFER_SIZE等。

    - 创建新的PDB、启动现有PDB时，要求PDB所需内存量在[下限值`CDB_MAX_MEMORY_SIZE * MEMORY_MIN`,上限值`CDB_MAX_MEMORY_SIZE * MEMORY_LIMIT`]区间内且CDB内存充足才能正常执行。

    - 若PDB运行过程中调低MEMORY_LIMIT使上限值小于PDB的使用量配置，会自动生成该PDB的内存参数推荐值，不影响PDB此次运行但此次运行期间将无法在线扩展PDB的内存配置。若新MEMORY_LIMIT设置合理，重启PDB后新的内存参数推荐值会生效，并按新MEMORY_LIMIT值计算内存上限。若新MEMORY_LIMIT值设置过小，可能会导致参数推荐值生成失败（运行日志中会记录WARN日志），进而导致后续无法重启，此时需重新调大MEMORY_LIMIT值。
    
    - 若PDB运行过程中调高下限值使其大于PDB的使用量配置，会自动生成该PDB的内存参数推荐值，不影响PDB此次运行。重启PDB后新的内存参数推荐值会生效，并会采用最新的下限值配置。
    



示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
   PLAN => 'RES_CDB_PLAN',
   PLUGGABLE_DATABASE => 'pdb1',
   COMMENT => 'RESOURCE PLAN DIRECTIVE FOR PLUGGABLE DATABASE pdb1',
   SHARES => 25,
   UTILIZATION_LIMIT => 50,
   PARALLEL_SERVER_LIMIT => 100,
   MEMORY_LIMIT => 50,
   MEMORY_MIN => 20);
```

<span id="CREATE_CDB_PROFILE_DIRECTIVE" name="CREATE_CDB_PROFILE_DIRECTIVE"></span>

## CREATE_CDB_PROFILE_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

该程序用于创建CDB资源模板指令，通过CDB资源模板指令可以同时为多个PDB[关联](#SET_PDB_PROFILE)相同的资源配额。  

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 现有CDB资源计划的名称 |
|PROFILE|通用参数|CDB资源模板的名称|
|COMMENT|通用参数|注释信息|
|SHARES                | CPU/IOPS资源管理参数 | CPU/IOPS的使用份额（单位：%），取值范围是[1,100]中的整数或NULL值。默认值NULL表示最小值1。 |
| UTILIZATION_LIMIT     | CPU/IOPS资源管理参数        | CPU/IOPS的使用最大上限（单位：%），取值范围是[1,100]中的整数或NULL值。默认值NULL表示最大值100。 |
| PARALLEL_SERVER_LIMIT | 并行执行资源管理参数  | 可用的最大并行资源百分比（单位：%），取值范围是[0,100]中的整数或NULL值。默认值NULL表示最大值100。 |
| MEMORY_LIMIT          | 内存管理参数          | 可用内存的上限百分比（单位：%），取值范围是[MEMORY_MIN,100]中的整数或NULL值，该值允许超配（即所有容器的MEMORY_LIMIT之和大于100）。默认值NULL表示最大值100。 |
| MEMORY_MIN            | 内存管理参数          | 可用内存的下限百分比（单位：%），取值范围是[1,MEMORY_LIMIT]中的整数或NULL值。默认值NULL表示最小值1。  |



使用说明：

- CPU资源上限值计算公式为`UTILIZATION_LIMIT * CPU个数`，例如某用户映射的资源使用组中MAX_UTILIZATION_LIMIT为10，环境中各节点的CPU个数为2，则该用户在每个节点的最大CPU使用率为20%。

- IOPS资源项及其上限值计算公式分别为：
  
    - 每秒读写字节数上限：计算公式为`UTILIZATION_LIMIT * MAX_BPS`，MAX_BPS参数默认为0，即不对相应资源进行限制。

    - 每秒IO操作次数上限：计算公式为`UTILIZATION_LIMIT * MAX_IOPS`，MAX_IOPS参数默认为0，即不对相应资源进行限制。

- PDB的内存资源上下限值计算公式为CDB_MAX_MEMORY_SIZE乘以对应百分比，PDB的所需内存量则以内存相关参数配置值为标准（而非当前实际使用量），包括DATA_BUFFER_SIZE、SHARE_POOL_SIZE、VM_BUFFER_SIZE等。

    - 创建新的PDB、启动现有PDB时，要求PDB所需内存量在[下限值`CDB_MAX_MEMORY_SIZE * MEMORY_MIN`,上限值`CDB_MAX_MEMORY_SIZE * MEMORY_LIMIT`]区间内且CDB内存充足才能正常执行。

    - 若PDB运行过程中调低MEMORY_LIMIT使上限值小于PDB的使用量配置，会自动生成该PDB的内存参数推荐值，不影响PDB此次运行但此次运行期间将无法在线扩展PDB的内存配置。若新MEMORY_LIMIT设置合理，重启PDB后新的内存参数推荐值会生效，并按新MEMORY_LIMIT值计算内存上限。若新MEMORY_LIMIT值设置过小，可能会导致参数推荐值生成失败（运行日志中会记录WARN日志），进而导致后续无法重启，此时需重新调大MEMORY_LIMIT值。
    
    - 若PDB运行过程中调高下限值使其大于PDB的使用量配置，会自动生成该PDB的内存参数推荐值，不影响PDB此次运行。重启PDB后新的内存参数推荐值会生效，并会采用最新的下限值配置。
    



示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PROFILE_DIRECTIVE(
   PLAN => 'RES_CDB_PLAN',
   PROFILE => 'RES_CDB_PROFILE',
   COMMENT => 'RESOURCE PROFILE DIRECTIVE',
   SHARES => 10,
   UTILIZATION_LIMIT => 30,
   PARALLEL_SERVER_LIMIT => 50,
   MEMORY_LIMIT => 25,
   MEMORY_MIN => 10);
```

<span id="SET_PDB_PROFILE" name="SET_PDB_PROFILE"></span>

## SET_PDB_PROFILE

```plsql
DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64) DEFAULT NULL);
```

该程序用于设置PDB与CDB资源模板指令的映射关系。  

各类指令对PDB生效的优先级为CDB资源计划指令（CDB_PLAN_DIRECTIVE） > CDB资源计划模板（CDB_PROFILE_DIRECTIVE） > CDB默认资源计划指令（CDB_DEFAULT_DIRECTIVE）。  



|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLUGGABLE_DATABASE    | 通用参数              | PDB名称，单次只能指定一个PDB。                          |
| PROFILE            | 通用参数 | CDB资源模板的名称，默认值NULL表示解除PDB到资源模板的映射关系 |

示例（单机部署）

```plsql
-- 设置PDB的资源模板
EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE => 'pdb1',
    PROFILE => 'RES_CDB_PROFILE');

-- 删除PDB的资源模板
EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE => 'pdb1');
```

<span id="UPDATE_CDB_DEFAULT_DIRECTIVE" name="UPDATE_CDB_DEFAULT_DIRECTIVE"></span>

## UPDATE_CDB_DEFAULT_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_DEFAULT_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

该程序用于更新CDB默认资源计划指令的配置，初始配置为：

- SHARES = 1
- UTILIZATION_LIMIT = 100
- MEMORY_LIMIT = 100
- MEMORY_MIN = 1


|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 默认CDB资源计划的名称 |
|COMMENT    |   通用参数    |   注释信息    |
|  SHARES                | CPU/IOPS资源管理参数 | CPU/IOPS的使用份额（单位：%），取值范围是[1,100]中的整数或NULL值 |
| UTILIZATION_LIMIT     | CPU/IOPS资源管理参数        | CPU/IOPS的使用最大上限（单位：%），取值范围是[1,100]中的整数或NULL值 |
| PARALLEL_SERVER_LIMIT | 并行执行资源管理参数  | 可用的最大并行资源百分比（单位：%），取值范围是[0,100]中的整数或NULL值 |
| MEMORY_LIMIT          | 内存管理参数          | 可用内存的上限百分比（单位：%），取值范围是[MEMORY_MIN,100]中的整数或NULL值，该值允许超配（即所有容器的MEMORY_LIMIT之和大于100） |
| MEMORY_MIN            | 内存管理参数          | 可用内存的下限百分比（单位：%），取值范围是[1,MEMORY_LIMIT]中的整数或NULL值  |

使用说明：

- 可选参数不设置值或输入为NULL时，不对该参数做任何处理。

示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_DEFAULT_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    COMMENT => 'DEFAULT DIRECTIVE FOR RES_CDB_PLAN',
    SHARES => 10,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 25,
    MEMORY_LIMIT => 50,
    MEMORY_MIN => 25);
```

<span id="UPDATE_CDB_PLAN_DIRECTIVE" name="UPDATE_CDB_PLAN_DIRECTIVE"></span>

## UPDATE_CDB_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

该程序用于更新CDB资源计划指令的配置信息。  

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 待更新的CDB资源计划指令所属CDB资源计划的名称 |
| PLUGGABLE_DATABASE    | 通用参数              | PDB名称，如果更新PDB名称则表示将该计划指令从原PDB解绑再关联到另外的PDB             |
| COMMENT               | 通用参数              | 注释信息                        |
| SHARES                | CPU/IOPS资源管理参数 | CPU/IOPS的使用份额（单位：%），取值范围是[1,100]中的整数或NULL值 |
| UTILIZATION_LIMIT     | CPU/IOPS资源管理参数        | CPU/IOPS的使用最大上限（单位：%），取值范围是[1,100]中的整数或NULL值 |
| PARALLEL_SERVER_LIMIT | 并行执行资源管理参数  | 可用的最大并行资源百分比（单位：%），取值范围是[0,100]中的整数或NULL值 |
| MEMORY_LIMIT          | 内存管理参数          | 可用内存的上限百分比（单位：%），取值范围是[MEMORY_MIN,100]中的整数或NULL值，该值允许超配（即所有容器的MEMORY_LIMIT之和大于100） |
| MEMORY_MIN            | 内存管理参数          | 可用内存的下限百分比（单位：%），取值范围是[1,MEMORY_LIMIT]中的整数或NULL值  |

使用说明：

- 可选参数不设置值或输入为NULL时，不对该参数做任何处理。

示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_PLAN_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'pdb1',
    COMMENT => NULL,
    SHARES => 50,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 50,
    MEMORY_LIMIT => 100,
    MEMORY_MIN => 50);
```

<span id="UPDATE_CDB_PROFILE_DIRECTIVE" name="UPDATE_CDB_PROFILE_DIRECTIVE"></span>

## UPDATE_CDB_PROFILE_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

该程序用于更新CDB资源模板指令的信息。  

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 待更新的CDB资源模板指令所属CDB资源计划的名称 |
|PROFILE|通用参数|目标CDB资源模板指令的名称|
|COMMENT|通用参数|注释信息|
| SHARES                | CPU/IOPS资源管理参数 | CPU/IOPS的使用份额（单位：%），取值范围是[1,100]中的整数或NULL值 |
| UTILIZATION_LIMIT     | CPU/IOPS资源管理参数        | CPU/IOPS的使用最大上限（单位：%），取值范围是[1,100]中的整数或NULL值 |
| PARALLEL_SERVER_LIMIT | 并行执行资源管理参数  | 可用的最大并行资源百分比（单位：%），取值范围是[0,100]中的整数或NULL值 |
| MEMORY_LIMIT          | 内存管理参数          | 可用内存的上限百分比（单位：%），取值范围是[MEMORY_MIN,100]中的整数或NULL值，该值允许超配（即所有容器的MEMORY_LIMIT之和大于100） |
| MEMORY_MIN            | 内存管理参数          | 可用内存的下限百分比（单位：%），取值范围是[1,MEMORY_LIMIT]中的整数或NULL值  |

使用说明：

- 可选参数不设置值或输入为NULL时，不对该参数做任何处理。


示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_PROFILE_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PROFILE => 'RES_CDB_PROFILE',
    COMMENT => NULL,
    SHARES => 50,
    UTILIZATION_LIMIT => 100,
    PARALLEL_SERVER_LIMIT => 100,
    MEMORY_LIMIT => 100,
    MEMORY_MIN => 50);
```

<span id="DELETE_CDB_PLAN_DIRECTIVE" name="DELETE_CDB_PLAN_DIRECTIVE"></span>

## DELETE_CDB_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64));
```

该程序用于删除CDB资源计划指令。删除成功后，对应PDB将会使用已关联的[资源模板指令](#SET_PDB_PROFILE)或默认指令。

|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 待删除的CDB资源计划指令所属CDB资源计划的名称 |
| PLUGGABLE_DATABASE    | 通用参数              | 待删除的CDB资源计划指令所关联的PDB                          |


示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN_DIRECTIVE(
    PLAN = > 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'pdb1');
```

<span id="DELETE_CDB_PROFILE_DIRECTIVE" name="DELETE_CDB_PROFILE_DIRECTIVE"></span>

## DELETE_CDB_PROFILE_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64));
```

该程序用于删除CDB资源模板指令，删除前需执行[SET_PDB_PROFILE](#SET_PDB_PROFILE)解除目标资源模板与PDB的关联。


|  参数| 参数分类| 描述|
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | 通用参数 | 待删除的CDB资源模板指令所属CDB资源计划的名称 |
|PROFILE|通用参数|待删除的CDB资源模板指令的名称|


示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PROFILE_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PROFILE => 'RES_CDB_PROFILE');
```

<span id="DELETE_CDB_PLAN" name="DELETE_CDB_PLAN"></span>

## DELETE_CDB_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN(
    PLAN             IN    VARCHAR(64));
```

该程序用于删除CDB资源计划，无法删除正在生效的资源计划以及内置资源计划DEFAULT_CDB_PLAN。  

|  参数| 描述|
| --- | --- |
| PLAN | 现有CDB资源计划的名称  |

示例（单机部署）

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN(
   PLAN => 'RES_CDB_PLAN');
```

<span id="CREATE_CONSUMER_GROUP" name="CREATE_CONSUMER_GROUP"></span>

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

示例

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

<span id="CREATE_PLAN" name="CREATE_PLAN"></span>

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
| PLAN | CDB资源计划名，名称唯一且符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)，允许使用系统保留关键字，但不满足[双引号的通用规则](../../SQL参考手册/基本SQL元素/双引号)  |
| COMMENT | 注释 |



示例

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

<span id="CREATE_PLAN_DIRECTIVE" name="CREATE_PLAN_DIRECTIVE"></span>

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
| PLAN                    | 通用参数 | 现有资源计划的名称 |
| GROUP_OR_SUBPLAN        | 通用参数 | 资源使用组 |
|    SHARES              | CPU资源管理参数 | CPU共享模式下的使用份额（单位：%），须为[1,100]中的整数，默认值NULL表示最小值1 |
| PARALLEL_SERVER_LIMIT   | 并行执行资源管理参数 | 资源使用组可以使用的最大并行资源百分比，须为[0,100]中的整数，默认值NULL表示最大值100 |
|  UTILIZATION_LIMIT  | CPU资源管理参数 | CPU最大使用上限（单位：%），须为[1,100]中的整数，默认值NULL表示最大值100 |
| MAX_UTILIZATION_LIMIT       | CPU资源管理参数 | 与字段UTILIZATION_LIMIT相同表现 |
| MGMT_P1                  | CPU资源管理参数 | 与字段SHARES相同表现 |
| SPA_LIMIT               | 内存管理参数 | 资源使用组占用用户内存的上限百分比（单位：%），公共内存使用不在限制内。须为[1,100]的整数，默认值NULL表示最大值100 |
| SPA_LIMIT_RESERVED      | 内存管理参数 | 资源使用组内用户内存预留一定比例作为各会话私有部分（单位：%）。预留范围内的用户内存，会话无需向资源使用组申请。须为[0,100]的整数，默认值NULL表示最小值0 |
| SESSION_SPA_LIMIT       | 内存管理参数 | 会话占用资源组可用内存的上限百分比（单位：%）。须为[1,100]的正整数，默认值NULL表示最大值100 |
| EXECUTION_QUEUE_TIMEOUT | 执行调度管理参数 | 当资源不足时在队列中等待的超时时间，单位是s。须为[-1, 4294967295]的整数，-1表示无限超时，默认值NULL等同最小值-1 |
| CONCURRENCY_LIMIT | 执行调度管理参数 | 资源使用组内允许同时执行资源密集型SQL的数量，需为[0, 2147483647]中的整数，NULL表示不限制 |

使用说明：

- 当同时设置MGMT_P1和SHARES参数时，以SHARES参数为准。
- 当同时设置MAX_UTILIZATION_LIMIT和UTILIZATION_LIMIT参数时，以UTILIZATION_LIMIT参数为准。
- 最大CPU使用率计算公式为`MAX_UTILIZATION_LIMIT * CPU个数`，例如某用户映射的资源使用组中MAX_UTILIZATION_LIMIT为10，环境中各节点的CPU个数为2，则该用户在每个节点的最大CPU使用率为20%。
- 为保障SYS_GROUP具备足够的资源可用，当该组的共享资源（MGMT_P1）较少或较多时，系统会在用户输入时做动态调整。若SYS_GROUP的MGMT_P1全局占比小于40%或大于60%时，系统会将SYS_GROUP的MGMT_P1动态调整为剩余资源组的MGMT_P1总和，此时SYS_GROUP的MGMT_P1值可能会超过100（可查看视图DBA_RSRC_PLAN_DIRECTIVES的MGMT_P1字段，全局占比计算公式：`SYS_GROUP的MGMT_P1 / 所有资源使用组的MGMT_P1总和)`）。
- 较低版本的数据库升级后，如果原本已将MGMT_P1或MAX_UTILIZATION_LIMIT参数配置为0，实际表现与当前版本中将其对应配置为100相同。
- 在容器数据库中，如果某个PDB同时配置了全局资源管理和本地资源管理，SHARES和UTILIZATION_LIMIT的配额将是全局和本地的乘积，例如全局和本地的资源管理计划指令均配置UTILIZATION_LIMIT=80则其实际配额为`80 * 80 = 64`。  

示例

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

<span id="SET_CONSUMER_GROUP_MAPPING" name="SET_CONSUMER_GROUP_MAPPING"></span>

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

示例

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

<span id="UPDATE_PLAN_DIRECTIVE" name="UPDATE_PLAN_DIRECTIVE"></span>

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
| PLAN                    | 通用参数 | 现有资源计划的名称 |
| GROUP_OR_SUBPLAN        | 通用参数 | 资源使用组 |
| SHARES                 | CPU资源管理参数 | CPU共享模式下的使用份额，须为[1,100]中的整数 |
| PARALLEL_SERVER_LIMIT   | 并行执行资源管理参数 | 资源使用组可以使用的最大并行资源百分比，须为[0,100]中的整数 |
| UTILIZATION_LIMIT   | CPU资源管理参数 | CPU最大使用上限，须为[1,100]中的整数 |
| MAX_UTILIZATION_LIMIT       | CPU资源管理参数 | 与字段UTILIZATION_LIMIT相同表现 |
| MGMT_P1                  | CPU资源管理参数 | 与字段SHARES相同表现 |
| SPA_LIMIT               | 内存管理参数 | 资源使用组占用用户内存的上限百分比（单位：%），公共内存使用不在限制内。须为[1,100]的整数 |
| SPA_LIMIT_RESERVED      | 内存管理参数 | 资源使用组内用户内存预留一定比例作为各会话私有部分（单位：%）。预留范围内的用户内存，会话无需向资源使用组申请。须为[0,100]的整数 |
| SESSION_SPA_LIMIT       | 内存管理参数 | 会话占用资源组可用内存的上限百分比（单位：%）。须为[1,100]的整数 |
| EXECUTION_QUEUE_TIMEOUT | 执行调度管理参数 | 当资源不足时在队列中等待的超时时间，单位是s。须为[-1, 4294967295]的整数，-1表示无限超时，默认值NULL等同最小值-1 |
| CONCURRENCY_LIMIT | 执行调度管理参数 | 资源使用组内允许同时执行资源密集型SQL的数量，需为[0, 2147483647]中的整数，NULL表示不限制 |

使用说明：

- 可选参数不设置值或输入为NULL时，不对该参数做任何处理。

示例

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

<span id="DELETE_CONSUMER_GROUP_MAPPING" name="DELETE_CONSUMER_GROUP_MAPPING"></span>

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

示例

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

<span id="DELETE_PLAN_DIRECTIVE" name="DELETE_PLAN_DIRECTIVE"></span>

## DELETE_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN             IN VARCHAR(64),
    GROUP_OR_SUBPLAN IN VARCHAR(64));
```

该程序用于删除资源计划指令。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| PLAN                    | 通用参数 | 现有资源计划的名称 |
| GROUP_OR_SUBPLAN | 资源使用组 |

示例

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

<span id="DELETE_PLAN" name="DELETE_PLAN"></span>

## DELETE_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN (
    PLAN IN VARCHAR(64));
```

该程序用于删除资源计划，无法删除正在生效的资源计划及内置资源计划TOALL和SYS_GROUP。

|  参数| 描述|
| :--- | :--- |
| PLAN    | 现有资源计划的名称 |

示例

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

<span id="DELETE_CONSUMER_GROUP" name="DELETE_CONSUMER_GROUP"></span>

## DELETE_CONSUMER_GROUP

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP IN VARCHAR(64));
```

该程序用于删除一个资源使用组。

|  参数| 描述|
| :------------- | :--------- |
| CONSUMER_GROUP | 资源使用组 |

示例

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
