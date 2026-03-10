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
// Single row binding to import data
sword testSingleBind() 
{
    OCIStmt* stmthp = NULL;
    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&stmthp, OCI_HTYPE_STMT, (size_t)0, (dvoid**)0);

    OCIBind*   bindp1 = NULL;
    OCIDefine* definep1 = NULL;
    ub4        intOut1[3] = {0, 1, 2};

    OraText* sql = "drop table if exists tbl_bind";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    sql = "create table tbl_bind(col1 int, col2 varchar(20))";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    sql = "insert into tbl_bind values (:1, '11')";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIBindByPos(stmthp, &bindp1, errhp, (ub4)1, (dvoid*)intOut1, (sb4)sizeof(ub4), SQLT_INT, 0, 0, 0, 0, NULL, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)3, (ub4)0, NULL, NULL, OCI_DEFAULT));
    
    ub4 rowcnt;
    OCI_TEST_CALL(OCIAttrGet((CONST dvoid*)stmthp, (ub4)OCI_HTYPE_STMT, (void*)&rowcnt, (ub4*)0, (ub4)OCI_ATTR_ROW_COUNT, errhp));
    if (rowcnt != 3) {
        return OCI_ERROR;
    }
   
    // Single row fetch data
    sql = "select col1 + 99 from tbl_bind";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIDefineByPos(stmthp, &definep1, errhp, (ub4)1, (dvoid*)intOut1, (sb4)sizeof(ub4), SQLT_INT, 0, 0, 0, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)3, (ub4)0, NULL, NULL, OCI_DEFAULT));
    if (intOut1[0] != 99 || intOut1[1] != 100 || intOut1[2] != 101) {
        return OCI_ERROR;
    }

    (void)OCIHandleFree((dvoid*)stmthp, (ub4)OCI_HTYPE_STMT);
    return OCI_SUCCESS;
}

// Batch binding to import data
sword testBatchBind()
{
    OCIStmt* stmthp = NULL;
    (void)OCIHandleAlloc((dvoid*)envhp, (dvoid**)&stmthp, OCI_HTYPE_STMT, (size_t)0, (dvoid**)0);

    OCIBind*   bindp = NULL;
    OCIDefine* definep = NULL;

    sb1        intIn1[4];
    sb2        ind1[4];
    ub2        rlen1[4];

    OraText* sql = "drop table if exists tbl_bind";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    sql = "create table tbl_bind(col1 bigint)";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql, (ub4)strlen((char*)sql), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)1, (ub4)0, NULL, NULL, OCI_DEFAULT));

    intIn1[0] = 0;
    intIn1[1] = 1;
    intIn1[2] = 2;

    ind1[0] = 0;
    ind1[1] = 0;
    ind1[2] = 0;

    rlen1[0] = 1;
    rlen1[1] = 1;
    rlen1[2] = 1;

    OraText* sql1 = "insert into tbl_bind values (:1)";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql1, (ub4)strlen((char*)sql1), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIBindByPos(stmthp, &bindp, errhp, (ub4)1, (dvoid*)intIn1, (sb4)sizeof(sb1), SQLT_INT, ind1, rlen1, 0, 0, NULL, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)3, (ub4)0, NULL, NULL, OCI_DEFAULT));
    
    // Batch fetch data
    OraText* sql2 = "select col1 from tbl_bind order by rowid";
    OCI_TEST_CALL(OCIStmtPrepare(stmthp, errhp, sql2, (ub4)strlen((char*)sql2), (ub4)OCI_NTV_SYNTAX, (ub4)OCI_DEFAULT));
    OCI_TEST_CALL(OCIDefineByPos(stmthp, &definep, errhp, (ub4)1, (dvoid*)intIn1, (sb4)sizeof(sb1), SQLT_INT, ind1, rlen1, 0, OCI_DEFAULT));
    OCI_TEST_CALL(OCIStmtExecute(svchp, stmthp, errhp, (ub4)3, (ub4)0, NULL, NULL, OCI_DEFAULT));

    if (intIn1[0] != 0 || intIn1[1] != 1 || intIn1[2] != 2) {
        return OCI_ERROR;
    }

    if (ind1[0] != 0 || ind1[1] != 0 || ind1[2] != 0) {
        return OCI_ERROR;
    }

    if (rlen1[0] != 1 || rlen1[1] != 1 || rlen1[2] != 1) {
        return OCI_ERROR;
    }
    
    (void)OCIHandleFree((dvoid*)stmthp, (ub4)OCI_HTYPE_STMT);
    return OCI_SUCCESS;
}

// Connect to the database
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

// Disconnect from the database
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
    if (testSingleBind() != OCI_SUCCESS) {
        return 2;
    }
    if (testBatchBind() != OCI_SUCCESS) {
        return 3;
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
