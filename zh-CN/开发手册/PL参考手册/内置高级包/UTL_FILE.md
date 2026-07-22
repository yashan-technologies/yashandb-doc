UTL_FILE包为用户提供了读写系统文件的能力，用户可以通过UTL_FILE操作系统文件。

用户需拥有FILE权限，且操作的目标文件需在SECURE_FILE_PRIV参数指定的安全目录下，可通过SHOW PARAMETER SECURE_FILE_PRIV查看安全目录。

## FOPEN

```plsql
UTL_FILE.FOPEN (
   location     IN VARCHAR2,
   filename     IN VARCHAR2,
   open_mode    IN VARCHAR2,
   max_linesize IN BINARY_INTEGER DEFAULT 1024) 
  RETURN FILE_TYPE;
```

FOPEN函数用于打开文件，可以指定一行的最大长度max_lineSize，取值范围为[1,32000]，单位为字节，不指定时默认为1024。

在一个会话中最多支持打开50个文件，文件打开的默认权限为-rw-r-----，文件所有者可读写，用户组可读。

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| location     | 文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。 |
| filename     | 操作的文件名字。                                             |
| open_mode    | 打开的方式，支持如下方式：<br/>* a：表示以追加方式打开文件，默认在文件末尾追加，若不存在指定文件会以写方式创建新文件。<br/>* w：表示以写方式打开文件，此时会创建一个新的文件并覆盖旧文件。<br/>* r：表示以只读方式打开文件。<br />* ab：表示以二进制、追加方式打开文件。<br />* wb：表示以二进制、写方式打开文件。<br />* rb：表示以二进制、读方式打开文件。 |
| max_linesize | 对文件读写的一行的最大长度。                                 |

返回的FILE_TYPE是UTL_FILE包定义的私有RECORD类型（其内容不应被引用或修改）。若HANDLE的成员BYTE_MODE被修改或未被赋值，调用子函数可能会失败。

```plsql
TYPE file_type IS RECORD (
id                BINARY_INTEGER, 
datatype          BINARY_INTEGER,
byte_mode         BOOLEAN);
```

|  参数| 描述|
| ------------ | ------------------------------------------------------------ |
| id     | 内部文件句柄编号的数值。 |
| datatype     | 该参数用于语法兼容，无实际含义，其值恒为1。                                             |
| byte_mode    | 文件打开的编码方式，作为二进制文件或作为文本文件。 |

示例

```plsql
DECLARE  
    HANDLE_R UTL_FILE.FILE_TYPE;
	HANDLE_A UTL_FILE.FILE_TYPE;
	HANDLE_W UTL_FILE.FILE_TYPE;
BEGIN
    HANDLE_R := UTL_FILE.fopen('/data','yashan.txt','r',100);
    HANDLE_A := UTL_FILE.fopen('/data','yashan.txt','a');
	HANDLE_W := UTL_FILE.fopen('/data','yashan.txt','w',32000);
END;
/
```

## FCOPY

``` plsql
UTL_FILE.FCOPY (
   src_location    IN VARCHAR2,
   src_filename    IN VARCHAR2,
   dest_location   IN VARCHAR2,
   dest_filename   IN VARCHAR2,
   start_line      IN BINARY_INTEGER DEFAULT 1,
   end_line        IN BINARY_INTEGER DEFAULT NULL);
```

FCOPY复制源文件到一个新创建的目标文件中。如果不指定start_line和end_line，将默认复制整个源文件。源文件以只读模式打开，目标文件以只写方式打开。将复制源文件的start_line到end_line的内容到目标文件中。

|  参数| 描述|
| ------------- | ------------------------------------------------------ |
| src_location  | 源文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。                                       |
| src_filename  | 源文件名字。                                             |
| dest_location | 目标文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。                                     |
| dest_filename | 目标文件名字。                                           |
| start_line    | 从源文件的第几行开始复制，默认为1。                      |
| end_line      | 复制到源文件的第几行，默认为NULL，表示源文件的最后一行。 |

示例

```plsql
BEGIN
    UTL_FILE.FCOPY('/data','yashan.txt','/data','yashan1.txt');   
END;
/  
```

## FFLUSH

```plsql
UTL_FILE.FFLUSH (
   file  IN FILE_TYPE);
```

FFLUSH函数将指定的打开的文件句柄对应的还没有刷盘的数据刷到磁盘上。一般情况下，数据先写到缓冲区再写到磁盘上。

