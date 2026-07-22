DBMS_BRANCH包提供了一组用于数据库分支（Branch）生命周期管理的存储过程/函数，支持分支的创建、切换、删除、重置、恢复、冻结、激活、重命名、过期设置及资源管理等功能。

> **Note**:
>
> - 仅适用于分支数据库部署（配置参数ENABLE_BRANCH=TRUE）。
> - 仅SYS用户可调用该高级包的存储过程/函数。

<span id="CREATE" name="CREATE"></span>

## CREATE

```plsql
DBMS_BRANCH.CREATE (
    name      IN    VARCHAR DEFAULT NULL,
    parent    IN    VARCHAR DEFAULT NULL,
    checkout  IN    BOOLEAN DEFAULT FALSE,
    default   IN    BOOLEAN DEFAULT FALSE);
```

该程序用于创建分支，支持以下三种创建方式：

- 创建缺省分支：分支数据为空，不继承任何已有分支的数据。如需创建该类分支，请将default指定为TRUE且parent设置为NULL。
- 从当前分支克隆：分支数据继承当前会话所在分支。如需创建该类分支，请将default指定为FALSE且parent设置为NULL。
- 从指定分支克隆：分支数据继承指定的父分支。如需创建该类分支，请将default指定为FALSE且parent设置为指定分支。

| 参数 | 描述 |
| :--- | :--- |
| name | 分支名称，不指定时由系统自动生成，名称须唯一且符合YashanDB的[对象命名规范](../../SQL参考手册/基本SQL元素/标识符) |
| parent | 父分支名称，不指定时从当前分支克隆，指定时从指定分支克隆 |
| checkout | 创建后是否立即切换到新分支，默认值为FALSE。当设置为TRUE时，创建成功后当前会话将自动切换到新分支 |
| default | 是否创建缺省分支（空分支），默认值为FALSE。当设置为TRUE时，创建的分支数据为空。当parent不为空时，该参数无效 |


示例（单机部署）

```plsql
-- 从当前分支克隆，分支名由系统自动生成
EXEC DBMS_BRANCH.CREATE();

-- 创建一个缺省分支test1
EXEC DBMS_BRANCH.CREATE(name => 'test1', default => TRUE);

-- 从test1分支克隆出一个test2分支，且立即切换到test2分支
EXEC DBMS_BRANCH.CREATE('test2', 'test1', TRUE);

-- 指定参数名创建分支
EXEC DBMS_BRANCH.CREATE(
    name => 'test3',
    parent => 'test1',
    checkout => FALSE,
    default => FALSE);
```

<span id="CURRENT" name="CURRENT"></span>

## CURRENT

```plsql
DBMS_BRANCH.CURRENT();
```

该函数用于获取当前会话所在的分支名称。

示例（单机部署）

```sql
-- 获取当前分支名
SELECT DBMS_BRANCH.CURRENT() FROM DUAL;
DBMS_BRANCH.CURRENT()                                            
---------------------------------------------------------------- 
BRANCH1
-- 也可以执行以下命令查询当前分支名
show branch;
BRANCH                                                           
---------------------------------------------------------------- 
BRANCH1
```

<span id="LIST" name="LIST"></span>

## LIST

```plsql
DBMS_BRANCH.LIST();
```

该函数用于列举当前数据库中所有的分支信息，包括分支名称（BRANCH_NAME）、分支状态（STATUS）、父分支名称（PARENT_NAME）、创建时间（CREATED_AT）以及过期时间（EXPIRED_AT）。

示例（单机部署）

```sql
-- 列举所有的分支信息
SELECT DBMS_BRANCH.LIST() FROM DUAL;
DBMS_BRANCH.LIST()                                               
---------------------------------------------------------------- 
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                           BRANCH_NAME                            | STATUS |                           PARENT_NAME                            |      CREATED_AT     |      EXPIRED_AT     |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| BRANCH$TEMPLATE                                                  | FROZEN |                                                                  | 2026-07-08 20:34:33 |                     |
| MASTER                                                           | ACTIVE | BRANCH$TEMPLATE                                                  | 2026-07-08 20:34:47 |                     |
| BRANCH1                                                          | ACTIVE | MASTER                                                           | 2026-07-08 20:37:29 |                     |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3 branches found.
-- 也可以执行以下命令查询所有分支信息
show branches;
```

