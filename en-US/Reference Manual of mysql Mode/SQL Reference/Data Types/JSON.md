
The JSON type in YashanDB is a variable-length data type that obtains binary data by parsing strings that conform to the standard JSON format.

> **Note**: 
>
> The JSON object data type is based on the UTF8 character set. When encoding a string into a JSON object, if the character set of the string is not UTF8, the string will be automatically converted to the UTF8 character set before JSON object encoding.

For a detailed description of the JSON format, please refer to the [JSON documentation](../General SQL Syntax/json).

## Storage Attributes

|Type |Byte Length |
|--------|--------------------------------|
| JSON   | 1 ~ 32MB  |

Definition format:

|Type |Format |Rules |
|--------|------| -------------------------- |
| JSON   | JSON | Variable-length binary string, no size specification needed |



The JSON type storage method in YashanDB is the same as the BLOB type, which includes both inline storage and out-of-line storage:

- When the data in a JSON column of a row is less than a certain byte limit, the JSON data will be stored inline. For HEAP tables, this limit is 4000 bytes.
- When the byte limit is exceeded, the JSON data is stored in a separate LOB space (which can have a specific tablespace assigned).

> **Note**: 
>
> Some internal metadata is generated during storage; the byte limit for heap tables (HEAP tables) includes the space occupied by this metadata.



## Usage Rules

- JSON represents variable-length binary data, which is obtained by parsing strings that conform to the standard format. The maximum length for a string that can be parsed into a JSON object is 32MB.
- The length of the data parsed into the JSON type may not be the same as the original string length; in most cases, the data converted to JSON type will be larger than the original string.
- The JSON type supports mutual conversion with strings but cannot perform arithmetic operations, comparisons, concatenations, etc. The computational capabilities of JSON are mainly provided by specific JSON-related built-in functions.
- Columns of the JSON type cannot be used as partition keys.

- When using the MySQL database client tool to connect and operate, the query results for JSON column data will be padded with spaces in front of the values, while using the Yasql tool for queries does not add such padding.

    ```sql
    -- Example using MySQL Client
    DROP TABLE IF EXISTS t1;
    CREATE TABLE t1(c_json JSON,c_int INT);
    INSERT INTO t1 VALUES('{"a":1,"b":[1,2,3],"c":{"x":1}}',NULL);
    INSERT INTO t1 VALUES('{"a":1,"b":[1,2,3],"c":{"x":1},"a":2,"a":3}',1);
    COMMIT;

    SELECT * FROM t1;
    +-----------------------------------------+-------+
    | c_json                                  | c_int |
    +-----------------------------------------+-------+
    | {"a": 1, "b": [1, 2, 3], "c": {"x": 1}} |  NULL |
    | {"a": 1, "b": [1, 2, 3], "c": {"x": 1}} |     1 |
    +-----------------------------------------+-------+

    SELECT CAST(c_json AS CHAR) AS col, c_int FROM t1;
    +-----------------------------------------+-------+
    | col                                     | c_int |
    +-----------------------------------------+-------+
    | {"a": 1, "b": [1, 2, 3], "c": {"x": 1}} |  NULL |
    | {"a": 1, "b": [1, 2, 3], "c": {"x": 1}} |     1 |
    +-----------------------------------------+-------+
    ```

- When inserting data into a JSON type column, if there are duplicate keys, the first key-value pair of the duplicate keys will be inserted while subsequent duplicate key-value pairs will be discarded.