|  参数| 描述|
| ---- | --------------------------- |
| file | FOPEN成功执行后返回的file值。 |

示例

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum INT;
BEGIN
      writenNum := 0;
	  BUFFER_W := lpad('*',150 ,'*');
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan.txt','w',200);
    for i in 1 .. 2 loop     
      UTL_FILE.PUT_LINE(HANDLE_W,BUFFER_W);
    end loop; 
    UTL_FILE.Fflush(HANDLE_W);	
END;
/ 
```

## FGETATTR

```plsql
UTL_FILE.FGETATTR(
   location     IN VARCHAR2, 
   filename     IN VARCHAR2, 
   fexists      OUT BOOLEAN, 
   file_length  OUT NUMBER, 
   block_size   OUT BINARY_INTEGER);
```

FGETATTR函数返回磁盘文件的属性。

|  参数| 描述|
| ----------- | ------------------------------------------ |
| location    | 源文件所在的位置，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。                           |
| filename    | 文件的名字。                                 |
| fexists     | 一个表示文件是否存在的BOOL值。               |
| file_length | 文件的字节总长度，如果文件不存在，返回NULL。 |
| block_size  | 该参数用于语法兼容，无实际含义。 |

示例

```plsql
DECLARE
    HANDLE_R  UTL_FILE.FILE_TYPE;
    BUFFER_W  VARCHAR2(1000);
    fexists   boolean;
    flen      number;
    blockSize int;
BEGIN
    UTL_FILE.FGETATTR('/data','yashan.txt',fexists,flen,blockSize);
    DBMS_OUTPUT.PUT_LINE('yashan.txt:');
    if fexists then
    DBMS_OUTPUT.PUT_LINE(flen);
    DBMS_OUTPUT.PUT_LINE(blockSize);
    end if;
  END;
/

--result
yashan.txt:
302
0
```

## FRENAME

```plsql
UTL_FILE.FRENAME (
   src_location     IN   VARCHAR2,
   src_filename     IN   VARCHAR2, 
   dest_location    IN   VARCHAR2,
   dest_filename    IN   VARCHAR2,
   overwrite        IN   BOOLEAN DEFAULT FALSE);
```

FRENAME函数用于重命名一个已经存在的文件。

|  参数| 描述|
| ------------- | -------------------------------- |
| src_location  | 源文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。                 |
| src_filename  | 源文件的名字。                     |
| dest_location  | 目标文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。               |
| dest_filename | 重命名的文件的名字。               |
| overwrite     | 如果目标文件已经存在，是否要覆盖。 |

示例

```plsql
BEGIN
    UTL_FILE.FRENAME('/data','yashan1.txt','/data','yashan2.txt');   
END;
/
```

## FREMOVE

```plsql
UTL_FILE.FREMOVE (
   location IN VARCHAR2,
   filename IN VARCHAR2);
```

FREMOVE函数用于删除磁盘的文件，如果有足够的权限的话可以成功删除。

|  参数| 描述|
| -------- | -------------- |
| location | 文件所在的路径，仅支持起始为“.”或“/”的路径。<br/>若被赋值起始为“.”的路径，表示相对于环境变量YASDB_DATA的路径。 |
| filename | 文件的名字。     |

示例

```plsql
BEGIN
    UTL_FILE.FREMOVE('/data','yashan2.txt');   
END;
/
```

## FSEEK

```plsql
UTL_FILE.FSEEK (
   file             IN OUT  UTL_FILE.FILE_TYPE,
   absolute_offset  IN      PLS_INTEGER,
   relative_offset  IN      PLS_INTEGER DEFAULT NULL);
```

FSEEK用来将当前的文件指针向前或者向后移动指定的字节数，只能用于以读方式打开的文件句柄，操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| --------------- | ------------------------------------------------------------ |
| file            | 文件句柄。                                                     |
| absolute_offset | 文件指针移动的绝对偏移值，默认为NULL。                       |
| relative_offset | 文件指针移动的相对偏移，基于当前的偏移值，如果<0则向前移动n个字节，如果>0则向后移动n个字节。 |

示例

```plsql
DECLARE  
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R  VARCHAR2(500);
BEGIN
    IF UTL_FILE.is_open(HANDLE_R) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE HAS OPENED');
    ELSE
        HANDLE_R := UTL_FILE.fopen('/data','yashan.txt','r',500);
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE FIRST OPEN');
    END IF;
     
    UTL_FILE.FSEEK(HANDLE_R,10,0);
    UTL_FILE.GET_LINE(HANDLE_R,BUFFER_R,200);
    DBMS_OUTPUT.PUT_LINE(BUFFER_R);
