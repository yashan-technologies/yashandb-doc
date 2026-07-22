The DBMS_LOB package provides a set of built-in functions for operations related to LOB types (or types that can be implicitly converted to LOB). The DBMS_LOB package is not applicable to ISC Distributed Cluster Deployment.

## COMPARE

```plsql
DBMS_LOB.COMPARE (
   LOB_1            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   LOB_2            IN BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   AMOUNT           IN BIGINT DEFAULT MAX(LENGTH(LOB_1), LENGTH(LOB_2)),
   OFFSET_1         IN BIGINT DEFAULT 1,
   OFFSET_2         IN BIGINT DEFAULT 1)
  RETURN INTEGER;
```

The COMPARE function is used to compare the sizes of two LOB data, returning an INTEGER type value of 0, -1, or 1.

|Parameter |Description |
| -------- |--------------------------------------------------------------------------------------------------------------------|
| LOB_1    | The first LOB data to compare, which can be of type BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE.                 |
| LOB_2    | The second LOB data to compare, which can be of type BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE.                 |
| AMOUNT    | The length of the data to compare; the function retrieves the specified number of bytes (for BLOB or RAW) or characters (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR) from the offset position for comparison. The default is the larger value of the lengths of LOB_1 and LOB_2. |
| OFFSET_1 | The offset for LOB_1 used for comparison. The default is 1, meaning it starts retrieving the AMOUNT length of data from the first byte (for BLOB or RAW) or character (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR). |
| OFFSET_2 | The offset for LOB_2 used for comparison. The default is 1, meaning it starts retrieving the AMOUNT length of data from the first byte (for BLOB or RAW) or character (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR). |

The main rules for this function are:

- If LOB_1 and LOB_2 fully match within the range specified by the OFFSET and AMOUNT parameters, the function returns 0; if LOB_1 is less than LOB_2, the function returns -1; if LOB_1 is greater than LOB_2, the function returns 1.
- When one of LOB_1 and LOB_2 is of BLOB or RAW type, while the other is of CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR type, the function returns an error.
- When one of LOB_1 and LOB_2 is of CLOB, CHAR or VARCHAR type, while the other is of NCLOB, NCHAR or NVARCHAR type, the function encodes the CLOB, CHAR or VARCHAR type string into the national character set format and then compares it with the NCLOB, NCHAR or NVARCHAR type.
- When one of LOB_1 and LOB_2 is of BFILE type, the other must also be of BFILE type for comparison of the content data of the corresponding file.
- The function returns NULL in any of the following cases:
  - AMOUNT < 1
  - OFFSET_1 < 1
  - OFFSET_2 < 1
  - Any parameter input is NULL
- AMOUNT, OFFSET_1, OFFSET_2 can be numeric or character types, and the function implicitly converts them to BIGINT; this conversion truncates the decimal part for NUMBER type, while other types round to the nearest value.
- NCLOB, NCHAR, or NVARCHAR types are encoded according to the national character set UTF-16, calculated based on 2B per character.

***Example*** for Heap tables

```sql
CREATE TABLE clob_compare(C1 CLOB, C2 CLOB);
INSERT INTO clob_compare VALUES('124SD', '12332');
--Complete comparison of C1 and C2
SELECT DBMS_LOB.COMPARE(C1, C2) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           1
--Comparing the first two characters of C1 and C2
SELECT DBMS_LOB.COMPARE(C1, C2, 2) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           0
--Comparing the 2nd and 3rd characters of C1 with the 5th character of C2
SELECT DBMS_LOB.COMPARE(C1, C2, 2, 2, 5) compare_res FROM clob_compare;
 COMPARE_RES 
------------ 
           1

CREATE TABLE raw_compare(C1 RAW(10), C2 RAW(10));
INSERT INTO raw_compare VALUES('AB', 'A234');
--Complete comparison of C1 and C2
SELECT DBMS_LOB.COMPARE(C1, C2) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
           1
--Comparing the first byte of C1 and C2
SELECT DBMS_LOB.COMPARE(C1, C2, 1) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
           1
--C1 did not retrieve data, while C2 retrieved the comparison of the 2nd byte
SELECT DBMS_LOB.COMPARE(C1, C2, 2, 2, 2) compare_res FROM raw_compare;
 COMPARE_RES 
------------ 
          -1
```

## CONVERTTOBLOB

```plsql
DBMS_LOB.CONVERTTOBLOB(
  dest_lob       IN OUT     BLOB,
  src_clob       IN         CLOB,
  amount         IN         BIGINT,
  dest_offset    IN OUT     BIGINT,
  src_offset     IN OUT     BIGINT, 
  blob_csid      IN         NUMBER,
  lang_context   IN OUT     BIGINT,
  warning        OUT        BIGINT);
```

The CONVERTTOBLOB function is used to extract data from the source CLOB or NCLOB and convert it to the specified character set, then write it in binary format to the target BLOB.

