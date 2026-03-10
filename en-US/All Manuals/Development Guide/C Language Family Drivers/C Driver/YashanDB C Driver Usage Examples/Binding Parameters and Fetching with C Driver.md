## Interface

|Function |Description |
| ------------------------------------------------------------ | --------------------------------------------------- |
| [yacPrepare](../YashanDB C Driver Interfaces/Statement Functions/yacPrepare)          | The client initiates a request to prepare a SQL statement.                           |
| [yacBindParameter](../YashanDB C Driver Interfaces/Binding Functions/yacBindParameter) | The client binds values to the attributes of specified parameters.                      |
| [yacBindParameterByName](../YashanDB C Driver Interfaces/Binding Functions/yacBindParameterByName) | The client binds values to the attributes of specified parameters by name.              |
| [yacExecute](../YashanDB C Driver Interfaces/Statement Functions/yacExecute)          | The client initiates a request to execute a prepared SQL statement.                   |
| [yacDirectExecute](../YashanDB C Driver Interfaces/Statement Functions/yacDirectExecute) | The client initiates a request to execute a SQL statement without needing to bind data or bind parameter data. |
| [yacBindColumn](../YashanDB C Driver Interfaces/Binding Functions/yacBindColumn)     | The client binds values to the attributes of specified columns.                        |

## Usage Example

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

// Connect to the database
YacResult testConnect()
{
    // Change to the actual database server IP and port
    const YacChar* gSrvStr = "192.168.1.2:1688";
    const YacChar* user = "sales";
    const YacChar* pwd = "sales";

    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
    YAC_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));

    return YAC_SUCCESS;
}
// Disconnect from the database
YacResult testDisConnect()
{
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, gTestEnv.stmt));
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

// Single row bind import data
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

// Single row fetch data
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

// Batch bind import data
YacResult testBatchBind()
{
    YacUint32 paramSet = 10;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_PARAMSET_SIZE, &paramSet, sizeof(YacUint32)));
    // Set batch bind row count to 10

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
    // Set batch bind row count to 1

    return YAC_SUCCESS;
}

// Batch fetch data
YacResult testBatchFetch()
{
    YacUint32 rowSet = 10;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_ROWSET_SIZE, &rowSet, sizeof(YacUint32)));
    // Set batch fetch data binding row count to 10

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
    // Set batch fetch data binding row count to 1

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
