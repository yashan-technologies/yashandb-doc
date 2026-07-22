```ebnf
json_contains = JSON_CONTAINS "(" target "," candidate ["," path] ")".
```

The JSON_CONTAINS function is used to determine whether a given JSON document `candidate` is contained in the target JSON document `target`; if the `path` parameter is provided in the function, it will determine whether the `candidate` JSON document exists at the specified path of the target JSON document `target`. The function returns [0|1|null], with a return type of BIGINT. It returns 1 for a match, 0 for no match, and null when any input parameter is null.

The following rules determine whether `candidate` is contained in the target JSON document `target`: 

- A `candidate` scalar is contained in a `target` scalar if and only if they are comparable and are equal. Two scalar values are comparable if they have the same JSON types, with the exception that values of types INTEGER and DECIMAL are also comparable to each other.
- A `candidate` array is contained in a `target` array if and only if every element in the `candidate` is contained in some element of the `target`.
- A `candidate` nonarray is contained in a `target` array if and only if the `candidate` is contained in some element of the `target`.
- A `candidate` object is contained in a `target` object if and only if for each key in the `candidate` there is a key with the same name in the `target` and the value associated with the `candidate` key is contained in the value associated with the `target` key.

**target**

`target` is a binary [json](../General SQL Syntax/json) data, representing the target JSON document.

The data type of the input can be JSON, CHAR, VARCHAR, TEXT, BLOB, or RAW.

If `target` is NULL, the function returns NULL.

**candidate**

`candidate` is a binary [json](../General SQL Syntax/json) data, representing the JSON document to search for.

The data type of the input can be JSON, CHAR, VARCHAR, TEXT, BLOB, or RAW.

If `candidate` is NULL, the function returns NULL.

**path**

`path` is an optional parameter that specifies the path in `target` to search for `candidate`.

The data type of the input can be CHAR, VARCHAR, or TEXT.

If path is NULL, the function returns NULL.

When `target` does not have the path, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT JSON_CONTAINS('{"a": 1, "b": 2}', '2', '$.*') AS result;

               result
---------------------
                    1

CREATE TABLE t1(id int, js1 varchar(255), js2 varchar(255));

INSERT INTO t1 VALUES(1, '{"a": 1, "b": 2, "c": {"d": 4}}', '1');

SELECT JSON_CONTAINS(js1, js2, '$.a') res FROM t1 where id = 1;

                  res
---------------------
                    1

SELECT JSON_CONTAINS(js1, js2, '$.b') res FROM t1 where id = 1;

                  res
---------------------
                    0

INSERT INTO t1 VALUES(2, '{"d": 4}', '1');

SELECT JSON_CONTAINS(js1, js2, '$.a') res FROM t1 where id = 2;

                  res
---------------------


SELECT JSON_CONTAINS(js1, js2, '$.c') res FROM t1 where id = 2;

                  res
---------------------


```