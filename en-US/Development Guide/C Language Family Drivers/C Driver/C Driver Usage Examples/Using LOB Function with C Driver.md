## Interface

|  Function |   Description |
| ------------------------------------------------------------ | ------------------------------------------- |
| [yacLobDescAlloc2](../C Driver Interfaces/LOB Functions/yacLobDescAlloc2.md) | Allocates space for loblocator in LOB protocol (Recommended) |
| [yacLobFreeTemporary](../C Driver Interfaces/LOB Functions/yacLobFreeTemporary.md) | Notifies the server to release the temporary LOB in LOB protocol |
| [yacLobDescFree2](../C Driver Interfaces/LOB Functions/yacLobDescFree2.md) | Releases space for loblocator in LOB protocol (Recommended) |
| [yacLobRead2](../C Driver Interfaces/LOB Functions/yacLobRead2.md)  | Reads LOB data from a specific location in LOB protocol (Recommended) |
| [yacLobWrite2](../C Driver Interfaces/LOB Functions/yacLobWrite2.md)  | Inserts data into LOB from a specific location in LOB protocol (Recommended) |
| [yacLobWriteAppend](../C Driver Interfaces/LOB Functions/yacLobWriteAppend.md) | Appends data to the end of the LOB in LOB protocol |
| [yacLobTrim](../C Driver Interfaces/LOB Functions/yacLobTrim.md)  | Trims the LOB data in LOB protocol      |

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


// Single row bind LOB import data
YacResult testSingleBindLob()
{
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yacli", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yacli(col1 clob, col2 blob)", YAC_NULL_TERM_STR));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "insert into yacli values(?, ?)", YAC_NULL_TERM_STR));

    YacLobLocator* lobLocator1;
    YacLobLocator* lobLocator2;

    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_CLOB, &lobLocator1, 0, 0, NULL));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 2, YAC_PARAM_INPUT, YAC_SQLT_BLOB, &lobLocator2, 0, 0, NULL));

    /* insert lob data
    '01234567899876543210',  'aaaaaaaaaabbbbbbbbbb'
    '98765432100123456789',  'bbbbbbbbbbaaaaaaaaaa'
    */

    // first row insert
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator1));
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator2));
    YAC_CALL(yacLobCreateTemporary(gTestEnv.conn, lobLocator1));
    YAC_CALL(yacLobCreateTemporary(gTestEnv.conn, lobLocator2));
    YacChar buf1[100] = "01234567899876543210";
    YacUint64 byteSize1 = 10;
    YacUint64 charSize1 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, 1, (YacUint8*)buf1, 10));
    byteSize1 = 10;
    charSize1 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, 11, (YacUint8*)(buf1 + 10), 10));
    YacChar buf2[100] = "aaaaaaaaaabbbbbbbbbb";
    YacUint64 byteSize2 = 10;
    YacUint64 charSize2 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, 1, (YacUint8*)buf2, 10));
    byteSize2 = 10;
    charSize2 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, 11, (YacUint8*)(buf2 + 10), 10));
    YAC_CALL(yacExecute(gTestEnv.stmt));
    YAC_CALL(yacLobFreeTemporary(gTestEnv.conn, lobLocator1));
    YAC_CALL(yacLobFreeTemporary(gTestEnv.conn, lobLocator2));
    YAC_CALL(yacLobDescFree2(lobLocator1));
    YAC_CALL(yacLobDescFree2(lobLocator2));

    // second row insert
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator1));
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator2));
    YAC_CALL(yacLobCreateTemporary(gTestEnv.conn, lobLocator1));
    YAC_CALL(yacLobCreateTemporary(gTestEnv.conn, lobLocator2));
    YacChar buf3[100] = "98765432100123456789";
    byteSize1 = 10;
    charSize1 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, 1, (YacUint8*)buf3, 10));
    byteSize1 = 10;
    charSize1 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, 11, (YacUint8*)(buf3 + 10), 10));
    YacChar buf4[100] = "bbbbbbbbbbaaaaaaaaaa";
    byteSize2 = 10;
    charSize2 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, 1, (YacUint8*)buf4, 10));
    byteSize2 = 10;
    charSize2 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, 11, (YacUint8*)(buf4 + 10), 10));
    YAC_CALL(yacExecute(gTestEnv.stmt));
    YAC_CALL(yacLobFreeTemporary(gTestEnv.conn, lobLocator1));
    YAC_CALL(yacLobFreeTemporary(gTestEnv.conn, lobLocator2));
    YAC_CALL(yacLobDescFree2(lobLocator1));
    YAC_CALL(yacLobDescFree2(lobLocator2));

    YAC_CALL(yacCommit(gTestEnv.conn));

    return YAC_SUCCESS;
}


