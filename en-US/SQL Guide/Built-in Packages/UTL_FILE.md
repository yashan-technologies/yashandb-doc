The UTL_FILE package provides users with the ability to read and write system files, allowing users to manipulate files directly through UTL_FILE.

Users must possess the FILE privilege, and the target file must reside in the secure directory specified by the SECURE_FILE_PRIV parameter, which can be viewed using the SHOW PARAMETER SECURE_FILE_PRIV command.

## FOPEN

```PLSQL
UTL_FILE.FOPEN (
   location     IN VARCHAR2,
   filename     IN VARCHAR2,
   open_mode    IN VARCHAR2,
   max_linesize IN BINARY_INTEGER DEFAULT 1024) 
  RETURN FILE_TYPE;
```

The FOPEN function is used to open a file, allowing the specification of a maximum line length `max_lineSize`, which can range from [1,32000] bytes; if not specified, the default is 1024.

A maximum of 50 files can be opened in a session. The default privilege for opened files is -rw-r-----, where the file owner has read and write access, and the user group has read access.

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| location       | The file path must start with either "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| filename       | The name of the file to operate on.                           |
| open_mode      | The mode to open the file, supporting the following:<br/>* a: Opens the file in append mode; data is appended at the end of the file. If the specified file does not exist, a new file is created in write mode.<br/>* w: Opens the file in write mode, creating a new file and overwriting any existing file.<br/>* r: Opens the file in read-only mode.<br />* ab: Opens the file in binary append mode.<br />* wb: Opens the file in binary write mode.<br />* rb: Opens the file in binary read mode. |
| max_linesize   | The maximum line length for reading and writing files.       |

The returned FILE_TYPE is a private RECORD type defined by the UTL_FILE package (its contents should not be referenced or modified). If the BYTE_MODE member of HANDLE is modified or not assigned, subsequent calls to the function may fail.

```PLSQL
TYPE file_type IS RECORD (
id                BINARY_INTEGER, 
datatype          BINARY_INTEGER,
byte_mode         BOOLEAN);
```

|Parameter |Description |
| ------------ | ------------------------------------------------------------ |
| id             | The numerical value of the internal file handle ID.        |
| datatype       | This parameter is for syntax compatibility and has no actual meaning; its value is always 1. |
| byte_mode      | The encoding mode of the file, indicating whether it is opened as a binary file or a text file. |

***Example***

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

FCOPY copies the source file to a newly created destination file. If `start_line` and `end_line` are not specified, the entire source file will be copied by default. The source file is opened in read-only mode, while the destination file is opened in write mode. The content from `start_line` to `end_line` of the source file will be copied to the destination file.

|Parameter |Description |
| ------------- | ------------------------------------------------------ |
| src_location    | The path where the source file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| src_filename    | The name of the source file.                                |
| dest_location   | The path where the destination file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| dest_filename   | The name of the destination file.                            |
| start_line      | The starting line from which to copy from the source file, default is 1. |
| end_line        | The ending line to copy from the source file, default is NULL, indicating the last line of the source file. |

***Example***

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

The FFLUSH function flushes data from the specified open file handle that has not yet been written to disk. Generally, data is first written to cache and then to disk.

|Parameter |Description |
| ---- | --------------------------- |
| file      | The file value returned after a successful FOPEN. |

***Example***

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum INT;
BEGIN
      writenNum := 0;
	  BUFFER_W := LPAD('*',150 ,'*');
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan.txt','w',200);
    FOR i IN 1 .. 2 LOOP     
      UTL_FILE.PUT_LINE(HANDLE_W,BUFFER_W);
    END LOOP; 
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

The FGETATTR function returns the attributes of a disk file.

|Parameter |Description |
| ----------- | ------------------------------------------ |
| location       | The path where the source file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| filename       | The name of the file.                          |
| fexists        | A BOOL value indicating whether the file exists. |
| file_length    | The total byte length of the file; returns NULL if the file does not exist. |
| block_size     | This parameter is for syntax compatibility and has no actual meaning. |

***Example***

