DBMS_PROFILER是Oracle兼容的PL代码性能分析工具，用于收集PL应用程序的执行性能数据，实现行级性能分析。

DBMS_PROFILER核心功能包括：

- 执行次数统计：记录每行代码的执行次数
- 执行时间测量：以纳秒为单位记录每行代码的执行时间（总时间、最小时间、最大时间）
- 代码覆盖率分析：识别哪些代码行被执行，哪些未被执行
- 性能瓶颈定位：帮助开发者快速定位耗时最长的代码段

> **Note**:
>
> - DBMS_PROFILER高级包不适用于存算一体分布式集群部署。
> 
> - 执行DBMS_PROFILER高级包的用户需具备该高级包的执行权限。
>
> - 使用DBMS_PROFILER的第一步是先调用CREATE_TABLES过程创建性能分析数据表。**不建议**自行修改表结构，以免引起不必要的报错。
>
> - 性能分析会话对并行场景不生效，其收集结果将不会包含管道表函数（PIPELINED）相关数据。
>
> - 对于外置自定义函数，性能分析会话仅收集其执行时间，不会收集内部执行细节。
>
> - 启动性能分析会话将会对性能产生一定影响。性能分析会话期间，性能数据先在内存中累积。如果剖析时间过长或执行代码量极大，内存占用会持续增加。可通过调用FLUSH_DATA过程将数据刷入磁盘并释放内存，但这会增加I/O开销并可能影响被剖析程序的性能。
>
> - 性能收集数据的生命周期为会话级，会话断开或手动调用STOP_PROFILER均可释放内存。

## CREATE_TABLES

```plsql
DBMS_PROFILER.CREATE_TABLES (
   force_it IN BOOLEAN DEFAULT FALSE);
```

该子存储过程用于创建性能分析数据表，包括PLSQL_PROFILER_RUNS、PLSQL_PROFILER_UNITS和PLSQL_PROFILER_DATA三张表。

| 参数 | 说明 |
|:---|:---|
| force_it | 是否强制重建。默认为FALSE，若数据表已存在则返回错误。当设置为TRUE时，若数据表已存在将先删除再重新创建。 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 创建性能分析数据表
BEGIN
    DBMS_PROFILER.CREATE_TABLES();
END;
/

-- 强制重建数据表
BEGIN
    DBMS_PROFILER.CREATE_TABLES(force_it => TRUE);
END;
/
```

## START_PROFILER

```plsql
DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '',
   run_number    OUT BINARY_INTEGER)
 RETURN BINARY_INTEGER;

DBMS_PROFILER.START_PROFILER (
   run_comment  IN VARCHAR := SYSDATE,
   run_comment1 IN VARCHAR := '')
 RETURN BINARY_INTEGER;

DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '',
   run_number    OUT BINARY_INTEGER);

DBMS_PROFILER.START_PROFILER (
   run_comment   IN VARCHAR := SYSDATE,
   run_comment1  IN VARCHAR := '');
```

该子存储函数用于启动性能分析会话，生成新的运行ID并初始化性能数据收集。返回0表示执行成功，返回1表示因参数错误而执行失败。

| 参数 | 说明 |
|:---|:---|
| run_comment | 运行注释，默认为当前时间戳。最大长度为2047字节。 |
| run_comment1 | 附加注释，最大长度为2047字节。可省略，默认为空字符串。 |
| run_number | 当执行成功时，设置本次运行的唯一标识符（RUNID）。 |

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    run_id BINARY_INTEGER;
    ret    BINARY_INTEGER;
BEGIN
    ret := DBMS_PROFILER.START_PROFILER('Performance test', '', run_id);
    DBMS_OUTPUT.PUT_LINE('Run ID: ' || run_id || ', Return: ' || ret);
END;
/
```

## STOP_PROFILER

```plsql
DBMS_PROFILER.STOP_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.STOP_PROFILER;
```

该子存储函数用于停止性能分析会话，并将内存中的性能数据写入数据库表。返回0表示执行成功，返回2表示因数据刷新失败而执行失败。

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- 先启动性能分析
    DBMS_PROFILER.START_PROFILER('Test stop');

    -- 执行待分析的代码
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- 停止性能分析
    ret := DBMS_PROFILER.STOP_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Return: ' || ret);
END;
/
```

## PAUSE_PROFILER

```plsql
DBMS_PROFILER.PAUSE_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.PAUSE_PROFILER;
```

该子存储函数用于暂停性能数据收集。暂停期间执行的代码不会被记录。返回0表示执行成功。

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- 启动性能分析
    DBMS_PROFILER.START_PROFILER('Pause test');

    -- 记录初始阶段性能数据
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- 暂停性能分析
    ret := DBMS_PROFILER.PAUSE_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Paused. Return: ' || ret);

    -- 这段代码不会被记录
    FOR i IN 1..1000 LOOP
        NULL;
    END LOOP;

    -- 停止性能分析
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## RESUME_PROFILER

```plsql
DBMS_PROFILER.RESUME_PROFILER
 RETURN BINARY_INTEGER;

DBMS_PROFILER.RESUME_PROFILER;
```

该子存储函数用于恢复性能数据收集。返回0表示执行成功。

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- 启动性能分析
    DBMS_PROFILER.START_PROFILER('Resume test');

    -- 记录初始阶段性能数据
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- 暂停性能分析
    DBMS_PROFILER.PAUSE_PROFILER();

    -- 恢复性能分析
    ret := DBMS_PROFILER.RESUME_PROFILER();
    DBMS_OUTPUT.PUT_LINE('Resumed. Return: ' || ret);

    -- 继续记录性能数据
    FOR i IN 1..100 LOOP
        NULL;
    END LOOP;

    -- 停止性能分析
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## FLUSH_DATA

```plsql
DBMS_PROFILER.FLUSH_DATA
 RETURN BINARY_INTEGER;

