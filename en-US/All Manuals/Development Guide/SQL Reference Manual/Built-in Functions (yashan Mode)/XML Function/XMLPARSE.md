```ebnf+diagram
xmlparse::= XMLPARSE "(" "DOCUMENT|CONTENT" xml_string [WELLFORMED] ")"
```

The XMLPARSE function is used to parse the input string into a data type of XMLTYPE.

* When using the DOCUMENT field, xml_string must be a complete XML document with a root node.
* When using the CONTENT field, xml_string can be without a root node, but must be a valid XML value.
* When using the WELLFORMED field, it indicates that the input xml is a complete and valid XML data, and parsing will not validate its legality; otherwise, the legality of xml_string will be checked.

**xml_string**

[General Expression](../../General SQL Syntax/expr), the XML string to be parsed, follows these rules:

* Supports types VARCHAR, CHAR, NVARCHAR, NCHAR, CLOB, and NCLOB.
* If the input is NULL, the return value will also be NULL.

***Example*** for Heap tables

```sql
-- Missing DOCUMENT or CONTENT field will cause an error
SELECT XMLParse('<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res FROM dual;

[1:17]YAS-04115 "DOCUMENT|CONTENT" expected but missing

-- Parse as DOCUMENT format
SELECT XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<root><role1>Jerry</role1>Tom<role2></role2></root>

-- Parse as CONTENT format
SELECT XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>

-- Parse as DOCUMENT format without legal check
SELECT XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>' WELLFORMED).GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>
```
