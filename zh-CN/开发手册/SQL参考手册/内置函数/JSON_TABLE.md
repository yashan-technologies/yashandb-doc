```ebnf
json_table= JSON_TABLE "(" expr [FORMAT JSON] "," json_path [json_table_on_error_clause] [json_table_on_empty_clause] json_columns_clause ")".

json_table_on_error_clause = ( ERROR | NULL) ON ERROR.

json_table_on_empty_clause = ( ERROR | NULL) ON EMPTY.

json_column_clause = COLUMNS "(" (ordinality_column | json_value_column) {"," (ordinality_column | json_value_column)} ")".

ordinality_column = column_name FOR ORDINALITY.

json_value_column = column_name [JSON_value_return_type] [TRUNCATE] [PATH json_path].
```
JSON_TABLE函数将创建一个虚拟关系表，用于映射源数据expr基于json_path计算后所产生的JSON数据。

本函数仅适用于HEAP表。

**expr**

expr为一个可转换为二进制[json](../通用SQL语法/json)数据的类型数据，包括JSON、RAW、CLOB、BLOB、NCLOB、CHAR、NCHAR、VARCHAR、NVARCAHR。

当expr为NULL时，函数返回空表对象。

**FORMAT JSON**

仅用于语法兼容，无实际含义，可省略。

**json_path**

路径表达式，为一个常量字符串，其格式定义请参考[json](../通用SQL语法/json)。

**json_table_on_error_clause**

仅用于语法兼容，无实际含义，可省略。默认在列数据转换错误时返回NULL。

**json_table_on_empty_clause**

仅用于语法兼容，无实际含义，可省略。默认在检索为空时返回NULL。

**json_column_clause**

用于指定虚拟关系表的列属性，包括列名、数据数据类型以及路径表达式。

允许指定多个不同的json_value_column子句对JSON数据进行不同方式的检索，从而生成相应的结果列。

最大支持1024列。

**ordinality_column**

生成对应的行号，类型默认为NUMBER，仅可指定一次。

**json_value_column**

用于指定虚拟关系表的列值与数据类型。

该子句类似于[JSON_VALUE函数](JSON_VALUE)，对JSON数据进行相应计算并返回相应的标量数据。

JSON_value_return_type用于指定列值的数据类型，支持TINYINT、SMALLINT、INTEGER、BIGINT、FLOAT、DOUBLE、NUMBER、TIME、TIMESTAMP、DATE、VARCHAR、CHAR、NCHAR、VARCHAR、NVARCAHR、RAW、JSON。

TRUNCATE仅用作语法兼容，无实际含义，可省略。

PATH json_path可省略，省略时默认使用'$.'。

示例（HEAP表）

```sql
SELECT * FROM JSON_TABLE('{"a":100, "b":200, "c":{"d":300, "e":400}}', '$' COLUMNS (outer_value_0 NUMBER PATH '$.a', outer_value_1 NUMBER PATH '$.b', row_id FOR ORDINALITY));

OUTER_VALUE_0 OUTER_VALUE_1      ROW_ID 
------------- ------------- ----------- 
          100           200           1

SELECT * FROM JSON_TABLE('{"a":100, "b":"20d", "c":{"d":300, "e":400}}' , '$' COLUMNS (outer_value_0 NUMBER PATH '$.d', outer_value_1 NUMBER PATH '$.f', outer_value_2 NUMBER PATH '$.a'));

OUTER_VALUE_0 OUTER_VALUE_1 OUTER_VALUE_2 
------------- ------------- ------------- 
                                      100

-- 创建用于存储json数据的表json_table_demo
CREATE TABLE json_table_demo
(   id BIGINT,
    date_create TIMESTAMP(6) WITH TIME ZONE,
    po_document JSON
);

INSERT INTO json_table_demo
VALUES (
           123456,
           SYSTIMESTAMP,
           '{"PONumber" : 1234,
           "User" : "ABULL",
           "Instructions" : {"name" : "lisi",
           "Address": {"street" : "ChengHua Ave.",
           "city" : "ChengDu",
           "zipCode" : 720000,
           "country" : "China"},
           "Phone" : [{"type" : "Office", "number" : "88420199"},
           {"type" : "Mobile", "number" : "13888888888"}]},
           "LineItems" : [{"ItemNumber" : 1,
           "Part" : {"Description" : "Adidas Shoes",
           "UnitPrice" : 799},
           "Quantity" : 13},
           {"ItemNumber" : 2,
           "Part" : {"Description" : "T Shirt",
           "UnitPrice" : 199},
           "Quantity" : 11}]}');

SELECT jt.* FROM json_table_demo, JSON_TABLE(po_document, '$.Instructions.Phone[*]' COLUMNS (row_number FOR ORDINALITY, phone_type VARCHAR(10) PATH '$.type', phone_num VARCHAR(20) PATH '$.number')) AS jt;

ROW_NUMBER PHONE_TYPE    PHONE_NUM             
----------- ------------- --------------------- 
          1 Office        88420199             
          2 Mobile        13888888888

SELECT jt.* FROM json_table_demo, JSON_TABLE(po_document, '$.LineItems[*]' COLUMNS (ItemNumber BIGINT, Part VARCHAR(50), Quantity FLOAT)) AS jt;

ITEMNUMBER PART                                                     QUANTITY 
--------------------- ----------------------------------------------------- ----------- 
                    1                                                          1.3E+001
                    2                                                          1.1E+001
```