END;
/

--result
HANDLE_R FILE FIRST OPEN
********************************************************************************************************************************************
```

## GET_LINE

```plsql
UTL_FILE.GET_LINE (
   file        IN  FILE_TYPE,
   buffer      OUT VARCHAR2,
   len         IN  PLS_INTEGER DEFAULT NULL);
```

GET_LINE函数用于从文件中读取一行，但是读取的一行的长度不能超过FOPEN函数中设置的max_linesize。

GET_LINE函数仅支持在以只读模式打开的文件句柄中使用，操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| ------ | ------------------------------------------------------------ |
| file   | 文件句柄。                                                     |
| buffer | 从文件中读取的一行数据放置的缓冲区。                           |
| len    | 从文件中读取几个字节，默认为NULL。如果为NULL，数据库会将该值设置为max_linesize。 |

示例

```plsql
DECLARE  
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R  VARCHAR2(500);
BEGIN
    IF UTL_FILE.IS_OPEN(HANDLE_R) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE HAS OPENED');
    ELSE
        HANDLE_R := UTL_FILE.fopen('/data','yashan.txt','r',500);
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE FIRST OPEN');
    END IF;
     
    UTL_FILE.FSEEK(HANDLE_R,10,0);
    UTL_FILE.GET_LINE(HANDLE_R,BUFFER_R,200);
    DBMS_OUTPUT.PUT_LINE(BUFFER_R);
END;
/

--result
HANDLE_R FILE FIRST OPEN
********************************************************************************************************************************************
```

## IS_OPEN

```plsql
UTL_FILE.IS_OPEN(
   FILE IN FILE_TYPE)
  RETURN BOOLEAN;
```

IS_OPEN用于判断当前fileHandle对应的文件是否已经打开。

|  参数| 描述|
| --------- | -------------------- |
| FILE_TYPE | 对文件进行操作的句柄。 |

示例

```plsql
DECLARE
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(200);
BEGIN
    IF UTL_FILE.IS_OPEN(HANDLE_R) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE HAS OPENED');
    ELSE
        HANDLE_R := UTL_FILE.FOPEN('/data','yashan.txt','r',200);
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE FIRST OPEN');
    END IF;     
END;
/  

--result
HANDLE_R FILE FIRST OPEN
```

## NEW_LINE

```plsql
UTL_FILE.NEW_LINE (
   file     IN FILE_TYPE,
   lines    IN BINARY_INTEGER DEFAULT 1);
```

NEW_LINE函数用于对文件句柄指定的文件增加一行或者多行换行，这个函数和PUT函数配合的效果相当于PUT_LINE。操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| ----- | ------------ |
| file  | 文件句柄。     |
| lines | 增加几个换行。 |

示例

```plsql
DECLARE
    HANDLE_R UTL_FILE.FILE_TYPE;
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(200);
    writenNum int;
BEGIN
    writenNum := 0;
    HANDLE_R := UTL_FILE.FOPEN('/data','yashan.txt','r',200);
    HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',300);
       
    for i in 1 .. 2 loop     
      UTL_FILE.GET_LINE(HANDLE_R,BUFFER_R,110);
      DBMS_OUTPUT.PUT_LINE(BUFFER_R);
       
      UTL_FILE.PUT(HANDLE_W,BUFFER_R);
      if i = 2 then
        UTL_FILE.NEW_LINE(HANDLE_W);
        UTL_FILE.FFLUSH(HANDLE_W);
      end if;
    end loop;
    UTL_FILE.FFLUSH(HANDLE_W);
END;
/

--result
**************************************************************************************************************
****************************************
```

## PUT

```plsql
UTL_FILE.PUT (
   file      IN FILE_TYPE,
   buffer    IN VARCHAR2);
```

PUT函数将buffer中的数据写入到file指定的文件中，但是PUT函数中使用的file必须是以写模式打开的。PUT函数不会为数据添加换行符，如果要添加换行符可以使用NEW_LINE或者PUT_LINE函数。操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| ------ | -------------------------------- |
| file   | 文件句柄。                         |
| buffer | 需要写入到文件的数据存放的位置。 |

示例

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum int;
BEGIN
    writenNum := 0;
    BUFFER_W := lpad('*', 20, '*');
    IF UTL_FILE.IS_OPEN(HANDLE_W) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    ELSE
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',130);
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE FIRST OPEN');
    END IF;
    for i in 1 .. 2 loop     
      UTL_FILE.PUT(HANDLE_W,BUFFER_W);
      if i = 2 then
        UTL_FILE.NEW_LINE(HANDLE_W);
        UTL_FILE.FFLUSH(HANDLE_W);
      end if;
    end loop;
     
    UTL_FILE.fflush(HANDLE_W);
END;
/

--result
HANDLE_W FILE FIRST OPEN
```

