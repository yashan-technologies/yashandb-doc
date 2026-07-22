```ebnf
vector_norm = VECTOR_NORM "(" expr ")".
```

VECTOR_NORM函数用于计算向量的欧几里得范数，也称为L2范数。L2范数是一种常用的向量范数，用于衡量向量的长度或大小。它的计算方法是将向量中所有元素的平方和再开平方根。

该函数返回值为BINARY_DOUBLE类型。

**expr**

表示输入的向量或字符串类型向量值。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 当某个expr输入为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
-- expr: VECTOR类型

SELECT VECTOR_NORM(TO_VECTOR('[34.6, 77.8]', 2, FLOAT64)) FROM dual;

VECTOR_NORM(TO_VECTOR('[34.6,77.8]',2,FLOAT64))
-----------------------------------------------
8.515E+001
```