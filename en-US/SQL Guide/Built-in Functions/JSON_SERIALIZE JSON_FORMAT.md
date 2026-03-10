```ebnf+diagram
json_serialize::= JSON_SERIALIZE "(" json_value [returning_clause] [PRETTY] [EXTENDED] ")"

returning_clause::= RETURNING "VARCHAR(size)"
```

The JSON_SERIALIZE function converts binary [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) data into a string in either pretty or compact format, with the length of the serialized string not exceeding the limit defined by the `returning_clause`.

`json_value` is a binary json data that can be obtained from the [JSON](./JSON JSON_PARSE) function. When its value is NULL, the function returns NULL.

The `JSON_SERIALIZE` function has an alias, `JSON_FORMAT`, and its functionality is identical to that of `JSON_SERIALIZE`.

**returning_clause**

This clause is used to limit the length of the output string; it can be omitted, in which case the system defaults the output string length to 65534.

Size is an integer constant, and its valid range is [0,65534].

If the actual string length exceeds the size limit, the function returns an error.

**PRETTY**

PRETTY is used to specify that the string should be printed in a pretty format. If PRETTY is not specified, the system defaults to printing the string in a compact format.

Compact format means that all formatting white spaces and new lines are removed when printing the string.

The pretty formatting rules are as follows:

1. For JSON Array types:

- Number, String, Bool, and Null type array elements appear on separate lines.
- Object or Array type array elements occupy multiple lines.
- `[` and `]` each occupy a line at the same level; all array elements are indented one additional level relative to `[` and `]`, with each level indented by two spaces.
- A comma is added before the newline character of two array elements to separate them.
- An empty array still requires a newline.

2. For JSON Object types:

- If the Value in the object's members is of type Number, String, Bool, or Null, the key and value are printed on the same line.
- If the Value in the object's members is of type Object or Array, the key and value are not printed on the same line.
- `{` and `}` each occupy a line at the same level; all object members are indented one additional level relative to `{` and `}`, with each level indented by two spaces.
- The key and value of object members are separated by "space colon space".
- A comma is added before the newline character of two object members to separate them.
- An empty object still requires a newline.

3. For JSON String types:

- Both keys in an Object and String type scalar values will be printed with double quotes before and after.
- The string itself containing '\b (backspace)', '\f (form feed)', '\n (newline, line feed)', '\r (carriage return)', '\t (tab, horizontal tab)', 'Double quotation mark (")', 'slash (/)', and 'backslash (\\)' must be escaped when printed.

4. For JSON Number types:

- If the exponent of the number is greater than or equal to 0, and the width of the printed number exceeds 40 when expanded, scientific notation is used; otherwise, it is printed in expanded form. For example:

    - 1e39 prints as: 1000000000000000000000000000000000000000
    - 1e40 prints as: 1E+40
- If the exponent of the number is less than 0, and the width of the decimal part (after removing trailing zeros) exceeds 40 when expanded, scientific notation is used; otherwise, it is printed in expanded form. For example:
    - 12300e-43 prints as: 1.23E-39 (expanded form results in 0.00000000000000000000000000000000000000123, with 41 decimal places, thus scientific notation is used)
    - 12300e-42 prints as: 0.0000000000000000000000000000000000000123 (exactly 40 decimal places)

**EXTENDED**

The EXTENDED keyword is used to set the output of JSON data in extended mode, see [JSON extended format output](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json).

***Example***

```sql
SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') PRETTY) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
{
  "key" : true,
  "keyA" : 234,
  "keyB" : 345,
  "keyC" : 123,
  "keyabc" : false
}

SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}')) res FROM DUAL;
RES
----------------------------------------------------------------
{"key":true,"keyA":234,"keyB":345,"keyC":123,"keyabc":false}

SELECT JSON_SERIALIZE(JSON('{"keyC":123, "keyabc":false, "keyA":234, "keyB":345, "key":true}') RETURNING VARCHAR(10) PRETTY) res FROM DUAL;
YAS-00215 length of concat texts exceeds the buffer limit

-- JSON extended mode input and output
SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED)) res FROM DUAL ;
RES
----------------------------------------------------------------
"48656C6C6F20776F726C6421"

SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) EXTENDED) res FROM DUAL ;
RES
----------------------------------------------------------------
{"$binary": "SGVsbG8gd29ybGQh"}

-- Extended output in JSON format is also affected by the PRETTY keyword
SELECT JSON_SERIALIZE(JSON('{"$binary": "SGVsbG8gd29ybGQh"}' EXTENDED) PRETTY EXTENDED) res FROM DUAL ;
RES
----------------------------------------------------------------
{
  "$binary": "SGVsbG8gd29ybGQh"
}
```
