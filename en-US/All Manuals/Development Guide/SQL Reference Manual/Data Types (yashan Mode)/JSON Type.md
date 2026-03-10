The JSON type in YashanDB is a variable-length data type that obtains binary data by parsing strings that conform to the standard JSON format.

> **Note**: 
>
> The JSON object data type is based on the UTF8 character set. When encoding a string into a JSON object, if the character set of the string is not UTF8, the string will be automatically converted to the UTF8 character set before JSON object encoding.

For a detailed description of the JSON format, please refer to the [JSON documentation](../General SQL Syntax/json).

## Storage Attributes

|Type |Byte Length |
|--------|--------------------------------|
| JSON   | Row Storage:<br>1-32MB<br>Column Storage:<br>1~32MB |

Definition Format:

|Type |Format |Rules |
|--------|------| -------------------------- |
| JSON   | JSON | Variable-length binary string, no size specification needed |

The JSON type storage method in YashanDB is the same as the BLOB type in [Large Object Types], which includes both inline storage and out-of-line storage:

- When the data in a JSON column of a row is less than a certain byte limit, the JSON data will be stored inline. For HEAP tables, this limit is 4000 bytes; for TAC/LSC tables, this limit is 32000 bytes.
- When the byte limit is exceeded, the JSON data is stored in a separate LOB space (which can have a specific tablespace assigned).

> **Note**: 
>
> Some internal metadata is generated during storage; the byte limit for heap tables (HEAP tables) includes the space occupied by this metadata, while the byte limit for LSC tables (TAC and LSC tables) does not include the space occupied by metadata.
> Taking DB_BLOCK_SIZE = 8K as an example, the maximum byte length for JSON type data in heap tables is `4G*8K=32T`.

## Usage Rules

JSON represents variable-length binary data, which is obtained by parsing strings that conform to the standard format. The maximum length for a string that can be parsed into a JSON object is 32MB.

The length of the data parsed into the JSON type may not be the same as the original string length; in most cases, the data converted to JSON type will be larger than the original string.

The JSON type supports mutual conversion with strings but cannot perform arithmetic operations, comparisons, concatenations, etc. The computational capabilities of JSON are mainly provided by specific JSON-related built-in functions.

Columns of the JSON type cannot be used as partition keys.

***Example***

```sql
--1. Create a customer_intro_json table with a JSON field under the sales user
CREATE TABLE customer_intro_json (id INT, intro JSON);

--2. Implicitly convert and insert JSON data through a string
INSERT INTO customer_intro_json 
VALUES (1, '{"name":"Jack", "city":"Beijing","school":"TsingHua University"}');
COMMIT;

--3. Insert JSON data using the JSON constructor function
INSERT INTO customer_intro_json
VALUES (2, JSON('{"name":"Bob", "city":"Shenzhen","school":"Shenzhen University"}'));
COMMIT;

--4. Query JSON data using the JSON_QUERY function
SELECT JSON_QUERY(intro, '$.name' WITH WRAPPER) name FROM customer_intro_json;
NAME
----------------------------------------------------------------
["Jack"]
["Bob"]
```
