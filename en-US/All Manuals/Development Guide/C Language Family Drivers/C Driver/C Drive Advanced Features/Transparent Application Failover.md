TAF is a client functionality that minimizes disruptions to end user applications when database connections fail due to instance or network issues.

## TAF Configuration

### Configuration Parameters

Parameter names and values are case-insensitive. The parameter details are as follows:

|Parameter Name |Parameter Description | 
| ---------------- |------------------------------------------------------------------| 
| FAILOVER          | Whether to enable TAF, the parameter value can be ON or OFF, default is OFF.<br />* ON: Indicates that TAF is enabled, and the application will automatically connect to other configured database nodes when a failure occurs.<br />* OFF: Indicates that TAF is disabled.       | 
| FAILOVER_TYPE     | The type of Failover, supports NONE, SESSION, and SELECT, default is NONE.<br />* NONE: Indicates that Failover is not used.<br />* SESSION: Indicates that a new session is automatically created when the user's connection is lost.<br />* SELECT: Indicates that on the basis of SESSION type, the SELECT statement that failed due to the fault will be re-executed after reconnection (at this time, the SELECT statement cannot be a query statement with lob type binding parameters), but does not support secondary operations on objects already acquired before the switch. | 
| FAILOVER_METHOD   | The method of Failover, this value will determine the speed of Failover from the primary node to the standby node.<br/>* BASIC: Indicates that a connection is established again during Failover.                | 
| FAILOVER_RETRIES  | This parameter specifies the number of attempts to connect after failover.<br />If the FAILOVER_DELAY parameter is configured, FAILOVER_RETRIES defaults to 5.<br/>This parameter will be invalidated after TAF callback function registration.                 |
| FAILOVER_DELAY    | This parameter specifies the time to wait between connection attempts (in seconds).<br />If the FAILOVER_RETRIES parameter is configured, FAILOVER_DELAY defaults to 1.<br/>This parameter will be invalidated after TAF callback function registration.               |

The parameter configuration format is:

```C
url?key1=value1&key2=value2
```

The format and configuration of the url is the same as the url (IN) parameter of the [yacConnect](../C Driver Interfaces/Connection & Authorization & Initialization Functions/yacConnect) function.

### Configuration Methods

#### Method One

1. Navigate to the $YASDB_HOME/client path, and add the configuration in the yasc_service.ini file. The configuration example is as follows:
   ```C
   REMOTE = 192.168.1.2:1688, 192.168.1.3:1688, 192.168.1.4:1688 ? FAILOVER = ON & FAILOVER_TYPE = SESSION & FAILOVER_METHOD = BASIC & FAILOVER_RETRIES = 10 & FAILOVER_DELAY = 2
   ```
2. Connect using the REMOTE data source:
   ```c
   const YacChar* gSrvStr = "REMOTE";
   YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
   ```

#### Method Two

Directly configure in the connection URL:
```c
const YacChar* gSrvStr = "192.168.1.2:1688, 192.168.1.3:1688; 192.168.1.3:1688, 192.168.1.4:1688; 192.168.1.5:1688, 192.168.1.6:1688 ? FAILOVER = ON & FAILOVER_TYPE = SESSION & FAILOVER_METHOD = BASIC & FAILOVER_RETRIES = 10 & FAILOVER_DELAY = 2";
   YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
```

## TAF Interface and Structure

### Data Type Description

|Data Type |Valid Values |Description |
| -------------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| YacTafResult        | YAC_TAF_SUCCESS: TAF callback function returns success<br />YAC_TAF_RETRY: TAF callback function returns retry, this value only makes sense when YacTafEvent is YAC_TAF_EVENT_ERROR<br />YAC_TAF_ERROR: TAF callback function returns failure, returning this value only indicates that the status does not affect the normal TAF process | TAF callback function return value                       |
| YacTafType          | YAC_TAF_TYPE_NONE: No transparent failover <br />YAC_TAF_TYPE_SESSION: SESSION type failover <br />YAC_TAF_TYPE_SELECT: SELECT type failover  | TAF type                                            |
| YacTafEvent         | YAC_TAF_EVENT_BEGIN: Transparent failover begins<br />YAC_TAF_EVENT_END: Transparent failover ends, successfully resumes connection<br />YAC_TAF_EVENT_ABORT: Transparent failover fails, current connection is disconnected<br />YAC_TAF_EVENT_ERROR: Transparent failover fails, can choose whether to retry, optionally return YAC_TAF_RETRY  | TAF time points                                   |
| YacTafResult        | -                                                                                               | TAF callback function definition                        |
| YacTafCallbackStruct| -                                                                                               | Context for TAF callback function registration          |

