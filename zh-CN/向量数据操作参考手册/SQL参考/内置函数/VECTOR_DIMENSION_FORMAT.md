```ebnf
vector_dimension_format = VECTOR_DIMENSION_FORMAT "(" expr ")".
```

VECTOR_DIMENSION_FORMAT函数用于获取向量维度值的数据类型是FLOAT32或者是FLOAT64。

该函数返回值为类型为VARCHAR2。

**expr**

表示输入的向量或字符串类型向量值。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 当某个expr输入为NULL时，函数返回NULL。

示例（HEAP表）

```sql
-- expr: VECTOR类型
SELECT VECTOR_DIMENSION_FORMAT(TO_VECTOR('[34.6, 77.8]', 2, FLOAT64) ) FROM dual;

VECTOR_DIMENSION_FORMAT(TO_VECTOR('[34.6,77.8]',2,
--------------------------------------------------
FLOAT64      

```