|Parameter |Description |
| ------- |------------------------------------------------------------------|
| dest_lob | The target BLOB to receive the conversion result, must be an existing BLOB, and cannot be NULL. |
| src_clob | The source CLOB or NCLOB to be converted, must be an existing CLOB or NCLOB, and cannot be NULL.  |
| amount |The number of characters to convert each time. Use `DBMS_LOB.LOBMAXSIZE` to convert the entire CLOB or NCLOB data. |
| dest_offset | * As an input parameter, indicates the byte offset in the target BLOB where the write operation begins; specifying 1 indicates starting from the beginning.<br />* As an output parameter, indicates the new byte offset in the target BLOB after the write operation. |
| src_offset | * As an input parameter, indicates the character offset in the source source data where the read conversion begins; specifying 1 indicates starting from the beginning.<br />* As an output parameter, indicates the character offset in the source data immediately following the read operation.  |
| blob_csid | The target character set ID. Available values include:  <br>0: Indicates that the source and target data use the same character set <br>1: ASCII  <br>31: ISO88591  852: GBK  <br>854: GB18030  <br>873: UTF-8  <br>2000: UTF-16  |
| lang_context | * As an input parameter, indicates the current context information for the conversion.<br />* As an output parameter, indicates the context information after the conversion is complete. |
| warning        | Warning message indicating an exception occurred during conversion. Currently, the only possible warning is "inconvertible character," meaning the source character cannot be correctly converted to the target character. This message is defined as the constant value `DBMS_LOB.WARN_INCONVERTIBLE_CHAR`.           |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SET serveroutput ON;

DECLARE
  v_dest_blob BLOB;
  v_src_clob CLOB;
  v_amount BIGINT := DBMS_LOB.LOBMAXSIZE;
  v_dest_offset BIGINT := 1;
  v_src_offset BIGINT := 1;
  v_blob_csid BIGINT := 0;
  v_lang_context INTEGER := 0;
  v_warning INTEGER;
  v_clob_data VARCHAR(100) := 'test converttoblob';
BEGIN

-- Create a temporary CLOB and populate it with data
DBMS_LOB.CREATETEMPORARY(v_src_clob, DBMS_LOB.SESSION);
DBMS_LOB.WRITEAPPEND(v_src_clob, LENGTH(v_clob_data), v_clob_data);
  
-- Create a temporary BLOB to receive the conversion result
DBMS_LOB.CREATETEMPORARY(v_dest_blob, DBMS_LOB.CALL);
  
-- Execute the conversion
DBMS_LOB.CONVERTTOBLOB(
  dest_lob => v_dest_blob,
  src_clob => v_src_clob,
  amount => v_amount,
  dest_offset => v_dest_offset,
  src_offset => v_src_offset,
  blob_csid => v_blob_csid,
  lang_context => v_lang_context,
  warning => v_warning
);
DBMS_OUTPUT.PUT_LINE('CLOB converted to BLOB successfully, contents: ' || RAWTOHEX(DBMS_LOB.SUBSTR(v_dest_blob, DBMS_LOB.GET_LENGTH(v_dest_blob), 1)));
DBMS_LOB.FREETEMPORARY(v_dest_blob);
DBMS_LOB.FREETEMPORARY(v_src_clob);
END;
/
--result
CLOB converted to BLOB successfully, contents: 7465737420636F6E76657274746F626C6F62
```

## CONVERTTOCLOB

```plsql
DBMS_LOB.CONVERTTOCLOB(
  dest_lob       IN OUT     CLOB,
  src_blob       IN         BLOB,
  amount         IN         BIGINT,
  dest_offset    IN OUT     BIGINT,
  src_offset     IN OUT     BIGINT, 
  blob_csid      IN         NUMBER,
  lang_context   IN OUT     BIGINT,
  warning        OUT        BIGINT);
```

The CONVERTTOCLOB function is used to extract binary data from the source BLOB, convert it to character data according to the specified character set, and write it to the target CLOB.

|Parameter |Description |
| ------- |------------------------------------------------------------------|
| dest_lob | The target CLOB to receive the conversion result, must be an existing CLOB, and cannot be NULL.  |
| src_blob | The source BLOB to be converted, must be an existing BLOB, and cannot be NULL. |
| amount | The number of bytes to convert each time. Use `DBMS_LOB.LOBMAXSIZE` to convert the entire BLOB data.  |
| dest_offset | * As an input parameter, indicates the character offset in the target CLOB where the write operation begins; specifying 1 indicates starting from the beginning.<br />* As an output parameter, indicates the new character offset in the target CLOB after the write operation.  |
| src_offset | * As an input parameter, indicates the byte offset in the source BLOB where the read conversion begins; specifying 1 indicates starting from the beginning.<br />* As an output parameter, indicates the byte offset in the source BLOB immediately following the read operation. |
| blob_csid | The character set ID of the source data. Available values include:  <br>0: Indicates that the source and target data use the same character set <br>1: ASCII  <br>31: ISO88591  852: GBK  <br>854: GB18030  <br>873: UTF-8  <br>2000: UTF-16  |
| lang_context | * As an input parameter, indicates the current context information for the conversion.<br />* As an output parameter, indicates the context information after the conversion is complete. |
| warning        | Warning message, indicates an exception occurred during conversion. Currently, the only possible warning is "inconvertible character," meaning the source character cannot be correctly converted to the target character. This message is defined as the constant value `DBMS_LOB.WARN_INCONVERTIBLE_CHAR`.           |


***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SET serveroutput ON;

DECLARE
  v_dest_clob CLOB;
  v_src_blob BLOB;
  v_amount BIGINT := DBMS_LOB.LOBMAXSIZE;
  v_dest_offset BIGINT := 1;
  v_src_offset BIGINT := 1;
  v_blob_csid BIGINT := 0;
  v_lang_context INTEGER := 0;
  v_warning INTEGER;
  v_test_data VARCHAR(100) := 'test converttoclob';
  v_raw RAW(32767);
BEGIN

-- Create a temporary CLOB and populate it with data
DBMS_LOB.CREATETEMPORARY(v_src_blob);
v_raw := UTL_RAW.CAST_TO_RAW(v_test_data);
DBMS_LOB.WRITEAPPEND(v_src_blob, UTL_RAW.LENGTH(v_raw), v_raw);
  
-- Create a temporary BLOB to receive the conversion result
DBMS_LOB.CREATETEMPORARY(v_dest_clob);
  
-- Execute the conversion
DBMS_LOB.CONVERTTOCLOB(
  dest_lob => v_dest_clob,
  src_blob => v_src_blob,
  amount => v_amount,
  dest_offset => v_dest_offset,
  src_offset => v_src_offset,
  blob_csid => v_blob_csid,
  lang_context => v_lang_context,
  warning => v_warning
);
DBMS_OUTPUT.PUT_LINE('BLOB converted to CLOB successfully, contents: ' || v_dest_clob);
DBMS_LOB.FREETEMPORARY(v_dest_clob);
DBMS_LOB.FREETEMPORARY(v_src_blob);
END;
/

--result
BLOB converted to CLOB successfully, contents: test converttoclob
```

