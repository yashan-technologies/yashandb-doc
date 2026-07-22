```ebnf
extractvalue = EXTRACTVALUE "(" xml_data ',' xpath [',' namespace ]")".
```

The EXTRACTVALUE function locates a node via the input XPath and extracts its scalar value. The result must be a single text node, attribute, or element, with return type VARCHAR.

If the xpath points to an element node, the element must have exactly one text node as its child; otherwise, the function will raise an error and return the text node value.

If the xpath points to a node with multiple children or non-text children, the function will raise an error.



**xml_data**

[General Expression](../../General SQL Syntax/expr), follows these rules:

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
select extractvalue(xmltype('<root>text</root>'), 'root') res from dual;

RES
----------------------------------------------------------------
text
```