```plsql
DECLARE
    HANDLE_R  UTL_FILE.FILE_TYPE;
    BUFFER_W  VARCHAR2(1000);
    fexists   BOOLEAN;
    flen      NUMBER;
    blockSize INT;
BEGIN
    UTL_FILE.FGETATTR('/data','yashan.txt',fexists,flen,blockSize);
    DBMS_OUTPUT.PUT_LINE('yashan.txt:');
    IF fexists THEN
    DBMS_OUTPUT.PUT_LINE(flen);
    DBMS_OUTPUT.PUT_LINE(blockSize);
    END IF;
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

The FRENAME function renames an existing file.

|Parameter |Description |
| ------------- | -------------------------------- |
| src_location    | The path where the source file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| src_filename    | The name of the source file.                    |
| dest_location   | The path where the destination file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| dest_filename   | The name of the renamed file.                   |
| overwrite       | Whether to overwrite the destination file if it already exists. |

***Example***

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

The FREMOVE function is used to delete a file on disk, provided sufficient privilege is available.

|Parameter |Description |
| -------- | -------------- |
| location       | The path where the file is located; must start with "." or "/".<br/>If assigned a path starting with ".", it indicates a path relative to the YASDB_DATA environment variable. |
| filename       | The name of the file.                           |

***Example***

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

FSEEK is used to move the current file pointer forward or backward by a specified number of bytes; it can only be used with file handles opened in read mode, and an error will be thrown when using files opened in binary mode.

|Parameter |Description |
| --------------- | ------------------------------------------------------------ |
| file               | The file handle.                                           |
| absolute_offset    | The absolute offset value for moving the file pointer; defaults to NULL. |
| relative_offset    | The relative offset for moving the file pointer; if \<0 moves backward n bytes, if >0 moves forward n bytes. |

***Example***

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

## GET\_LINE

```plsql
UTL_FILE.GET_LINE (
   file        IN  FILE_TYPE,
   buffer      OUT VARCHAR2,
   len         IN  PLS_INTEGER DEFAULT NULL);
```

The GET_LINE function reads a line from the file, but the length of the read line cannot exceed the `max_linesize` set in the FOPEN function.

The GET_LINE function is only supported for file handles opened in read-only mode, and will throw an error when using files opened in binary mode.

|Parameter |Description |
| ------ | ------------------------------------------------------------ |
| file      | The file handle.                                 |
| buffer    | The cache where the read line data from the file is stored. |
| len       | The number of bytes to read from the file; defaults to NULL. If NULL, the database sets this value to `max_linesize`. |

***Example***

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

## IS\_OPEN

```plsql
UTL_FILE.IS_OPEN(
   FILE IN FILE_TYPE)
  RETURN BOOLEAN;
```

IS_OPEN is used to determine whether the current file handle is open.

|Parameter |Description |
| --------- | -------------------- |
| FILE_TYPE      | The handle for file operations.  |

***Example***

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

## NEW\_LINE

```plsql
UTL_FILE.NEW_LINE (
   file     IN FILE_TYPE,
   lines    IN BINARY_INTEGER DEFAULT 1);
```

The NEW_LINE function adds one or more newline characters to the file specified by the file handle. This function, in conjunction with the PUT function, is equivalent to the PUT_LINE function. An error will be thrown when using files opened in binary mode.

|Parameter |Description |
| ----- | ------------ |
| file       | The file handle.           |
| lines      | The number of new lines to add. |

***Example***

```plsql
DECLARE
    HANDLE_R UTL_FILE.FILE_TYPE;
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_R VARCHAR2(200);
    writenNum INT;
BEGIN
    writenNum := 0;
    HANDLE_R := UTL_FILE.FOPEN('/data','yashan.txt','r',200);
    HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',300);
       
    FOR i IN 1 .. 2 LOOP     
      UTL_FILE.GET_LINE(HANDLE_R,BUFFER_R,110);
      DBMS_OUTPUT.PUT_LINE(BUFFER_R);
       
      UTL_FILE.PUT(HANDLE_W,BUFFER_R);
      IF i = 2 THEN
        UTL_FILE.NEW_LINE(HANDLE_W);
        UTL_FILE.FFLUSH(HANDLE_W);
      END IF;
    END LOOP;
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

The PUT function writes data from the buffer to the specified file, but the file must be opened in write mode. The PUT function does not add newline characters to the data; to add new lines, use the NEW_LINE or PUT_LINE function. An error will be thrown when using files opened in binary mode.

|Parameter |Description |
| ------ | -------------------------------- |
| file      | The file handle.                       |
| buffer    | The location where the data to be written to the file is stored. |

***Example***

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum INT;
BEGIN
    writenNum := 0;
    BUFFER_W := LPAD('*', 20, '*');
    IF UTL_FILE.IS_OPEN(HANDLE_W) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    ELSE
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',130);
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE FIRST OPEN');
    END IF;
    FOR i IN 1 .. 2 LOOP     
      UTL_FILE.PUT(HANDLE_W,BUFFER_W);
      IF i = 2 THEN
        UTL_FILE.NEW_LINE(HANDLE_W);
        UTL_FILE.FFLUSH(HANDLE_W);
      END IF;
    END LOOP;
     
    UTL_FILE.fflush(HANDLE_W);
END;
/

--result
HANDLE_W FILE FIRST OPEN
```

## PUT\_LINE

```plsql
UTL_FILE.PUT_LINE (
   file      IN FILE_TYPE,
   buffer    IN VARCHAR2,
   autoflush IN BOOLEAN DEFAULT FALSE);
```

The PUT_LINE function writes data from the buffer to the specified file. The file must be opened in write mode; PUT_LINE automatically adds a newline character after the data. An error will be thrown when using files opened in binary mode.

|Parameter |Description |
| --------- | ----------------------------------------------------- |
| file          | The file handle.                             |
| buffer        | The cache buffer where the data to be written to the file is stored. |
| autoflush     | A boolean value indicating whether to immediately flush to disk after writing; defaults to false. |

***Example***

```plsql
DECLARE
    HANDLE_W UTL_FILE.FILE_TYPE;
    BUFFER_W VARCHAR2(200);
    writenNum INT;
