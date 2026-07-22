The DBMS_XMLGEN advanced package provides a set of built-in stored procedures, constants, and data structures for converting query result data to standard XML format documents.

Current usage requirements for the DBMS_XMLGEN advanced package are as follows:

- The XMLGEN batch conversion of query result sets to XML format cannot exceed 64MB in size. If the query result set exceeds this limit, please perform the conversion in batches.
- The maximum length of query result data that can be converted into XML node values is 64KB.
- When generating XML format documents, it supports defining encoding rules for special characters in the text.

## Data Structures

The DBMS_XMLGEN package uses UDT data type as function parameters or return values.

```plsql
SUBTYPE CTXHANDLE IS NUMBER;
SUBTYPE CTXTYPE IS NUMBER;
SUBTYPE CONVERSIONTYPE IS NUMBER;
```

## Constants

The DBMS_XMLGEN system package defines a set of constants for use by DBMS_XMLGEN advanced package related functions and subroutines. The table below provides detailed information.

|Name   |Data Type  |Value|Description         |
| ----------- | ----------|-----------|-----------|
| NONE                | NUMBER | 0   | Controls whether metadata information is generated when creating XML-formatted documents.       |
| DTD              | NUMBER | 1   | Embeds DTD `<!DOCTYPE ...>` into XML-formatted documents to define the valid structure of elements and attributes.       |
| SCHEMA                   | NUMBER | 2   | Embeds XML Schema (XSD) into XML-formatted documents to define the namespace and schema of the XML document.       |
| ENTITY_ENCODE          | NUMBER | 0   | Converts special characters in text to corresponding XML entity references to ensure safe parsing in XML without breaking document structure.       |
| ENTITY_DECODE       | NUMBER | 1   | Reverts XML entity references in the document back to their original special characters.       |
| DROP_NULLS      | NUMBER | 0   | Indicates that NULL values in data will not generate empty element nodes.       |
| NULL_ATTR                | NUMBER | 1   | Indicates that NULL values in data will generate complete empty element nodes with the attribute `xsi:nil="true"`, where xsi is the XML namespace. <br> After setting this, the namespace will become: xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance".       |
| EMPTY_TAG               | NUMBER | 2   | Indicates that NULL values in data will be generated as self-closing tags `<TAG/>` for empty element nodes.      |

## Subprograms Summary of DBMS_XMLGEN

###  CLOSECONTEXT Procedure

```plsql
DBMS_XMLGEN.CLOSECONTEXT(ctx IN CTXHANDLE);
```

This procedure is used to end the XML document generation operation and release system resources.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |

### CONVERT Function

```plsql
DBMS_XMLGEN.CONVERT(xmlData IN VARCHAR2, flag IN BIGINT DEFAULT ENTITY_ENCODE) RETURN VARCHAR2;

DBMS_XMLGEN.CONVERT(xmlData IN CLOB, flag IN BIGINT DEFAULT ENTITY_ENCODE) RETURN CLOB;
```

This function is used to convert special symbols in XML data into entities that comply with XML specifications.

This function do not support input parameters that are binding parameters.

|Parameter |Purpose |
| --- | --- |
| xmlData    | Can be directly passed as a string-type XML document, or as a CLOB-type XML document returned by the GETXML function or subroutine.   |
| flag  | Used to configure the converting behavior for special symbols in XML data. Default is ENTITY_ENCODE; passing 0 indicates special symbols should be encoded; passing any non-zero value or ENTITY_DECODE indicates no escaping should be decoded.   |

The following table lists the special symbols affected by this function.

|Symbol |Code |
| --- | --- |
| & |   &amp; | 
| < |    &lt; |
| > | &gt; |
| " |    &quot; |
| ' |    &apos; | 

### GETNUMROWSPROCESSED Function

```plsql
DBMS_XMLGEN.GETNUMROWSPROCESSED(ctx IN CTXHANDLE) RETURN NUMBER;
```

This function is used to retrieve the total number of rows of SQL query result data that need to be processed for the current XML document generation.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |

### GETXML Function and Procedure

```plsql
-- Procedure
DBMS_XMLGEN.GETXML(ctx IN CTXHANDLE, tmpclob IN OUT NOCOPY clob, dtdOrSchema IN NUMBER DEFAULT NONE);

-- Function
DBMS_XMLGEN.GETXML(ctx IN CTXHANDLE, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN CLOB;
DBMS_XMLGEN.GETXML(sqlQuery IN VARCHAR2, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN CLOB;
```

