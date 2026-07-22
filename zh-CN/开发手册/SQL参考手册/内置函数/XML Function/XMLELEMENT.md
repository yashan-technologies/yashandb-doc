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

XMLELEMENT是XML构造函数，用于创建一个XML元素（element），支持指定元素名称、属性（attributes）和内容（contents），最终返回XMLTYPE类型的值。

**identifier**

标识符，最长长度65534字节。

**EVALNAME name_expr**

用于动态计算元素名称或属性名称，value_expr是一个可以转换为VARCHAR类型的表达式，表达式的计算结果将作为元素名称或属性名称。

**XML_attributes_clause**

用于在元素中添加属性，子句中指定的值将成为元素的属性值，须为VARCHAR类型或可转换为该类型的其他类型。若没有指定属性名，属性值表达式必须为表列，默认以列名作为属性名。

**XML_contents_clause**

用于在元素中添加内容。
- value_expr：内容的值，不能为UDT和除XMLTYPE外的扩展类型。如果是XMLTYPE类型，将解析为该元素的子元素节点；如果是其他类型，将作为该元素的子文本节点。
- 指定name的子句：仅用于语法兼容，无实际含义。

示例（HEAP表）

```sql
-- 创建指定元素名称的XML元素
SELECT XMLELEMENT(NAME "employee").getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee/>

-- 创建带有内容值的XML元素
SELECT XMLELEMENT(NAME "employee", 'John').getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee>John</employee>

-- 使用列值作为内容
SELECT XMLELEMENT(NAME "employee", t.deptno).getclobval() res
FROM (SELECT 10 AS deptno FROM dual) t;

RES
----------------------------------------------------------------
<employee>10</employee>

-- 创建带有属性的XML元素
SELECT XMLELEMENT(NAME "employee",
    XMLATTRIBUTES('1001' AS "id", 'John' AS "name"),
    'Engineer').getclobval() res
FROM dual;

RES
----------------------------------------------------------------
<employee id="1001" name="John">Engineer</employee>

-- 嵌套使用XMLELEMENT，创建带有多个子元素的XML元素
SELECT XMLELEMENT(NAME "employee",
    XMLELEMENT(NAME "name", 'John'),
    XMLELEMENT(NAME "dept", 'Engineering')).getclobval() res
FROM dual;

RES
----------------------------------------------------------------
<employee><name>John</name><dept>Engineering</dept></employee>

-- 使用EVALNAME动态指定元素名称
SELECT XMLELEMENT(EVALNAME 'emp' || 'loyee', 'test').getclobval() res FROM dual;

RES
----------------------------------------------------------------
<employee>test</employee>
```