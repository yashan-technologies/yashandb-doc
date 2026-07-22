## 接口

|  函数    | 说明      |
| ------------------------------------------------------------ | ------------------------------------------- |
| [yacFsSetCurrentVolume](../C驱动接口说明/文件系统函数/yacFsSetCurrentVolume) | 设置当前连接使用的卷 |
| [yacFsGetCurrentVolume](../C驱动接口说明/文件系统函数/yacFsGetCurrentVolume) | 获取当前连接使用的卷 |
| [yacFsMkDir](../C驱动接口说明/文件系统函数/yacFsMkDir) | 在当前卷下创建指定目录 |
| [yacFsRmDir](../C驱动接口说明/文件系统函数/yacFsRmDir) | 在当前卷下删除指定目录 |
| [yacFsCreateFile](../C驱动接口说明/文件系统函数/yacFsCreateFile) | 在当前卷下创建指定文件 |
| [yacFsRmFile](../C驱动接口说明/文件系统函数/yacFsRmFile) | 在当前卷下删除指定文件 |
| [yacFsFileExist](../C驱动接口说明/文件系统函数/yacFsFileExist) | 在当前卷下判断指定文件是否存在 |
| [yacFsFileStat](../C驱动接口说明/文件系统函数/yacFsFileStat) | 在当前卷下获取文件信息 |
| [yacFsFileRead](../C驱动接口说明/文件系统函数/yacFsFileRead) | 在当前卷下从指定位置读取文件数据 |
| [yacFsFileWrite](../C驱动接口说明/文件系统函数/yacFsFileWrite) | 在当前卷下将数据写入指定文件 |
| [yacFsListDir](../C驱动接口说明/文件系统函数/yacFsListDir) | 在当前卷下列出指定目录下的文件或目录 |

## 使用示例

```c
#include <stdio.h>
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

    return YAC_SUCCESS;
}

// 断开数据库
YacResult testDisConnect()
{
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

// 目录条目迭代回调
YacBool listCallback(YacPointer ctx, const YacFsEntry* entries, YacUint32 count)
{
    for (YacUint32 i = 0; i < count; i++) {
        const char* type = (entries[i].type == YAC_FSENTRY_FILE) ? "file" : "dir";
        printf("  [%s] %s\n", type, entries[i].name);
    }
    return YAC_TRUE;
}

// 从磁盘文件读取数据的流回调
YacUint32 fileStreamRead(YacFsStream* stream)
{
    FILE* fp = (FILE*)stream->ctx;
    if (fp == NULL) {
        return 0;
    }
    return (YacUint32)fread(stream->buf, 1, stream->bufLen, fp);
}

// 文件系统功能综合示例
YacResult testFileSystem()
{
    YacChar volumeName[256];
    YacBool exists;
    YacFileStat fileStat;
    YacUint8 buffer[4096];
    YacUint32 bytesRead;
    YacUint32 bytesWritten;

    // 设置当前卷
    YAC_CALL(yacFsSetCurrentVolume(gTestEnv.conn, "myvolume"));

    // 获取当前卷
    YAC_CALL(yacFsGetCurrentVolume(gTestEnv.conn, volumeName, sizeof(volumeName)));
    printf("Current volume: %s\n", volumeName);

    // 创建目录
    YAC_CALL(yacFsMkDir(gTestEnv.conn, "/mydir"));
    printf("Directory created\n");

    // 创建文件
    YAC_CALL(yacFsCreateFile(gTestEnv.conn, "/mydir/myfile.txt"));
    printf("File created\n");

    // 检查文件是否存在
    YAC_CALL(yacFsFileExist(gTestEnv.conn, "/mydir/myfile.txt", &exists));
    if (exists) {
        printf("File exists\n");
    }

    // 从磁盘文件读取数据并写入到服务端文件
    FILE* fp = fopen("/local/path/source.txt", "rb");
    if (fp == NULL) {
        return YAC_ERROR;
    }
    YacFsStream stream;
    stream.ctx = fp;
    stream.buf = buffer;
    stream.bufLen = sizeof(buffer);
    stream.read = fileStreamRead;
    YAC_CALL(yacFsFileWrite(gTestEnv.conn, "/mydir/myfile.txt", &stream, &bytesWritten));
    fclose(fp);
    printf("Wrote %u bytes\n", bytesWritten);

    // 获取文件属性
    YAC_CALL(yacFsFileStat(gTestEnv.conn, "/mydir/myfile.txt", &fileStat));
    printf("File size: %llu bytes\n", fileStat.size);

    // 读取文件
    YAC_CALL(yacFsFileRead(gTestEnv.conn, "/mydir/myfile.txt", 0, buffer, sizeof(buffer), &bytesRead));
    printf("Read %u bytes: %.*s\n", bytesRead, bytesRead, buffer);

    // 列出目录
    printf("Directory listing:\n");
    YAC_CALL(yacFsListDir(gTestEnv.conn, "/mydir", 100, listCallback, NULL));

    // 删除文件
    YAC_CALL(yacFsRmFile(gTestEnv.conn, "/mydir/myfile.txt"));
    printf("File deleted\n");

    // 级联删除目录
    YAC_CALL(yacFsRmDir(gTestEnv.conn, "/mydir", YAC_TRUE));
    printf("Directory deleted\n");

    return YAC_SUCCESS;
}

YacResult cexample()
{
    YAC_CALL(testConnect());
    YAC_CALL(testFileSystem());
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
