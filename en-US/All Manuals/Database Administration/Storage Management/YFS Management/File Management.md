YFS provides two management interfaces:

-  Manage files through the command line using `yfscmd`.
- Operate YFS files in the program using the C API.

Specify the YFS file path:

-  Paths starting with `/` are local file system paths.
- Paths starting with `+` are YFS paths, for example, '+DG0/data'.

## Manage via *yfscmd*

Through the *yfscmd* tool, users can use file operation commands similar to Linux shell commands to query and manage files in YFS.

### Start yfscmd shell

The prerequisite for successfully starting the yfscmd shell:

-  The YFS service must be started (check with ycsctl status).
- The `YASCS_HOME` environment variable must be set correctly.

```shell
$ yfscmd
```

After successfully entering the shell, you can execute the following commands.

### View the current directory

Use `pwd` to view the current working directory.

The working path when `yfscmd` starts is the root directory of YFS, which is `+`.

```bash
YFSCMD >  pwd
+
```

### Change directory

Use the `cd` command to change the working directory. You can specify an absolute path or a relative path for YFS.

```bash
YFSCMD >  cd +DG2
```

### Create directory

Use `mkdir` to create a directory. The new directory must not have the same name as any other file or directory in the same level.

Note: The subdirectories under the root directory `+` are virtual directories for the disk group and cannot be created here using `mkdir`. Directories can only be created here by creating a disk group.

```bash
YFSCMD >  mkdir data1
YFSCMD >  ls
data1
```

### Copy

Use the `cp` command to copy files or directories. If the first parameter is a directory, it will copy recursively.

The `cp` command supports copying not only files and directories inside YFS but also allows copying between YFS and the local file system.

The `cp` command identifies the file system by the first character of the parameter:

-  Paths starting with `/` are local file system paths.
- Paths starting with `+` are YFS paths.
- Relative paths are YFS paths.

```bash
# System copy to YFS
YFSCMD > cp  /home/yashan/data2.dat data2
cp OK: SYS:/home/yashan/data2.dat --> YFS:+DG2/data2
YFSCMD >  ls
data1
data2
# YFS copy to system
YFSCMD >  cp data1 /home/yashan/data1
cp OK: YFS:+DG2/data1 --> SYS:/home/yashan/data1
# Copying within YFS
YFSCMD >  cp data2 data3.dat
cp OK: YFS:+DG2/data2 --> YFS:+DG2/data3.dat
```

> **Note**: 
>
> Unlike typical file systems where objects with the same name can be created by default, the second parameter of the `cp` command must explicitly specify the new name.

### View file list

Use the `ls` command to view the file list. For detailed usage, please refer to the [yfscmd](../../../Tools Guide/yfscmd/00yfscmd) documentation.

```bash
YFSCMD >  ls
data1
data2
data3.dat
```

### Delete

Use the `rm` command to delete files or directories.

By default, non-empty directories cannot be deleted, but the `-r` parameter can be specified to recursively delete non-empty directories.

The subdirectories under the root directory `+` are virtual directories for the disk group and cannot be deleted with `rm`. Only by deleting the disk group can the corresponding virtual directory be removed.

```bash
YFSCMD >  rm data1
DELETE +DG2/data1
```

### Rename

Use `mv` to rename files or directories. The second parameter must explicitly specify the new name.

The subdirectories under the root directory `+` are virtual directories for the disk group and cannot be renamed.

```bash
YFSCMD >  mv data2 data1
mv: YFS:+DG2/data2 --> YFS:+DG2/data1.
YFSCMD >  ls
data1
data3.dat
```

## Manage via C API

### Basic Concepts

YFS provides a set of C APIs (hereinafter referred to as API) to facilitate access to YFS through programs.

Programs accessing YFS through the API are referred to as YFS clients. YFS provides services through UDS and shared memory and only allows clients to access the local YFS service (deployed on the same server). The client process must have access privileges to this UDS and shared memory.

The API abstracts the connection between the client and the YFS service through `YfsiConn`. Each thread must use a separate `YfsiConn` for higher data security. All APIs require passing in `YfsiConn` and ensuring to **create the connection before starting operations, close and release the connection in a timely manner after all operations are finished**, and do not release the connection repeatedly.

