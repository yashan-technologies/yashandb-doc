The DBMS_LOCK package provides a set of built-in stored procedures and functions for managing and controlling user lock resources. It allows users to explicitly define locks to achieve concurrent control of shared resources. It supports creating, acquiring, releasing locks, and setting lock wait times. It is commonly used to coordinate multiple sessions or transactions accessing critical resources.

> **Note**: 
>
> - REQUEST and CONVERT functions currently only support S_MODE and X_MODE lock modes.
>
> - Regular users need to obtain execute permission on DBMS_LOCK package before using lock functions.
>
> - Except for SLEEP, the other subprograms of the DBMS_LOCK advanced package can only be executed on the primary database/primary cluster in Standalone Primary-Standby or Primary-Standby Cluster Deployment.
>
> - Except for SLEEP, the other subprograms/function of the DBMS_LOCK package are not suitable for ISC Distributed Cluster Deployment.

## Predefined Constants

### Lock Mode Constants

| Constant Name | Type | Value | Description |
|---------------|------|-------|-------------|
| NL_MODE | NUMBER | 1 | Null lock mode |
| SS_MODE | NUMBER | 2 | Row share mode |
| SX_MODE | NUMBER | 3 | Row exclusive mode |
| S_MODE | NUMBER | 4 | Share mode, allows other sessions to read but not modify |
| SSX_MODE | NUMBER | 5 | Share exclusive mode |
| X_MODE | NUMBER | 6 | Exclusive mode, other sessions cannot read or modify |

> **Note**: 
>
> Currently, the REQUEST and CONVERT functions only support S_MODE (4) and X_MODE (6) modes. Other lock modes are only for compatibility.

### Timeout Constants

| Constant Name | Type | Value | Description |
|---------------|------|-------|-------------|
| MAXWAIT | NUMBER | 32767 | Maximum wait time (seconds), indicates infinite wait |

### Lock Mode Conversion Rules

| Current Holding Mode | Request S_MODE | Request X_MODE |
|----------------------|----------------|----------------|
| S_MODE | Success | Wait |
| X_MODE | Wait | Wait |

## ALLOCATE_UNIQUE

```plsql
DBMS_LOCK.ALLOCATE_UNIQUE(
    lockname         IN   VARCHAR,
    lockhandle       OUT  VARCHAR,
    expiration_secs  IN   NUMBER   DEFAULT 864000);
```

This stored subprogram allocates a unique lock identifier LOCKID for the specified lock name. The lock identifier enables applications to coordinate lock usage.

| Parameter | Type | Description |
|-----------|------|-------------|
| lockname | VARCHAR | The lock name used to assign a unique lock identifier, with a maximum length of 128 characters |
| lockhandle | VARCHAR | Returns the handle of the lock identifier. Use this handle to identify the lock resource in subsequent calls to REQUEST, CONVERT, and RELEASE. All sessions using the same lock name's lock handle reference the same lock |
| expiration_secs | NUMBER | Lock expiration time (seconds). After the last ALLOCATE_UNIQUE call, how many seconds to wait before allowing deletion of the lock. Default value is 864000 (10 days) |

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

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
-- Method 1: Using lock ID
DBMS_LOCK.REQUEST(
    id                 IN  NUMBER,
    lockmode           IN  NUMBER  DEFAULT X_MODE,
    timeout            IN  NUMBER  DEFAULT MAXWAIT,
    release_on_commit  IN  BOOLEAN  DEFAULT FALSE)
RETURN NUMBER;

-- Method 2: Using lock handle
DBMS_LOCK.REQUEST(
    lockhandle         IN  VARCHAR,
    lockmode           IN  NUMBER  DEFAULT X_MODE,
    timeout            IN  NUMBER  DEFAULT MAXWAIT,
    release_on_commit  IN  BOOLEAN  DEFAULT FALSE)
