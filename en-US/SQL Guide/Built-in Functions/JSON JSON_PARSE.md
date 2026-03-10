```ebnf+diagram
json::= JSON "(" expr [EXTENDED] ")"
```

The JSON function is used to convert the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) into binary [JSON](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) data. When the converted JSON data is of the JSON Object type, the function returns the result sorted by key values.

This function has aliases JSON_FORMAT and JSON_PARSE, and its functionality is identical to that of JSON.

**expr**

A generic expression whose value must be of CHAR, VARCHAR, BFILE, or CLOB data type.

- expr cannot be a VARCHAR, LOB, or BFILE type data exceeding 65534 bytes.

- When the value of expr is NULL, the function returns NULL.

**EXTENDED**

The EXTENDED keyword is used to specify parsing of the JSON string using the extended mode; for details, please refer to [JSON Extended Format Parsing](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json).

***Example***

```sql
SELECT JSON_FORMAT(JSON('[123, "ABC", false]')) yason_res FROM DUAL;
YASON_RES                                                        
---------------------------------------------------------------- 
[123,"ABC",false] 

-- JSON Object type will be sorted by key values
SELECT JSON_FORMAT(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') pretty) yason_res FROM DUAL;
YASON_RES                                                        
---------------------------------------------------------------- 
{
  "key" : true,
  "keyA" : 234,
  "keyB" : 345,
  "keyC" : 123,
  "keyabc" : false
}

-- Parse JSON string using extended mode
SELECT JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) yason_res FROM DUAL;
YASON_RES
----------------------------------------------------------------
"48656C6C6F20776F726C6421"
```
