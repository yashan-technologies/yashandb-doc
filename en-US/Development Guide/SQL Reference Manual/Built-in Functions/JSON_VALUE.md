```ebnf
json_value = JSON_VALUE "(" expr [FORMAT JSON] ","  json_path [RETURNING type] ")".
```
The JSON_VALUE function retrieves the scalar value from json_value based on the path described by json_path.

This function follows these rules:

- When expr is NULL or conversion to JSON type fails, the function returns NULL.
- When the results obtained from the current path expression contain multiple values, the function returns NULL.
- If the path expression ends with json_function_step and returns multiple values, the function returns NULL.
- When the returned value is an object or array, the function returns NULL.

**expr**

expr supports data types such as STRING, LOB, BFILE, RAW, and JSON; it will be converted to JSON type before operations, failing which the function returns NULL.

**FORMAT JSON**

This clause is for syntax compatibility only and has no actual meaning; it can be omitted.

**json_path**

The path expression is a constant string, and its format definition can be referred to in the [json](../General SQL Syntax/json) documentation.

**returning type**

The RETURNING clause specifies a SQL scalar type for the returned value. Currently supported types include CHAR, VARCHAR, VARCHAR2, NCHAR, NVARCHAR, TINYINT, SMALLINT, INT, BIGINT, NUMBER, FLOAT, DOUBLE, CLOB.

***Example*** (HEAP table)

```sql
SELECT JSON_VALUE('{"key4":-0.123,"key5":"test"}','$.key4') res FROM DUAL;
RES
----------------------------------------------------------------
-0.123     

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$."a".a.a') res FROM DUAL;
RES
----------------------------------------------------------------
1 

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$.."a".count()') res FROM DUAL;
RES
----------------------------------------------------------------
3    

-- Create a table to store json data table_json
CREATE TABLE IF NOT EXISTS table_json (id INT, c1 VARCHAR(300));
INSERT INTO table_json VALUES(0, '{"key1": 123, "key2": true, "key3": null, "key4": [456, false, null, {"key1": true, "key2": 789, "key3": {"key6": 123}}, [10, false, null]], "key5": {"key1": true, "key2": 789, "key3": null}}');

SELECT JSON_VALUE(c1, '$.key4.key1') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
true

SELECT JSON_VALUE(c1, '$.key4[last]') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
  

SELECT JSON_VALUE(JSON(c1), '$[0].key4[0][0][0]') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
456

SELECT JSON_VALUE('{"data": 123}', '$.data' RETURNING CHAR(3))  v  from dual;

V     
----- 
123  

SELECT JSON_VALUE('{"data": 123}', '$.data' RETURNING DOUBLE)  v  from dual;

          V 
----------- 
  1.23E+002

SELECT JSON_VALUE('{"data": 123}', '$.data' RETURNING NUMBER)  v  from dual;

          V 
----------- 
        123


SELECT JSON_VALUE('{"data": "123.45"}', '$.data' RETURNING int) v from dual;

           V 
------------ 
         123

SELECT JSON_VALUE('{"data": "-99999"}', '$.data' RETURNING int) v from dual;

           V 
------------ 
      -99999


--- Create a table with a json column for index testing
create table test(a int, b json);
insert into test values(1,  '{"key" : 2147483641 }'),
                       (8,  '{"key" : 2147483642 }'),
                       (11, '{"key" : 2147483643 }'),
                       (12, '{"key" : 2147483644 }'),
                       (15, '{"key" : 2147483647 }'),
                       (16, '{"key" : 2147483648 }'),
                       (17, '{"key" : 2147483649 }'),
                       (18, '{"key" : 2147483650 }'),
                       (19, '{"key" : 2147483651 }');

--- Create an index using json_value with the index column typed as bigint
create index int_index on test(JSON_VALUE(b, '$.key' RETURNING BIGINT));

--- Use json_value in a predicate; the query plan selects the int_index
explain select * from test where json_value(b, '$.key' returning bigint) > 2147483644;

PLAN_DESCRIPTION                                                 
---------------------------------------------------------------- 
SQL hash value: 54853043                                        
Optimizer: ADOPT_C                                              
                                                                
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  TABLE ACCESS BY INDEX ROWID   | TEST                 | SYS        |     33000|        6( 0)|                                |
|* 2 |   INDEX RANGE SCAN             | SMALLINT_INDEX       | SYS        |     33000|        4(100)|                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
                                                                
Operation Information (identified by operation id):             
---------------------------------------------------             
                                                                
   2 - Predicate : access("TEST"."JSON_VALUE(b, '$.key' RETURNING smallint)" > 2147483644)
```
