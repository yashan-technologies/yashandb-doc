```ebnf
to_vector = TO_VECTOR "(" expr [ "," number_of_dimensions [ "," format ]  ] ")".

vector = VECTOR "(" expr [ "," number_of_dimensions [ "," format ] ] ")".
```

TO_VECTOR函数用于将字符串类型的向量值转换为向量类型。

VECTOR函数与TO_VECTOR函数含义相同。

**expr**

表示输入的向量值或字符串类型向量值。

- 输入向量值时，该函数用于调整向量的**format**。
- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 当expr的值为NULL时，函数返回NULL。

**number_of_dimesions**

配置向量的维度，该维度必须与输入的字符串实际维度一致。

**format**

配置向量值的数据类型，支持配置为FLOAT32或FLOAT64，缺省时表示数据类型FLOAT32。

示例（HEAP表）

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

-- expr为CHAR类型

CREATE TABLE chtb(a CHAR(10));
INSERT INTO chtb VALUES('[1,1,1]');
SELECT TO_VECTOR(CHTB.A) FROM chtb;
TO_VECTOR(CHTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]

-- expr为VARCHAR类型

CREATE TABLE vctb(a VARCHAR(10));
INSERT INTO vctb VALUES('[1,1,1]');
SELECT TO_VECTOR(vctb.a) FROM vctb;
TO_VECTOR(VCTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]
  
-- expr为CLOB类型

CREATE TABLE ctb(a CLOB);
INSERT INTO ctb VALUES('[1,1,1]');
SELECT TO_VECTOR(ctb.a) FROM ctb;
TO_VECTOR(CTB.A)
------------------------------------------------------------
[1.0E+000,1.0E+000,1.0E+000]
```
