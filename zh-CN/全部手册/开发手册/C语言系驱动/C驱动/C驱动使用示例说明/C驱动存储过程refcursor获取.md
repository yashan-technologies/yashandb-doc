## 操作过程

REFCURSOR是一种特殊的数据类型，它允许在数据库中查询的结果集被封装为一个游标对象并通过存储过程返回给调用者，适用于需要处理大量数据或需要逐行处理结果集的场景。
在使用C驱动程序连接到数据库并执行一个存储过程时，存储过程会返回一个或多个结果集，结果集中将包含一个或多个REFCURSOR类型的游标。

使用C驱动程序获取REFCURSOR的结果集：

1. 建立数据库连接：使用数据库驱动程序提供的连接方法，建立与数据库的连接。
2. 准备并执行存储过程：准备并执行包含存储过程调用的SQL语句。
3. 绑定REFCURSOR变量：在执行存储过程之前，定义一个变量来接收REFCURSOR类型的结果集。使用绑定函数将REFCURSOR变量与存储过程的输出参数关联起来。
4. 执行存储过程：使用数据库驱动程序的执行方法，执行存储过程。
5. 获取结果集：使用数据库驱动程序提供的方法，获取REFCURSOR的结果集。
6. 关闭游标和释放资源：在处理完结果集后，关闭游标并释放相关的资源。

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

// 单行获取cursor数据。
YacResult testSelectCursor()
{
    YacHandle stmt = NULL;
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &stmt));

    const YacChar* sql1 = "drop table if exists t2_cursor";
    const YacChar* sql2 = "create table t2_cursor (col1 int,col2 varchar(10))";
    const YacChar* sql3 = "insert into t2_cursor values (1,'9999')";
    const YacChar* sql4 = "insert into t2_cursor values (10,'99999')";
    YacInt32       sqlLen1 = (YacInt32)strlen(sql1);
    YacInt32       sqlLen2 = (YacInt32)strlen(sql2);
    YacInt32       sqlLen3 = (YacInt32)strlen(sql3);
    YacInt32       sqlLen4 = (YacInt32)strlen(sql4);

    YAC_CALL(yacDirectExecute(stmt, sql1, sqlLen1));
    YAC_CALL(yacDirectExecute(stmt, sql2, sqlLen2));
    YAC_CALL(yacDirectExecute(stmt, sql3, sqlLen3));
    YAC_CALL(yacDirectExecute(stmt, sql4, sqlLen4));

    YacChar* functionSql = (YacChar*)
        "create or replace function selectcursor(c1 in int) "
        "return sys_refcursor as "
        "c2 sys_refcursor; "
        "begin "
        "  open c2 for "
        "    select * from t2_cursor where col1 = c1; "
        "  return c2; "
        "end;";

    YacHandle cursor1;
    YacInt32  proLen = (YacInt32)strlen(functionSql);
    YAC_CALL(yacDirectExecute(stmt, functionSql, proLen));

    YacChar* selectSql = "select selectcursor(1) from dual";
    YAC_CALL(yacDirectExecute(stmt, selectSql, (YacInt32)strlen(selectSql)));
    YAC_CALL(yacBindColumn(stmt, 0, YAC_SQLT_CURSOR, (YacPointer)&cursor1, sizeof(YacHandle), NULL));

    YacUint32 cursorRows = 0;
    YAC_CALL(yacFetch(stmt, &cursorRows));

    // cursor的数据
    YacInt32 value1;
    YacInt32 value2;
    YAC_CALL(yacBindColumn(cursor1, 0, YAC_SQLT_INTEGER, &value1, sizeof(YacInt32), NULL));
    YAC_CALL(yacBindColumn(cursor1, 1, YAC_SQLT_INTEGER, &value2, sizeof(YacInt32), NULL));

    YacUint32 rows = 0;
    YAC_CALL(yacFetch(cursor1, &rows));

    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, stmt));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, cursor1));

    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testSelectCursor());
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
