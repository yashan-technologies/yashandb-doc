```ebnf
xmlparse = XMLPARSE "(" "DOCUMENT|CONTENT" xml_data [WELLFORMED] ")".
```

The XMLPARSE function is used to parse the input string into a data type of XMLTYPE.

* When using the DOCUMENT field, xml_data must be a complete XML document with a root node, and the XML file encoding format must be UTF-8.
* When using the CONTENT field, xml_data can be without a root node, but must be a valid XML value.
* When using the WELLFORMED field, it indicates that the input xml_data is a complete and valid XML data, and parsing will not validate its legality; otherwise, the legality of xml_data will be checked.

**xml_data**

[General Expression](../../General SQL Syntax/expr), the XML string to be parsed, follows these rules:

* Supports types VARCHAR, CHAR, NVARCHAR, NCHAR, CLOB, and NCLOB.
* The data length must not exceed 64MB.
* If the input is NULL, the return value will also be NULL.

***Example*** for Heap tables

```sql
-- Missing DOCUMENT or CONTENT field will cause an error
select XMLParse('<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res from dual;

[1:17]YAS-04115 "DOCUMENT|CONTENT" expected but missing

-- Parse as DOCUMENT format
select XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>').GetClobVal() res from dual;

RES
----------------------------------------------------------------
<root><role1>Jerry</role1>Tom<role2></role2></root>

-- Parse as CONTENT format
select XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>').GetClobVal() res from dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>

-- Parse as DOCUMENT format without legal check
select XMLParse(CONTENT '<role1>Jerry</role1>Tom<role2></role2>' WELLFORMED).GetClobVal() res from dual;

RES
----------------------------------------------------------------
<role1>Jerry</role1>Tom<role2></role2>
```
