```ebnf
vector_dimension_count = VECTOR_DIMENSION_COUNT "(" expr ")".
```

VECTOR_DIMENSION_COUNT函数用于获取向量的维度数。

该函数返回值为NUMBER类型。

**expr**

表示输入的向量或字符串类型向量值。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 当某个expr输入为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT VECTOR_DIMENSION_COUNT('[7.03,3.55,-2.93,8.96]') AS col FROM dual;

        COL
-----------
          4

```