```ebnf
to_vector = TO_VECTOR "(" expr [ "," number_of_dimensions [ "," format ]  [ "," storage_format ] ] ")".

vector = VECTOR "(" expr [ "," number_of_dimensions [ "," format ] [ "," storage_format ] ] ")".
```

The TO_VECTOR function is used to convert string-type vector values to vector type.

The VECTOR function has the same meaning as the TO_VECTOR function.

**expr**

Indicates the input vector value or string-type vector value.

- When the input is a vector value, this function is used to adjust the **format** of the vector.
- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
When the value of expr is NULL, the function returns NULL.

**number_of_dimesions**

Configure the dimension of the vector, which must be consistent with the actual dimension of the input string.

**format**

Configure the data type of vector values, supporting configuration as FLOAT32 or FLOAT64. When omitted, the default data type is FLOAT32.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT TO_VECTOR('[34.6, 77.8]') FROM dual;
TO_VECTOR('[34.6,77.8]')
---------------------------------------------------------
[3.45999985E+001,7.78000031E+001]

SELECT TO_VECTOR('[34.6, 77.8]', 2, FLOAT32) FROM dual;
TO_VECTOR('[34.6,77.8]',2,FLOAT32)
---------------------------------------------------------
[3.45999985E+001,7.78000031E+001]

SELECT TO_VECTOR('[34.6, 77.8, -89.34]', 3, FLOAT32) FROM dual;
TO_VECTOR('[34.6,77.8,-89.34]',3,FLOAT32)
-----------------------------------------------------------
[3.45999985E+001,7.78000031E+001,-8.93399963E+001]

-- Input expr as CHAR type

CREATE TABLE chtb(a CHAR(10));
INSERT INTO chtb VALUES('[1,1,1]');
SELECT TO_VECTOR(CHTB.A) FROM chtb;
TO_VECTOR(CHTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]

-- Input expr as VARCHAR type

CREATE TABLE vctb(a VARCHAR(10));
INSERT INTO vctb VALUES('[1,1,1]');
SELECT TO_VECTOR(vctb.a) FROM vctb;
TO_VECTOR(VCTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]
  
-- Input expr as CLOB type

CREATE TABLE ctb(a CLOB);
INSERT INTO ctb VALUES('[1,1,1]');
SELECT TO_VECTOR(ctb.a) FROM ctb;
TO_VECTOR(CTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]
```
