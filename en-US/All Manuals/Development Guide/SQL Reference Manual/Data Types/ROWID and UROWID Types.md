The default storage method for table objects in YashanDB standalone and YAC/Distributed Cluster Deployment is HEAP, with data organized by rows (Row). The system generates a globally unique record, known as ROWID, based on the physical address information of each row of data. ROWID is used for addressing when retrieving table data and can also serve as a unique identifier for each row of data.

ROWID is a special data type that can be declared by users as a column field or used as a pseudocolumn (a column not defined in the table structure but can be queried as a column field).

UROWID (Universal ROWID) is a type of data in YashanDB that stores ROWID in the form of a binary string.

Storage Properties
----

|Type |Byte Length |
| -------- | ---------------------- |
| ROWID    | 16                     |
| UROWID   | 1~8000, default length is 4000 |

Definition Format:

|Type |Format |Rule |
| -------- | ------------ | ------------------------------------------ |
| ROWID    | ROWID          | See data format below                      |
| UROWID   | UROWID(Size)   | Variable length binary string; if Size is not specified, default length is 4000 |

Size: Width, representing the maximum byte length.

<span id="Format" name="Format" class="yaslink"></span>

Data Format
----

The data format of ROWID is `dataoid:spaceid:fileid:blockid:dir`.

When used to store ROWID for row tables, the data format of UROWID is `0x1+hexadecimal representation of ROWID`.

### dataoid

Data object ID: the ID of the segment where the row resides; this value can be queried from the DATA_OBJECT_ID field in views such as USER_OBJECTS.

### spaceid

Space ID: the ID of the tablespace where the row resides; this value can be queried from the ID field in views such as V$TABLESPACE.

### fileid

File ID: the data file ID of the data file where the row resides within the corresponding tablespace; this value can be queried from the ID field in views such as V$DATAFILE.

### blockid

Block ID: the block ID within the corresponding file where the row resides.

### dir

Dir: the slot position of the row in the data block.

ROWID Usage Rules
----

ROWID can be used as a pseudocolumn but must be explicitly specified; it cannot be retrieved using a SELECT * statement.

ROWID can also be used as a column data type to store ROWID type data and supports DML operations such as insert, delete, update, and select.

In DML operations, YashanDB supports implicit data type conversion between ROWID and UROWID/RAW/CHAR types. However, if ROWID is used as a parameter in a function and does not match the required parameter data type, the function will not perform implicit conversion and will return an error.

The following restrictions apply to the use of ROWID type data:

- Cannot be used as a partition key.
- Only applicable to HEAP tables.

The following restrictions apply to the use of ROWID pseudocolumns:

- Cannot be used in subquery statements that include DISTINCT, GROUP BY, CONNECT BY, JOIN operators, ROWNUM pseudocolumns, or aggregate functions.

***Example*** for Heap tables

```sql
-- Query ROWID pseudocolumn
SELECT ROWID,* FROM area WHERE area_no='01';
ROWID              AREA_NO AREA_NAME    DHQ     
------------------ ------- ------------ ---------
1350:5:0:148:0     01      EastChina         Shanghai
 
SELECT LENGTH(ROWID) FROM area WHERE area_no='01';
        LENGTH(ROWID)
---------------------
                   14
 
SELECT ISNULL(ROWID) FROM area WHERE area_no='01';
[1:7]YAS-04401 data type STRING expected, but ROWID got

SELECT ROWID FROM (SELECT area_no FROM area GROUP BY area_no);
YAS-04827 cannot select ROWID from, or sample, a view with DISTINCT, GROUP BY, etc.
    
-- Create a column of ROWID type
CREATE TABLE ROWID_BASE(ID INT);

BEGIN
FOR I IN 1..10 LOOP
  INSERT INTO ROWID_BASE VALUES(I);
END LOOP;
COMMIT;
END;
/

CREATE TABLE ROWID_TEST(C1 ROWID);
INSERT INTO ROWID_TEST SELECT ROWID FROM ROWID_BASE;

-- Insert ROWID values as strings
INSERT INTO ROWID_TEST VALUES ('1350:5:0:148:0');
```

UROWID Usage Rules
----

YashanDB internally equates UROWID to the [RAW](RAW Type) type, hence the usage rules for UROWID are identical to those for RAW.

***Example*** for Heap tables

```sql
-- Insert ROWID from the previous example's ROWID_TEST table into UROWID_TABLE's C1 column, where C1 is of UROWID type
CREATE TABLE UROWID_TABLE(C1 UROWID);
INSERT INTO UROWID_TABLE SELECT ROWID FROM ROWID_TEST;

-- Insert UROWID values as strings, as long as they satisfy hexadecimal representation it will succeed
INSERT INTO UROWID_TABLE VALUES ('1234ABCD');
-- When converting to ROWID, it must meet the data format requirements of ROWID; otherwise, the conversion will fail
INSERT INTO ROWID_TEST 
SELECT c1 FROM UROWID_TABLE
WHERE c1 = '1234ABCD';
YAS-00008 type convert error : invalid ROWID

SELECT ISNULL(c1) FROM UROWID_TABLE
WHERE c1='0171070000000000000061000009000500' AND ROWNUM=1;
ISNULL(C1)           
-------------------- 
false 
```