YFS represents open file descriptors with `uint32_t`.

YFS only supports DIRECT IO; therefore, the offset, length, and buffer address for file IO must be aligned to `YFS_ALIGN_SIZE` bytes, which is defined as:
```c
#define YFS_ALIGN_SIZE 512
```

Please follow these steps to operate YFS files:
1. Open the file.
2. Read and write to the file any number of times.
3. Close the file.

YFS is a high-performance clustered file system, and I/O across all nodes is completely parallel. Clients should properly handle concurrent IO, and concurrent file addition and deletion operations to avoid errors.

All APIs are C functions prefixed with `yfsi`, following camel case naming conventions.

Except for functions returning `void`, functions returning `int` follow these conventions:

-  Return 0 indicates successful execution.
- Non-zero return indicates failure, and error codes can be obtained via `codGetErrorCode`, and error messages can be obtained via `codGetErrorMsg`.

### API Description

#### yfsiAllocConn

```c
int  yfsiAllocConn(YfsiConn** conn);
```

Create a connection object. It must be used independently in different threads.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |

#### yfsiFreeConn

```c
void yfsiFreeConn(YfsiConn* conn);
```

Release the connection object.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |

#### yfsiConnect

```c
int  yfsiConnect(YfsiConn* conn, const char* url);
```

Connect to the service.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |
| url       | Connection address, please format this URL using `yfsiFormatUrl` |

***Example***

```c
char linkUrl[1024] = {0};
yfsiFormatUrl("/YFS/home", linkUrl, 1024);

if (yfsiConnect(conn, linkUrl) != 0) {
    printf("failed to connect to YFS server. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
    // error handling
}
```

#### yfsiDisconnect

```c
void yfsiDisconnect(YfsiConn* conn);
```

Disconnect from the service.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |

#### yfsiOpenFile

```c
int yfsiOpenFile(YfsiConn* conn, const char* fileName, uint32_t* fd);
```

Open a file. Only YFS files can be opened; attempting to open a directory or non-YFS file will fail.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |
| fileName  | Absolute path of the file, supports only YFS path, format: `+ disk group name/directory/filename` |
| fd        | Pointer to the file descriptor |

***Example***

```c
uint32_t fd = 0;
if (yfsiOpenFile(conn, "+DG0/testdir/testfile", &fd) != 0) {
    printf("failed to open file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
    // error handling
}
```

#### yfsiCloseFile

```c
int yfsiCloseFile(YfsiConn* conn, uint32_t fd);
```

Close the file.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |
| fd        | File descriptor obtained from `yfsiOpenFile` |

***Example***

```c
(void) yfsiCloseFile(conn, fd);
```

#### yfsiWriteFile

```c
int yfsiWriteFile(YfsiConn* conn, uint32_t fd, char* buf, uint64_t offset, uint32_t size);
```

Write data to an opened file. YFS does not support automatic file expansion; ensure there is enough space to write data.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |
| fd        | File descriptor obtained from `yfsiOpenFile` |
| buf       | Buffer containing data to write; the buffer address must be aligned to `YFS_ALIGN_SIZE` bytes |
| offset    | Offset for writing to the file, aligned to `YFS_ALIGN_SIZE` bytes |
| size      | Size of the data to write, aligned to `YFS_ALIGN_SIZE` bytes |

***Example***

```c
if (yfsiWriteFile(conn, fd, buffer, 0, 1024) != 0) {
    printf("failed to write file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
    // error handling
}
```

#### yfsiReadFile

```c
int yfsiReadFile(YfsiConn* conn, uint32_t fd, char* buf, uint64_t offset, uint32_t size, uint32_t* actualSize);
```

Read from an opened file. YFS does not allow read operations beyond the end of the file.

|Parameter |Description |
| ----------- | ----------- |
| conn      | Pointer to the connection object |
| fd        | File descriptor obtained from `yfsiOpenFile` |
| buf       | Buffer to save read data; the buffer address must be aligned to `YFS_ALIGN_SIZE` bytes |
| offset    | Offset for reading the file, aligned to `YFS_ALIGN_SIZE` bytes |
| size      | Size of the data to read, aligned to `YFS_ALIGN_SIZE` bytes |
| actualSize| Actual length of data read |

