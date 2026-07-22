```ebnf
inner_product = INNER_PRODUCT "(" expr1 "," expr2 ")".
```

INNER_PRODUCT函数用于两个向量之间的内积距离，函数返回值为DOUBLE类型。

INNER_PRODUCT()与`-1 * VECTOR_DISTANCE(DOT)`为同义词。

使用INNER_PRODUCT函数查询时，无法通过向量列上创建的基于DOT算法的索引加速查询。


**expr**

表示输入的向量类型数据或向量格式的CHAR/VARCHAR/CLOB类型数据。

- 输入CHAR/VARCHAR/CLOB类型的向量数据时，必须是一组包裹在`[]`内的非空数组，且数值不允许为nan和inf。
- 两个expr的维度数据类型和维度数必须一致。
- 当某个expr输入为NULL时，函数返回NULL。

