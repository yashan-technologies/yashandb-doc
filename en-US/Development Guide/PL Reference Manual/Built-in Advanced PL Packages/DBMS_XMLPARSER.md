The DBMS_XMLPARSER advanced package includes a Parser-type data interface and a set of built-in subprograms primarily used for parsing, validating, and transforming XML documents.



Current usage requirements for the DBMS_XMLPARSER advanced package are as follows:

- YashanDB has pre-configured the synonym XMLPARSER, which functions identically to the DBMS_XMLPARSER advanced package when used.

- XML files to be processed must be encoded in UTF8 character encoding.

- When accessing XML files at specified paths, ensure the files are located within the secure directory **SECURE_FILE_PRIV**. Please config the secure directory through user with ALTER SYSTEM privileges

  ```sql
  ALTER SYSTEM SET secure_file_priv='/data/tmp' scope=memory;
  
  SHOW PARAMETER SECURE_FILE_PRIV;

  NAME                                                             VALUE                                                  
  ---------------------------------------------------------------- ----------------------------------------------------------------
  SECURE_FILE_PRIV                                                 /data/tmp                                              

  ```

- The DBMS_XMLPARSER.PARSE function currently does not support accessing network addresses.

- The size of XML files to be processed must not exceed 64MB.

- External DTD references within and outside XML files are not currently supported.

- The DBMS_XMLPARSER package does not currently support concurrent execution.

## Data Structure

The DBMS_XMLPARSER system package defines data structures Parser for use by XMLPARSER advanced package related functions and procedures. 

|Name   |Data Type  |Description         |
| ------------- | ------- | ----------------------- |
| Parser   |RECORD  | Implements the DOM Document interface.         |

## Subprograms Summary of DBMS_XMLDOM

### freeParser Procedure

This procedure is used to release an instance of XML file parsing.

```plsql
XMLPARSER.freeParser(n IN Parser);
```

|Parameter |Purpose |
| --- | --- |
| n    | Specify the Parser  |

### getDocument Function

This function is used to obtain the DOM document object (DOMDocument) built by the Parser instance. It must be used after successfully calling the `parse` function or procedure; otherwise, it returns NULL.


```plsql
XMLPARSER.getDocument(p Parser) RETURN DBMS_XMLDOM.DOMDocument;
```

|Parameter |Purpose |
| --- | --- |
| p    | Specify the Parser  |

### newParser Function

Create and initialize an XML file Parser.

```plsql
XMLPARSER.newParser() RETURN Parser;
```

**Example**

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  value xmltype;
BEGIN
  parser := XMLPARSER.NEWPARSER();
  XMLPARSER.freeparser(parser);
END;
/

PL/SQL Succeed.
```

### parse Function/Procedure

The parse function and procedure are used to parse XML files stored at a given URL or within a file.

```plsql
Function XMLPARSER.parse(url IN VARCHAR2) RETURN DBMS_XMLDOM.DOMDocument;

Procedure XMLPARSER.parse(p Parser, url IN VARCHAR2);
```

|Parameter |Purpose |
| --- | --- |
| p    | Specify the Parser  |
| url  | Specify the location of the XML file. It must be an absolute path or an accessible URL, and must be configured in secure directory of the database.  |


### parseBuffer Procedure

This procedure is used to parse XML data in VARCHAR2 format.

```plsql
XMLPARSER.parseBuffer(p Parser, doc IN VARCHAR2);
```

|Parameter |Purpose |
| --- | --- |
| p    | Specify the Parser  |
| doc  | Input XML data in VARCHAR2 format. The maximum length must not exceed 32,767 characters  |


### parseClob Procedure

This procedure is used to parse XML data in CLOB format.

```plsql
XMLPARSER.parseClob(p Parser, doc IN CLOB);
```

|Parameter |Purpose |
| --- | --- |
| p    | Specify the Parser  |
| doc  | Input XML data in CLOB format. The CLOB content must be well-formed XML data.  |

## Example 

Example 1: Parse XML data

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  curr_node DBMS_XMLDOM.DOMNODE;
  node_list DBMS_XMLDOM.DOMNODELIST;
  element DBMS_XMLDOM.domelement;
  elem DBMS_XMLDOM.domelement;
  value clob;
  a int;
BEGIN
parser := XMLPARSER.NEWPARSER();
  value := '<?xml version="1.0"?>
<Employees>
  <Employee id="101">
    <Name>Alice</Name>
    <Dept>IT</Dept>
  </Employee>
  <Employee id="102">
     <Name>Bob</Name>
     <Dept>HR</Dept>
   </Employee>
</Employees>';
  
  -- Parse the XML file
  XMLPARSER.PARSEBUFFER(parser, value);
  doc := XMLPARSER.GETDOCUMENT(parser);
  
  element := DBMS_XMLDOM.GETDOCUMENTELEMENT(doc);
  node_list := DBMS_XMLDOM.getelementsbytagname(element, 'Employee');

  for i in 0..DBMS_XMLDOM.getlength(node_list) -1 loop
    curr_node := DBMS_XMLDOM.item(node_list, i);
    elem := DBMS_XMLDOM.makeElement(curr_node);
    
    -- get the value
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || 
        DBMS_XMLDOM.getAttribute(elem, 'id'));
    
    
    DBMS_OUTPUT.PUT_LINE('-----');
  end loop;
  XMLPARSER.FREEPARSER(parser);

END;
/

--result

Employee ID: 101
-----
Employee ID: 102
-----

PL/SQL Succeed.
```

Example 2: Parse XML file

```plsql
DECLARE
  parser XMLPARSER.PARSER;
  doc XMLDOM.DOMDOCUMENT;
  node XMLDOM.DOMNODE;
  value xmltype;
  curr_node DBMS_XMLDOM.DOMNODE;
  node_list DBMS_XMLDOM.DOMNODELIST;
  element DBMS_XMLDOM.domelement;
  elem DBMS_XMLDOM.domelement;
BEGIN
  parser := XMLPARSER.NEWPARSER();
  XMLPARSER.parse(parser, 'SECURE_FILE_PRIV/sample.xml');
  
  doc := XMLPARSER.GETDOCUMENT(parser);

  element := DBMS_XMLDOM.GETDOCUMENTELEMENT(doc);
  node_list := DBMS_XMLDOM.getelementsbytagname(element, 'Employee');

  for i in 0..DBMS_XMLDOM.getlength(node_list) -1 loop
    curr_node := DBMS_XMLDOM.item(node_list, i);
    elem := DBMS_XMLDOM.makeElement(curr_node);
    
    DBMS_OUTPUT.PUT_LINE('Employee ID: ' || 
        DBMS_XMLDOM.getAttribute(elem, 'id'));
    
    
    
    DBMS_OUTPUT.PUT_LINE('-----');
  end loop;
  XMLPARSER.FREEPARSER(parser);
END;
/

--result

Employee ID: 101
-----
Employee ID: 102
-----

PL/SQL Succeed.
```