These functions are used to convert query result data to standard XML format documents, and return it as CLOB type.

This function do not support input parameters that are binding parameters.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |
| sqlQuery  | Input the query SQL statement.   |
| tmpclob   | Convert the query result into XML format and return it as a CLOB. <br> When using the GEMXML procedure, it concatenates the original data and the converted data before returning the result. |
| dtdOrSchema     | Configures whether the generated XML document includes DTD or Schema information.<br>- Default is None; can also pass 0 to indicate no DTD or Schema information.<br>- Passing 1 or DTD indicates adding DTD information. Currently only for compatibility, no specific meaning.<br>- Passing 2 or SCHEMA indicates adding Schema information. Currently only for compatibility, no specific meaning.   |

### GETXMLTYPE Function and Procedure

```plsql
-- Procedure
DBMS_XMLGEN.GETXMLTYPE(ctx IN CTXHANDLE, tmpxmltype IN OUT NOCOPY XMLTYPE, dtdOrSchema IN NUMBER DEFAULT NONE);

-- Function
DBMS_XMLGEN.GETXMLTYPE(ctx IN CTXHANDLE, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN sys.XMLTYPE;
DBMS_XMLGEN.GETXMLTYPE(sqlQuery IN VARCHAR2, dtdOrSchema IN NUMBER DEFAULT NONE) RETURN sys.XMLTYPE;
```

These functions are used to convert query result data to standard XML format documents, and return it as XMLTYPE type.

This function do not support input parameters that are binding parameters.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |
| sqlQuery  | Input the query SQL statement.   |
| tmpxmltype   | Convert the query result into XML format and return it as a XMLTYPE.  <br> When using the GEMXMLTYPE procedure, it concatenates the original data and the converted data before returning the result.   |
| dtdOrSchema     | Configures whether the generated XML document includes DTD or Schema information.<br>- Default is None; can also pass 0 to indicate no DTD or Schema information.<br>- Passing 1 or DTD indicates adding DTD information. Currently only for compatibility, no specific meaning.<br>- Passing 2 or SCHEMA indicates adding Schema information. Currently only for compatibility, no specific meaning.   |

### NEWCONTEXT Function

```plsql
DBMS_XMLGEN.NEWCONTEXT(queryString IN VARCHAR2) RETURN CTXHANDLE;
DBMS_XMLGEN.NEWCONTEXT(queryString IN SYS_REFCURSOR) RETURN CTXHANDLE;
```

This function is used to input the SQL statement.

SQL query statements can be passed directly as strings through input parameters, or they can be passed via the system dynamic cursor SYS_REFCURSOR and return a CTXHANDLE.

###  SETMAXROWS Procedure

```plsql
DBMS_XMLGEN.SETMAXROWS(ctx IN CTXHANDLE, maxRows IN NUMBER);
```

This procedure is used to configure the maximum number of rows of SQL query data to process during XML document generation. If the total number of rows in the SQL query exceeds the configured value, the XML conversion will stop once the configured limit is reached.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |
| maxRows  | Configures the maximum number of rows to convert.    |

###  SETNULLHANDLING Procedure

```plsql
DBMS_XMLGEN.SETNULLHANDLING(ctx IN CTXHANDLE, flag IN NUMBER DEFAULT DROP_NULLS);
```

This procedure sets how you want nulls handled during generation.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |
| flag     | Configures how NULL column values are handled when generating XML-formatted documents.<br>- Default is DROP_NULLS; can also pass 0 to indicate no empty element nodes will be generated.<br>- Passing 1 or NULL_ATTR indicates generating complete empty element nodes with the attribute `xsi:nil="true"`, where xsi is the XML namespace. After setting this, the namespace will become: xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance". <br>- Passing 2 or EMPTY_TAG indicates generating empty element nodes using self-closing tags `<TAG/>`.   |

###  SETSKIPROWS Procedure

```plsql
DBMS_XMLGEN.SETSKIPROWS(ctx IN CTXHANDLE, skipRows IN NUMBER);
```

This procedure is used to configure the maximum number of rows of SQL query data to skip during XML document generation, starting the XML conversion from the row immediately after the configured value.

