```ebnf+diagram
xmltable::= XMLTABLE "(" [XMLnamespaces_clause ","]  XQuery_string XMLTALE_options ")"
```

```ebnf+diagram
XMLTALE_options::= PASSING expr [AS identifier] (COLUMNS column_name datatype path string) { "," column_name datatype path string}
```

The **XMLTABLE** function is used to parse XML data into relational data, extracting data from XML documents and converting it into rows and columns for easier use in SQL queries.

**XMLnamespaces_clause**

Defines namespace prefixes and URIs for referencing elements and attributes with namespaces in XML queries.

This clause contains a set of XML namespace declarations that are referenced in the XPath expressions of XML_table_column's PATH clause. If you want to use qualified names in the PATH expressions of the COLUMNS clause, this clause must be specified.

Namespace prefixes and URIs must be string constants and have a value range of [0,65534].

**XQuery_string**

Is a literal string that specifies the XQuery expression used to select the data to be converted from XML data.

It must comply with XQuery syntax; otherwise, it cannot return correct data.

It must be a string constant with a value range of [0,65534].

**expr**

Must be an expression of type XMLTYPE; otherwise, an error will occur.

**AS identifier**

An optional item that specifies an alias for the result of the expr parameter. This alias can be used in the XQuery expression to reference the passed data.

**column_name**

The column name of the final returned relational table.

Must be a string constant with a value range of [0,65534].

**datatype**

The data type of the columns in the final returned relational table, supporting only data types that can be converted to character types.

Only supports data types that can be converted to character types.

**XPath_string**

A constant string that is combined with the XQuery_string to locate data in the XML document.

It must comply with XQuery syntax; otherwise, it cannot return correct data.

It must be a string constant with a value range of [0,65534].

***Example***
```sql
SELECT
    *
FROM
    xmltable(
            '/root' passing xmltype('<root><element>test value</element></root>')
  columns
    element_value VARCHAR2(20) path 'element'
);

ELEMENT_VALUE
---------------------
test value


-- Using XML AS alias
SELECT
    *
FROM
    xmltable(
            '$B/root' passing xmltype('<root><element>test value</element></root>') AS B
  columns
    element_value VARCHAR2(20) path 'element'
);

ELEMENT_VALUE
---------------------
test value


-- Using XML namespaces
SELECT
    *
FROM
    xmltable(
            XMLNAMESPACES('http://rt.example.com' AS "rt"),
                'root' passing xmltype(
				'<root xmlns:rt="http://rt.example.com">
					<rt:element>test value</rt:element>
				</root>')
  columns
    element_value VARCHAR2(20) path 'rt:element'
);

ELEMENT_VALUE
---------------------
test value

```
