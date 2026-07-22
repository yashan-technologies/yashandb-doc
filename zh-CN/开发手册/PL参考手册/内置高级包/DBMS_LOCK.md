DBMS_LOCK包提供了一组内置的存储过程和函数，用于管理和控制用户锁资源，允许用户显式定义锁，实现对共享资源的并发控制。支持创建、获取、释放锁，以及设置锁的等待时间等，常用于协调多个会话或事务对临界资源的访问。

> **Note**: 
>
> - REQUEST和CONVERT函数目前只支持S_MODE和X_MODE两种锁模式。
>
> - 普通用户使用锁函数前需要先获取DBMS_LOCK包的执行权限。
>
> - 除SLEEP外，DBMS_LOCK高级包的其他子程序在单机主备部署或主备集群部署中，只能在主库/主集群上执行。
>
> - 除SLEEP外，DBMS_LOCK高级包的其他子程序不适用于存算一体分布式集群部署。

## 预定义常量

### 锁模式常量

| 常量名 | 类型 | 值 | 说明 |
|--------|------|-----|------|
| NL_MODE | NUMBER | 1 | 空锁模式 |
| SS_MODE | NUMBER | 2 | 行共享模式 |
| SX_MODE | NUMBER | 3 | 行排他模式 |
| S_MODE | NUMBER | 4 | 共享模式，允许其他会话读取但不允许修改 |
| SSX_MODE | NUMBER | 5 | 共享排他模式 |
| X_MODE | NUMBER | 6 | 排他模式，其他会话不能读取或修改 |

> **Note**: 
>
> 目前REQUEST和CONVERT函数只支持S_MODE（4）和X_MODE（6）两种模式，其他锁模式仅作兼容处理。

### 超时常量

| 常量名 | 类型 | 值 | 说明 |
|--------|------|-----|------|
| MAXWAIT | NUMBER | 32767 | 最大等待时间（单位：秒），表示无限等待 |

### 锁模式转换规则

| 当前持有模式 | 请求S_MODE | 请求X_MODE |
|--------------|------------|------------|
| S_MODE | 成功 | 等待 |
| X_MODE | 等待 | 等待 |

## ALLOCATE_UNIQUE

```plsql
DBMS_LOCK.ALLOCATE_UNIQUE(
    lockname         IN   VARCHAR,
    lockhandle       OUT  VARCHAR,
    expiration_secs  IN   NUMBER   DEFAULT 864000);
```

该子存储过程用于为指定的锁名称分配一个唯一的锁标识符LOCKID，锁标识符用于使应用程序能够协调使用锁。

| 参数 | 类型 | 描述 |
|------|------|------|
| lockname | VARCHAR | 锁名称，用于分配唯一锁标识符，长度不超过128字符 |
| lockhandle | VARCHAR | 返回锁标识符的句柄，在后续调用REQUEST、CONVERT和RELEASE时使用此句柄标识锁资源。所有会话使用相同锁名称的锁句柄都引用同一个锁 |
| expiration_secs | NUMBER | 锁过期时间（单位：秒），在最后一次ALLOCATE_UNIQUE之后等待多少秒才允许删除该锁，默认值为864000（10天） |

示例（单机/共享集群/分布式集群部署）

```plsql
DECLARE
    lock_name   VARCHAR(200);
    lock_handle VARCHAR(200);
BEGIN
    lock_name := 'user_lock';
    DBMS_LOCK.ALLOCATE_UNIQUE(
        lockname         => lock_name,
        lockhandle       => lock_handle,
        expiration_secs  => 60);
    DBMS_OUTPUT.PUT_LINE('lockhandle: ' || lock_handle);
END;
/
```

## REQUEST

```plsql
-- 方式一：使用锁ID
DBMS_LOCK.REQUEST(
    id                 IN  NUMBER,
    lockmode           IN  NUMBER  DEFAULT X_MODE,
    timeout            IN  NUMBER  DEFAULT MAXWAIT,
    release_on_commit  IN  BOOLEAN  DEFAULT FALSE)
RETURN NUMBER;

-- 方式二：使用锁句柄
DBMS_LOCK.REQUEST(
    lockhandle         IN  VARCHAR,
    lockmode           IN  NUMBER  DEFAULT X_MODE,
    timeout            IN  NUMBER  DEFAULT MAXWAIT,
    release_on_commit  IN  BOOLEAN  DEFAULT FALSE)
RETURN NUMBER;
```

该存储函数用于获取指定模式的锁。返回0表示执行成功，返回其他值则表示失败，具体原因取决于返回值：

- 返回1表示超时。
- 返回2表示死锁。
- 返回3表示参数错误。
- 返回4表示已经持有指定的锁。
- 返回5表示非法的锁句柄。

| 参数 | 类型 | 描述 |
|------|------|------|
| id | NUMBER | 用户自定义锁标识符，范围为0到1073741823，避免使用区间2000000000到2147483647 |
| lockhandle | VARCHAR | 锁句柄，通过ALLOCATE_UNIQUE的出参lockhandle获取 |
| lockmode | NUMBER | 需要获取的锁模式，仅支持S_MODE（4）和X_MODE（6） |
| timeout | NUMBER | 获取锁的尝试秒数，0表示非阻塞立即返回，32767（MAXWAIT）表示无限等待 |
| release_on_commit | BOOLEAN | 是否在事务提交或回滚时释放锁。FALSE表示锁将保持持有直到显式释放或会话结束 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 使用锁句柄请求X_MODE锁
DECLARE
    lock_name   VARCHAR(200);
    lock_handle VARCHAR(200);
    res         INTEGER;
