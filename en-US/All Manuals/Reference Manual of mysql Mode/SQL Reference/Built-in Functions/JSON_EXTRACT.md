```ebnf+diagram
json_extract::= JSON_EXTRACT "(" (json_value|expr) "," (json_path) {"," (json_path)} ")"
```

The JSON_EXTRACT function extracts data from a JSON path that matches the specified path parameters. The return value of the function consists of all values matched by the path parameters. When there are multiple matching values, the function automatically encapsulates them into an array (the order of the values in the array corresponds to the order in which they were generated) before returning.

This function follows these rules:

- If any parameter is NULL or the json_path lookup result is empty, the function returns NULL.
- If the value of the json_value parameter or the expr, when converted to binary JSON data, is not valid JSON data, or if any path parameter is not a valid path expression, an error occurs.
- The return value consists of all values matched by the path parameters. If these parameters may return multiple values, the matching values will be automatically encapsulated into an array, with the order corresponding to the paths that generated these values. Otherwise, the return value is a single matching value.
- The number of input parameters for the function ranges from [1,65535].
- In mysql syntax mode, JSON_EXTRACT does not support nesting with the following functions:
    - RPAD
    - LPAD
    - REPLACE
    - TRIM
    - LTRIM
    - RTRIM
    - REVERSE
    - SPACE
    - UNHEX
    - CONCAT
    - LCASE
    - UCASE
    - SUBSTR
    - FIND_IN_SET
    - SUBSTRING_INDEX
    - FROM_BASE64
    - LEFT
    - RIGHT

**json_value**

json_value is a binary [json](../General SQL Syntax/json) data, which can be obtained through the [JSON function](../../../Development Guide/SQL Reference Manual/Built-in Functions/JSON JSON_PARSE).

When json_value is NULL, the function returns NULL.

**json_path**

Path expression, which is a constant string, and its format definition can be referenced in the [json](../General SQL Syntax/json) documentation.

***Example*** for  Heap tables

```sql
SELECT JSON_EXTRACT('[1, 2, {"x": 3}]', '$[2]', '$[1]', '$[0]') RES1, JSON_EXTRACT(JSON('[1, 2, {"x": 3}]'), '$[2]', '$[1]', '$[0]') RES2;

RES1                                                             RES2                                                             
---------------------------------------------------------------- ---------------------------------------------------------------- 
[{"x":3},2,1]                                                    [{"x":3},2,1]                                                   
```
