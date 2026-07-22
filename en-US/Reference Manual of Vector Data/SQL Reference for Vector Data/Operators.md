YashanDB provides the following operators for vector data:

|Operator |Operand |Meaning |NULL Participation |
| --- | --- | --- | --- |
| <->   | Binary | Calculate the Euclidean distance between two vectors | Result is NULL |
| <#> | Binary | Calculate the negative inner product distance between two vectors | Result is NULL |
| <=>   | Binary | Calculate the cosine distance between two vectors | Result is NULL |

When performing vector calculations, the input vector dimensions and data type must be consistent.

In YashanDB, bitwise operations can be achieved through:

*   Vector operators: <-> , <#> , <=>
*   Built-in functions: [VECTOR_DISTANCE](./Built-in Functions/VECTOR_DISTANCE)

The operators and functions in the table below are synonyms for each other.

|Operator|Function |
|----|----|
|<->|VECTOR_DISTANCE(expr1, expr2, EUCLIDEAN)|
|<#>|VECTOR_DISTANCE(expr1, expr2, DOT)|
|<=>|VECTOR_DISTANCE(expr1, expr2, COSINE)|

***Example*** for Heap tables

```sql
SELECT '[2.33,6.10,0.77,5.06,3.18]'<#>'[5.66,3.51,8.81,6.53,2.89]' AS col FROM dual;

        COL
-----------
 -8.36E+001

SELECT VECTOR('[2.33,6.10,0.77,5.06,3.18]')<#>TO_VECTOR('[5.66,3.51,8.81,6.53,2.89]') AS col FROM dual;

        COL
-----------
 -8.36E+001

SELECT VECTOR('[2.33,6.10,0.77,5.06,3.18]')<=>TO_VECTOR('[5.66,3.51,8.81,6.53,2.89]') AS col FROM dual;

        COL
-----------
 2.845E-001

SELECT VECTOR('[2.33,6.10,0.77,5.06,3.18]')<->TO_VECTOR('[5.66,3.51,8.81,6.53,2.89]') AS col FROM dual;

        COL
-----------
 9.202E+000

SELECT VECTOR('[2.33,6.10,0.77,5.06,3.18]',5,float32)<#>TO_VECTOR('[5.66,3.51,8.81,6.53,2.89]',5,float64) AS col FROM dual;

[1:54]YAS-00906 requires all input VECTORs to have the same dimension format : encountered dimension formats (FLOAT32, FLOAT64)
```
