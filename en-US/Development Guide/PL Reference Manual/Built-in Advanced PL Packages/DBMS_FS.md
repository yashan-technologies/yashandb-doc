DBMS_FS is the advanced package entry point for YashanDB FS, offering unified file operation interfaces for storage volumes.

> **Note**:
>
> DBMS_FS is only supported in standalone deployment.

When using the DBMS_FS advanced package, the operation sequence is as follows: first call CREATE_VOLUME to create a volume, then call SET_VOLUME to set the current volume, after which you can perform management operations on files in the current volume, such as creating, reading, writing, and deleting.

## Data Types

### DIRENTRY

```plsql
TYPE DIRENTRY IS RECORD (
    FILE_NAME VARCHAR(255),
    FILE_TYPE NUMBER);
```

DIRENTRY is the record type for directory entry information.

|  Field| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR(255) | Name of the file or directory.                           |
| FILE_TYPE   | NUMBER        | Entry type. 0 indicates file, 1 indicates directory.     |

### DIRENTRY_TABLE

```plsql
TYPE DIRENTRY_TABLE IS TABLE OF DIRENTRY;
```

DIRENTRY_TABLE is the output parameter type for the LISTDIR interface, used to return the list of files or subdirectories under a directory.

### FILESTAT

```plsql
TYPE FILESTAT IS RECORD (
    FILE_ID     NUMBER,
    FILE_SIZE   NUMBER,
    CREATE_TIME DATE,
    MODIFY_TIME DATE,
    FILE_TYPE   NUMBER);
```

FILESTAT is the output parameter type for the STAT interface, used to return file metadata information.

|  Field| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_ID     | NUMBER | File ID.                                                   |
| FILE_SIZE   | NUMBER | File size (in bytes).                                      |
| CREATE_TIME | DATE   | Creation time.                                             |
| MODIFY_TIME | DATE   | Modification time.                                         |
| FILE_TYPE   | NUMBER | File type. 0 indicates regular file, 1 indicates directory. |

## CREATE_VOLUME

```plsql
DBMS_FS.CREATE_VOLUME(
    VOLUME_NAME     IN VARCHAR2,
    TS_NAME         IN VARCHAR2,
    BUCKET_NAME     IN VARCHAR2,
    FILE_SIZE_LIMIT IN BIGINT);
```

CREATE_VOLUME procedure is used to create a new storage volume. A volume is the top-level organizational unit of a file system, and each volume is bound to a specified tablespace and storage bucket.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME     | VARCHAR2 | Volume name. Name length limit is 64 bytes.            |
| TS_NAME         | VARCHAR2 | Tablespace name.                                       |
| BUCKET_NAME     | VARCHAR2 | Storage bucket name.                                   |
| FILE_SIZE_LIMIT | BIGINT   | Capacity limit (in bytes).                             |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.CREATE_VOLUME('test_vol', 'main_tbs', 'main_bucket', 1073741824);
END;
/
```

## DROP_VOLUME

```plsql
DBMS_FS.DROP_VOLUME(
    VOLUME_NAME IN VARCHAR2,
    CASCADE     IN BOOLEAN DEFAULT NULL);
```

DROP_VOLUME is used to delete a specified storage volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | Name of the volume to delete.                          |
| CASCADE     | BOOLEAN  | Cascade delete flag. TRUE means cascade delete all files in the volume, FALSE or unspecified means only delete empty volume. |

***Example*** for Standalone Deployment

```plsql
BEGIN
    -- Delete empty volume
    DBMS_FS.DROP_VOLUME('test_vol', FALSE);

    -- Cascade delete volume and all its files
    DBMS_FS.DROP_VOLUME('test_vol', TRUE);
END;
/
```

## SET_VOLUME

```plsql
DBMS_FS.SET_VOLUME(
    VOLUME_NAME IN VARCHAR2);
```

SET_VOLUME is used to set the default volume for the current session. After setting, subsequent file operations automatically use this volume as the operation target without the need to specify it repeatedly. The volume must be an existing volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | Name of the volume to activate (must be an existing volume). |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
END;
/
```

## GET_VOLUME

```plsql
DBMS_FS.GET_VOLUME(
    VOLUME_NAME OUT VARCHAR2);
```

GET_VOLUME is used to return the name of the currently activated volume in the session.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | Name of the currently activated volume (output parameter). |

***Example*** for Standalone Deployment

```plsql
DECLARE
    vol_name VARCHAR2(256);
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.GET_VOLUME(vol_name);
    DBMS_OUTPUT.PUT_LINE('Current volume: ' || vol_name);
END;
/

--result
Current volume: my_volume
```

## MKDIR

```plsql
DBMS_FS.MKDIR(
    DIR IN VARCHAR2);
```

MKDIR is used to create a directory under the currently activated volume. Directory names must be unique within the same volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2 | Directory path, supports multi-level paths. Name length limit is 256 bytes. |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.MKDIR('/mydir');
    DBMS_FS.MKDIR('/mydir/subdir');
END;
/
```

## RMDIR

```plsql
DBMS_FS.RMDIR(
    DIR     IN VARCHAR2,
    CASCADE IN BOOLEAN DEFAULT NULL);
```

RMDIR is used to delete a specified directory. Directory names must be unique within the same volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2 | Directory path. Name length limit is 256 bytes.       |
| CASCADE     | BOOLEAN  | Cascade delete flag. TRUE means recursively delete the directory and its contents, FALSE or unspecified means only delete empty directory.<br>If the target directory already has contents, specifying FALSE or not specifying this parameter will fail to delete the directory. |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');

    -- Delete empty directory
    DBMS_FS.RMDIR('/mydir');

    -- Cascade delete directory and all its files
    DBMS_FS.RMDIR('/mydir', TRUE);
END;
/
```

