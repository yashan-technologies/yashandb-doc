The XMLTYPE type in YashanDB is a built-in UDT (User Defined Type) used to store XML data.

## Storage Attributes

|**Type** |Byte Length |
| -------- | ----------------------------- |
| XMLTYPE   | Row storage: [1,4G*DB_BLOCK_SIZE] |

## Definition Format

|**Type** |Format |Rules |
|---------|---------| ------------------------ |
| XMLTYPE   | XMLTYPE   | Custom object type, no need to specify size |

## XMLTYPE Attributes

|**Attribute Name** |Type |Length |
|----------|------|-----------------------|
| XML      | CLOB | [1,4G*DB_BLOCK_SIZE]  |

Usage rules are as follows:

- It is only applicable to HEAP tables.
- The XMLTYPE type can be generated using the OBJECT method or XMLParse function, and can be queried using the syntax `table_alias.column_name.attribute_name`.
- XMLTYPE supports the GetClobVal member function, which is used to retrieve the member of XMLTYPE, returning CLOB data type. You can query data with `table_alias.column_name.GetClobVal()`.

***Example*** for Heap tables

```sql
-- 1. Create a table tbl_xml with an XMLTYPE field under the sales user
CREATE TABLE tbl_xml (col xmltype); 

-- 2. Insert data into tbl_xml table
INSERT INTO tbl_xml VALUES(XMLType('<employee><id>2</id><name>hahaha</name></employee>'));

-- 3.1 View the data in tbl_xml table using table_alias.column_name.attribute_name
SELECT t.col.xml FROM tbl_xml t;
-- 3.2 Call getClobVal member function to view data in tbl_xml table
SELECT t.col.getClobVal() FROM tbl_xml t;

-- 4. Modify data in tbl_xml table
UPDATE tbl_xml SET col=XMLType('<employee><id>1</id><name>join</name></employee>');
```
