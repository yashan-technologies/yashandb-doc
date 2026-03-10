## 接口

| 函数                                                         | 说明                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| [yacPrepare](../C驱动接口说明/语句函数/yacPrepare)           | 客户端发起预编译SQL的请求                           |
| [yacBindParameter](../C驱动接口说明/绑定函数/yacBindParameter) | 客户端对指定参数的属性绑定赋值                      |
| [yacBindParameterByName](../C驱动接口说明/绑定函数/yacBindParameterByName) | 客户端按参数名对指定参数的属性绑定赋值              |
| [yacExecute](../C驱动接口说明/语句函数/yacExecute)           | 客户端发起执行yacPrepare预编译的SQL的请求           |
| [yacDirectExecute](../C驱动接口说明/语句函数/yacDirectExecute) | 客户端发起执行不需绑定数据或绑定参数数据的SQL的请求 |
| [yacBindColumn](../C驱动接口说明/绑定函数/yacBindColumn)     | 客户端对指定列的属性绑定赋值                        |

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

// 单行绑定导入数据
YacResult testSingleBind()
{
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yacli", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yacli(col1 int, col2 varchar(200))", YAC_NULL_TERM_STR));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "insert into yacli values(?, ?)", YAC_NULL_TERM_STR));

    YacInt32 inputInt;
    YacChar  inputVarchar[200];
    YacInt32 indicator;
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_INTEGER, &inputInt, sizeof(YacInt32), sizeof(YacInt32), NULL));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 2, YAC_PARAM_INPUT, YAC_SQLT_VARCHAR2, inputVarchar, 200, 200, &indicator));

    /* insert data
    99,  '0123456789'
    100, '9876543210'
    */
    inputInt = 99;
    memcpy(inputVarchar, "0123456789", 10);
    indicator = 10;
    YAC_CALL(yacExecute(gTestEnv.stmt));

    inputInt = 100;
    memcpy(inputVarchar, "98765", 10);
    indicator = 5;
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YAC_CALL(yacCommit(gTestEnv.conn));

    return YAC_SUCCESS;
}

// 单行取数据
YacResult testSingleFetch()
{
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select * from yacli", YAC_NULL_TERM_STR));

    YacInt32 outputInt;
    YacChar  outputVarchar[200];
    YacInt32 indicator;
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_INTEGER, &outputInt, sizeof(YacInt32), NULL));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_VARCHAR2, outputVarchar, 200, &indicator));

    /* fetch data
    99,  '0123456789'
    100, '9876543210'
    */
    YacUint32 fetchedRows;
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (fetchedRows != 1 || outputInt != 99 || memcmp(outputVarchar, "0123456789", indicator) != 0 || indicator != 10)
    {
        return YAC_ERROR;
    }

    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (fetchedRows != 1 || outputInt != 100 || memcmp(outputVarchar, "9876543210", indicator) != 0 || indicator != 5)
    {
        return YAC_ERROR;
    }

    return YAC_SUCCESS;
}

// 批量绑定导入数据
YacResult testBatchBind()
{
    YacUint32 paramSet = 10;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_PARAMSET_SIZE, &paramSet, sizeof(YacUint32)));
    // 设置批量绑定行数为10

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yacli", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yacli(col1 int, col2 varchar(200))", YAC_NULL_TERM_STR));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "insert into yacli values(?, ?)", YAC_NULL_TERM_STR));

    YacInt32 inputInt[10];
    YacChar  inputVarchar[10][200];
    YacInt32 indicator[10];
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_INTEGER, inputInt, sizeof(YacInt32), sizeof(YacInt32), NULL));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 2, YAC_PARAM_INPUT, YAC_SQLT_VARCHAR2, (YacPointer)inputVarchar, 200, 200, indicator));

    /*insert data
    batch insert 1
    1,   '0123456789'
    2,   '0123456789'
    3,   '0123456789'
    4,   '0123456789'
    5,   '0123456789'
    6,   '0123456789'
    7,   '0123456789'
    8,   '0123456789'
    9,   '0123456789'
    10,  '0123456789'
    batch insert 2
    11,  '9876543210'
    12,  '9876543210'
    13,  '9876543210'
    14,  '9876543210'
    15,  '9876543210'
    16,  '9876543210'
    17,  '9876543210'
    18,  '9876543210'
    19,  '9876543210'
    20,  '9876543210'
    */
    for (YacInt32 i = 0; i < 10; i++)
    {
        inputInt[i] = i + 1;
        memcpy(inputVarchar[i], "0123456789", 10);
        indicator[i] = 10;
    }
    YAC_CALL(yacExecute(gTestEnv.stmt));

    for (YacInt32 i = 0; i < 10; i++)
    {
        inputInt[i] = i + 11;
        memcpy(inputVarchar[i], "9876543210", 10);
        indicator[i] = 10;
    }
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YAC_CALL(yacCommit(gTestEnv.conn));

    paramSet = 1;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_PARAMSET_SIZE, &paramSet, sizeof(YacUint32)));
    // 设置批量绑定行数为1

    return YAC_SUCCESS;
}

// 批量取数据
YacResult testBatchFetch()
{
    YacUint32 rowSet = 10;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_ROWSET_SIZE, &rowSet, sizeof(YacUint32)));
    // 设置批量取数据绑定行数为10

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select * from yacli", YAC_NULL_TERM_STR));

    YacInt32 outputInt[10];
    YacChar  outputVarchar[10][200];
    YacInt32 indicator[10];
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_INTEGER, outputInt, sizeof(YacInt32), NULL));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_VARCHAR2, (YacPointer)outputVarchar, 200, indicator));

    /*fetch data
    batch fetch 1
    1,   '0123456789'
    2,   '0123456789'
    3,   '0123456789'
    4,   '0123456789'
    5,   '0123456789'
    6,   '0123456789'
    7,   '0123456789'
    8,   '0123456789'
    9,   '0123456789'
    10,  '0123456789'
    batch fetch 2
    11,  '9876543210'
    12,  '9876543210'
    13,  '9876543210'
    14,  '9876543210'
    15,  '9876543210'
    16,  '9876543210'
    17,  '9876543210'
    18,  '9876543210'
    19,  '9876543210'
    20,  '9876543210'
    */
    YacUint32 fetchedRows;
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (fetchedRows != 10)
    {
        return YAC_ERROR;
    }
    for (YacInt32 i = 0; i < 10; i++)
    {
        if (outputInt[i] != i + 1 || memcmp(outputVarchar[i], "0123456789", indicator[i]) != 0 || indicator[i] != 10)
        {
            return YAC_ERROR;
        }
    }

    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (fetchedRows != 10)
    {
        return YAC_ERROR;
    }
    for (YacInt32 i = 0; i < 10; i++)
    {
        if (outputInt[i] != i + 11 || memcmp(outputVarchar[i], "9876543210", indicator[i]) != 0 || indicator[i] != 10)
        {
            return YAC_ERROR;
        }
    }

    rowSet = 1;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_ROWSET_SIZE, &rowSet, sizeof(YacUint32)));
    // 设置批量取数据绑定行数为1

    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testSingleBind());
    YAC_CALL(testSingleFetch());
    YAC_CALL(testBatchBind());
    YAC_CALL(testBatchFetch());
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