### Data Type Structures

```c
/* YACLI TAF CallBackSet */
typedef enum EnYacTafResult {
    YAC_TAF_SUCCESS = 0,
    YAC_TAF_RETRY = 25410,
    YAC_TAF_ERROR = -1,
} YacTafResult;

typedef enum EnYacTafType {
    YAC_TAF_TYPE_NONE = 0,
    YAC_TAF_TYPE_SESSION = 1,
    YAC_TAF_TYPE_SELECT = 2,
} YacTafType;

typedef enum EnYacTafEvent {
    YAC_TAF_EVENT_BEGIN = 0,
    YAC_TAF_EVENT_END = 1,
    YAC_TAF_EVENT_ABORT = 2,
    YAC_TAF_EVENT_ERROR = 3,
} YacTafEvent;

typedef YacTafResult (*YacTafCallback)(YacHandle hConn, YacHandle hEnv, YacPointer tafCtx, YacTafType tafType, YacTafEvent tafEvent);

typedef struct {
    YacTafCallback tafCallbackFunc;
    YacPointer     tafCtx;
} YacTafCallbackStruct;
```

## TAF Usage Example

### Regular TAF Example

```c
YacResult testConnect()
{
    const YacChar* gSrvStr = "192.168.1.2:1688?FAILOVER = ON & FAILOVER_TYPE = SESSION & FAILOVER_RETRIES = 10 & FAILOVER_DELAY = 1";
    const YacChar* user = "sys";
    const YacChar* pwd = "password";
 
    YacHandle env;
    YacHandle conn;
    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, env, &conn));
    YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    printf("connected!");
 
    YacHandle stmt;
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, conn, &stmt));
    // If there is a network interruption here, the next conn request will trigger TAF reconnection and subsequent requests will succeed
    YAC_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR));
 
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, stmt));
    yacDisconnect(conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, env));
 
    return YAC_SUCCESS;
}
```

### TAF Callback Function Usage Example

```c
YacTafResult tafCb(YacHandle hConn, YacHandle hEnv, YacPointer tafCtx, YacTafType tafType, YacTafEvent tafEvent) 
{
    // This callback function controls that the number of TAF retries does not exceed 2, including:
    // The first TAF retry attempt + returning YAC_TAF_RETRY after encountering YAC_TAF_EVENT_ERROR specifies retry
    YacUint32* intRetry = tafCtx;
    if (tafEvent == YAC_TAF_EVENT_BEGIN) {
        *intRetry = 0;
    }

    printf("TAF callback: %d, %d\n", tafType, tafEvent);
    if (tafEvent == YAC_TAF_EVENT_ERROR && *intRetry < 1) {
        (*intRetry)++;
        return YAC_TAF_RETRY;
    }
    return YAC_TAF_SUCCESS;
}

YacResult testConnect1WithCallBack()
{
    const YacChar* gSrvStr = "192.168.1.2:1688?failover = on & FAILOVER_TYPE = SESSION";
    const YacChar* user = "sys";
    const YacChar* pwd = "password";

    YacHandle env;
    YacHandle conn;
    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, env, &conn));

    YacChar* buf = malloc(sizeof(YacUint32));
    if (buf == NULL) {
        return YAC_ERROR;
    }
    YacUint32* intRetry = (YacUint32*)buf;
    YacTafCallbackStruct cbStruct = { .tafCtx = intRetry,.tafCallbackFunc = tafCb };

    YAC_CALL(yacSetConnAttr(conn, YAC_ATTR_TAF_CALLBACK, (YacVoid*)&cbStruct, sizeof(YacTafCallbackStruct)));
    YAC_CALL(yacConnect(conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    printf("connected!");

    YacHandle stmt;
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, conn, &stmt));
    // If there is a network interruption here, the next conn request will trigger TAF reconnection and subsequent requests will succeed
    YAC_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(stmt, "select 1 from dual", YAC_NULL_TERM_STR));
    
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, stmt));
    yacDisconnect(conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, env));
    free(buf);

    return YAC_SUCCESS;
}
```