// Single row fetch LOB data
YacResult testSingleFetchLob()
{
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select * from yacli", YAC_NULL_TERM_STR));

    YacLobLocator* lobLocator1;
    YacLobLocator* lobLocator2;

    YacInt32 indicator[2];
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_CLOB, &lobLocator1, 0, &indicator[0]));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_BLOB, &lobLocator2, 0, &indicator[1]));

    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator1));
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator2));

    /* fetch lob data
    '01234567899876543210',  'aaaaaaaaaabbbbbbbbbb'
    '98765432100123456789',  'bbbbbbbbbbaaaaaaaaaa'
    */

    YacUint32 fetchedRows;
    YacChar buf1[100];
    YacUint64 byteSize1 = 100;
    YacUint64 charSize1 = 100;
    YacChar buf2[100];
    YacUint64 byteSize2 = 100;
    YacUint64 charSize2 = 100;
    YacUint64 offset = 1;

    // first row fetch
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (indicator[0] == YAC_NULL_DATA || indicator[1] == YAC_NULL_DATA) {
        return YAC_ERROR;
    }
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, offset, (YacUint8*)buf1, 100));
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, offset, (YacUint8*)buf2, 100));
    if (fetchedRows != 1 || memcmp(buf1, "01234567899876543210", byteSize1) != 0 || memcmp(buf2, "aaaaaaaaaabbbbbbbbbb", byteSize2) != 0)
    {
        return YAC_ERROR;
    }

    // second row fetch
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    byteSize1 = 100;
    charSize1 = 100;
    byteSize2 = 100;
    charSize2 = 100;
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, offset, (YacUint8*)buf1, 100));
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, offset, (YacUint8*)buf2, 100));
    if (fetchedRows != 1 || memcmp(buf1, "98765432100123456789", byteSize1) != 0 || memcmp(buf2, "bbbbbbbbbbaaaaaaaaaa", byteSize2) != 0)
    {
        return YAC_ERROR;
    }

    YAC_CALL(yacLobDescFree2(lobLocator1));
    YAC_CALL(yacLobDescFree2(lobLocator2));

    return YAC_SUCCESS;
}


