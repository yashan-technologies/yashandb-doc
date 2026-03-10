YashanDB provides the following concatenation operator:

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| &#124;&#124; | Binary | String concatenation, supports two or more concatenations | Any data &#124;&#124; NULL: Result is any data |

In YashanDB, the ways to perform concatenation operations are:

* Concatenation Operator: ||

* Built-in function: [CONCAT](../Built-in Functions (yashan Mode)/CONCAT)

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
```
