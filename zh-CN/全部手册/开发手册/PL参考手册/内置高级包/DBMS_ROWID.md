DBMS_ROWID包提供了一组内置的函数，用于获取[ROWID](../../SQL参考手册/数据类型/ROWID UROWID)的数据块号blockId、相对文件号fileId和行号dir信息。

该高级包不适用于存算一体分布式集群部署。

## ROWID\_BLOCK\_NUMBER

```plsql
DBMS_ROWID.ROWID_BLOCK_NUMBER (
   row_id      IN  ROWID,
   ts_type_in  IN  VARCHAR DEFAULT 'SMALLFILE')
RETURN NUMBER;
```

ROWID_BLOCK_NUMBER函数用于获取指定ROWID所对应的数据块号blockId，返回值为NUMBER类型。

|  参数| 描述|
| :---- |:-----------|
|row_id| 待解释的ROWID。该参数的数据类型可为ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR类型。 |
|ts_type_in| 保留字段，使用缺省值，默认值为'SMALLFILE'，不区分大小写，且仅接受SMALLFILE、BIGFILE。该参数的数据类型可以为VARCHAR类型和可以隐式转换为VARCHAR类型的其他数据类型，详情请查阅[数据类型转换](../../SQL参考手册/数据类型/数据类型转换)。 |

示例（单机/共享集群/分布式集群部署）

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

## ROWID\_RELATIVE\_FNO

```plsql
DBMS_ROWID.ROWID_RELATIVE_FNO (
   row_id      IN  ROWID,
   ts_type_in  IN  VARCHAR DEFAULT 'SMALLFILE')
RETURN NUMBER;
```

ROWID_RELATIVE_FNO函数用于获取指定ROWID所对应的相对文件号fileId，返回值为NUMBER类型。

|  参数| 描述|
| :---- |:-----------------------------------------------------------------------------------------------------------------------|
|row_id| 待解释的ROWID。该参数的数据类型可为ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR类型。                                                                                                              |
|ts_type_in| 保留字段，使用缺省值，默认值为'SMALLFILE'，不区分大小写，且仅接受SMALLFILE、BIGFILE。该参数的数据类型可以为VARCHAR类型和可以隐式转换为VARCHAR类型的其他数据类型，详情参阅[数据类型转换](../../SQL参考手册/数据类型/数据类型转换)。|

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT ROWID, DBMS_ROWID.ROWID_RELATIVE_FNO(ROWID)
FROM tbl_rowid
WHERE ID = 2;
--result
ROWID                                        DBMS_ROWID.ROWID_REL 
-------------------------------------------- -------------------- 
2801:0:0:3572:1                                                 0
```

## ROWID\_ROW\_NUMBER

```plsql
DBMS_ROWID.ROWID_ROW_NUMBER (
   row_id      IN  ROWID)
RETURN NUMBER;
```

ROWID_ROW_NUMBER函数用于获取指定ROWID所对应的行号dir，返回值为NUMBER类型。

|  参数| 描述|
| :---- | :---- |
|row_id|待解释的ROWID。该参数的数据类型可为ROWID/UROWID/RAW/CHAR/VARCHAR/NCHAR/NVARCHAR类型。|

示例（单机/共享集群/分布式集群部署）

```plsql
SELECT ROWID, DBMS_ROWID.ROWID_ROW_NUMBER(ROWID)
FROM tbl_rowid
WHERE ID = 2;
--result
ROWID                                        DBMS_ROWID.ROWID_ROW 
-------------------------------------------- -------------------- 
2801:0:0:3572:1                                                 1
```

## 异常说明

|  异常| 描述|
| :---- | :---- |
|ROWID_INVALID|无效的rowid|

示例（单机/共享集群/分布式集群部署）

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
