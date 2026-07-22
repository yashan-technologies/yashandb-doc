DBMS_FS是YashanDB FS的高级包入口，为用户提供面向存储卷的统一文件操作接口。

> **Note**:
>
> DBMS_FS仅支持单机部署。

使用DBMS_FS高级包时，操作顺序如下：首先调用CREATE_VOLUME创建卷，接着调用SET_VOLUME设置当前卷，此后便可针对当前卷中的文件执行管理操作，例如创建、读写、删除等。

## 数据类型

### DIRENTRY

```plsql
TYPE DIRENTRY IS RECORD (
    FILE_NAME VARCHAR(255),
    FILE_TYPE NUMBER);
```

DIRENTRY是目录条目的记录类型。

|  字段| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR(255) | 文件或目录名称。                                       |
| FILE_TYPE   | NUMBER        | 条目类型。0表示文件，1表示目录。                       |

### DIRENTRY_TABLE

```plsql
TYPE DIRENTRY_TABLE IS TABLE OF DIRENTRY;
```

DIRENTRY_TABLE是LISTDIR接口的出参类型，用于返回目录下的文件或子目录列表。

### FILESTAT

```plsql
TYPE FILESTAT IS RECORD (
    FILE_ID     NUMBER,
    FILE_SIZE   NUMBER,
    CREATE_TIME DATE,
    MODIFY_TIME DATE,
    FILE_TYPE   NUMBER);
```

FILESTAT是STAT接口的出参类型，用于返回文件的元数据信息。

|  字段| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_ID     | NUMBER | 文件ID。                                                   |
| FILE_SIZE   | NUMBER | 文件大小（单位：字节）。                                   |
| CREATE_TIME | DATE   | 创建时间。                                                 |
| MODIFY_TIME | DATE   | 修改时间。                                                 |
| FILE_TYPE   | NUMBER | 文件类型。0表示普通文件，1表示目录。                       |

## CREATE_VOLUME

```plsql
DBMS_FS.CREATE_VOLUME(
    VOLUME_NAME     IN VARCHAR2,
    TS_NAME         IN VARCHAR2,
    BUCKET_NAME     IN VARCHAR2,
    FILE_SIZE_LIMIT IN BIGINT);
```

CREATE_VOLUME过程用于创建一个新的存储卷。卷是文件系统的顶层组织单元，每个卷绑定到指定的表空间和存储桶。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME     | VARCHAR2 | 卷名称。名称长度限制为64字节。                        |
| TS_NAME         | VARCHAR2 | 表空间名称。                                           |
| BUCKET_NAME     | VARCHAR2 | 存储桶名称。                                           |
| FILE_SIZE_LIMIT | BIGINT   | 容量限制（单位：字节）。                               |

示例（单机部署）

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

DROP_VOLUME用于删除指定的存储卷。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | 待删除的卷名称。                                       |
| CASCADE     | BOOLEAN  | 级联删除标志。TRUE表示级联删除卷内所有文件，FALSE或不指定表示仅删除空卷。 |

示例（单机部署）

```plsql
BEGIN
    -- 删除空卷
    DBMS_FS.DROP_VOLUME('test_vol', FALSE);

    -- 级联删除卷及其所有文件
    DBMS_FS.DROP_VOLUME('test_vol', TRUE);
END;
/
```

## SET_VOLUME

```plsql
DBMS_FS.SET_VOLUME(
    VOLUME_NAME IN VARCHAR2);
```

SET_VOLUME用于设置当前会话的默认卷。设置后，后续的文件操作自动使用该卷作为操作目标，无需重复指定。必须指定为已存在的卷。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | 待激活的卷名称（已存在的卷）。                       |

示例（单机部署）

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

GET_VOLUME用于返回当前会话激活的卷名称。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| VOLUME_NAME | VARCHAR2 | 当前激活的卷名称（出参）。                           |

示例（单机部署）

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

MKDIR用于在当前激活的卷中创建目录。目录名称在同一卷内必须唯一。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2 | 目录路径，支持多级路径。名称长度限制为256字节。      |

示例（单机部署）

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

RMDIR用于删除指定的目录。目录名称必须在同一个卷内唯一。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2 | 目录路径。名称长度限制为256字节。                  |
| CASCADE     | BOOLEAN  | 级联删除标志。TRUE表示递归删除目录及其内容，FALSE或不指定表示仅删除空目录。<br>若目标目录下已有内容，指定为FALSE或不指定该参数将无法删除该目录。 |

示例（单机部署）

```plsql
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');

    -- 删除空目录
    DBMS_FS.RMDIR('/mydir');

    -- 级联删除目录及其所有文件
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

LISTDIR用于返回指定目录下的所有文件和子目录列表。目录名称必须在同一个卷内唯一。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DIR         | VARCHAR2        | 目录路径。名称长度限制为256字节。            |
| DIR_TABLE   | DIRENTRY_TABLE  | 文件目录表（出参）。                         |
| DIR_COUNT   | INTEGER         | 文件目录数量（出参）。                       |

示例（单机部署）

```plsql
SET serveroutput ON;

DECLARE
    dir_tab  DBMS_FS.DIRENTRY_TABLE;
    dir_cnt  INTEGER;
BEGIN
    DBMS_FS.SET_VOLUME('my_volume');
    DBMS_FS.MKDIR('/testdir');
    DBMS_FS.CREATE('/testdir/file1.txt');
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

CREATE用于在当前激活的卷中创建空文件。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |

示例（单机部署）

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

REMOVE用于删除指定的文件。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |

示例（单机部署）

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

WRITE用于将数据写入指定文件。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |
| DATA        | VARCHAR2 | 待写入的数据。                                       |

示例（单机部署）

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

READ用于从指定文件读取数据。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |
| OFFSET      | BIGINT   | 读取起始位置（单位：字节）。                       |
| LENGTH      | INTEGER  | 读取的字节数。                                     |
| BUFFER      | VARCHAR2 | 用于存储读取数据的缓冲区（出参）。                 |
| BUFFER_SIZE | INTEGER  | 实际读取的字节数（出参）。                         |

示例（单机部署）

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

STAT用于返回文件的元数据信息。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |
| FILE_STAT   | FILESTAT | 文件属性结构（出参）。                             |

示例（单机部署）

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

EXIST用于检查指定文件是否存在于当前卷中。

|  参数| 类型| 说明|
| :---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| FILE_NAME   | VARCHAR2 | 文件路径。名称长度限制为256字节。                  |
| IS_EXIST    | BOOLEAN  | 文件是否存在（出参）。                             |

示例（单机部署）

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