## PUT_LINE

```plsql
UTL_FILE.PUT_LINE (
   file      IN FILE_TYPE,
   buffer    IN VARCHAR2,
   autoflush IN BOOLEAN DEFAULT FALSE);
```

PUT_LINE函数将buffer中的数写入到file指定的文件中。文件必须以写打开，PUT_LINE会在数据后面自动添加换行符，操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| --------- | ----------------------------------------------------- |
| file      | 文件句柄。                                              |
| buffer    | 需要写入到文件的缓冲buffer。                            |
| autoflush | bool值，用于表示在写之后是否立即刷到磁盘，默认是false。 |

示例

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum int;
BEGIN
    writenNum := 0;
    BUFFER_W := lpad('*', 20, '*');
    IF UTL_FILE.IS_OPEN(HANDLE_W) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    ELSE
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',130);
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    END IF;
    for i in 1 .. 2 loop     
      UTL_FILE.PUT_LINE(HANDLE_W,BUFFER_W,false);
    end loop;
    UTL_FILE.FFLUSH(HANDLE_W);
END;
/

--result
HANDLE_W FILE HAS OPENED
```

## FGETPOS

```plsql
UTL_FILE.FGETPOS (
   file      IN FILE_TYPE)
  RETURN PLS_INTEGER;
```

FGETPOS函数返回文件中当前打开文件的相对偏移位置（以字节为单位）。操作以二进制模式打开的文件时会报错。

|  参数| 描述|
| --------- | ----------------------------------------------------- |
| file      | 文件句柄。                                              |

示例

```plsql
DECLARE  
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(500);
    POS      INTEGER;
BEGIN
    IF UTL_FILE.IS_OPEN(HANDLE_R) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE HAS OPENED');
    ELSE
        HANDLE_R := UTL_FILE.fopen('/data','yashan1.txt','r',500);
        DBMS_OUTPUT.PUT_LINE('HANDLE_R FILE FIRST OPEN');
    END IF;
	
    UTL_FILE.get_line (HANDLE_R, BUFFER_R);        
    POS := UTL_FILE.FGETPOS(HANDLE_R);
    DBMS_OUTPUT.PUT_LINE('BEFORE_SEEK_POS:'||POS);

    UTL_FILE.FSEEK(HANDLE_R, NULL, -10);  

    POS := UTL_FILE.FGETPOS(HANDLE_R);
    DBMS_OUTPUT.PUT_LINE('AFTER_SEEK_POS:'||POS);
    UTL_FILE.fclose (HANDLE_R);  
END;
/

--result
HANDLE_R FILE FIRST OPEN
BEFORE_SEEK_POS:21
AFTER_SEEK_POS:11
```

## FCLOSE

``` plsql
UTL_FILE.FCLOSE (
   file IN OUT FILE_TYPE);
```

FCLOSE函数用于关闭通过FILE_TYPE指定的文件。

|  参数| 描述|
| ---- | --------------------------- |
| file | FOPEN成功执行后返回的file值。 |

示例

```plsql
DECLARE
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(200);
BEGIN
        HANDLE_R := UTL_FILE.FOPEN('/data','yashan1.txt','r',200);
        UTL_FILE.FCLOSE(HANDLE_R);
END;
/ 
```

## FCLOSE_ALL

```plsql
UTL_FILE.FCLOSE_ALL (
  );
```

FCLOSE_ALL函数用于关闭当前session下所有打开的file handle，多数用于紧急情况下的文件句柄清理。

示例

``` plsql
DECLARE
   BEGIN
   UTL_FILE.FCLOSE_ALL();
   END;
   /
```

## PUT_RAW

```plsql
UTL_FILE.PUT_RAW (
   file      IN FILE_TYPE,
   buffer    IN RAW,
   autoflush IN BOOLEAN DEFAULT FALSE);