RETURN NUMBER;
```

This stored function acquires a lock in the specified mode. Returns 0 for success, other values indicate failure. The specific reason depends on the return value:

- Returns 1 indicates timeout.
- Returns 2 indicates dead lock.
- Returns 3 indicates invalid parameter.
- Returns 4 indicates the specified lock is already held.
- Returns 5 indicates invalid lock handle.

| Parameter | Type | Description |
|-----------|------|-------------|
| id | NUMBER | User-defined lock identifier, range is 0 to 1073741823, avoid using range 2000000000 to 2147483647 |
| lockhandle | VARCHAR | Lock handle obtained from the output parameter lockhandle of ALLOCATE_UNIQUE |
| lockmode | NUMBER | The lock mode to acquire. Only S_MODE (4) and X_MODE (6) are supported |
| timeout | NUMBER | The number of seconds to attempt acquiring the lock. 0 means non-blocking immediate return. 32767 (MAXWAIT) means infinite wait |
| release_on_commit | BOOLEAN | Whether to release the lock on transaction commit or rollback. FALSE means the lock will remain held until explicitly released or the session ends |

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```plsql
-- Request X_MODE lock using lock handle
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

-- Request S_MODE lock using lock ID
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
-- Method 1: Using lock ID
DBMS_LOCK.CONVERT(
    id        IN   NUMBER,
    lockmode  IN   NUMBER,
    timeout   IN   NUMBER   DEFAULT MAXWAIT)
RETURN NUMBER;

-- Method 2: Using lock handle
DBMS_LOCK.CONVERT(
    lockhandle  IN   VARCHAR,
    lockmode    IN   NUMBER,
    timeout     IN   NUMBER   DEFAULT MAXWAIT)
RETURN NUMBER;
```

This stored function converts a lock from one mode to another. Returns 0 for success, other values indicate failure. The specific reason depends on the return value:

- Returns 1 indicates timeout.
- Returns 2 indicates dead lock.
- Returns 3 indicates invalid parameter.
- Returns 4 indicates the specified lock is not held.
- Returns 5 indicates invalid lock handle.

| Parameter | Type | Description |
|-----------|------|-------------|
| id | NUMBER | User-defined lock identifier, range is 0 to 1073741823, avoid using range 2000000000 to 2147483647 |
| lockhandle | VARCHAR | Lock handle obtained from the output parameter lockhandle of ALLOCATE_UNIQUE |
| lockmode | NUMBER | The target lock mode to convert to. Only conversion between S_MODE (4) and X_MODE (6) is supported |
| timeout | NUMBER | The number of seconds to attempt converting the lock mode. 0 means non-blocking immediate return. 32767 means infinite wait |

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```plsql
-- Convert from S_MODE to X_MODE
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
-- Method 1: Using lock ID
DBMS_LOCK.RELEASE(
    id  IN  NUMBER)
RETURN NUMBER;

-- Method 2: Using lock handle
DBMS_LOCK.RELEASE(
    lockhandle  IN  VARCHAR)
RETURN NUMBER;
```

This stored function explicitly releases a lock previously acquired using the REQUEST function. Locks are also automatically released when the session ends. Returns 0 for success, other values indicate failure. The specific reason depends on the return value:

- Returns 3 indicates invalid parameter.
- Returns 4 indicates the specified lock is not held.
- Returns 5 indicates invalid lock handle.

| Parameter | Type | Description |
|-----------|------|-------------|
| id | NUMBER | User-defined lock identifier, range is 0 to 1073741823 or 2000000000 to 2147483647 |
| lockhandle | VARCHAR | Lock handle obtained from the output parameter lockhandle of ALLOCATE_UNIQUE |

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```plsql
-- Release lock using lock handle
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

This stored subprogram causes the system to sleep for the specified number of seconds.

After executing this stored subprogram, the system will sleep for the specified number of seconds. To stop the sleep before the specified time expires, you must force exit by executing commands such as CTRL+C or Shutdown in the terminal.

| Parameter | Type | Description |
|------|------|------|
| second | NUMBER | Specifies the sleep time, cannot be empty, value range must be [0,21474836.47], supports implicit type conversion for input parameters. |

***Example*** for Standalone Deployment and YAC/Distributed Cluster Deployment

```plsql
-- Numeric type
EXEC DBMS_LOCK.SLEEP(10);

-- String type
EXEC DBMS_LOCK.SLEEP('10');

-- BIT type
DECLARE
vsql VARCHAR(256);
vbit BIT(8);
BEGIN
vsql := 'exec DBMS_LOCK.SLEEP (?)';
vbit := 5;
EXECUTE IMMEDIATE vsql USING vbit;
END;
/

-- BOOLEAN type
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