BEGIN
    lock_name := 'user_lock';
    DBMS_LOCK.ALLOCATE_UNIQUE(lockname => lock_name, lockhandle => lock_handle);
    res := DBMS_LOCK.REQUEST(lock_handle, DBMS_LOCK.X_MODE, 60, FALSE);
    DBMS_OUTPUT.PUT_LINE('result: ' || res);
END;
/

-- 使用锁ID请求S_MODE锁
DECLARE
    res INTEGER;
BEGIN
    res := DBMS_LOCK.REQUEST(1, DBMS_LOCK.S_MODE, 60, FALSE);
    DBMS_OUTPUT.PUT_LINE('result: ' || res);
END;
/
```

## CONVERT

```plsql
-- 方式一：使用锁ID
DBMS_LOCK.CONVERT(
    id        IN   NUMBER,
    lockmode  IN   NUMBER,
    timeout   IN   NUMBER   DEFAULT MAXWAIT)
RETURN NUMBER;

-- 方式二：使用锁句柄
DBMS_LOCK.CONVERT(
    lockhandle  IN   VARCHAR,
    lockmode    IN   NUMBER,
    timeout     IN   NUMBER   DEFAULT MAXWAIT)
RETURN NUMBER;
```

该存储函数用于将锁从一种模式转换成另一种模式。返回0表示执行成功，返回其他值则表示失败，具体原因取决于返回值：

- 返回1表示超时。
- 返回2表示死锁。
- 返回3表示参数错误。
- 返回4表示未持有指定的锁。
- 返回5表示非法的锁句柄。

| 参数 | 类型 | 描述 |
|------|------|------|
| id | NUMBER | 用户自定义锁标识符，范围为0到1073741823，避免使用区间2000000000到2147483647 |
| lockhandle | VARCHAR | 锁句柄，通过ALLOCATE_UNIQUE的出参lockhandle获取 |
| lockmode | NUMBER | 需要转换的目标锁模式，仅支持S_MODE（4）和X_MODE（6）之间的转换 |
| timeout | NUMBER | 转换锁模式的尝试秒数，0表示非阻塞立即返回，32767表示无限等待 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- S_MODE转换为X_MODE
DECLARE
    lock_name   VARCHAR(200);
    lock_handle VARCHAR(200);
    res         INTEGER;
BEGIN
    lock_name := 'user_lock';
    DBMS_LOCK.ALLOCATE_UNIQUE(lockname => lock_name, lockhandle => lock_handle);
    res := DBMS_LOCK.REQUEST(lock_handle, DBMS_LOCK.S_MODE, 60, FALSE);
    DBMS_OUTPUT.PUT_LINE('request result: ' || res);
    res := DBMS_LOCK.CONVERT(lock_handle, DBMS_LOCK.X_MODE, 60);
    DBMS_OUTPUT.PUT_LINE('convert result: ' || res);
END;
/
```

## RELEASE

```plsql
-- 方式一：使用锁ID
DBMS_LOCK.RELEASE(
    id  IN  NUMBER)
RETURN NUMBER;

-- 方式二：使用锁句柄
DBMS_LOCK.RELEASE(
    lockhandle  IN  VARCHAR)
RETURN NUMBER;
```

该存储函数用于显式释放先前使用REQUEST函数获取的锁。锁在会话结束时也会自动释放。返回0表示执行成功，返回其他值则表示失败，具体原因取决于返回值：

- 返回3表示参数错误。
- 返回4表示未持有指定的锁。
- 返回5表示非法的锁句柄。

| 参数 | 类型 | 描述 |
|------|------|------|
| id | NUMBER | 用户自定义锁标识符，范围为0到1073741823或2000000000到2147483647 |
| lockhandle | VARCHAR | 锁句柄，通过ALLOCATE_UNIQUE的出参lockhandle获取 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 使用锁句柄释放锁
DECLARE
    lock_name   VARCHAR(200);
    lock_handle VARCHAR(200);
    res         INTEGER;
BEGIN
    lock_name := 'user_lock';
    DBMS_LOCK.ALLOCATE_UNIQUE(lockname => lock_name, lockhandle => lock_handle);
    res := DBMS_LOCK.REQUEST(lock_handle, DBMS_LOCK.X_MODE, 60, FALSE);
    DBMS_OUTPUT.PUT_LINE('request result: ' || res);
    res := DBMS_LOCK.RELEASE(lock_handle);
    DBMS_OUTPUT.PUT_LINE('release result: ' || res);
END;
/
```

## SLEEP

```plsql
DBMS_LOCK.SLEEP(
    second IN NUMBER);
```

该子存储过程用于将系统按参数指定的秒数进行休眠，单位为秒。

执行该子存储过程后，系统将按参数指定的秒数进行休眠。如需在秒数结束前停止休眠，须在终端执行CTRL+C、Shutdown等指令进行强制退出。

| 参数 | 类型 | 描述 |
|------|------|------|
| second | NUMBER | 指定休眠的时间，不可输入空，数值范围必须为[0,21474836.47]，支持入参类型隐式转换。 |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 数值型
EXEC DBMS_LOCK.SLEEP(10);

-- 字符串型
EXEC DBMS_LOCK.SLEEP('10');

-- BIT型
DECLARE
vsql VARCHAR(256);
vbit BIT(8);
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbit := 5;
EXECUTE IMMEDIATE vsql USING vbit;
END;
/

-- 布尔型
DECLARE
vsql VARCHAR(256);
vbool BOOLEAN;
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbool := 'true';
EXECUTE IMMEDIATE vsql USING vbool;
END;
/
```
