YStream是YashanDB提供的逻辑日志解析接口，其服务端API（又称为“YStream服务”）由DBMS_YSTREAM_ADM高级包提供的内置函数进行管理，包括创建、调整配置、启停以及删除等。

> **Note**: 
>
> - 存算一体分布式集群部署中无YStream功能。
> - 执行该高级包的用户需具备YSTREAM_CAPTURE角色。
> - 该高级包不能操作逻辑备库相关配置。

使用YStream服务的前提条件如下：

- 创建YStream服务前，必须先按需合理配置STREAM_POOL_SIZE值。

- 创建YStream服务前，必须先开启归档模式（ARCHIVELOG）。

- 创建YStream服务前，建议先开启附加日志（[库级](../../SQL参考手册/SQL语句/ALTER DATABASE.html#supplementallogclauses)或[表级](../../SQL参考手册/SQL语句/ALTER DATABASE.html#addsupplementalloggingclause)）。若不开启，则只能解析已有日志中的附加信息，且在已有YStream服务时再开启附加日志会报错。

<span id="STATUS" name="STATUS" class="yaslink"></span>

YStream服务通过状态标示其运行情况，且在不同状态下允许对其执行的管理操作略有不同，YStream服务的状态、类型等信息均可通过查询[V$YSTREAM_SERVER](../../../参考手册/系统视图/动态视图/V$YSTREAM_SERVER)视图获取。

|  状态| 描述| 允许执行的操作|
| :---- |:-----------|:-----------|
|CREATED | 初始状态，表示YStream服务已创建但未启动       |\* [ADD_TABLES](#add_tables)：新增解析表名和模式<br/>\* [DROP_TABLES](#drop_tables)：删除解析表名和模式<br/>\* [SET_PARAMETER](#set_parameter)：设置参数<br/>\* [START](#start)：启动YStream服务<br/>\* [DROP](#drop)：删除YStream服务|
|STARTED | 表示YStream服务已启动，等待[YStream客户端](../../YStream参考手册/00YStream参考手册)连接 |启动YStream服务的实例允许：<br/>\* [ADD_TABLES](#add_tables)：新增解析表名和模式<br/>* [STOP](#stop)：停止YStream服务<br/><br/> 启动YStream服务的实例离线时，集群其他实例允许：</br>* [STOP](#stop)：停止YStream服务<br/>\* [TEST](#test)：测试YStream服务的start_scn是否合适|
|RUNNING | * 表示[YStream客户端](../../YStream参考手册/00YStream参考手册)已连接，YStream服务在当前实例运行中<br/> * 表示当前实例正在执行[TEST](#test)函数 |启动YStream服务的实例允许：<br/>\* [ADD_TABLES](#add_tables)：新增解析表名和模式<br/>* [STOP](#stop)：停止YStream服务<br/><br/> 启动YStream服务的实例离线时，集群其他实例允许：</br>* [STOP](#stop)：停止YStream服务|
|STOPPED | 表示YStream服务已停止         |\* [ADD_TABLES](#add_tables)：新增解析表名和模式<br/>\* [DROP_TABLES](#drop_tables)：删除解析表名和模式<br/>\* [SET_PARAMETER](#set_parameter)：设置参数<br/>\* [START](#start)：启动YStream服务<br/>\* [DROP](#drop)：删除YStream服务|

<span id="create" name="create" class="yaslink"></span>

## CREATE

```plsql
DBMS_YSTREAM_ADM.CREATE(
    server_name    IN  VARCHAR(64), 
    connect_user   IN  VARCHAR(64) DEFAULT NULL, 
    start_scn      IN  BIGINT DEFAULT NULL
);
```

CREATE函数用于新建YStream服务。

YStream服务创建时，会记录日志解析起始点。若数据库已开启归档，不论YStream服务处于何种状态，日志解析起始点后的所有未被解析的归档都不会自动清理。若无需再使用某个YStream服务对应的日志解析，请及时删除不必要的YStream服务，以免妨碍数据库自动清理归档，造成存储空间浪费。

函数使用规则如下：

- YStream服务数量上限：
  - 单机部署中，每个实例最多创建32个YStream服务。
  - 共享集群/分布式集群部署中，每个集群最多创建32个YStream服务，各服务可以运行在集群的任意实例上。
- 在高可用环境中，仅允许在主库/主集群上调用该函数。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，不能与已有服务名冲突，该参数不能为NULL|
|connect_user|允许连接该YStream服务的数据库用户，指定用户必须具备YSTREAM_CAPTURE权限，设置为NULL表示允许所有数据库用户连接<br />由于YashanDB的语法模式会影响用户名的大小写敏感规则，在指定用户名时，请根据实际情况进行转义：<br />- yashan模式：大小写不敏感且会自动转为全大写。若用户名需区分大小写应使用双引号包围，例如用户名为`User`时应指定为`'"User"'`<br />- mysql模式：大小写敏感，且不会进行自动转换。若添加双引号会将其视作用户名的一部分，例如`'"User"'`将被解析为`"User"`|
|start_scn|日志解析的起始点，设置为NULL表示从当前位置开始<br/><br/>如果start_scn位于一个DDL事务中间，则只能解析到该DDL事务在start_scn之后的部分日志，YStream无法通过不完整的DDL解析构建元数据，后续与该DDL相关的日志均无法解析而被忽略。如遇此种场景，需重新创建YStream服务，指定新的start_scn，以避免start_scn在某个DDL事务内部<br/><br/>您可以在创建和启动YStream服务后，调用[TEST](#test)函数测试start_scn是否合适。<br/><br/>YStream仅支持解析归档模式下生成的redo或归档日志，请确保start_scn之后的日志都符合条件。|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.CREATE('server_1');
EXEC DBMS_YSTREAM_ADM.CREATE('server_2', 'sales');
EXEC DBMS_YSTREAM_ADM.CREATE('server_3', 'sales', 563517721634004992);
EXEC DBMS_YSTREAM_ADM.CREATE('server_4');
EXEC DBMS_YSTREAM_ADM.CREATE('server_5');
```

<span id="add_tables" name="add_tables" class="yaslink"></span>

## ADD\_TABLES

```plsql
DBMS_YSTREAM_ADM.ADD_TABLES(
    server_name   IN  VARCHAR(64),
    table_names   IN  VARCHAR(4096),
    schemas       IN  VARCHAR(4096)
);
```

ADD_TABLES函数用于为已有YStream服务新增解析表名和模式。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 在高可用环境中，仅允许在主库/主集群上调用该函数。
- 若不执行ADD_TABLES或执行ADD_TABLES时将table_names和schemas参数均设置为空字符串，表示不做任何过滤，解析所有表和模式。
- 系统不会校验table_names和schemas参数所传入的表名和schema名称是否存在，方便扩展配置（预先将规划中暂未创建的表或schema加至YStream服务）。
- 允许table_names和schemas参数传入重复的表名、shcema名称，系统会自动去重，且不会报错。
- 允许为同一YStream服务多次新增解析表名和模式，最终结果将为多次操作的之和（系统自动去重后的并集）。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|
|table_names|添加的表名清单，若表名或schema名称中存在空格、逗号等特殊字符需使用双引号`""`包围，多个表名以`,`分隔，每个服务最多支持1000000个表。指定表名时可以指定表所在的模式，例如'sales.tab'，表的默认模式为当前模式。若不指定表名，请传入NULL|
|schemas|添加的模式清单，若schema名称中存在空格、逗号等特殊字符需使用双引号`""`包围，多个模式以`,`分隔，每个YStream服务最多支持10000个模式。若不指定模式，请传入NULL|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_1', 'sales.employees', '');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_2', 'sales.employees,sales.employees,sales.department,sales.branches', '');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_3', '', 'schema1,schema2');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_4', 'sales.department,sales.branches', '"schema{3}",schema4');
EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_5', '', '');
```

<span id="drop_tables" name="drop_tables" class="yaslink"></span>

## DROP\_TABLES

```plsql
DBMS_YSTREAM_ADM.DROP_TABLES(
    server_name   IN  VARCHAR(64), 
    table_names   IN  VARCHAR(4096),
    schemas       IN  VARCHAR(4096)
);
```

DROP_TABLES函数用于为YStream服务删除表名、模式。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 在高可用环境中，仅允许在主库/主集群上调用该函数。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|
|table_names|删除的表名清单，若表名或schema名称中存在空格、逗号等特殊字符需使用双引号`""`包围，多个表名以`,`分隔。指定表名时可以指定表所在的模式，例如'sales.tab'，表的默认模式为当前模式。若不指定表名，请传入NULL|
|schemas|删除的模式清单，若schema名称中存在空格、逗号等特殊字符需使用双引号`""`包围，多个模式以`,`分隔。若不指定模式，请传入NULL|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.DROP_TABLES('server_1', 'sales.department,sales.branches', '');
EXEC DBMS_YSTREAM_ADM.DROP_TABLES('server_2', 'sales.department,sales.department', 'schema1,schema2');
```

<span id="set_parameter" name="set_parameter" class="yaslink"></span>

## SET\_PARAMETER

```plsql
DBMS_YSTREAM_ADM.SET_PARAMETER(
    server_name   IN  VARCHAR(64), 
    parameter     IN  VARCHAR(64), 
    value         IN  VARCHAR(64)
);
```

SET_PARAMETER函数用于为已有服务设置参数。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 在高可用环境中，仅允许在主库/主集群上调用该函数。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|
|parameter|参数名，该参数不能为NULL|
|value|参数值，应在取值范围内，该参数不能为NULL|

支持设置以下参数：

|  参数名| 取值范围| 默认值| 描述|
| :---- |:-----------|:-----------|:-----------|
|PARALLELISM|[1,128]|1|Redo解析线程并发数，指定1个YStream服务中解析1个实例日志的并发线程数<br/><br/>* 共享集群/分布式集群部署中，YStream服务需同时解析所有实例的日志，在M个实例的集群中，启动一个并行度为N的YStream服务，该服务创建的日志解析线程数为`M*N`<br/>* 单机部署中，YStream服务仅需解析当前实例的日志，启动一个并行度为N的YStream服务，该服务创建的日志解析线程数=`1*N`<br/><br/>并发数设置过高会消耗更多资源，可能触发操作系统的线程数限制，请合理配置或调整并发数|
|TXN_AGE_SPILL_THRESHOLD|[1,100000]|600|LCR溢出触发的时间阈值（单位为秒），解析过程中，若某个事务长时间不提交，等待时间超过该值时该事务所有LCR会溢出到系统表进行持久化并释放内存，此类LCR所对应的日志无需再被重复解析，用户可自行按需清理归档日志|
|TXN_LCR_SPILL_THRESHOLD|[32K,1T]|128M|LCR溢出触发的内存占用阈值（单位为字节），解析过程中，若某个事务所占内存超过该值，该事务所有LCR会溢出到系统表进行持久化并释放内存，此类LCR所对应的日志无需再被重复解析，用户可自行按需清理归档日志|
|CHECKPOINT_INTERVAL|[1,3600]|3|Checkpoint执行的间隔（单位为秒），每次Checkpoint会在系统表持久化最新的重启恢复点，客户端设置最新的applied position后，需要等待一个Checkpoint间隔才能被数据库感知|

 示例（单机、共享集群部署）

 ```plsql
 EXEC DBMS_YSTREAM_ADM.SET_PARAMETER('server_1', 'PARALLELISM', '3');
 ```

<span id="start" name="start" class="yaslink"></span>

## START

```plsql
DBMS_YSTREAM_ADM.START(
    server_name   IN  VARCHAR(64)
);
```

START函数用于启动一个已有YStream服务，YStream服务启动后才允许[YStream客户端](../../YStream参考手册/00YStream参考手册)进行连接。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 每个YStream服务对应的客户端仅允许连接到启动该服务的实例，后续使用中如需切换连接，需先[停止该服务](#stop)再通过目标实例重新启动YStream服务。
- 若目标YStream服务的start_scn过旧（SCN对应的UNDO日志已被清理），启动时会提示“too old snapshot”。如遇此类情况，建议先删除目标YStream服务再重新创建同名YStream服务并指定新的start_scn。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.START('server_1');
EXEC DBMS_YSTREAM_ADM.START('server_2');
```

<span id="test" name="test" class="yaslink"></span>

## TEST

```plsql
DBMS_YSTREAM_ADM.TEST(
    server_name   IN  VARCHAR(64),
    target_scn    IN  BIGINT
);
```

TEST函数用于测试一个已有YStream服务的start_scn是否合适，测试过程中不会产生逻辑日志，客户端无需连接服务端。

该函数会从YStream服务的日志解析的起始点start_scn开始扫描redo文件直至用户给定的目标target_scn，检测start_scn是否处于某个DDL事务的过程中。

如果测试过程中发现一个DDL事务commit日志，但该DDL的事务begin scn在当前YStream服务的start_scn之前，则无法完整解析该DDL，相应元数据无法被正确维护，解析将无法继续。此时需要重新创建YStream服务并指定合适的start_scn，避免start_scn处于某个DDL事务的过程中。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 每个YStream服务对应的客户端仅允许连接到启动该服务的实例，后续使用中如需切换连接，需先[停止该服务](#stop)再通过目标实例重新启动YStream服务。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|
|target_scn|测试结束点对应的SCN，可以指定为当前数据库最新的SCN，该参数不能为NULL，如target_scn小于YStream服务的start_scn，系统自动调整为start_scn进行容错|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.TEST('server_1', 563517721634005992);
```

<span id="stop" name="stop" class="yaslink"></span>

## STOP

```plsql
DBMS_YSTREAM_ADM.STOP(
    server_name   IN  VARCHAR(64),
    force         IN  BOOL DEFAULT FALSE
);
```

STOP函数用于停止一个已有YStream服务。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 在共享集群/分布式集群部署中，同一YStream服务的启停通常要求在同一个实例上操作，除非对应实例不可用（例如已离线）方才允许在其他实例上停止该服务。若为主备集群部署，则始终不允许在备集群实例停止主集群实例启动的YStream服务。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|
|force|强制停止YStream服务。当连接到备库，备库与备库断连时，忽略错误直接停止YStream服务，系统表中状态不会发生变化|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.STOP('server_1');
EXEC DBMS_YSTREAM_ADM.STOP('server_2', TRUE);
```

<span id="drop" name="drop" class="yaslink"></span>

## DROP

```plsql
DBMS_YSTREAM_ADM.DROP(
    server_name   IN  VARCHAR(64)
);
```

DROP函数用于删除一个已有YStream服务。

函数使用规则如下：

- 执行该操作的目标YStream服务的状态等要求请查阅[状态介绍](#STATUS)。
- 在高可用环境中，仅允许在主库/主集群上调用该函数。

|  参数| 描述|
| :---- |:-----------|
|server_name|YStream服务的名称，该参数不能为NULL|

示例（单机、共享集群部署）

```plsql
EXEC DBMS_YSTREAM_ADM.DROP('server_1');
```