## LISTDIR

```plsql
DBMS_FS.LISTDIR(
    DIR         IN VARCHAR2,
    DIR_TABLE   OUT DIRENTRY_TABLE,
    DIR_COUNT   OUT INTEGER);
```

LISTDIR is used to return all files and subdirectories under a specified directory. Directory names must be unique within the same volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2        | Directory path. Name length limit is 256 bytes. |
| DIR_TABLE   | DIRENTRY_TABLE  | File directory table (output parameter).         |
| DIR_COUNT   | INTEGER         | File directory count (output parameter).         |

***Example*** for Standalone Deployment

```plsql
SET serveroutput ON;

DECLARE
    dir_tab  DBMS_FS.DIRENTRY_TABLE;
    dir_cnt  INTEGER;
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.MKDIR('/testdir');
    DBMS_FS.CREATE('testdir/file1.txt');
    DBMS_FS.MKDIR('/testdir/subdir');

    DBMS_FS.LISTDIR('/testdir', dir_tab, dir_cnt);

    FOR i IN 1..dir_cnt LOOP
        DBMS_OUTPUT.PUT_LINE('file_name: ' || dir_tab(i).FILE_NAME ||
                             ', file_type: ' || dir_tab(i).FILE_TYPE);
    END LOOP;
END;
/

--result
file_name: file1.txt, file_type: 0
file_name: subdir, file_type: 1
```

## CREATE

```plsql
DBMS_FS.CREATE(
    FILE_NAME IN VARCHAR2);
```

CREATE is used to create an empty file under the currently activated volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.CREATE('/mydir/myfile.txt');
END;
/
```

## REMOVE

```plsql
DBMS_FS.REMOVE(
    FILE_NAME IN VARCHAR2);
```

REMOVE is used to delete a specified file.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.REMOVE('/mydir/myfile.txt');
END;
/
```

## WRITE

```plsql
DBMS_FS.WRITE(
    FILE_NAME IN VARCHAR2,
    DATA      IN VARCHAR2);
```

WRITE is used to write data to a specified file.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |
| DATA        | VARCHAR2 | Data to write.                                        |

***Example*** for Standalone Deployment

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.CREATE('/mydir/myfile.txt');
    DBMS_FS.WRITE('/mydir/myfile.txt', 'Hello, YashanDB!');
END;
/
```

## READ

```plsql
DBMS_FS.READ(
    FILE_NAME    IN VARCHAR2,
    OFFSET       IN BIGINT,
    LENGTH       IN INTEGER,
    BUFFER       OUT VARCHAR2,
    BUFFER_SIZE  OUT INTEGER);
```

READ is used to read data from a specified file.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |
| OFFSET      | BIGINT   | Read starting position (in bytes).                     |
| LENGTH      | INTEGER  | Number of bytes to read.                               |
| BUFFER      | VARCHAR2 | Buffer to store the read data (output parameter).      |
| BUFFER_SIZE | INTEGER  | Actual number of bytes read (output parameter).        |

***Example*** for Standalone Deployment

```plsql
DECLARE
    buffer      VARCHAR2(4000);
    buffer_size INTEGER;
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.CREATE('/mydir/myfile.txt');
    DBMS_FS.WRITE('/mydir/myfile.txt', 'Hello, YashanDB!');

    DBMS_FS.READ('/mydir/myfile.txt', 0, 100, buffer, buffer_size);
    DBMS_OUTPUT.PUT_LINE('File content: ' || buffer);
    DBMS_OUTPUT.PUT_LINE('Bytes read: ' || buffer_size);
END;
/

--result
File content: Hello, YashanDB!
Bytes read: 16
```

## STAT

```plsql
DBMS_FS.STAT(
    FILE_NAME  IN VARCHAR2,
    FILE_STAT  OUT FILESTAT);
```

STAT is used to return file metadata information.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |
| FILE_STAT   | FILESTAT | File attribute structure (output parameter).           |

***Example*** for Standalone Deployment

```plsql
SET serveroutput ON;

DECLARE
    fstat DBMS_FS.FILESTAT;
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.CREATE('/mydir/myfile.txt');
    DBMS_FS.WRITE('/mydir/myfile.txt', 'Test data');

    DBMS_FS.STAT('/mydir/myfile.txt', fstat);
    DBMS_OUTPUT.PUT_LINE('file_id: ' || fstat.FILE_ID);
    DBMS_OUTPUT.PUT_LINE('file_size: ' || fstat.FILE_SIZE);
    DBMS_OUTPUT.PUT_LINE('file_type: ' || fstat.FILE_TYPE);
END;
/

--result
file_id: 12345
file_size: 9
file_type: 0
```

## EXIST

```plsql
DBMS_FS.EXIST(
    FILE_NAME IN VARCHAR2,
    IS_EXIST  OUT BOOLEAN);
```

EXIST is used to check whether a specified file exists in the current volume.

|  Parameter| Type| Description|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | File path. Name length limit is 256 bytes.            |
| IS_EXIST    | BOOLEAN  | Whether the file exists (output parameter).            |

***Example*** for Standalone Deployment

```plsql
DECLARE
    is_exist BOOLEAN;
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.CREATE('/mydir/myfile.txt');

    DBMS_FS.EXIST('/mydir/myfile.txt', is_exist);
    IF is_exist THEN
        DBMS_OUTPUT.PUT_LINE('File exists');
    ELSE
        DBMS_OUTPUT.PUT_LINE('File does not exist');
    END IF;
END;
/

--result
File exists
```
