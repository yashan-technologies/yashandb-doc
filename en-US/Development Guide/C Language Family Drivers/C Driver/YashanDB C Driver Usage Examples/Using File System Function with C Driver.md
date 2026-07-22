## Interfaces

|  Function    | Description      |
| ------------------------------------------------------------ | ------------------------------------------- |
| [yacFsSetCurrentVolume](../YashanDB C Driver Interfaces/File System Functions/yacFsSetCurrentVolume) | Sets the volume used by the current connection |
| [yacFsGetCurrentVolume](../YashanDB C Driver Interfaces/File System Functions/yacFsGetCurrentVolume) | Gets the volume used by the current connection |
| [yacFsMkDir](../YashanDB C Driver Interfaces/File System Functions/yacFsMkDir) | Creates a specified directory under the current volume |
| [yacFsRmDir](../YashanDB C Driver Interfaces/File System Functions/yacFsRmDir) | Deletes a specified directory under the current volume |
| [yacFsCreateFile](../YashanDB C Driver Interfaces/File System Functions/yacFsCreateFile) | Creates a specified file under the current volume |
| [yacFsRmFile](../YashanDB C Driver Interfaces/File System Functions/yacFsRmFile) | Deletes a specified file under the current volume |
| [yacFsFileExist](../YashanDB C Driver Interfaces/File System Functions/yacFsFileExist) | Checks whether a specified file exists under the current volume |
| [yacFsFileStat](../YashanDB C Driver Interfaces/File System Functions/yacFsFileStat) | Gets file information under the current volume |
| [yacFsFileRead](../YashanDB C Driver Interfaces/File System Functions/yacFsFileRead) | Reads file data from a specified position under the current volume |
| [yacFsFileWrite](../YashanDB C Driver Interfaces/File System Functions/yacFsFileWrite) | Writes data to a specified file under the current volume |
| [yacFsListDir](../YashanDB C Driver Interfaces/File System Functions/yacFsListDir) | Lists files or directories under a specified directory of the current volume |

## Example (Standalone Deployment)

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

// Connect to database
YacResult testConnect()
{
    // Change to actual database server IP and port
    const YacChar* gSrvStr = "192.168.1.2:1688";
    const YacChar* user = "sales";
    const YacChar* pwd = "sales";

    YAC_CALL(yacAllocHandle(YAC_HANDLE_ENV, NULL, &gTestEnv.env));
    YAC_CALL(yacAllocHandle(YAC_HANDLE_DBC, gTestEnv.env, &gTestEnv.conn));
    YAC_CALL(yacConnect(gTestEnv.conn, gSrvStr, YAC_NULL_TERM_STR, user, YAC_NULL_TERM_STR, pwd, YAC_NULL_TERM_STR));

    return YAC_SUCCESS;
}

// Disconnect from database
YacResult testDisConnect()
{
    yacDisconnect(gTestEnv.conn);
    YAC_CALL(yacFreeHandle(YAC_HANDLE_DBC, gTestEnv.conn));
    YAC_CALL(yacFreeHandle(YAC_HANDLE_ENV, gTestEnv.env));

    return YAC_SUCCESS;
}

// Directory entry iteration callback
YacBool listCallback(YacPointer ctx, const YacFsEntry* entries, YacUint32 count)
{
    for (YacUint32 i = 0; i < count; i++) {
        const char* type = (entries[i].type == YAC_FSENTRY_FILE) ? "file" : "dir";
        printf("  [%s] %s\n", type, entries[i].name);
    }
    return YAC_TRUE;
}

// Stream callback to read data from disk file
YacUint32 fileStreamRead(YacFsStream* stream)
{
    FILE* fp = (FILE*)stream->ctx;
    if (fp == NULL) {
        return 0;
    }
    return (YacUint32)fread(stream->buf, 1, stream->bufLen, fp);
}

// File system function comprehensive example
YacResult testFileSystem()
{
    YacChar volumeName[256];
    YacBool exists;
    YacFileStat fileStat;
    YacUint8 buffer[4096];
    YacUint32 bytesRead;
    YacUint32 bytesWritten;

    // Set current volume
    YAC_CALL(yacFsSetCurrentVolume(gTestEnv.conn, "myvolume"));

    // Get current volume
    YAC_CALL(yacFsGetCurrentVolume(gTestEnv.conn, volumeName, sizeof(volumeName)));
    printf("Current volume: %s\n", volumeName);

    // Create directory
    YAC_CALL(yacFsMkDir(gTestEnv.conn, "/mydir"));
    printf("Directory created\n");

    // Create file
    YAC_CALL(yacFsCreateFile(gTestEnv.conn, "/mydir/myfile.txt"));
    printf("File created\n");

    // Check if file exists
    YAC_CALL(yacFsFileExist(gTestEnv.conn, "/mydir/myfile.txt", &exists));
    if (exists) {
        printf("File exists\n");
    }

    // Read data from disk file and write to server file
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

    // Get file attributes
    YAC_CALL(yacFsFileStat(gTestEnv.conn, "/mydir/myfile.txt", &fileStat));
    printf("File size: %llu bytes\n", fileStat.size);

    // Read file
    YAC_CALL(yacFsFileRead(gTestEnv.conn, "/mydir/myfile.txt", 0, buffer, sizeof(buffer), &bytesRead));
    printf("Read %u bytes: %.*s\n", bytesRead, bytesRead, buffer);

    // List directory
    printf("Directory listing:\n");
    YAC_CALL(yacFsListDir(gTestEnv.conn, "/mydir", 100, listCallback, NULL));

    // Delete file
    YAC_CALL(yacFsRmFile(gTestEnv.conn, "/mydir/myfile.txt"));
    printf("File deleted\n");

    // Cascade delete directory
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