BEGIN
    writenNum := 0;
    BUFFER_W := LPAD('*', 20, '*');
    IF UTL_FILE.IS_OPEN(HANDLE_W) THEN
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    ELSE
        HANDLE_W := UTL_FILE.FOPEN('/data','yashan1.txt','w',130);
        DBMS_OUTPUT.PUT_LINE('HANDLE_W FILE HAS OPENED');
    END IF;
    FOR i IN 1 .. 2 LOOP     
      UTL_FILE.PUT_LINE(HANDLE_W,BUFFER_W,false);
    END LOOP;
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

The FGETPOS function returns the current relative offset position of the open file (in bytes). An error will be thrown when using files opened in binary mode.

|Parameter |Description |
| --------- | ----------------------------------------------------- |
| file          | The file handle.                             |

***Example***

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

The FCLOSE function is used to close the file specified by FILE_TYPE.

|Parameter |Description |
| ---- | --------------------------- |
| file           | The file value returned after a successful FOPEN. |

***Example***

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

## FCLOSE\_ALL

```plsql
UTL_FILE.FCLOSE_ALL (
  );
```

The FCLOSE_ALL function closes all file handles that are open in the current session, typically used for emergency cleanup of file handles.

***Example***

``` plsql
DECLARE
   BEGIN
   UTL_FILE.FCLOSE_ALL();
   END;
   /
```

## PUT\_RAW

```plsql
UTL_FILE.PUT_RAW (
   file      IN FILE_TYPE,
   buffer    IN RAW,
   autoflush IN BOOLEAN DEFAULT FALSE);
```

The PUT_RAW function writes RAW data from the buffer to the specified file. The file must be opened in write mode, supporting binary mode. When opened in binary mode, no newline characters will be added.

|Parameter |Description |
| --------- | ------------------------------------------------------- |
| file          | The file handle.                             |
| buffer        | The RAW data buffer to be written to the file. |
| autoflush     | A boolean value indicating whether to immediately flush to disk after writing; defaults to false. |

***Example***

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

```

## GET\_RAW

```plsql
UTL_FILE.GET_RAW (
   file    IN  UTL_FILE.FILE_TYPE,
   buffer  OUT NOCOPY RAW,
   len     IN  PLS_INTEGER DEFAULT NULL);
```

The GET_RAW function reads RAW data from the file, with the read length not limited by the `max_linesize` set in the FOPEN function. The file must be opened in read-only mode, supporting binary mode.

|Parameter |Description |
| ------ | ------------------------------------------------------------ |
| file          | The file handle.                             |
| buffer        | Out parameter where RAW data read from the file is stored. |
| len           | The length to read in bytes, maximum value is 8000; defaults to NULL, indicating read until the end of file or the maximum length of RAW data (8000 bytes). |

***Example***

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

```

## Exception Handling

|Parameter |Description |
|---------------------|----------------------------------------------------------------------------------------------|
| INVALID_PATH            | The path of the file does not exist.                                     |
| INVALID_MODE            | The file open mode is incorrect.                                         |
| INVALID_FILEHANDLE      | The file handle is invalid.                                              |
| INVALID_OPERATION       | Operation error.                                                         |
| READ_ERROR              | An error occurred during the read operation.                             |
| WRITE_ERROR             | An error occurred during the write operation.                            |
| INVALID_MAXLINESIZE     | Invalid max_linesize value; valid range is 1-65534.                    |
| INVALID_FILENAME        | Invalid filename.                                                        |
| ACCESS_DENIED           | The file is denied access.                                               |
| INVALID_OFFSET          | Invalid offset address; both absolute_offset and relative_offset cannot be NULL, or absolute_offset < 0, or no valid point can be specified to move within the file. |
| DELETE_FAILED           | Deletion failed.                                                         |
| RENAME_FAILED           | Renaming failed.                                                         |

***Example***

```plsql
DECLARE 
       HANDLE_R UTL_FILE.FILE_TYPE;
	    fexists   BOOLEAN;
    flen      NUMBER;
    blockSize INT;
    BUFFER_R VARCHAR(10);
    BEGIN
		UTL_FILE.fremove('./a','t2.txtrttt');   
       DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
	  EXCEPTION WHEN UTL_FILE.INVALID_PATH THEN
	   dbms_output.PUT_LINE('test invalid path success!');
	   DBMS_OUTPUT.PUT_LINE(SQLCODE||':'||SQLERRM);
	WHEN UTL_FILE.INVALID_FILENAME   THEN
	   dbms_output.PUT_LINE('test invalid fileName success!');
   END;
   /
   
--result
test invalid path success!
330:YAS-00330 invalid path
```