DBMS_PROFILER.FLUSH_DATA;
```

该子存储函数用于将内存中的性能数据刷新到数据库表，并释放已刷新数据的内存。返回0表示执行成功，返回2表示因数据刷新失败而执行失败。

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    -- 启动性能分析
    DBMS_PROFILER.START_PROFILER('Flush test');

    -- 执行长时间运行的代码
    FOR outer_i IN 1..10 LOOP
        FOR inner_i IN 1..10000 LOOP
            NULL;
        END LOOP;

        -- 每轮循环后刷新数据到磁盘，释放内存
        ret := DBMS_PROFILER.FLUSH_DATA();
        DBMS_OUTPUT.PUT_LINE('Flushed. Return: ' || ret);
    END LOOP;

    -- 停止性能分析
    DBMS_PROFILER.STOP_PROFILER();
END;
/
```

## GET_VERSION

```plsql
DBMS_PROFILER.GET_VERSION (
   major  OUT BINARY_INTEGER,
   minor  OUT BINARY_INTEGER);
```

GET_VERSION用于获取DBMS_PROFILER包的版本信息。

| 参数 | 说明 |
|:---|:---|
| major | 返回主版本号，当前值为1。 |
| minor | 返回次版本号，当前值为0。 |

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    major INTEGER;
    minor INTEGER;
BEGIN
    DBMS_PROFILER.GET_VERSION(major, minor);
    DBMS_OUTPUT.PUT_LINE('Version: ' || major || '.' || minor);
END;
/
```

## INTERNAL_VERSION_CHECK

```plsql
DBMS_PROFILER.INTERNAL_VERSION_CHECK
 RETURN BINARY_INTEGER;
```

该子存储函数用于内部版本兼容性检查。返回0表示执行成功。

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    ret BINARY_INTEGER;
BEGIN
    ret := DBMS_PROFILER.INTERNAL_VERSION_CHECK();
    DBMS_OUTPUT.PUT_LINE('Return: ' || ret);
END;
/
```

## 性能分析数据表

性能分析数据存储在以下三张表中：

### PLSQL_PROFILER_RUNS

存储每次性能分析的运行信息。

| 字段 | 类型 | 说明 |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | 唯一运行标识符，主键。 |
| RELATED_RUN | NUMBER | 关联运行ID，当前固定为NULL。 |
| RUN_OWNER | VARCHAR2(128) | 启动运行的用户。 |
| RUN_DATE | DATE | 运行开始时间。 |
| RUN_COMMENT | VARCHAR2(2047) | 用户提供的运行注释。 |
| RUN_TOTAL_TIME | NUMBER | 运行总耗时，单位为纳秒。 |
| RUN_SYSTEM_INFO | VARCHAR2(2047) | 系统信息，当前固定为NULL。 |
| RUN_COMMENT1 | VARCHAR2(2047) | 附加注释。 |
| SPARE1 | VARCHAR2(256) | 保留字段。 |

### PLSQL_PROFILER_UNITS

存储每个库单元的性能分析信息，表中主键为(RUNID, UNIT_NUMBER)。

| 字段 | 类型 | 说明 |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | 关联运行ID，外键。 |
| UNIT_NUMBER | NUMBER | 库单元内部编号，从1开始递增。 |
| UNIT_TYPE | VARCHAR2(128) | 库单元类型，包括PACKAGE SPEC、PACKAGE BODY、TYPE BODY、FUNCTION、PROCEDURE、ANONYMOUS BLOCK。 |
| UNIT_OWNER | VARCHAR2(128) | 库单元所有者。 |
| UNIT_NAME | VARCHAR2(128) | 库单元名称。 |
| UNIT_TIMESTAMP | DATE | 单元时间戳。 |
| TOTAL_TIME | NUMBER | 单元总耗时，单位为纳秒。 |
| SPARE1 | NUMBER | 保留字段，当前固定为NULL。 |
| SPARE2 | NUMBER | 保留字段，当前固定为NULL。 |


### PLSQL_PROFILER_DATA

存储每行代码的性能数据，表中主键为(RUNID, UNIT_NUMBER, LINE#)。

| 字段 | 类型 | 说明 |
| :---------- | :---------- | :----------------------------------------------------------- |
| RUNID | NUMBER | 关联运行ID。 |
| UNIT_NUMBER | NUMBER | 库单元编号。 |
| LINE# | NUMBER | 源代码行号。 |
| TOTAL_OCCUR | NUMBER | 执行次数。 |
| TOTAL_TIME | NUMBER | 总执行时间，单位为纳秒。 |
| MIN_TIME | NUMBER | 最小执行时间，单位为纳秒。 |
| MAX_TIME | NUMBER | 最大执行时间，单位为纳秒。 |
| SPARE1 | NUMBER | SQL执行时间，例如执行DML语句或者使用游标进行open等，单位为纳秒。 |
| SPARE2 | NUMBER | 子调用执行时间，例如执行某个子过程或者跳转到异常处理，begin行执行时进行变量初始化等，单位为纳秒。 |
| SPARE3 | NUMBER | 保留字段，当前固定为NULL。 |
| SPARE4 | NUMBER | 保留字段，当前固定为NULL。 |
| LINE_TYPE | VARCHAR2(64) | 行类型。 |