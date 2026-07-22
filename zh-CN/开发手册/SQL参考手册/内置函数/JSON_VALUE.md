```ebnf
json_value = JSON_VALUE "(" expr [FORMAT JSON] ","  json_path  [RETURNING type] ")".
```
JSON_VALUE函数将基于json_path所描述的路径对json_value进行检索，并返回对应的标量值。

本函数遵循如下规则：

- 当expr为NULL或转换JSON类型失败时，函数返回NULL。
- 函数根据当前路径表达式查询得出的结果包含多个值时，函数返回NULL。
- 路径表达式末尾指定了json_function_step并且返回多个值时，函数返回NULL。
- 返回的value为object或array时，函数返回NULL。

**expr**

expr支持字符串类型、LOB、BFILE、RAW以及JSON类型。在运算之前会先转成JSON类型，转换失败后函数返回NULL。

**FORMAT JSON**

用于语法兼容，无实际含义，可省略。

**json_path**

路径表达式，为一个常量字符串，其格式定义请参考[json](../通用SQL语法/json)文档中描述。

**RETURNING type**

RETURNING子句，type为SQL标量类型，目前支持CHAR、VARCHAR、VARCHAR2、NCHAR、NVARCHAR、TINYINT、SMALLINT、INT、BIGINT、NUMBER、FLOAT、DOUBLE以及CLOB类型。

示例（HEAP表）

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

--创建用于存储json数据的表table_json
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


--- 创建带有json列的索引表
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

--- 使用json-value创建索引，索引列为bigint类型
create index int_index on test(JSON_VALUE(b, '$.key' RETURNING BIGINT));

--- 谓词中使用json_value，查询计划选中int_index索引
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
