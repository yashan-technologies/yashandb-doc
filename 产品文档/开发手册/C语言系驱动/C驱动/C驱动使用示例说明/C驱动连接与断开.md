
## 接口

| 函数                                                         | 说明                   |
| ------------------------------------------------------------ | ---------------------- |
| [yacAllocHandle](../C驱动接口说明/句柄和描述符函数/yacAllocHandle) | 为句柄分配空间         |
| [yacFreeHandle](../C驱动接口说明/句柄和描述符函数/yacFreeHandle) | 释放句柄空间           |
| [yacConnect](../C驱动接口说明/连接、授权和初始化函数/yacConnect) | 客户端发起连接请求     |
| [yacDisconnect](../C驱动接口说明/连接、授权和初始化函数/yacDisconnect) | 客户端发起断开连接请求 |

## 使用示例

```c
#include <string.h>
#include "yacli.h"


#define YAC_CALL(proc)                           \
    do {                                         \
        if ((YacResult)(proc) != YAC_SUCCESS) {  \
            return YAC_ERROR;                    \
        }                                        \
    } while (0)

void printError()
{
    YacInt32   code;
    YacChar    msg[1000];
    YacTextPos pos;

    yacGetDiagRec(&code, msg, 1000, NULL, NULL, 0, &pos);
    if (pos.line != 0) {
        printf("[%d:%d]", pos.line, pos.column);
    }
    printf("YAC-%05d %s\n", code, msg);
}

typedef struct {
    YacHandle env;
    YacHandle conn;
    YacHandle stmt;
} YacTestEnv;

YacTestEnv gTestEnv = { 0 };

// 连接数据库
YacResult testConnect()
{
    // 更改为实际数据库服务器的IP和端口
    const YacChar* gSrvStr = "192.168.1.2:1688";
    const YacChar* user = "sales";
    const YacChar* pwd = "sales";

    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
    YAC_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));

    return YAC_SUCCESS;
}
// 断开数据库
YacResult testDisConnect()
{
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, gTestEnv.stmt));
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testDisConnect());

    return YAC_SUCCESS;
}

int main()
{
    if (cexample() == YAC_SUCCESS) {
        printf("cexample succeed!\n");
    } else {
        printError();
        printf("cexample failed!\n");
    }
    return 0;
}
```

