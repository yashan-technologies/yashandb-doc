```ebnf
xmlsequence = XMLSEQUENCE "(" xml_data ")".
```
The XMLSEQUENCE function extracts multiple nodes from XML data of type XMLType, converts them into a table, and returns the result in a predefined xmltype array format.

**xml_data**

[General Expression](../../General SQL Syntax/expr), follows these rules:

* It must be a complete XML document with a root node. Its data type is XMLTYPE.
* The data length must not exceed 64MB.
* Its type is XMLTYPE. If the input is NULL, NULL will be returned.

***Example*** for Heap tables

```sql
-- Convert multiple nodes from XML data into table
SQL> SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence(xmlextract(xmltype('<root><book>Java</book><book>Python</book><book>Go</book><book>Rust</book></root>'),'/root/book'))) x;

RESULT                                                           
---------------------------------------------------------------- 
<book>Java</book>                                               
<book>Python</book>                                             
<book>Go</book>                                                 
<book>Rust</book>                                               

4 rows fetched.

-- The type of the result is a predefined UDT array format
SQL> SELECT typeof(x.column_value) AS result FROM TABLE(XMLSequence(XMLType('<root>John Doe</root>'))) x;

RESULT                                                           
---------------------------------------------------------------- 
udt_object                                                      

1 row fetched.

-- Returns an error message when the input format is invalid
SQL> SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence('<root>John Doe</root>')) x;

[1:75]YAS-04121 invalid datatype

-- Return NULL when the input is NULL
SQL> SELECT x.column_value.getclobval() AS result FROM TABLE(XMLSequence(null)) x;

RESULT                                                           
---------------------------------------------------------------- 

0 rows fetched.

SQL>

```
