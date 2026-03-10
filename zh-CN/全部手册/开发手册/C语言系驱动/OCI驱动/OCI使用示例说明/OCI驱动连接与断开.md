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

//HA场景多Ip primary模式（PRIMARY:关键字可省略）：
//static text* dbname2 = (text*)"PRIMARY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688";
//多Ip loadBalance模式：
//static text* dbname3 = (text*)"LOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688";
//HA场景多Ip standby模式：
//static text* dbname4 = (text*)"STANDBY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688";
//多Ip primaryLoadBalance模式：
//static text* dbname5 = (text*)"PRIMARYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688";
//多Ip standbyLoadBalance模式：
//static text* dbname6 = (text*)"STANDBYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688";

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
