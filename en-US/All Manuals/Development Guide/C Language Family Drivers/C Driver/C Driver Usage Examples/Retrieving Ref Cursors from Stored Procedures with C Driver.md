## Operating Procedure

REFCURSOR is a special data type that allows the result set of a query in the database to be encapsulated as a cursor object and returned to the caller through a stored procedure. It is suitable for scenarios that require handling large volumes of data or processing result sets row by row. When using a C driver program to connect to the database and execute a stored procedure, the stored procedure can return one or more result sets, which will include one or more REFCURSOR type cursors.
Steps to obtain the REFCURSOR result set using a C driver program:

1. Establish a database connection: Use the connection method provided by the database driver to establish a connection to the database.

2. Prepare and execute the stored procedure: Prepare and execute the SQL statement that contains the call to the stored procedure.
3. Bind the REFCURSOR variable: Before executing the stored procedure, define a variable to receive the REFCURSOR type result set. Use the binding function to associate the REFCURSOR variable with the output parameters of the stored procedure.
4. Execute the stored procedure: Use the execution method of the database driver to run the stored procedure.
5. Retrieve the result set: Use the methods provided by the database driver to obtain the REFCURSOR result set.
6. Close the cursor and release resources: After processing the result set, close the cursor and release the associated resources.
## Usage Example

## ```c

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

YacResult testConnect()
YacResult testConnect()
{
    const YacChar* gSrvStr = "192.168.1.2:1688";
    const YacChar* gSrvStr = "192.168.1.2:1688";
    const YacChar* user = "sales";
    const YacChar* pwd = "sales";

    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
    YAC_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_STMT, gTestEnv.conn, &gTestEnv.stmt));

    return YAC_SUCCESS;
}
YacResult testDisConnect()
YacResult testDisConnect()
{
    YAC_CALL(yacFreeHandle(YAC_HANDLE_STMT, gTestEnv.stmt));
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

YacResult testSelectCursor()
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

    YacInt32 value1;
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
