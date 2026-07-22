```ebnf
json_length = JSON_LENGTH "(" json_doc ["," path] ")".
```

The JSON_LENGTH function returns the length of a JSON document; if the path parameter is specified, it returns the length of the value identified by that path in the document. The return data type of JSON_LENGTH is BIGINT.

The length of a document is determined as follows:

- The length of a scalar is 1.
- The length of an array is the number of array elements.
- The length of an object is the number of object members.
- The length does not count the length of nested arrays or objects.

**json_doc**

`json_doc` is a binary [json](../General SQL Syntax/json) data that can be obtained through the [JSON_ARRAY](./JSON_ARRAY) function.

The data type of the input can be JSON, CHAR, VARCHAR, TEXT, BLOB, or RAW.

If json_doc is NULL, the function returns NULL.

**path**

`path` is an optional parameter that specifies the path to the value for which to get the length.

The data type of the input can be CHAR, VARCHAR, or TEXT.

If `path` is NULL, the function returns NULL.

When `json_doc` does not have the `path`, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT JSON_LENGTH('[1, 2, {"a": 3}]') res FROM DUAL;

                  res
---------------------
                    3

SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30, "d": 40}}') res FROM DUAL;

                  res
---------------------
                    2

SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30, "d": 40}}', '$.b') res FROM DUAL;

                  res
---------------------
                    2
```