```

PUT_RAW函数将buffer中的RAW数据写入到file指定的文件中。文件必须以写打开，支持二进制打开模式。当以二进制打开时，不会追加换行符。

|  参数| 描述|
| --------- | ------------------------------------------------------- |
| file      | 文件句柄。                                              |
| buffer    | 需要写入到文件的RAW数据buffer。                         |
| autoflush | bool值，用于表示在写之后是否立即刷到磁盘，默认是false。 |

示例

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_RAW RAW(200);
BEGIN
        HANDLE_W := UTL_FILE.fopen('/data','file1.txt','WB',200);
        UTL_FILE.PUT_RAW(HANDLE_W, '313233203535354348454E0AE99988');
        DBMS_OUTPUT.PUT_LINE('put_raw 313233203535354348454E0AE99988 to file1.txt done');
        UTL_FILE.fclose(HANDLE_W);
END;
/

-- result
put_raw 313233203535354348454E0AE99988 to file1.txt done

PL/SQL Succeed.
```

## GET_RAW

```plsql
UTL_FILE.GET_RAW (
   file    IN  UTL_FILE.FILE_TYPE,
   buffer  OUT NOCOPY RAW,
   len     IN  PLS_INTEGER DEFAULT NULL);
```

GET_RAW函数用于从文件中读取RAW数据，读取长度不受FOPEN函数中设置的max_linesize影响。文件必须以只读打开，支持二进制打开模式。

|  参数| 描述|
| ------ | ------------------------------------------------------------ |
| file   | 文件句柄。                                                   |
| buffer | 出参，从文件中读取RAW数据的存放缓冲区。                      |
| len    | 指定读取的长度，单位为字节，最大值为8000；默认为NULL，表示读取到文件末尾或RAW数据的最大长度（8000字节）。 |

示例

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    HANDLE_R UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(200);
    BUFFER_RAW RAW(200);
BEGIN
        HANDLE_W := UTL_FILE.fopen('/data','file1.txt','WB',200);
        UTL_FILE.PUT_RAW(HANDLE_W, '313233203535354348454E0AE99988');
        DBMS_OUTPUT.PUT_LINE('put_raw 313233203535354348454E0AE99988 to file1.txt done');
        UTL_FILE.fclose(HANDLE_W);

        HANDLE_R := UTL_FILE.fopen('/data','file1.txt','R',200);
        DBMS_OUTPUT.PUT_LINE('get_raw from file1');
        UTL_FILE.GET_RAW(HANDLE_R, BUFFER_RAW);
        DBMS_OUTPUT.PUT_LINE(BUFFER_RAW);
        UTL_FILE.fclose(HANDLE_R);
END;
/

-- result
put_raw 313233203535354348454E0AE99988 to file1.txt done
get_raw from file1
313233203535354348454E0AE999880A

PL/SQL Succeed.
```

## 异常说明

|  参数| 描述|
|---------------------|----------------------------------------------------------------------------------------------|
| INVALID_PATH        | 文件所在的路径不存在。                                                                                  |
| INVALID_MODE        | 文件打开模式不对。                                                                                    |
| INVALID_FILEHANDLE    | 文件句柄无效。                                                                                      |
| INVALID_OPERATION   | 操作错误。                                                                                        |
| READ_ERROR          | 在读操作时发生了错误。                                                                                  |
| WRITE_ERROR         | 在写操作时发生了错误。                                                                                  |
| INVALID_MAXLINESIZE | 无效的max_linesize值，正常范围在1-65534。                                                               |
| INVALID_FILENAME    | 无效的文件名。                                                                                      |
| ACCESS_DENIED       | 文件拒绝被访问。                                                                                     |
| INVALID_OFFSET      | 无效的偏移地址，当absolute_offset和relative_offset同时为NULL，或absolute_offset<0，或没有一个可以有效地指定需要跳转到文件的哪个位置。 |
| DELETE_FAILED       | 删除失败。                                                                                        |
| RENAME_FAILED       | 重命名失败。                                                                                       |

示例

```plsql
DECLARE 
       HANDLE_R UTL_FILE.FILE_TYPE;
	    fexists   boolean;
    flen      number;
    blockSize int;
    BUFFER_R VARCHAR(10);
    BEGIN
		UTL_FILE.fremove('./a','t2.txtrttt');   
       DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
	  exception when UTL_FILE.INVALID_PATH THEN
	   dbms_output.PUT_LINE('test invalid path success!');
	   DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
	when UTL_FILE.INVALID_FILENAME   THEN
	   dbms_output.PUT_LINE('test invalid fileName success!');
   END;
   /
   
--result
test invalid path success!
330:YAS-00330 invalid path
```