***Example***

```c
uint32_t realLen = 0;
if (yfsiReadFile(conn, fd, buffer, 0, 1024, &realLen) != 0) {
    printf("failed read file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
    // error handling
}
```

#### yfsiFormatUrl

```c 
void yfsiFormatUrl(const char *home, char *url, int len);
```

Format the URL for YFS connection.
|Parameter |Description |
| ----------- |-----------|
| home      | YFS home path, usually the value of the `YASCS_HOME` environment variable, which is the parent directory of the YFS configuration file |
| url       | Buffer for outputting URL |
| len       | Length of the URL buffer |

***Example***

```c
char linkUrl[1024] = {0};
yfsiFormatUrl("/YFS/home", linkUrl, 1024);
```

#### Error Information

```c
uint32_t codGetErrorCode();
char*    codGetErrorMsg();
```

Get the error code and error message.

### Complete Example

```c
#include <stdio.h>
#include <stdlib.h>
// Include YFS API header file
#include "yfsi_include.h"

int main(int argc, const char ** argv) 
{
    if (argc < 2 || argv[1][0] != '+') {
        printf("expecting YFS path, like: +DG0/file/path\n");
        return 1;
    }

    const char* path = argv[1];

    /*
    * Get the home path of YFS, usually the YASCS_HOME environment variable or the -D parameter of yascs or *yasfs* process.
    * Here we take the YASCS_HOME environment variable as an example.
    */
    const char* home = getenv("YASCS_HOME");
    if (home == NULL || strlen(home) == 0) {
        printf("YASCS_HOME not set yet.\n");
        return 1;
    }

    /**
     * Allocate buffer, must be aligned to YFS_ALIGN_SIZE.
    */
    char* buffer = NULL;
    if (posix_memalign((void **)&buffer, YFS_ALIGN_SIZE, 1024) != 0 ) {
        printf("failed to allocate buffer.\n");
        return 1;
    }

    /**
     * Create connection
     */
    YfsiConn *conn = NULL;
    if (yfsiAllocConn(&conn) != 0) {
        printf("failed to allocate connection. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
        free(buffer);
        return 1;
    }

    /**
     * Generate connection URL
     */
    char linkUrl[1024] = {0};
    yfsiFormatUrl(home, linkUrl, 1024);

    /**
     * Connect to the service
     */
    if (yfsiConnect(conn, linkUrl) != 0) {
        printf("failed to connect to the yfs server. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
        yfsiFreeConn(conn);
        free(buffer);
        return 1;
    }

    /**
     * Open file, obtain fd
     */
    uint32_t fd;
    if (yfsiOpenFile(conn, path, &fd) != 0) {
        printf("failed to open file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
        yfsiDisconnect(conn);
        yfsiFreeConn(conn);
        free(buffer);
        return 1;
    }

    printf("file opened.\n");

    /**
     * Read file, ensure the read range is within the file size, otherwise it will fail.
    */
    uint32_t realLen = 0;
    if (yfsiReadFile(conn, fd, buffer, 0, 1024, &realLen) != 0) {
        printf("failed to read file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
        yfsiCloseFile(conn, fd);
        yfsiDisconnect(conn);
        yfsiFreeConn(conn);
        free(buffer);
        return 1;
    }

    printf("read file, len = %u.\n", realLen);

    /**
     * Write to file, ensure the write range is within the file size, otherwise it will fail.
    */
    if (yfsiWriteFile(conn, fd, buffer, 0, 1024) != 0) {
        printf("failed to write file. [%u] %s\n", codGetErrorCode(), codGetErrorMsg());
        yfsiCloseFile(conn, fd);
        yfsiDisconnect(conn);
        yfsiFreeConn(conn);
        free(buffer);
        return 1;
    }

    printf("write file, len = %u.\n", 1024);

    /**
     * Close the opened file
     * Disconnect
     * Free the connection
     * Free the buffer memory
    */
    yfsiCloseFile(conn, fd);
    yfsiDisconnect(conn);
    yfsiFreeConn(conn);
    free(buffer);

    return 0;
}
```