// Batch bind import LOB data (using continuous memory import)
#define PARAM_SET_SIZE 10
#define MAX_LOB_SIZE (80 << 20) // 80M
YacResult testMultiBindLobByContinuousMemory()
{
    YacUint32 paramSet = PARAM_SET_SIZE;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_PARAMSET_SIZE, &paramSet, sizeof(YacUint32)));

    // Set batch binding row count to 10

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yacli", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yacli(col1 clob, col2 blob)", YAC_NULL_TERM_STR));

    YAC_CALL(yacPrepare(gTestEnv.stmt, "insert into yacli values(?, ?)", YAC_NULL_TERM_STR));

    YacChar* bufClob = (YacChar*)malloc(MAX_LOB_SIZE * PARAM_SET_SIZE);
    if (bufClob == NULL) {
        return YAC_ERROR;
    }

    YacChar* bufBlob = (YacChar*)malloc(MAX_LOB_SIZE * PARAM_SET_SIZE);
    if (bufBlob == NULL) {
        return YAC_ERROR;
    }

    YacInt32 indicator1[PARAM_SET_SIZE] = { 0 };
    YacInt32 indicator2[PARAM_SET_SIZE] = { 0 };

    YacChar* buf1 = bufClob + MAX_LOB_SIZE * 1;
    YacChar* buf2 = bufClob + MAX_LOB_SIZE * 2;
    YacChar* buf3 = bufClob + MAX_LOB_SIZE * 3;
    YacChar* buf4 = bufClob + MAX_LOB_SIZE * 4;

    for (YacUint32 i = 0; i < PARAM_SET_SIZE; i++) {
        memset(bufClob + MAX_LOB_SIZE * i, 'a' + i, i + 1);
        indicator1[i] = i + 1;
    }

    for (YacUint32 i = 0; i < PARAM_SET_SIZE; i++) {
        memset(bufBlob + MAX_LOB_SIZE * i, '0' + i, i + 1);
        indicator2[i] = i + 1;
    }

    YAC_CALL(yacBindParameter(gTestEnv.stmt, 1, YAC_PARAM_INPUT, YAC_SQLT_VARCHAR2, bufClob, MAX_LOB_SIZE, MAX_LOB_SIZE, indicator1));
    YAC_CALL(yacBindParameter(gTestEnv.stmt, 2, YAC_PARAM_INPUT, YAC_SQLT_BINARY2, bufBlob, MAX_LOB_SIZE, MAX_LOB_SIZE, indicator2));


    /* Data inserted per batch
    'a',           30
    'bb',          3131
    'ccc',         323232
    'dddd',        33333333
    'eeeee',       3434343434
    'ffffff',      353535353535
    'ggggggg',     36363636363636
    'hhhhhhhh',    3737373737373737
    'iiiiiiiii',   383838383838383838
    'jjjjjjjjjj',  39393939393939393939
    */


    // Execute as many times as the insert batch, here inserting 40 rows
    YAC_CALL(yacExecute(gTestEnv.stmt));

    // In yacExecute data can be modified in-between
    YAC_CALL(yacExecute(gTestEnv.stmt));
    YAC_CALL(yacExecute(gTestEnv.stmt));
    YAC_CALL(yacExecute(gTestEnv.stmt));

    YAC_CALL(yacCommit(gTestEnv.conn));

    free(bufClob);
    free(bufBlob);

    return YAC_SUCCESS;
}


// Batch fetch LOB data (using continuous memory retrieval)
#define ROW_SET_SIZE 10
YacResult testMultiFetchLobByContinuousMemory()
{
    YacUint32 rowSet = ROW_SET_SIZE;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_ROWSET_SIZE, &rowSet, sizeof(YacUint32)));

    // Set batch fetch row binding count to 10

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select * from yacli", YAC_NULL_TERM_STR));

    YacChar* bufClob = (YacChar*)malloc(MAX_LOB_SIZE * PARAM_SET_SIZE);
    if (bufClob == NULL) {
        return YAC_ERROR;
    }

    YacChar* bufBlob = (YacChar*)malloc(MAX_LOB_SIZE * PARAM_SET_SIZE);
    if (bufBlob == NULL) {
        return YAC_ERROR;
    }

    YacInt32 indicator1[PARAM_SET_SIZE] = { 0 };
    YacInt32 indicator2[PARAM_SET_SIZE] = { 0 };

    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_VARCHAR2, bufClob, MAX_LOB_SIZE, indicator1));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_BINARY2, bufBlob, MAX_LOB_SIZE, indicator2));


    /* Data fetched per batch
    'a',           30
    'bb',          3131
    'ccc',         323232
    'dddd',        33333333
    'eeeee',       3434343434
    'ffffff',      353535353535
    'ggggggg',     36363636363636
    'hhhhhhhh',    3737373737373737
    'iiiiiiiii',   383838383838383838
    'jjjjjjjjjj',  39393939393939393939
    */

    YacUint32 fetchedRows;


    // Fetch as many times as the batch, here fetching 40 rows, fetchedRows returns the current batch's fetched row count
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));

    free(bufClob);
    free(bufBlob);

    return YAC_SUCCESS;
}