|Parameter |Purpose |
| --- | --- |
| ctx    | Pass the return value of the NEWCONTEXT function as input.   |
| skipRows  | Configures the skip number of rows to convert.    | 


### Example

```sql
-- Create table and insert data
CREATE TABLE xmlgen_basic_employees (
                           employee_id NUMBER PRIMARY KEY,
                           first_name VARCHAR2(50),
                           last_name VARCHAR2(50),
                           email VARCHAR2(100),
                           department VARCHAR2(50),
                           salary NUMBER(10,2)
);

INSERT INTO xmlgen_basic_employees VALUES (1, 'John', 'Smith', 'john.smith@company.com', 'IT', 75000.00);
INSERT INTO xmlgen_basic_employees VALUES (2, 'Jane', 'Doe', 'jane.doe@company.com', 'HR', 65000.00);
INSERT INTO xmlgen_basic_employees VALUES (3, 'Michael', 'Johnson', 'michael.johnson@company.com', 'Finance', 80000.00);
INSERT INTO xmlgen_basic_employees VALUES (4, 'Sarah', 'Williams', 'sarah.williams@company.com', 'IT', 70000.00);
INSERT INTO xmlgen_basic_employees VALUES (5, 'David', 'Brown', 'david.brown@company.com', 'Marketing', 60000.00);
INSERT INTO xmlgen_basic_employees VALUES (6, 'Lisa', 'Davis', 'lisa.davis@company.com', 'HR', 68000.00);
INSERT INTO xmlgen_basic_employees VALUES (7, 'Robert', 'Miller', 'robert.miller@company.com', 'Finance', 85000.00);
INSERT INTO xmlgen_basic_employees VALUES (8, 'Jennifer', 'Wilson', 'jennifer.wilson@company.com', 'IT', 72000.00);
INSERT INTO xmlgen_basic_employees VALUES (9, NULL, 'Taylor', 'taylor@company.com', 'Marketing', 58000.00);
INSERT INTO xmlgen_basic_employees VALUES (10, 'Kevin', 'Anderson', NULL, 'Operations', 62000.00);

COMMIT;

-- Validate basic XML document generation capability
select dbms_xmlgen.getxml('select 1 as "-A" from dual') from dual;

DBMS_XMLGEN.GETXML('SELECT1AS"-A"FROMDUAL')                      
---------------------------------------------------------------- 
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <_x002D_A>1</_x002D_A>
  </ROW>
</ROWSET>

-- Validate ability to escape special characters; passing an empty flag parameter indicates encoding, while passing flag=1 indicates no decoding.
SELECT DBMS_XMLGEN.CONVERT('<>&"''') FROM dual;

DBMS_XMLGEN.CONVERT('<>&"''')                                  
---------------------------------------------------------------- 
&lt;&gt;&amp;&quot;&apos;                                       


SELECT DBMS_XMLGEN.CONVERT('<>&"''',1) FROM dual;

DBMS_XMLGEN.CONVERT('<>&"''',1)                                  
---------------------------------------------------------------- 
<>&"'   

```

The following example demonstrates generating an XML-formatted document as CLOB type, and controlling the handling of NULL values via NULL_ATTR and DROP_NULLS modes.

