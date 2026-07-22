```ebnf
xmlelement = XMLELEMENT
  "("
    ( [ NAME ] identifier | EVALNAME name_expr )
    [ "," XML_attributes_clause ]
    [ XML_contents_clause ]
  ")".

XML_attributes_clause = XMLATTRIBUTES
  "("
    ( value_expr [ [ AS ] c_alias | AS EVALNAME name_expr ]) { "," ( value_expr [ [ AS ] c_alias | AS EVALNAME name_expr ]) }
  ")".

XML_contents_clause = ( "," value_expr [ [ AS ] c_alias | AS EVALNAME name_expr])+.

c_alias = identifier.
```

XMLELEMENT is an XML constructor used to create an XML element, supporting the specification of element names, attributes, and contents, and ultimately returns a value of type XMLTYPE.

**identifier**

The identifier, with a maximum length of 65534 bytes.

**EVALNAME name_expr**

Used to dynamically compute the element name or attribute name. value_expr is an expression that can be converted to the VARCHAR type, and the result of evaluating the expression will be used as the element name or attribute name.

**XML_attributes_clause**

Used to add attributes to an element. The values specified in this clause become attribute values of the element, and must be VARCHAR type or convertible to it. If no attribute name is specified, the attribute value expression must be a table column, and the column name is used as the attribute name by default.

**XML_contents_clause**

Used to add contents to an element.
- value_expr: the value of the content. It cannot be UDT or extended types other than XMLTYPE. If it is of XMLTYPE type, it is resolved as child element node of this element; otherwise, it is used as a sub text node of this element.
- Clause specifying name: used for syntax compatibility only, with no practical meaning.

***Example*** for Heap tables

```sql
-- Create an XML element with an element name
SELECT XMLELEMENT(NAME "employee").getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee/>

-- Create an XML element with a content value
SELECT XMLELEMENT(NAME "employee", 'John').getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee>John</employee>

-- Use column values as content
SELECT XMLELEMENT(NAME "employee", t.deptno).getclobval() res
FROM (SELECT 10 AS deptno FROM dual) t;

RES
----------------------------------------------------------------
<employee>10</employee>

-- Create an XML element with attributes
SELECT XMLELEMENT(NAME "employee",
    XMLATTRIBUTES('1001' AS "id", 'John' AS "name"),
    'Engineer').getclobval() res
FROM dual;

RES
----------------------------------------------------------------
<employee id="1001" name="John">Engineer</employee>

-- Nest XMLELEMENT to create an XML element with multiple sub elements
SELECT XMLELEMENT(NAME "employee",
    XMLELEMENT(NAME "name", 'John'),
    XMLELEMENT(NAME "dept", 'Engineering')).getclobval() res
FROM dual;

RES
----------------------------------------------------------------
<employee><name>John</name><dept>Engineering</dept></employee>

-- Use EVALNAME to dynamically specify element name
SELECT XMLELEMENT(EVALNAME 'emp' || 'loyee', 'test').getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee>test</employee>
```