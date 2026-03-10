The XMLTYPE type in YashanDB is a built-in UDT (User Defined Type) used to store XML data.

## Storage Attributes

|Type |Byte Length |
| -------- | ----------------------------- |
| XMLTYPE   | Row storage: [1,4G*DB_BLOCK_SIZE] |

Definition format:

|Type |Format |Rules |
|---------|---------| ------------------------ |
| XMLTYPE | XMLTYPE | Custom object type, no need to specify size |

## XMLTYPE Attributes

|Attribute Name |Type |Length |
|----------|------|-----------------------|
| XML      | CLOB | [1,4G*DB_BLOCK_SIZE]  |

## Usage Rules

### Usage Restrictions

The usage limitations for XMLTYPE type data are as follows:

- Applicable only to HEAP tables.

- Cannot be used as a GROUP BY grouping key.

- For [XML data processing functions](../内置函数/XML Function/00XML Function), methods, and advanced packages, if not specially stated, accessing external resources is not supported by default.

### Usage Method

The XMLTYPE type can be generated using the OBJECT method or [XMLPARSE](../内置函数/XML Function/XMLPARSE) function, and can be queried using the syntax `table_alias.column_name.attribute_name`.

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

## Constructor

The constructor is a method for creating XMLTYPE objects, supporting the generation of XML data from strings or CLOBs and encapsulating it into XMLTYPE objects.

```c
constructor function XMLType( 
    xmlData IN varchar, 
    schema IN varchar default NULL, 
    validated IN number default 0, 
    wellformed IN number default 0) 
 return self as result deterministic;
```

```c
constructor function XMLType( 
    xmlData IN clob, 
    schema IN varchar default NULL, 
    validated IN number default 0, 
    wellformed IN number default 0) 
 return self as result deterministic; 
```

|Parameter       |Data Type      |Description      |
|--------------------|-----------|---------------------------|
| xmlData | VARCHAR or CLOB | Specify the XML data to be converted, i.e., the source data. The data length must not exceed 64MB. |
| schema | VARCHAR | Specify validation rules (XML Schema) for the source data, optional.<br/>This parameter is only for compatibility, has no actual meaning, but performs parameter value validity checks.<br/>The parameter value is fixed to NULL. The function reports errors when set to other values. |
| validated | NUMBER | Whether to perform XML Schema structural validation on input source data (requires schema parameter), optional.<br/>This parameter is only for compatibility, has no actual meaning, but performs parameter value validity checks.<br/>Valid values are 0, NULL or numbers ≥1. Default is 0. The function will throw an error when set to an invalid value.   |
| wellformed | NUMBER | Identifies whether input source data conforms to basic syntax specifications. When set, the database skips corresponding format checks, optional.<br/>Valid values are 0, NULL or numbers ≥1. The function will throw an error when set to an invalid value.  <br/>* 0 (default): Unidentified, database performs format checks.<br/>* NULL: Unidentified.<br/>* Values ≥1: Identified, skips format checks. |


## Static Methods

Static methods are functions called directly via the XMLTYPE type, allowing XML data manipulation without instantiation. Invocation format: `XMLTYPE.function_name()`.

createXML() converts XML data of type VARCHAR or CLOB into an XMLTYPE object.

```c
STATIC FUNCTION createXML(
    xmlData IN varchar,
    schema IN varchar default null,
    validated IN number default 0,
    wellformed number default 0)
 return SYS.XMLTYPE deterministic;
```

```c
STATIC FUNCTION createXML(
    xmlData IN clob,
    schema IN varchar default null,
    validated IN number default 0,
    wellformed number default 0)
 return SYS.XMLTYPE deterministic;
```

|Parameter       |Data Type      |Description      |
|--------------------|-----------|---------------------------|
| xmlData | VARCHAR or CLOB | Specify the XML data to be converted, i.e., the source data. The data length must not exceed 64MB. |
| schema | VARCHAR | Specify validation rules (XML Schema) for the source data, optional.<br/>This parameter is only for compatibility, has no actual meaning, but performs parameter value validity checks.<br/>The parameter value is fixed to NULL. The function reports errors when set to other values. |
| validated | NUMBER | Whether to perform XML Schema structural validation on input source data (requires schema parameter), optional.<br/>This parameter is only for compatibility, has no actual meaning, but performs parameter value validity checks.<br/>Valid values are 0, NULL or numbers ≥1. Default is 0. The function will returns NULL when set to an invalid value.   |
| wellformed | NUMBER | Identifies whether input source data conforms to basic syntax specifications. When set, the database skips corresponding format checks, optional.<br/>Valid values are 0, NULL or numbers ≥1. The function will returns NULL when set to an invalid value.  <br/>* 0 (default): Unidentified, database performs format checks.<br/>* NULL: Unidentified.<br/>* Values ≥1: Identified, skips format checks. |

## Member Methods

Member methods enable querying, modifying, and extracting data from XMLTYPE instances. Invocation format: `xml.function_name()`. Example: Use `alias.column.GetClobVal()` to retrieve XMLTYPE column data.

### existsNode()

```c
MEMBER FUNCTION existsNode(
    xpath IN varchar,
    nsmap IN varchar default null)
return number deterministic;
```

existsNode() checks if a specified node exists in XML data. Returns `1` if exists, `0` otherwise. `xml.existsNode()` is equivalent to the [EXISTSNODE function](../内置函数/XML Function/EXISTSNODE).



|Parameter       |Data Type      |Description      |
|--------------------|-----------|---------------------------|
| xpath | VARCHAR | Specify the xpath expression to search. Size range: [1, 65534] bytes. Cannot be NULL. |
| nsmap | VARCHAR | Specify the XML namespace mappings (optional). |



### extract()

```c
MEMBER FUNCTION extract(
    xpath IN varchar,
    nsmap IN varchar default null)
return XMLTYPE deterministic;
```

extract() retrieves an XML fragment based on an XPath, concatenating all matched nodes. Returns XMLTYPE. `xml.extract()` is equivalent to the [XMLEXTRACT function](../内置函数/XML Function/XMLEXTRACT).



|Parameter       |Data Type      |Description      |
|--------------------|-----------|---------------------------|
| xpath | VARCHAR | Specify the xpath expression to search. Size range: [1, 65534] bytes. Cannot be NULL. |
| nsmap | VARCHAR | Specify the XML namespace mappings (optional). |



### getClobVal()

```c
MEMBER FUNCTION getClobVal()
return clob deterministic;
```

getClobVal() is used to retrieve the member of XMLTYPE, returning CLOB data type.

This function has no input parameters.

### getStringVal()

```c
MEMBER FUNCTION getStringVal()
return varchar deterministic;
```

getStringVal() returns the value of an XMLTYPE object as a string, either as a serialized XML representation or as the text of a text node itself.

This function has no input parameters.

### transform()

```c
MEMBER FUNCTION transform(
xsl IN SYS.XMLTYPE,
parammap IN varchar default NULL)
return SYS.XMLTYPE deterministic;
```

transform() function is used to transform XML data based on the XSL stylesheet and top-level parameter key-value pairs.

|Parameter       |Data Type      |Description      |
|--------------------|-----------|---------------------------|
| xsl | XMLTYPE | Specify the XSL stylesheet that describes the transformation format. It supports all syntax of XSLT 1.0. |
| parammap | VARCHAR | Specify the top-level parameters of XSL in the form of `name="value"` key-value pairs. |