```plsql
DECLARE
    ctx_handle NUMBER;
    xml_result CLOB;
BEGIN
    DBMS_LOB.CREATETEMPORARY(xml_result);
    -- Create context for XML generation
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, email FROM xmlgen_basic_employees WHERE employee_id IN (9, 10) ORDER BY employee_id');

    -- Set null handling to show NULL values as empty tags (default behavior)
    DBMS_XMLGEN.setNullHandling(ctx_handle, DBMS_XMLGEN.NULL_ATTR);

    -- Generate XML output with NULL attributes
    DBMS_OUTPUT.PUT_LINE('XML with NULL handling using NULL_ATTR:');
    xml_result := DBMS_XMLGEN.getXML(ctx_handle);
    DBMS_OUTPUT.PUT_LINE(xml_result);

    -- Reset context and change null handling
    DBMS_XMLGEN.closeContext(ctx_handle);
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, email FROM xmlgen_basic_employees WHERE employee_id IN (9, 10) ORDER BY employee_id');

    -- Set null handling to omit NULL elements entirely
    DBMS_XMLGEN.setNullHandling(ctx_handle, DBMS_XMLGEN.DROP_NULLS);

    -- Generate XML output with omitted NULL values
    DBMS_OUTPUT.PUT_LINE('XML with NULL handling using DROP_NULLS:');
    xml_result := DBMS_XMLGEN.getXML(ctx_handle);
    DBMS_OUTPUT.PUT_LINE(xml_result);

    -- Close the context
    DBMS_XMLGEN.closeContext(ctx_handle);
    DBMS_LOB.FREETEMPORARY(xml_result);
END;
/

XML with NULL handling using NULL_ATTR:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ROW>
    <EMPLOYEE_ID>9</EMPLOYEE_ID>
    <FIRST_NAME xsi:nil="true"/>
    <LAST_NAME>Taylor</LAST_NAME>
    <EMAIL>taylor@company.com</EMAIL>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>10</EMPLOYEE_ID>
    <FIRST_NAME>Kevin</FIRST_NAME>
    <LAST_NAME>Anderson</LAST_NAME>
    <EMAIL xsi:nil="true"/>
  </ROW>
</ROWSET>

XML with NULL handling using DROP_NULLS:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <EMPLOYEE_ID>9</EMPLOYEE_ID>
    <LAST_NAME>Taylor</LAST_NAME>
    <EMAIL>taylor@company.com</EMAIL>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>10</EMPLOYEE_ID>
    <FIRST_NAME>Kevin</FIRST_NAME>
    <LAST_NAME>Anderson</LAST_NAME>
  </ROW>
</ROWSET>

PL/SQL Succeed.
```

The following example demonstrates generating an XML-formatted document as XMLTYPE type, and controlling to process at most 5 rows of data.

```plsql
DECLARE
    ctx_handle NUMBER;
    xml_result xmltype;
    rows_processed NUMBER;
BEGIN
    -- Create context for XML generation
    ctx_handle := DBMS_XMLGEN.newContext('SELECT employee_id, first_name, last_name, department, salary FROM xmlgen_basic_employees ORDER BY salary DESC');

    -- Limit to only 5 rows maximum
    DBMS_XMLGEN.setMaxRows(ctx_handle, 5);

    -- Generate XML output
    xml_result := DBMS_XMLGEN.getXmltype(ctx_handle);

    -- Get number of rows processed
    rows_processed := DBMS_XMLGEN.getNumRowsProcessed(ctx_handle);

    -- Display results
    DBMS_OUTPUT.PUT_LINE('Top 5 highest paid employees in XML format:');
    DBMS_OUTPUT.PUT_LINE(xml_result.getClobval());
    DBMS_OUTPUT.PUT_LINE('Number of rows processed: ' || rows_processed);

    -- Close the context
    DBMS_XMLGEN.closeContext(ctx_handle);
END;
/

Top 5 highest paid employees in XML format:
<?xml version="1.0" encoding="UTF-8"?>
<ROWSET>
  <ROW>
    <EMPLOYEE_ID>7</EMPLOYEE_ID>
    <FIRST_NAME>Robert</FIRST_NAME>
    <LAST_NAME>Miller</LAST_NAME>
    <DEPARTMENT>Finance</DEPARTMENT>
    <SALARY>85000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>3</EMPLOYEE_ID>
    <FIRST_NAME>Michael</FIRST_NAME>
    <LAST_NAME>Johnson</LAST_NAME>
    <DEPARTMENT>Finance</DEPARTMENT>
    <SALARY>80000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>1</EMPLOYEE_ID>
    <FIRST_NAME>John</FIRST_NAME>
    <LAST_NAME>Smith</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>75000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>8</EMPLOYEE_ID>
    <FIRST_NAME>Jennifer</FIRST_NAME>
    <LAST_NAME>Wilson</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>72000</SALARY>
  </ROW>
  <ROW>
    <EMPLOYEE_ID>4</EMPLOYEE_ID>
    <FIRST_NAME>Sarah</FIRST_NAME>
    <LAST_NAME>Williams</LAST_NAME>
    <DEPARTMENT>IT</DEPARTMENT>
    <SALARY>70000</SALARY>
  </ROW>
</ROWSET>

Number of rows processed: 5

PL/SQL Succeed.
```
