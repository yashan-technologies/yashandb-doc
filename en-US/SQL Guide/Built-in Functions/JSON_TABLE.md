```ebnf+diagram
json_table::= JSON_TABLE "(" expr [FORMAT JSON] "," json_path [json_table_on_error_clause] [json_table_on_empty_clause] json_columns_clause ")"

json_table_on_error_clause::= ( ERROR | NULL) ON ERROR

json_table_on_empty_clause::= ( ERROR | NULL) ON EMPTY

json_column_clause::= COLUMNS "(" (ordinality_column | json_value_column) {"," (ordinality_column | json_value_column)} ")"

ordinality_column::= column_name FOR ORDINALITY

json_value_column::= column_name [JSON_value_return_type] [TRUNCATE] [PATH json_path]
```
The JSON_TABLE function creates a virtual relational table that maps the source data `expr` to the JSON data generated after evaluating the `json_path`.

This function is applicable only to HEAP tables.

**expr**

expr is a data type that can be converted into binary [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) data, including JSON, RAW, CLOB, BLOB, NCLOB, CHAR, NCHAR, VARCHAR, NVARCHAR.

When expr is NULL, the function returns an empty table object.

**FORMAT JSON**

This is used for syntax compatibility and has no actual meaning. It can be omitted.

**json_path**

A path expression, which is a constant string. For its format definition, please refer to [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json). 

**json_table_on_error_clause**

This is used for syntax compatibility and has no actual meaning. It can be omitted. By default, when there is an error in column data conversion, it returns NULL.

**json_table_on_empty_clause**

This is used for syntax compatibility and has no actual meaning. It can be omitted. By default, when the retrieval is empty, it returns NULL.

**json_column_clause**

Used to specify the column attributes of the virtual relational table, including column names, data types, and path expressions.

Multiple different json_value_column clauses can be specified to retrieve JSON data in various ways to generate the corresponding result columns.

A maximum of 1024 columns is supported.

**ordinality_column**

Generates the corresponding row number, with the default type being NUMBER. It can only be specified once.

**json_value_column**

Used to specify the column value and data type for the virtual relational table.

This clause is similar to the [JSON_VALUE](./JSON_VALUE) function, which computes the corresponding scalar data from the JSON data and returns it.

JSON_value_return_type is used to specify the data type for the column value. Supported types include TINYINT, SMALLINT, INTEGER, BIGINT, FLOAT, DOUBLE, NUMBER, TIME, TIMESTAMP, DATE, VARCHAR, CHAR, NCHAR, VARCHAR, NVARCAHR, RAW, and JSON.

TRUNCATE is only used for syntax compatibility and has no actual meaning. It can be omitted.

PATH json_path is optional, and if omitted, it defaults to '$.'.

***Example*** for Heap tables

```sql
SELECT * FROM JSON_TABLE('{"a":100, "b":200, "c":{"d":300, "e":400}}', '$' COLUMNS (outer_value_0 NUMBER PATH '$.a', outer_value_1 NUMBER PATH '$.b', row_id FOR ORDINALITY));

OUTER_VALUE_0 OUTER_VALUE_1      ROW_ID 
------------- ------------- ----------- 
          100           200           1

SELECT * FROM JSON_TABLE('{"a":100, "b":"20d", "c":{"d":300, "e":400}}' , '$' COLUMNS (outer_value_0 NUMBER PATH '$.d', outer_value_1 NUMBER PATH '$.f', outer_value_2 NUMBER PATH '$.a'));

OUTER_VALUE_0 OUTER_VALUE_1 OUTER_VALUE_2 
------------- ------------- ------------- 
                                      100

-- Create Table json_table_demo for storing JSON data
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
