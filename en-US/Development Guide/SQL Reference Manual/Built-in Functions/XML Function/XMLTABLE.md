```ebnf
xmltable = XMLTABLE "(" [namespace ","]  XQuery_string XMLTALE_options ")".
```

```ebnf
XMLTALE_options = PASSING expr [AS identifier] COLUMNS ( for ordinality | column_name datatype xpath [default expr]) { "," column_name datatype xpath [default expr]} .
```

The XMLTABLE function is used to parse XML data into relational data, extracting data from XML documents and converting it into rows and columns for easier use in SQL queries.

**namespace**

Defines namespace prefixes and URIs for referencing elements and attributes with namespaces in XML queries.

This clause contains a set of XML namespace declarations that are referenced in the XPath expressions of XML_table_column's PATH clause. If you want to use qualified names in the PATH expressions of the COLUMNS clause, this clause must be specified.

Namespace prefixes and URIs must be string constants and have a value range of [0,65534].

- If there are namespaces without prefixes in the XML data, YashanDB will automatically add the default prefix `syspfx`. When specifying an xpath, the default prefix `syspfx` must be explicitly declared.

**XQuery_string**

Is a literal string that specifies the XQuery expression used to select the data to be converted from XML data.

It must comply with XQuery syntax; otherwise, it cannot return correct data.

It must be a string constant with a value range of [0,65534].

**expr**

Must be an expression of XMLTYPE type, and the data length must not exceed 64MB, otherwise an error will occur.

**AS identifier**

An optional item that specifies an alias for the result of the expr parameter. This alias can be used in the XQuery expression to reference the passed data.


**for ordinality**

Specify this column as the row number column. Only one row number column can be configured, and it is the NUMBER data type as default.

**column_name**

The column name of the final returned relational table.

Must be a string constant with a value range of [0,65534].

**datatype**

The data type of the columns in the final returned relational table, supporting only data types that can be converted to character types.

Only supports data types that can be converted to character types.

**xpath**

A constant string that is combined with the XQuery_string to locate data in the XML document.

It must comply with XQuery syntax; otherwise, it cannot return correct data.

It must be a string constant with a value range of [0,65534].

**default expr**

The default value of the column. `default expr` is only a syntax support and has no practical meaning.

***Example*** for Heap tables

```sql
select
    *
from
    xmltable(
            '/root' passing xmltype('<root><element>test value</element></root>')
  columns
    element_value varchar2(20) path 'element'
);

ELEMENT_VALUE
---------------------
test value

1 row fetched.

-- Using XML AS alias
select
    *
from
    xmltable(
            '$B/root' passing xmltype('<root><element>test value</element></root>') AS B
  columns
    element_value varchar2(20) path 'element'
);

ELEMENT_VALUE
---------------------
test value

1 row fetched.

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

1 row fetched.

--Specify the row number column
SELECT
    *
FROM
    xmltable(
            '/root' passing xmltype('<root><element>test value</element></root>')
  columns
    c1 FOR ORDINALITY,
    element_value VARCHAR2(20) path 'element'
);

         C1 ELEMENT_VALUE
----------- ---------------------
          1 test value

1 row fetched.

```
