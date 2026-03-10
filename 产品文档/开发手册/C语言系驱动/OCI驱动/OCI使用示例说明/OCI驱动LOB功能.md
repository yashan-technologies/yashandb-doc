
```c
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>
#include <string.h>
#include <oci.h>

#define OCI_TEST_CALL(ociFunc)   \
    do {                         \
        sword r = ociFunc;       \
        if (r != OCI_SUCCESS) {  \
            checkerr(errhp, r);  \
            return r;            \
        }                        \
    } while (0)

static text* username = (text*)"sys";
static text* password = (text*)"Cod-2022";
static text* dbname = (text*)"CITEST";

static OCIEnv*      envhp = NULL;
static OCIError*    errhp = NULL;
static OCISvcCtx*   svchp = NULL;
static OCISession*  authp = NULL;
static OCIServer*   srvhp = NULL;

void checkerr(OCIError* errhp, sword status)
{
    text errbuf[512];
    sb4  errcode = 0;

    switch (status) {
        case OCI_SUCCESS:
            break;
        case OCI_SUCCESS_WITH_INFO:
            (void)printf("Error - OCI_SUCCESS_WITH_INFO\n");
            break;
        case OCI_NEED_DATA:
            (void)printf("Error - OCI_NEED_DATA\n");
            break;
        case OCI_NO_DATA:
            (void)printf("Error - OCI_NODATA\n");
            break;
        case OCI_ERROR:
            (void)OCIErrorGet((dvoid*)errhp, (ub4)1, (text*)NULL, &errcode, errbuf, (ub4)sizeof(errbuf),
                              OCI_HTYPE_ERROR);
            (void)printf("Error - %.*s\n", 512, errbuf);
            break;
        case OCI_INVALID_HANDLE:
            (void)printf("Error - OCI_INVALID_HANDLE\n");
            break;
        case OCI_STILL_EXECUTING:
            (void)printf("Error - OCI_STILL_EXECUTE\n");
            break;
        case OCI_CONTINUE:
            (void)printf("Error - OCI_CONTINUE\n");
            break;
        default:
            break;
    }
}

// 单行绑定LOB导入数据
sword testSingleBindLob()
{
    OCIStmt* stmthp = NULL;
    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&stmthp, OCI_HTYPE_STMT, (size_t)0, (dvoid**)0);

    OraText* sql = "drop table if exists tbl_lob";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    sql = "create table tbl_lob(col1 blob, col2 int)";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    sql = "insert into tbl_lob values (empty_blob(), 1)";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));
    
    OCILobLocator* blob;
    (void)OCIDescriptorAlloc((dvoid*)envhp, (void**)&blob, OCI_DTYPE_LOB, (size_t)0, (dvoid**)0);
    OCIDefine* definep = NULL;
    sql = "select * from tbl_lob where col2 = 1";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIDefineByPos(stmthp, &definep, errhp, (ub4)1, (dvoid*)&blob, (sb4)sizeof(OCILobLocator*), SQLT_BLOB, 0, 0, 0, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)0, (ub4)0, NULL, NULL, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtFetch(stmthp, errhp, (ub4)1, (ub4)OCI_FETCH_NEXT, OCI_DEFAULT));

    oratext bufbIn[10] = "112233";
    ub4     amtp = 2;
    OCI_TEST_CALL(OCILobWrite(svchp, errhp, blob, &amtp, 1, bufbIn, 10, OCI_ONE_PIECE, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (amtp != 2) {
        return OCI_ERROR;
    }
    OCI_TEST_CALL(OCILobWrite(svchp, errhp, blob, &amtp, 3, bufbIn + 2, 8, OCI_ONE_PIECE, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (amtp != 2) {
        return OCI_ERROR;
    }
    OCI_TEST_CALL(OCILobWrite(svchp, errhp, blob, &amtp, 5, bufbIn + 4, 6, OCI_ONE_PIECE, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (amtp != 2) {
        return OCI_ERROR;
    }

    ub4 blobLen = 0;
    OCI_TEST_CALL(OCILobGetLength(svchp, errhp, blob, &blobLen));
    if (blobLen != 6) {
        return OCI_ERROR;
    }

    // 单行取LOB数据
    oratext bufb[100] = {0};
    ub4 readLen = 2;
    OCI_TEST_CALL(OCILobRead(svchp, errhp, blob, &readLen, 1, bufb, 100, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (readLen != 2 || memcmp(bufb, "11", 2) != 0) {
        return OCI_ERROR;
    }
    OCI_TEST_CALL(OCILobRead(svchp, errhp, blob, &readLen, 3, bufb + 2, 100, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (readLen != 2 || memcmp(bufb + 2, "22", 2) != 0) {
        return OCI_ERROR;
    }
    OCI_TEST_CALL(OCILobRead(svchp, errhp, blob, &readLen, 5, bufb + 4, 100, NULL, NULL, 0, SQLCS_IMPLICIT));
    if (readLen != 2 || memcmp(bufb + 4, "33", 2) != 0) {
        return OCI_ERROR;
    }

    (void)OCIDescriptorFree(blob, OCI_DTYPE_LOB);
    (void)OCIHandleFree((dvoid*)stmthp, (ub4)OCI_HTYPE_STMT);
    return OCI_SUCCESS;
}
// 连接数据库
sword testConnect() 
{
    sword errcode = 0;

    errcode = OCIEnvCreate((OCIEnv**)&envhp, (ub4)OCI_THREADED, (dvoid*)0, (dvoid * (*)(dvoid*, size_t))0,
                     (dvoid * (*)(dvoid*, dvoid*, size_t))0, (void (*)(dvoid*, dvoid*))0, (size_t)0, (dvoid**)0);

    if (errcode != 0) {
        (void)printf("OCIEnvCreate failed with errcode = %d.\n", errcode);
        return OCI_ERROR;
    }

    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&errhp, OCI_HTYPE_ERROR, (size_t)0, (dvoid**)0);
    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&svchp, OCI_HTYPE_SVCCTX, (size_t)0, (dvoid**)0);
    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&srvhp, OCI_HTYPE_SERVER, (size_t)0, (dvoid**)0);

    OCI_TEST_CALL(OCIServerAttach(srvhp, errhp, (text*)dbname, (sb4)strlen((char*)dbname), 0));
    (void)OCIAttrSet((dvoid*)svchp, OCI_HTYPE_SVCCTX, (dvoid*)srvhp, (ub4)0, OCI_ATTR_SERVER, (OCIError*)errhp);

    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&authp, (ub4)OCI_HTYPE_SESSION, (size_t)0, (dvoid**)0);
    (void)OCIAttrSet((dvoid*)authp, (ub4)OCI_HTYPE_SESSION, (dvoid*)username, (ub4)strlen((char*)username), (ub4)OCI_ATTR_USERNAME, errhp);
    (void)OCIAttrSet((dvoid*)authp, (ub4)OCI_HTYPE_SESSION, (dvoid*)password, (ub4)strlen((char*)password), (ub4)OCI_ATTR_PASSWORD, errhp);

    OCI_TEST_CALL(OCISessionBegin(svchp, errhp, authp, OCI_CRED_RDBMS, (ub4)OCI_DEFAULT));
    (void)OCIAttrSet((dvoid*)svchp, (ub4)OCI_HTYPE_SVCCTX, (dvoid*)authp, (ub4)0, (ub4)OCI_ATTR_SESSION, errhp);

    return OCI_SUCCESS;
}

// 关闭数据库连接
sword testDisconnect() 
{
    OCI_TEST_CALL(OCISessionEnd(svchp, errhp, authp, (ub4)0));
    OCI_TEST_CALL(OCIServerDetach(srvhp, errhp, (ub4)OCI_DEFAULT));

    (void)OCIHandleFree((dvoid*)authp, (ub4)OCI_HTYPE_SESSION);
    (void)OCIHandleFree((dvoid*)srvhp, (ub4)OCI_HTYPE_SERVER);
    (void)OCIHandleFree((dvoid*)svchp, (ub4)OCI_HTYPE_SVCCTX);
    (void)OCIHandleFree((dvoid*)errhp, (ub4)OCI_HTYPE_ERROR);
    (void)OCIHandleFree((dvoid*)envhp, (ub4)OCI_HTYPE_ENV);

    return OCI_SUCCESS;
}

sword runTest() 
{
    if (testConnect() != OCI_SUCCESS) {
        return 1;
    }
    if (testSingleBindLob() != OCI_SUCCESS) {
        return 4;
    }
    if (testDisconnect() != OCI_SUCCESS) {
        return 5;
    }
    return 0;
}

int   main(argc, argv)
int   argc;
char* argv[];
{
    sword runResult =  runTest();
    if (runResult == 0) {
        printf("test succeed!\n");
    } else {
        printf("test failed! failed test num : %d\n", runResult);
    }
    return runResult;
}

```

