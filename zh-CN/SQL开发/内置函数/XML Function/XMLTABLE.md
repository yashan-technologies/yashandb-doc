```ebnf+diagram
xmltable::= XMLTABLE "(" [namespace ","]  XQuery_string XMLTALE_options ")"
```

```ebnf+diagram
XMLTALE_options::= PASSING expr [AS identifier] COLUMNS ( for ordinality | column_name datatype xpath [default expr]) { "," column_name datatype xpath [default expr]} 
```

XMLTABLE函数用于将XML数据解析为关系型数据，把XML文档中的数据提取出来，转换为行和列的形式，以方便在SQL查询中使用。

**namespace**

定义命名空间前缀和URI，用于在XML查询中引用具有命名空间的元素和属性。

该子句包含一组XML命名空间声明，这些声明被XML_table_column的PATH子句中的XPath表达式引用。如果想在COLUMNS子句的PATH表达式中使用限定名称，则需要指定该子句。

空间前缀和URI需为字符串常量，取值范围为[0,65534]。

- 若XML数据中存在命名空间无前缀的情况，YashanDB会自动为其添加缺省前缀`syspfx`，在指定xpath时需显式指定缺省前缀`syspfx`。

**XQuery_string**

是一个文字字符串，指定XQuery表达式，用于从XML数据中选择需要转换的数据。

需符合XQuery语法，否则无法返回正确数据。

需为字符串常量，取值范围为[0,65534]。

**expr**

需为XMLTYPE的类型的表达式，且数据长度不得超过64MB，否则报错。

**AS identifier**

可选项，它为expr参数结果指定一个别名。在XQuery表达式中，可以使用这个别名来引用传递的数据。


**for ordinality**

指定该列为行号列。最多只能配置一个行号列，行号列为NUMBER类型。

**column_name**

最终返回的关系表的列名。

需为字符串常量，取值范围为[0,65534]。

**datatype**

最终返回的关系表的列数据类型，仅支持可以与字符型转换的数据类型。

仅支持可以与字符型转换的数据类型。

**xpath**

为常量字符串，与XQuery_string一同组合查找XML文档中的数据。

需符合XQuery语法，否则无法返回正确数据。

需为字符串常量，取值范围为[0,65534]。

**default expr**

column列默认值，当前仅语法支持。

示例（HEAP表）

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


--XML AS别名的使用
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


--XML命名空间的使用
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


--指定行号列
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


```
