YashanDB provides the following concatenation operator.

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| &#124;&#124; | Binary | String concatenation, supports two or more concatenations | Any data &#124;&#124; NULL: Result is NULL |

Before actual application, you need to append the PIPES_AS_CONCAT configuration in [SQL_MODE](../SQL_MODE).

```sql
SELECT @@SESSION.sql_mode;

@@session.sql_mode
----------------------------------------------------------------
ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL


BEGIN
   EXECUTE IMMEDIATE 'SET @@SESSION.sql_mode = CONCAT(@@SESSION.sql_mode, ",PIPES_AS_CONCAT")';
END;
/


SELECT @@SESSION.sql_mode;

@@session.sql_mode
----------------------------------------------------------------
ERROR_FOR_DIVISION_BY_ZERO,IGNORE_SPACE,NO_AUTO_CREATE_USER,NO_AUTO_VALUE_ON_ZERO,NO_BACKSLASH_ESCAPES,NO_ENGINE_SUBSTITUTION,NO_UNSIGNED_SUBTRACTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ONLY_FULL_GROUP_BY,PIPES_AS_CONCAT,STRICT_ALL_TABLES,STRICT_TRANS_TABLES,TIME_TRUNCATE_FRACTIONAL


```

In YashanDB (mysql mode), in addition to the concatenation operator, you can also use [CONCAT](../内置函数/CONCAT) to implement concatenation operations.

Data Types
----

All data types except UDT can participate in concatenation operations.

The concatenation operator requires the data to be of character type. For other types, YashanDB performs implicit conversion, unifying them as character type before the concatenation operation.

***Example***

```sql
SELECT 'abc'||'nnk'||123 FROM DUAL;
'ABC'||'NNK'||123
-----------------
abcnnk123    


SELECT 'abc'||'nnk'||123||null FROM DUAL;

'abc'||'nnk'||123||null
-----------------------


```