<span id="CHECKOUT" name="CHECKOUT"></span>

## CHECKOUT

```plsql
DBMS_BRANCH.CHECKOUT(
    name IN VARCHAR);
```

该程序用于将当前会话切换到目标分支。

| 参数 | 描述 |
| :--- | :--- |
| name | 目标分支名称，必须指定为已创建的分支 |

示例（单机部署）

```plsql
-- 从当前分支切换到test2分支
EXEC DBMS_BRANCH.CHECKOUT('test2');
```

<span id="FREEZE" name="FREEZE"></span>

## FREEZE

```plsql
DBMS_BRANCH.FREEZE(
    name IN VARCHAR);
```

该程序用于冻结自定义创建的分支。如需冻结当前会话正在使用的分支，需先调用CHECKOUT子程序切换至其他分支。

不允许冻结内置的BRANCH$ADMIN分支。

| 参数 | 描述 |
| :--- | :--- |
| name | 待冻结的分支名称，必须指定为已存在的分支 |


示例（单机部署）

```sql
-- 冻结test分支
EXEC DBMS_BRANCH.FREEZE('test');
```

<span id="ACTIVATE" name="ACTIVATE"></span>

## ACTIVATE

```plsql
DBMS_BRANCH.ACTIVATE(
    name IN VARCHAR);
```

该程序用于重新激活已冻结的分支。

| 参数 | 描述 |
| :--- | :--- |
| name | 待激活的分支名称，必须指定为已存在的分支 |

示例（单机部署）

```sql
-- 激活test分支
EXEC DBMS_BRANCH.ACTIVATE('test');
```

<span id="RENAME" name="RENAME"></span>

## RENAME

```plsql
DBMS_BRANCH.RENAME(
    name    IN VARCHAR,
    newname IN VARCHAR);
```

该程序用于重命名分支。
重命名前需调用FREEZE子程序将目标分支冻结。

| 参数 | 描述 |
| :--- | :--- |
| name | 原分支名称，必须指定为已存在的分支 |
| newname | 新分支名称，须符合[对象命名规范](../../SQL参考手册/基本SQL元素/标识符)且不与现有分支重名 |

示例（单机部署）

```plsql
-- 重命名分支test为test2
EXEC DBMS_BRANCH.RENAME('test', 'test2');
```

<span id="SET_RESOURCE" name="SET_RESOURCE"></span>

## SET_RESOURCE

```plsql
DBMS_BRANCH.SET_RESOURCE(
    name       IN VARCHAR,
    UTIL_LIMIT IN NUMBER DEFAULT NULL,
    MEMORY     IN VARCHAR DEFAULT NULL);
```

该程序用于设置分支的资源管理计划，包括CPU和内存的资源配额。

使用说明：

- 默认情况下不开启资源管理计划，该函数不生效。若要开启资源管理，请执行以下语句：
```plsql
EXEC DBMS_BRANCH.CHECKOUT('branch$admin');
alter system set RESOURCE_MANAGER_PLAN='DEFAULT_CDB_PLAN' scope=both;
```
- 计划配置可以通过DBA_BRANCH_RSRC_PLAN_DIRECTIVES视图查询
- UTIL_LIMIT和MEMORY参数可以单独指定，也可以同时指定。
- 不指定参数或指定为NULL时，不对该资源项进行限制。

| 参数 | 描述 |
| :--- | :--- |
| name | 分支名称，必须指定为已存在的分支 |
| UTIL_LIMIT | CPU占用百分比上限。取值范围为[1,100]的整数，单位为% |
| MEMORY | 内存占用上限。支持带单位的字符串格式，如'5G'、'1024M'等 |

示例（单机部署）

```plsql
-- 设置分支test的CPU占用上限为20%
EXEC DBMS_BRANCH.SET_RESOURCE('test', UTIL_LIMIT => 20);

-- 设置分支test的内存占用上限为5G
EXEC DBMS_BRANCH.SET_RESOURCE('test', MEMORY => '5G');

-- 同时设置分支test的CPU占用上限为30%，内存占用上限为10G
EXEC DBMS_BRANCH.SET_RESOURCE('test', 30, '10G');
```

<span id="SET_EXPIRATION" name="SET_EXPIRATION"></span>

## SET_EXPIRATION