// LOB interface to read and write LOB data (using LOB2 interface)
YacResult testOperatingLob()
{
    YacUint32 rowSet = 1;
    YAC_CALL(yacSetStmtAttr(gTestEnv.stmt, YAC_ATTR_ROWSET_SIZE, &rowSet, sizeof(YacUint32)));

    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "drop table if exists yacli_lob", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "create table yacli_lob(col1 clob, col2 blob)", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "insert into yacli_lob values('aaaaaaaaaabbbbbbbbbb', '4142434445')", YAC_NULL_TERM_STR));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "insert into yacli_lob values('bbbbbbbbbbaaaaaaaaaa', '4243444546')", YAC_NULL_TERM_STR));
    YAC_CALL(yacCommit(gTestEnv.conn));
    YAC_CALL(yacDirectExecute(gTestEnv.stmt, "select * from yacli_lob for update", YAC_NULL_TERM_STR));

    YacLobLocator* lobLocator1 = NULL;
    YacLobLocator* lobLocator2 = NULL;
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator1));
    YAC_CALL(yacLobDescAlloc2(gTestEnv.conn, &lobLocator2));

    YacInt32 indicator[2];
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 0, YAC_SQLT_CLOB, &lobLocator1, -1, &indicator[0]));
    YAC_CALL(yacBindColumn(gTestEnv.stmt, 1, YAC_SQLT_BLOB, &lobLocator2, -1, &indicator[1]));

    /* fetch lob data
    'aaaaaaaaaabbbbbbbbbb',  '4142434445'
    'bbbbbbbbbbaaaaaaaaaa',  '4243444546'
    */

    YacUint32 fetchedRows = 0;
    YacChar buf1[100];
    YacUint64 byteSize1 = 0;
    YacUint64 charSize1 = 100;
    YacChar buf2[100];
    YacUint64 byteSize2 = 100;
    YacUint64 charSize2 = 0;
    YacUint64 offset = 1;
    YacUint64 bytes1 = 100;
    YacUint64 bytes2 = 100;

    // first row fetch
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    if (indicator[0] == YAC_NULL_DATA || indicator[1] == YAC_NULL_DATA) {
        return YAC_ERROR;
    }
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, offset, (YacUint8*)buf1, 100));
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, offset, (YacUint8*)buf2, 100));
    if (fetchedRows != 1 || memcmp(buf1, "aaaaaaaaaabbbbbbbbbb", byteSize1) != 0 || memcmp(buf2, "ABCDE", byteSize2) != 0)
    {
        return YAC_ERROR;
    }

    // second row fetch
    YAC_CALL(yacFetch(gTestEnv.stmt, &fetchedRows));
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, offset, (YacUint8*)buf1, 100));
    YAC_CALL(yacLobRead2(gTestEnv.conn, lobLocator2, &byteSize2, &charSize2, offset, (YacUint8*)buf2, 100));
    if (fetchedRows != 1 || memcmp(buf1, "bbbbbbbbbbaaaaaaaaaa", byteSize1) != 0 || memcmp(buf2, "BCDEF", byteSize2) != 0)
    {
        return YAC_ERROR;
    }

    charSize1 = 10;
    YAC_CALL(yacLobWrite2(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, offset, (YacUint8*)buf1, 100));
    YAC_CALL(yacLobWriteAppend(gTestEnv.conn, lobLocator1, &byteSize1, &charSize1, (YacUint8*)buf1, 100));

    YacUint64 newlen = 5;
    YAC_CALL(yacLobTrim(gTestEnv.conn, lobLocator1, &newlen));

    YAC_CALL(yacRollback(gTestEnv.conn));

    YAC_CALL(yacLobDescFree2(lobLocator1));
    YAC_CALL(yacLobDescFree2(lobLocator2));
    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testSingleBindLob());
    YAC_CALL(testSingleFetchLob());
    YAC_CALL(testMultiBindLobByContinuousMemory());
    YAC_CALL(testMultiFetchLobByContinuousMemory());
    YAC_CALL(testOperatingLob());
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