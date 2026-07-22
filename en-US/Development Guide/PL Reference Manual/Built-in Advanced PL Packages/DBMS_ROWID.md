The DBMS_ROWID package provides a set of built-in functions to obtain the data block number (blockId), relative file number (fileId), and row directory information of a [ROWID](../../SQL Reference Manual/Data Types/ROWID and UROWID Types).

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## ROWID_BLOCK_NUMBER

```plsql
DBMS_ROWID.ROWID_BLOCK_NUMBER (
   row_id      IN  ROWID,
   ts_type_in  IN  VARCHAR DEFAULT 'SMALLFILE')
RETURN NUMBER;
```

The ROWID_BLOCK_NUMBER function is used to retrieve the data block number (blockId) corresponding to the specified ROWID. The return value is of NUMBER type.

|Parameter |Description |
| :---- |:-----------|
| row_id    | The ROWID to be explained. This parameter can be of type ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR. |
| ts_type_in| Reserved field, uses the default value, default is 'SMALLFILE', case insensitive, and only accepts SMALLFILE, BIGFILE. This parameter can be of VARCHAR type and can be implicitly converted to VARCHAR type. For details, refer to [Data Type Conversion](../../SQL Reference Manual/Data Types/Data Type Conversion). |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
CREATE TABLE tbl_rowid(ID INT);
INSERT INTO tbl_rowid VALUES(1);
INSERT INTO tbl_rowid VALUES(2);

SELECT ROWID, DBMS_ROWID.ROWID_BLOCK_NUMBER(ROWID)
FROM tbl_rowid
WHERE ID = 2;
--result
ROWID                                        DBMS_ROWID.ROWID_BLO 
-------------------------------------------- -------------------- 
2801:0:0:3572:1                                              3572
```

## ROWID_RELATIVE_FNO

```plsql
DBMS_ROWID.ROWID_RELATIVE_FNO (
   row_id      IN  ROWID,
   ts_type_in  IN  VARCHAR DEFAULT 'SMALLFILE')
RETURN NUMBER;
```

The ROWID_RELATIVE_FNO function is used to retrieve the relative file number (fileId) corresponding to the specified ROWID. The return value is of NUMBER type.

|Parameter |Description |
| :---- |:-----------------------------------------------------------------------------------------------------------------------|
| row_id    | The ROWID to be explained. This parameter can be of type ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR. |
| ts_type_in| Reserved field, uses the default value, default is 'SMALLFILE', case insensitive, and only accepts SMALLFILE, BIGFILE. This parameter can be of VARCHAR type and can be implicitly converted to VARCHAR type. For details, refer to [Data Type Conversion](../../SQL Reference Manual/Data Types/Data Type Conversion).|

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT ROWID, DBMS_ROWID.ROWID_RELATIVE_FNO(ROWID)
FROM tbl_rowid
WHERE ID = 2;
--result
ROWID                                        DBMS_ROWID.ROWID_REL 
-------------------------------------------- -------------------- 
2801:0:0:3572:1                                                 0
```

## ROWID_ROW_NUMBER

```plsql
DBMS_ROWID.ROWID_ROW_NUMBER (
   row_id      IN  ROWID)
RETURN NUMBER;
```

The ROWID_ROW_NUMBER function is used to retrieve the row number (dir) corresponding to the specified ROWID. The return value is of NUMBER type.

|Parameter |Description |
| :---- | :---- |
| row_id    | The ROWID to be explained. This parameter can be of type ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR. |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
SELECT ROWID, DBMS_ROWID.ROWID_ROW_NUMBER(ROWID)
FROM tbl_rowid
WHERE ID = 2;
--result
ROWID                                        DBMS_ROWID.ROWID_ROW 
-------------------------------------------- -------------------- 
2801:0:0:3572:1                                                 1
```

## Exception Description

|Exception |Description |
| :---- | :---- |
| ROWID_INVALID  | Invalid rowid      |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```plsql
--rowid_invalid exception
DECLARE
  BLK NUMBER;
  RID VARCHAR(100) := 'aaa';
BEGIN
  BLK := DBMS_ROWID.ROWID_BLOCK_NUMBER(RID);
EXCEPTION
  WHEN DBMS_ROWID.ROWID_INVALID THEN
    DBMS_OUTPUT.PUT_LINE('invalid rowid: ' || RID);
END;
/
--result
invalid rowid: aaa                                               
```