```plsql
DBMS_BRANCH.SET_EXPIRATION(
    name      IN VARCHAR,
    timestamp IN VARCHAR);
```

该程序用于设置分支的过期时间。到达过期时间后，分支将自动被清理。

| 参数 | 描述 |
| :--- | :--- |
| name | 分支名称，必须指定为已存在的分支 |
| timestamp | 过期时间，格式为'YYYY-MM-DD HH24:MI:SS' |

示例（单机部署）

```plsql
-- 设置分支test在2027年1月1日0点过期
EXEC DBMS_BRANCH.SET_EXPIRATION('test', '2027-01-01 00:00:00');
-- 查询所有分支的过期时间
SELECT DBMS_BRANCH.LIST() FROM DUAL;
```

<span id="RESET" name="RESET"></span>

## RESET

```plsql
DBMS_BRANCH.RESET();
```

该函数用于将当前分支的数据重置到分支创建时刻的状态。

>**Caution**：
>- 重置操作会丢弃分支创建后产生的所有数据变更，且不可恢复，**请谨慎使用**。
>- 如果分支创建时拥有的数据文件丢失，该操作不会恢复这些数据文件，相关数据将会丢失。

示例（单机部署）

```sql
-- 确定当前分支是希望恢复的分支
SELECT DBMS_BRANCH.CURRENT() FROM DUAL;
DBMS_BRANCH.CURRENT()                                           
----------------------------------------------------------------
BRANCH1                                                         

-- 将当前分支重置到初始状态
EXEC DBMS_BRANCH.RESET();
```

<span id="RESTORE" name="RESTORE"></span>

## RESTORE

```plsql
DBMS_BRANCH.RESTORE(
    timestamp IN VARCHAR);
```

该程序用于将当前分支的数据恢复到分支创建之后的指定时间点。

>**Caution**：
>- 恢复操作会丢弃指定时间点之后的所有数据变更，且不可恢复，**请谨慎使用**。
>- 如果分支在目标时间点拥有的数据文件丢失，该操作不会恢复这些数据文件，相关数据将会丢失。

| 参数 | 描述 |
| :--- | :--- |
| timestamp | 恢复目标时间点，支持时间戳格式或SCN值<br/>必须晚于该分支的创建时间 |

示例（单机部署）

```sql
-- 确定当前分支是希望恢复的分支
SELECT DBMS_BRANCH.CURRENT() FROM DUAL;
DBMS_BRANCH.CURRENT()                                           
----------------------------------------------------------------
BRANCH1                                                         

-- 查询分支的创建时间
SELECT DBMS_BRANCH.LIST() FROM DUAL;
DBMS_BRANCH.LIST()                                               
---------------------------------------------------------------- 
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                           BRANCH_NAME                            | STATUS |                           PARENT_NAME                            |      CREATED_AT     |      EXPIRED_AT     |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| BRANCH$TEMPLATE                                                  | FROZEN |                                                                  | 2026-07-08 20:34:33 |                     |
| MASTER                                                           | ACTIVE | BRANCH$TEMPLATE                                                  | 2026-07-08 20:34:47 |                     |
| BRANCH1                                                          | ACTIVE | MASTER                                                           | 2026-07-08 20:37:29 |                     |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3 branches found.

-- 查询当前的SCN
SELECT CURRENT_SCN FROM V$DATABASE;

          CURRENT_SCN 
--------------------- 
   842457321918119936

-- 将当前分支恢复到指定时间点
EXEC DBMS_BRANCH.RESTORE('2026-07-08 21:00:00');

-- 将当前分支恢复到指定SCN
EXEC DBMS_BRANCH.RESTORE(842457321918119936);
```

<span id="DELETE" name="DELETE"></span>

## DELETE

```plsql
DBMS_BRANCH.DELETE(
    name IN VARCHAR);
```

该程序用于删除自定义创建的分支。如需删除当前会话正在使用的分支，需先调用CHECKOUT子程序切换至其他分支。

不允许删除内置的BRANCH$ADMIN和BRANCH$TEMPLATE分支。

| 参数 | 描述 |
| :--- | :--- |
| name | 待删除的分支名称，必须指定为已存在的分支 |

示例（单机部署）

```plsql
-- 删除test2分支
EXEC DBMS_BRANCH.DELETE('test2');
```