<span id="get_length" name="get_length"></span>

## GET_LENGTH

```plsql
DBMS_LOB.GET_LENGTH (
   LOB_LOC    IN  BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE) 
RETURN BIGINT;
```

The GET_LENGTH function is used to get the length of the specified LOB data, returning a value of type BIGINT.

|Parameter |Description |
| ------- |------------------------------------------------------------------|
| LOB_LOC   | Input parameter, type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE. |

The main rules for this function are:

- When the input parameter is NULL, the function returns NULL.
- When the input parameter is of BLOB, RAW or BFILE type, the function returns the length in bytes; when the input parameter is of CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR type, the function returns the length in characters.
- NCLOB, NCHAR, or NVARCHAR types are encoded according to the national character set UTF-16, calculated based on 2B per character.

***Example*** for Heap tables

```sql
CREATE TABLE get_length(C1 CLOB, C2 BLOB);
INSERT INTO get_length VALUES('1U8WQHSD', 'AF1234');
SELECT DBMS_LOB.GET_LENGTH(C1) cl_len,
	   DBMS_LOB.GET_LENGTH(C2) c2_len
FROM get_length;
               CL_LEN                C2_LEN 
--------------------- --------------------- 
                    8                     3
```

## GETLENGTH

The same as [GET_LENGTH](#get_length).

<span id="sub_str" name="sub_str"></span>

## SUB_STR

```plsql
DBMS_LOB.SUB_STR (
   LOB_LOC     IN    BLOB/CLOB/CHAR/VARCHAR/NCLOB/NCHAR/NVARCHAR/RAW/BFILE,
   AMOUNT      IN    INTEGER DEFAULT 65534,
   OFFSET      IN    BIGINT DEFAULT 1)
RETURN RAW/VARCHAR2;
```

The SUB_STR function is used to read a substring of the given LOB data, returning RAW or VARCHAR type data.

|Parameter |Description |
|---------|-------------------|
| LOB_LOC   | The LOB data to read, type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE.                |
| AMOUNT    | The number of bytes to read (for BLOB, RAW or BFILE) or characters (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR), with a range of [1,65534], default 65534. <br/> * Special case 1: NCLOB, NCHAR, or NVARCHAR types are encoded according to the national character set UTF-16 (i.e., calculated as 2B/character), this data will actually be treated with a maximum character count of 32767. <br/> * Special case 2: To avoid character truncation with incomplete characters when AMOUNT = 65534 (reporting character set mismatch), the system will automatically discard the trailing incomplete characters. If such discarding occurs, the output length will be less than 65534. |
| OFFSET    | The offset; default is 1, meaning it starts reading the AMOUNT length of data from the first byte (for BLOB, RAW or BFILE) or character (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR).       |

The main rules for this function are:

- The function reads a substring of length AMOUNT starting from OFFSET in LOB_LOC, in bytes (for BLOB or RAW) or characters (for CLOB, CHAR, VARCHAR, NCLOB, NCHAR, or NVARCHAR).
- When LOB_LOC is of BLOB, RAW or BFILE type, the function returns RAW type; when LOB_LOC is of CLOB, CHAR or VARCHAR type, the function returns VARCHAR type. When LOB_LOC is of NCLOB, NCHAR or NVARCHAR type, the function returns NVARCHAR type.
- The function returns NULL in any of the following cases:
  - AMOUNT < 1
  - AMOUNT > 65534
  - OFFSET < 1
  - Any parameter input is NULL

***Example*** for Heap tables

```sql
CREATE TABLE sub_str(C1 CLOB, C2 BLOB);
INSERT INTO sub_str VALUES('1U8WQHSD', 'AF1234');
--Read all of C1 and C2
SELECT DBMS_LOB.SUB_STR(C1) sub_c1,
	   DBMS_LOB.SUB_STR(C2) sub_c2 
FROM sub_str;
SUB_C1             SUB_C2          
------------------ ------------------
1U8WQHSD           AF1234                                                        

--Read 2 characters/bytes starting from the 3rd character/byte of C1/C2
SELECT DBMS_LOB.SUB_STR(C1, 2, 3) sub_c1,
	   DBMS_LOB.SUB_STR(C2, 2, 3) sub_c2
FROM sub_str;
SUB_C1             SUB_C2          
------------------ ------------------
8W                 34    
```

## SUBSTR

The same as [SUB_STR](#sub_str).

## CREATETEMPORARY

```plsql
DBMS_LOB.CREATETEMPORARY (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   cache    IN      BOOLEAN DEFAULT FALSE,
   dur      IN      INTEGER DEFAULT 12);
```

The CREATETEMPORARY procedure is used to create a temporary LOB with a length of 0.

|Parameter |Description |
| :---- | :---- |
| lob_loc   | LOB variable; the parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, or RAW type. |
| cache     | Reserved field, using the default value. This is a BOOLEAN type or a parameter that can be implicitly converted to BOOLEAN. It cannot be NULL. |
| dur | Reserved field. For parameters of INTEGER type or those that can be implicitly converted to INTEGER type, their numerical range is the same as that of the INTEGER type. It can be specified as the constants `DBMS_LOB.CALL` or `DBMS_LOB.SESSION`, cannot be NULL, and the default value is 12 (i.e., `DBMS_LOB.CALL`). |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
declare
  clob1 clob;
  len int;
begin
  dbms_lob.createtemporary(clob1);
  len := dbms_lob.getlength(clob1);
  dbms_output.put_line(len);
end;
/
--result
0
```

## FREETEMPORARY

```plsql
DBMS_LOB.FREETEMPORARY (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB);
```

The FREETEMPORARY procedure is used to release a temporary LOB. After the temporary LOB is released, it has a length of 0 and is regarded as an invalid temporary LOB in the DBMS_LOB advanced package.

|Parameter |Description |
| :---- |:---------------------------------------------------------|
| lob_loc   | Temporary LOB variable, cannot be an invalid temporary LOB. The parameter's data type can be BLOB, CLOB or NCLOB type. It cannot be NULL. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
declare
  clob1 clob;
begin
  dbms_lob.createtemporary(clob1);
  dbms_lob.freetemporary(clob1);
  dbms_output.put_line(length(clob1));
end;
/
--result
0
```

## ISTEMPORARY

```plsql
DBMS_LOB.ISTEMPORARY (
   lob_loc  IN  BLOB/CLOB/NCLOB)
RETURN INTEGER;
```

The ISTEMPORARY procedure is used to determine whether the input LOB is a temporary LOB.

- If the given parameter is a valid temporary LOB, it returns 1.

- If the given parameter is an invalid temporary LOB or not a temporary LOB, it returns 0.

- If the given parameter is NULL, it returns NULL.

|Parameter |Description |
| :---- | :---- |
| lob_loc   | LOB variable; the parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, or RAW type. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
create table isnot_templob(id int, c1 clob);
insert into isnot_templob values(1, 'lob');

declare
  clob1 clob;
  istemp int;
begin
  --valid temporary lob
  dbms_lob.createtemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --invalid temporary lob
  dbms_lob.freetemporary(clob1);
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
  --is not temporary lob
  select c1 into clob1 from isnot_templob where id = 1;
  istemp := dbms_lob.istemporary(clob1);
  dbms_output.put_line(istemp);
end;
/
--result
1
0
0
```

## OPEN

```plsql
DBMS_LOB.OPEN (
   lob_loc    IN OUT  BLOB/CLOB/NCLOB/BFILE,
   open_mode  IN      INTEGER);
```

The OPEN procedure is used to open a LOB in the specified mode.

|Parameter |Description |
| :---- |:---------------------------------------|
| lob_loc   | The LOB variable to be opened, cannot be an invalid temporary LOB, cannot be NULL. The parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE type.<br/>When lob_loc is of BFILE type, the maximum number of files allowed to be opened in the same session is controlled by the SESSION_MAX_OPEN_FILES parameter. |
| open_mode | The mode in which to open the LOB, cannot be NULL.<br/>* DBMS_LOB.LOB_FILEONLY: Indicates to open the BFILE in read-only mode.<br/>* DBMS_LOB.LOB_READONLY: Indicates to open the LOB in read-only mode.<br/>* DBMS_LOB.LOB_READWRITE: Indicates to open the LOB in read-write mode.<br/>If the lob_loc to be opened is a BFILE, open_mode can only be DBMS_LOB.LOB_READONLY or DBMS_LOB.FILE_READONLY. |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'read lob';
  amount int := 3;
  buffer varchar(10);
begin
  dbms_lob.open(clob1, dbms_lob.lob_readonly);
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_lob.close(clob1);
end;
/
--result
lob
```

## CLOSE

```plsql
DBMS_LOB.CLOSE (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB/BFILE);
```

The CLOSE procedure is used to close an opened LOB.

|Parameter |Description |
| :---- |:-----------------------------------------------------------------|
| lob_loc   | The LOB variable to be closed, cannot be an invalid temporary LOB. The parameter's data type can be BLOB, CLOB, NCLOB, or BFILE type. It cannot be NULL. |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob';
begin
  dbms_lob.open(clob1, dbms_lob.lob_readwrite);
  dbms_lob.write(clob1, 5, 5, 'write');
  dbms_output.put_line(clob1);
  dbms_lob.close(clob1);
end;
/
--result
lob write
```

## ISOPEN

```plsql
DBMS_LOB.ISOPEN (
   lob_loc  IN  BLOB/CLOB/NCLOB/BFILE) 
RETURN INTEGER; 
```

The ISOPEN function is used to determine whether a LOB is open. If the LOB is open, it returns 1, otherwise it returns 0.

|Parameter |Description |
| :---- |:---------------------------------------------------------------------------------------------|
| lob_loc   | LOB variable, cannot be an invalid temporary LOB. The parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE type. It cannot be NULL. |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob';
  isopen int;
begin
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.open(clob1, dbms_lob.lob_readwrite);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
  dbms_lob.close(clob1);
  isopen := dbms_lob.isopen(clob1);
  dbms_output.put_line(isopen);
end;
/
--result
0
1
0
```

## READ

```plsql
DBMS_LOB.READ (
   lob_loc  IN      BLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     RAW);

DBMS_LOB.READ (
   lob_loc  IN      CLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     VARCHAR);

DBMS_LOB.READ (
   lob_loc  IN      NCLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     NVARCHAR);
   
 DBMS_LOB.READ (
   lob_loc  IN      BFILE,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT,
   buffer   OUT     RAW);
```

The READ procedure is used to read a specified portion of the LOB data.

|Parameter |Description |
| :---- |:-------------------------------------------------------------------------------------------------------------------------------------------------|
| lob_loc   | The LOB variable to be read, cannot be an invalid temporary LOB. The parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE type. It cannot be NULL.                                                 |
| amount    | Input is the number of bytes to read for BLOB/BFILE or characters for CLOB/NCLOB, at least 1, cannot exceed 65534; output is the actual bytes read for BLOB/BFILE or characters read for CLOB/NCLOB.                             |
| offset    | The offset in the LOB from which to read, in bytes for BLOB or BFILE and in characters for CLOB or NCLOB, starting from 1 and cannot exceed the end of the LOB. Cannot be NULL.                                                          |
| buffer    | Output the read portion of the LOB data. When reading BLOB or BFILE, the parameter can be of RAW, BLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type; when reading CLOB, the parameter can be of CLOB, NCLOB, CHAR, VARCHAR, NCHAR, NVARCHAR, or RAW type. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
declare
  clob1 clob := 'read lob';
  amount int := 10;
  buffer varchar(10);
begin
  dbms_lob.read(clob1, amount, 6, buffer);
  dbms_output.put_line(buffer);
  dbms_output.put_line(amount);
end;
/
--result
lob
3
```

## WRITE

```plsql
DBMS_LOB.WRITE (
   lob_loc  IN OUT  BLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      RAW);

DBMS_LOB.WRITE (
   lob_loc  IN OUT  CLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      VARCHAR);

DBMS_LOB.WRITE (
   lob_loc  IN OUT  NCLOB,
   amount   IN      BIGINT,
   offset   IN      BIGINT,
   buffer   IN      NVARCHAR);
```

The WRITE procedure is used to write a specified number of bytes or characters at a specified position in the LOB.

|Parameter |Description |
| :---- |:--------------------------------------------------------|
| lob_loc   | The LOB variable to be written, cannot be an invalid temporary LOB. An UPDATE privilege is required when writing to LOB columns. Cannot be NULL.     |
| amount    | The number of bytes to be written for BLOB or characters for CLOB/NCLOB, at least 1 and cannot exceed 65534. Cannot be NULL.     |
| offset    | The offset in the LOB from which to start writing, in bytes for BLOB and in characters for CLOB/NCLOB, starting from 1. Cannot be NULL. |
| buffer    | The data to be written to the LOB, if it is of LOB type, cannot be an invalid temporary LOB. Cannot be NULL.                  |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob';
  buffer varchar(10) := 'write lob';
begin
  dbms_lob.write(clob1, 5, 5, buffer);
  dbms_output.put_line(clob1);
end;
/
--result
lob write
```

## APPEND

```plsql
DBMS_LOB.APPEND (
   dest_lob  IN OUT  BLOB, 
   src_lob   IN      BLOB); 

DBMS_LOB.APPEND (
   dest_lob  IN OUT  CLOB, 
   src_lob   IN      CLOB); 

DBMS_LOB.APPEND (
   dest_lob  IN OUT  NCLOB, 
   src_lob   IN      NCLOB); 
```

The APPEND procedure is used to append the data from the source LOB to the end of the target LOB.

|Parameter |Description |
| :---- |:----------------|
| dest_lob  | The target LOB variable, cannot be an invalid temporary LOB. An UPDATE privilege is required when updating LOB columns. The data type can be BLOB, RAW, CLOB, NCLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type. |
| src_lob   | The source LOB variable, cannot be an invalid temporary LOB. The data type can be BLOB, RAW, CLOB, NCLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type.                       |

***Example*** for Heap tables

```plsql
declare
  dest_clob clob := 'lob';
  src_clob clob := ' append';
begin
  dbms_lob.append(dest_clob, src_clob);
  dbms_output.put_line(dest_clob);
end;
/
--result
lob append
```

## WRITEAPPEND

```plsql
DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  BLOB, 
   amount   IN      BIGINT, 
   buffer   IN      RAW); 

DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  CLOB, 
   amount   IN      BIGINT, 
   buffer   IN      VARCHAR);

DBMS_LOB.WRITEAPPEND (
   lob_loc  IN OUT  NCLOB, 
   amount   IN      BIGINT, 
   buffer   IN      NVARCHAR);   
```

The WRITEAPPEND procedure is used to append a specified number of bytes or characters to the end of the LOB.

|Parameter |Description |
| :---- |:-------------------------------------------------------------------------------------------------------|
| lob_loc   | The LOB variable to which the data will be appended, cannot be an invalid temporary LOB. An UPDATE privilege is required when updating LOB columns. The parameter's data type can be BLOB, CLOB, VARCHAR, RAW, NCLOB, or NVARCHAR type. Cannot be NULL. |
| amount    | The number of bytes to append for BLOB or characters to append for CLOB/NCLOB, at least 1, cannot exceed 65534. Cannot be NULL. |
| buffer    | The data to be appended to the end of the LOB, cannot be an invalid temporary LOB. The parameter's data type can be RAW, BLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type. | 

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob';
  buffer varchar(20) := ' append lob';
begin
  dbms_lob.writeappend(clob1, 7, buffer);
  dbms_output.put_line(clob1);
end;
/
--result
lob append
```

## COPY

```plsql
DBMS_LOB.COPY (
  dest_lob     IN OUT  BLOB,
  src_lob      IN      BLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);

DBMS_LOB.COPY (
  dest_lob     IN OUT  CLOB,
  src_lob      IN      CLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);

DBMS_LOB.COPY (
  dest_lob     IN OUT  NCLOB,
  src_lob      IN      NCLOB,
  amount       IN      BIGINT,
  dest_offset  IN      BIGINT DEFAULT 1,
  src_offset   IN      BIGINT DEFAULT 1);
```

The COPY procedure is used to copy a specified portion of source LOB data to a specified position in the target LOB.

|Parameter |Description |
| :---- |:-----------------|
| dest_lob  | The target LOB variable, cannot be an invalid temporary LOB. An UPDATE privilege is required when updating LOB columns. The data type can be BLOB, RAW, CLOB, NCLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type. |
| src_lob   | The source LOB variable, cannot be an invalid temporary LOB. The data type can be BLOB, RAW, CLOB, NCLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type.         |
| amount    | The number of bytes for BLOB or characters for CLOB/NCLOB to copy, at least 1. Cannot be NULL.                                                                    |
| dest_offset | The target LOB offset, in bytes for BLOB and in characters for CLOB/NCLOB, starting from 1. Cannot be NULL.                                                    |
| src_offset | The source LOB offset, in bytes for BLOB and in characters for CLOB/NCLOB, starting from 1. Cannot be NULL.                                                    |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob';
begin
  dbms_lob.copy(clob1, 'copy lob', 4, 5, 1);
  dbms_output.put_line(clob1);
end;
/
--result
lob copy
```

## ERASE

```plsql
DBMS_LOB.ERASE (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB,
   amount   IN OUT  BIGINT,
   offset   IN      BIGINT DEFAULT 1);
```

The ERASE procedure is used to erase a specified portion of LOB data.

|Parameter |Description |
| :---- | :---- |
| lob_loc   | LOB variable, cannot be an invalid temporary LOB. The parameter's data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, or RAW type. An UPDATE privilege is required when erasing LOB columns. Cannot be NULL. |
| amount    | The number of bytes for BLOB or characters for CLOB/NCLOB to erase, at least 1. Cannot be NULL. |
| offset    | The offset of the data to erase in the LOB, in bytes for BLOB and in characters for CLOB/NCLOB, starting from 1. Cannot be NULL. |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'loberaseerase';
  amount int := 5;
begin
  dbms_lob.erase(clob1, amount, 4);
  dbms_output.put_line(clob1);
end;
/
--result
lob     erase
```

## TRIM

```plsql
DBMS_LOB.TRIM (
   lob_loc  IN OUT  BLOB/CLOB/NCLOB/BFILE,
   newlen   IN      BIGINT);
```

The TRIM procedure is used to trim the LOB to a specified length.

|Parameter |Description |
| :---- |:------------------------------------------------------------------------------------------------------------------|
| lob_loc   | LOB variable, cannot be an invalid temporary LOB. The data type can be BLOB, CLOB, CHAR, VARCHAR, NCLOB, NCHAR, NVARCHAR, RAW, or BFILE. An UPDATE privilege is required when trimming LOB columns. Cannot be NULL. |
| newlen    | The new length of the trimmed LOB, in bytes for BLOB/BFILE and in characters for CLOB/NCLOB, must be at least 0 and cannot exceed the length of the LOB. Cannot be NULL.            |

***Example*** for Heap tables

```plsql
declare
  clob1 clob := 'lob trim newlen';
begin
  dbms_lob.trim(clob1, 8);
  dbms_output.put_line(clob1);
end;
/
--result
lob trim
```

## INSTR

```plsql
DBMS_LOB.INSTR (
   lob_loc  IN  BLOB,
   pattern  IN  RAW,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  CLOB,
   pattern  IN  VARCHAR,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  NCLOB,
   pattern  IN  NVARCHAR,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;

DBMS_LOB.INSTR (
   lob_loc  IN  BFILE,
   pattern  IN  RAW,
   offset   IN  BIGINT DEFAULT 1,
   nth      IN  BIGINT DEFAULT 1)
RETURN BIGINT;
```

The INSTR function is used to return the position of the nth occurrence of the substring represented by pattern in CLOB, BLOB or NCLOB formatted large objects, matching can start from a specified position in the LOB, and if the match fails, returns 0.

|Parameter |Description |
| :---- |:----------------------------------------------------------------------------------------------------------------------------------------------------------|
| lob_loc   | The large object LOB variable to match. The parameter's data type can be BLOB, CLOB, BFILE, CHAR, VARCHAR, RAW, NCLOB, NCHAR, or NVARCHAR type.                                   |
| pattern    | The string to match, with a maximum length of 65534. When matching BLOB or BFILE, the parameter's data type can be RAW, BLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type. When matching CLOB or NCLOB, the parameter's data type can be RAW, CLOB, NCLOB, CHAR, VARCHAR, NCHAR, or NVARCHAR type. |
| offset     | The offset in the LOB from which to start matching, in bytes for BLOB and in characters for CLOB or NCLOB, default starting from 1.                                  |
| nth        | The number of match. If not set, defaults to indicating the position of the 1st matching occurrence.                                                                          |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
declare
  clob1 clob := 'lob instr lob instr';
  pos bigint;
begin
  pos := dbms_lob.instr(clob1, 'instr', 4, 2);
  dbms_output.put_line(pos);
end;
/
--result
15
```
<span id="dbms_lob_bfile" name="dbms_lob_bfile"></span>

## FILEOPEN

```plsql
DBMS_LOB.FILEOPEN (
   lob_loc     IN OUT  BFILE,
   open_mode   IN      INTEGER);
```

The FILEOPEN procedure is used to open a BFILE in read-only mode.

The maximum number of files allowed to be opened in the same session is controlled by the SESSION_MAX_OPEN_FILES parameter.

|Parameter |Description |
|:----------|:----------------------------------------------------------------------------------|
| lob_loc     | BFILE locator, cannot be NULL.                                                           |
| open_mode   | The mode to open the BFILE: supports DBMS_LOB.LOB_READONLY or DBMS_LOB.FILE_READONLY, indicating to open the BFILE in read-only mode, optional. |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
  dbms_lob.fileopen(lob_loc, 0);
  dbms_output.put_line('file opened');
  dbms_lob.fileclose(lob_loc);
end;
/
--result
file opened
```

## FILEISOPEN

```plsql
DBMS_LOB.FILEISOPEN (
   lob_loc     IN  BFILE);
```

The FILEISOPEN method is used to check whether the file specified by the BFILE locator is open.

If the file is open, it returns 1, otherwise it returns 0.

|Parameter |Description |
|:--------|:------------------|
| lob_loc     | BFILE locator, cannot be NULL. |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
  DBMS_OUTPUT.PUT_LINE('is file open '||DBMS_LOB.FILEISOPEN(lob_loc));
end;
/
--result
is file open 0
```

## FILEEXISTS

```plsql
DBMS_LOB.FILEEXISTS (
   lob_loc     IN  BFILE);
```

The FILEEXISTS method is used to check whether the actual file corresponding to the BFILE locator exists.

If the file exists, it returns 1; otherwise, it returns 0.

|Parameter |Description |
|:--------|:------------------|
| lob_loc     | BFILE locator, cannot be NULL. |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile := BFILENAME('TEST_DIR', 'test.txt');
begin
   DBMS_OUTPUT.PUT_LINE('is file exist '||DBMS_LOB.FILEEXISTS(lob_loc));
end;
/
--result
is file exist 1
```

## FILEGETNAME

```plsql
DBMS_LOB.FILEGETNAME (
   lob_loc     IN  BFILE,
   dir_alias   OUT VARCHAR
   filename    OUT VARCHAR);
```

The FILEGETNAME procedure returns the directory alias and file name based on the BFILE locator and does not check whether the physical file or directory actually exists.

|Parameter |Description |
|:----------|:------------------|
| lob_loc     | BFILE locator, cannot be NULL. |
| dir_alias   | Directory alias, cannot be NULL.     |
| filename    | File name, cannot be NULL.          |

***Example*** for Heap tables

```plsql
declare
  lob_loc bfile;
  dir_alias varchar(30);
  filename varchar(30);
begin
  lob_loc := bfilename('TEST_DIR', 'test.txt');
  dbms_lob.filegetname(lob_loc, dir_alias, filename);
  dbms_output.put_line('dir_alias is ' || dir_alias);
  dbms_output.put_line('filename is ' || filename);
end;
/
--result
dir_alias is TEST_DIR
filename is test.txt
```

## LOADCLOBFROMFILE

```plsql
DBMS_LOB.LOADCLOBFROMFILE (
   dest_lob     IN OUT  CLOB/NCLOB,
   src_bfile    IN      BFILE,
   amount       IN      BIGINT
   dest_offset  IN OUT  BIGINT,
   src_offset   IN OUT  BIGINT,
   bfile_csid   IN      NUMBER,
   lang_context IN OUT  BIGINT,
   warning      OUT     BIGINT);
```

The LOADCLOBFROMFILE procedure loads data from a BFILE to a CLOB or NCLOB, performing necessary character set conversion during the load.

|Parameter |Description |
|:-------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| dest_lob       | The CLOB or NCLOB to load into, cannot be NULL.                                                                                                                |
| src_bfile      | The source BFILE locator, cannot be NULL.                                                                                                                    |
| amount         | The number of bytes to load from the source BFILE, cannot be NULL. Use `DBMS_LOB.LOBMAXSIZE` to indicate loading until the end of the BFILE.                                                   |
| dest_offset    | In characters, (IN) dest_offset specifies the starting position in the target CLOB or NCLOB to start loading (origin: 1), (OUT) dest_offset indicates the new offset after the loading completes, which is the starting position for the next loading. dest_offset always points to the start of the first complete character after the load; if the last character is incomplete, the offset will reflect the start of that incomplete character. |
| src_offset     | In bytes, (IN) src_offset specifies the starting offset in the source BFILE to begin loading (origin: 1), (OUT) src_offset indicates the byte offset of the source BFILE after the load completes, which is the starting position for the next reading.                                                                  |
| bfile_csid     | Character set of the source BFILE, defaults to 0. Possible values are:<br/>`ASCII: 1`<br/>`GBK: 852`<br/>`UTF-8: 873`<br/>`ISO88591: 31`<br/>`UTF-16: 2000`<br/>`GB18030: 854`                                                           |
| lang_context    | Context information for the current loading condition, defaults to 0. This parameter is only for compatibility, with no special validation of its legality.                                                                |
| warning        | Warning message indicating an exception occurred during loading. Currently, the only possible warning is "inconvertible character," meaning the source character cannot be correctly converted to the target character. This message is defined as the constant value `DBMS_LOB.WARN_INCONVERTIBLE_CHAR`.           |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  l_clob CLOB;
  l_bfile BFILE;
  l_src_offset BIGINT := 1;
  l_dest_offset BIGINT := 1;
  l_lang_context BIGINT := 0;
  l_warning BIGINT;
begin
  DBMS_LOB.CREATETEMPORARY(l_clob, TRUE);
  l_bfile := BFILENAME('TEST_DIR', 'test.txt');
  DBMS_LOB.FILEOPEN(l_bfile, 0);
  dbms_lob.loadclobfromfile(
      dest_lob    => l_clob,
      src_bfile   => l_bfile,
      amount      => DBMS_LOB.LOBMAXSIZE,
      dest_offset => l_dest_offset,
      src_offset  => l_src_offset,
      bfile_csid  => 0,
      lang_context  => l_lang_context,
      warning       => l_warning
  );
  DBMS_LOB.FILECLOSE(l_bfile);
end;
/
```

## LOADBLOBFROMFILE

```plsql
DBMS_LOB.LOADBLOBFROMFILE (
   dest_lob     IN OUT  BLOB,
   src_bfile    IN      BFILE,
   amount       IN      BIGINT
   dest_offset  IN OUT  BIGINT,
   src_offset   IN OUT  BIGINT)
```

The LOADBLOBFROMFILE procedure loads data from a BFILE to a BLOB.

|Parameter |Description |
|:------------|:------------------------------------------------------------------------------------------------------|
| dest_lob       | The BLOB to load into, cannot be NULL.                                                                                   |
| src_bfile      | The source BFILE locator, cannot be NULL.                                                                                   |
| amount         | The number of bytes to load from the source BFILE, cannot be NULL. Use `DBMS_LOB.LOBMAXSIZE` to indicate loading until the end of the BFILE.                              |
| dest_offset    | In bytes, (IN) dest_offest specifies the starting position in the target BLOB to begin loading (origin: 1), (OUT) dest_offest indicates the new offset after the loading completes, which is the starting position for the next loading. |
| src_offset     | In bytes, (IN) src_offset specifies the starting offset in the source BFILE to begin loading (origin: 1), (OUT) src_offset indicates the byte offset in the source BFILE after the loading completes, which is the starting position for the next reading.                      |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  l_blob BLOB;
  l_bfile BFILE;
  l_src_offset BIGINT := 1;
  l_dest_offset BIGINT := 1;
begin
  DBMS_LOB.CREATETEMPORARY(l_blob, TRUE);
  l_bfile := BFILENAME('TEST_DIR', 'test.txt');
  DBMS_LOB.FILEOPEN(l_bfile, 0);
  dbms_lob.loadblobfromfile(
      dest_lob    => l_blob,
      src_bfile   => l_bfile,
      amount      => DBMS_LOB.LOBMAXSIZE,
      dest_offset => l_dest_offset,
      src_offset  => l_src_offset
  );
  DBMS_LOB.FILECLOSE(l_bfile);
end;
/
```

## FILECLOSE

```plsql
DBMS_LOB.FILEOCLOSE (
   lob_loc     IN OUT  BFILE);
```

The FILECLOSE procedure is used to close an open BFILE.

|Parameter |Description |
|:----------|:---------|
| lob_loc     | BFILE locator, cannot be NULL. |

***Example*** for Heap tables

```plsql
CREATE OR REPLACE DIRECTORY TEST_DIR AS '/tmp';
!echo "test file" > /tmp/test.txt
declare
  lob_loc bfile;
begin
  lob_loc := BFILENAME('TEST_DIR', 'test.txt');
  dbms_lob.fileopen(lob_loc);
  dbms_lob.fileclose(lob_loc);
  dbms_output.put_line('file closed');
end;
/
--result
file closed
```

## FILECLOSEALL

```plsql
DBMS_LOB.FILEOCLOSEALL ();
```

The FILECLOSEALL procedure is used to close all opened BFILEs in the current session.

***Example*** for Heap tables

```plsql
begin
  dbms_lob.filecloseall();
  dbms_output.put_line('all file closed');
end;
/
--result
all file closed
```

## Constant Definitions

|Constant |Type |Value |Description |
|:---------------------------------|:--------|:---------|:-------------------------|
| DBMS_LOB.CALL | INTEGER | 12 | Temporary LOBs are automatically released after the current call ends (e.g., when a stored procedure execution completes). |
| DBMS_LOB.SESSION | INTEGER | 10 | Temporary LOBs are released when the current database session ends (e.g., when the user exits the session)<br />This is for backward compatibility only; currently, the actual behavior is always `DBMS_LOB.CALL`. |
| DBMS_LOB.LOBMAXSIZE                    | BIGINT    | 2^63 - 1     | Maximum size of LOB (in bytes)          |
| DBMS_LOB.LOB_READONLY                  | INTEGER   | 0            | Open specified LOB in read-only mode |
| DBMS_LOB.LOB_READWRITE                 | INTEGER   | 1            | Open specified LOB in read-write mode |
| DBMS_LOB.FILE_READONLY                 | INTEGER   | 0            | Open specified BFILE in read-only mode |
| DBMS_LOB.DEFAULT_CSID                  | INTEGER   | 0            | Default CSID, indicating that the character set of the source LOB is the database character set |
| DBMS_LOB.DEFAULT_LANG_CTX               | INTEGER   | 0            | Default language context information     |
| DBMS_LOB.NO_WARNING                     | INTEGER   | 0            | No warning                               |
| DBMS_LOB.WARN_INCONVERTIBLE_CHAR       | INTEGER   | 1            | Warning that the source character set cannot be correctly converted |

## Exceptions

|Exception |Description |
|:---------------|:------------------|
| INVALID_ARGVAL       | Parameter exceeds the value range (except for numeric overflow). |
| UNOPENED_FILE        | Close an unopened LOB.                          |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
--invalid_argval exception
declare
  c1 clob:='12345';
begin
  dbms_lob.write(c1,0,1,'6');
exception
  when DBMS_LOB.INVALID_ARGVAL then
    dbms_output.put_line('argument value out of range');
end;
/
--result
argument value out of range

--unopened_file exception
declare
  c1 clob:='12345';
begin
  dbms_lob.close(c1);
exception
  when DBMS_LOB.UNOPENED_FILE then
    dbms_output.put_line('try to close unopened lob');
end;
/
--result
try to close unopened lob
```
