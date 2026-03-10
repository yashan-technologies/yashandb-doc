```ebnf+diagram
xmlextract::= XMLEXTRACT "("xml_data "," xpath [ "," namespace] ")"
```

The XMLEXTRACT function is used to extract data from xml_data based on the input xpath and namespace, and return an XMLTYPE instance containing an XML fragment.



**xml_data**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), follows these rules:

- It must be a complete XML document with a root node. Its data type is XMLTYPE.

- The data length must not exceed 64MB.

- When the value of xml_data is NULL, the function returns NULL.




**xpath**

XML Path expressions comply with the W3C (World Wide Web Consortium) XPath 1.0 specification.

- The type must be CHAR, VARCHAR, NCHAR, or NVARCHAR. Any other type will result in an error.

- The maximum size of xpath is 65534 bytes.

- If the value of xpath is NULL, an error will be reported.




**namespace**

It is used to specify namespaces and can have multiple ones. Different namespaces can be separated by commas, Spaces, tabs, or line breaks. The namespace format is `xmlns:prefix=href`, case-sensitive. The prefix can be omitted, and in this case, it is the default namespace.

- Follow the XML namespace specification of W3C.

- The type is one of CHAR, VARCHAR, NCHAR, or NVARCHAR. It can be omitted, and when omitted, it defaults to NULL.

- The maximum size of xpath is 65534 bytes.

- If there are namespaces without prefixes in the XML data, YashanDB will automatically add the default prefix `syspfx`. When specifying an xpath, the default prefix `syspfx` must be explicitly declared.


***Example*** for Heap tables

```sql
-- Query content under the root node
SELECT XMLExtract(XMLParse(DOCUMENT '<root><role1>Jerry</role1>Tom<role2></role2></root>'), '/root').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<root><role1>Jerry</role1>Tom<role2/></root>

-- If xml_data input is NULL, the query result is NULL
SELECT XMLExtract(NULL, '/').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------

    
-- XPath expression cannot be NULL
SELECT XMLExtract(NULL, NULL).GetClobVal() res FROM dual;

YAS-07202 plugin execution error, Invalid XPATH expression

-- Query using namespaces
SELECT XMLExtract(XMLTYPE('<root xmlns:ns="http://example.com/namespace" xmlns:ns1="http://example.com/namespace1"><element>Content1</element><ns:element>Content2</ns:element></root>'),
                    '/root/element', 'xmlns:ns="http://example.com/namespace"').GetClobVal() res FROM dual;

RES
----------------------------------------------------------------
<element>Content1</element>

-- the default prefix `syspfx` must be explicitly declared
SELECT XMLEXTRACT(XMLTYPE('<a xmlns="ns1" xmlns:f="ns2"><f:b b1="b1" b2="b2">bbb1</f:b><b b1="b1" b2="b2">bbb2</b></a>'), 'syspfx:a/f:b', 'xmlns="ns1" xmlns:f="ns2"').getstringval() FROM dual;

XMLEXTRACT(XMLTYPE('<AXMLNS="NS1"XMLNS:F="NS2"><F:BB1="B1"B2="B2
----------------------------------------------------------------
<f:b xmlns:f="ns2" b1="b1" b2="b2">bbb1</f:b>
```
